# **TSSR-PARIS-0924-P3-G2-EcoTechSolutions**

## Sommaire

1) Contexte

2) Pré-requis techniques

3) Installation et Configuration des équipements et ressources

## Contexte

```
# 1. Objectifs

1. SÉCURITÉ - Mettre en place un serveur de gestion des mises à jour **WSUS**
   1. Installation sur VM dédiée
   2. Liaison avec l'AD :
      1. Les groupes dans WSUS sont liés à l'AD
      2. Les MAJ sont liées aux OU
   3. Gérer différemment les MAJ pour :
      1. Les clients
      2. Les serveurs
      3. Les DC
2. AD - Rôles FSMO
   1. Faire en sorte d'avoir au moins 3 DC sur le domaine
   2. Partager les rôles FSMO entre les DC
3. Amélioration de l'infrastructure
   1. serveur DHCP foctionnel (vyos ou serveur dédié)
   2. routage foctionnel entre les services Vyos ou via PFSENSE
   3. PFSENSE gére les régles entre DMZ, LAN et WAN
```

## Pré-requis techniques

## Installation et Configuration des équipements et ressources

### Installation du serveur WSUS

WSUS signifie Windows Server Update Service et il s’agit du rôle intégré à Windows Server qui a une mission bien précise, la distribution des mises à jour des produits Microsoft sur les postes de travail et serveurs de notre entreprise.

## Installation et Configuration WSUS - Étape par Étape

### 1. Préparation de l'Environnement

#### 1.1 Prérequis
- Une VM dédiée sous Windows Server avec un accès administrateur.
- Connexion Internet pour télécharger les mises à jour.
- Une partition dédiée pour le stockage des mises à jour WSUS (recommandé).

#### 1.2 Installation des Pré-requis
Ouvrir une session PowerShell en mode administrateur et exécuter :
```powershell
Install-WindowsFeature -Name UpdateServices -IncludeManagementTools
```
- Installer la base de données interne si nécessaire.
- Redémarrer le serveur après l’installation.

### 2. Configuration de WSUS

#### 2.1 Lancer l’Assistant de Configuration WSUS
- Ouvrir **WSUS Console** via le **Server Manager**.
- Suivre l’assistant de configuration.
- Sélectionner l’option de stockage des mises à jour.
- Configurer la connexion au serveur Microsoft Update.
- Choisir les classifications et langues des mises à jour.
- Synchroniser WSUS avec Microsoft Update.

### 3. Liaison WSUS avec Active Directory

#### 3.1 Création des Groupes de Mises à Jour dans WSUS
- Ouvrir la **Console WSUS**.
- Créer trois groupes : **Clients**, **Serveurs**, **Contrôleurs de Domaine (DC)**.

#### 3.2 Configuration des GPO pour WSUS
- Ouvrir l’éditeur de stratégie de groupe avec :
```powershell
gpedit.msc
```
- Naviguer vers **Configuration ordinateur > Modèles d'administration > Composants Windows > Windows Update**.
- Définir la politique **Spécifier l’emplacement intranet du service de mise à jour Microsoft**.
- Ajouter l’URL du serveur WSUS : `http://wsus-server:8530`.
- Appliquer les GPO aux Unités Organisationnelles (OU) correspondantes.

### 4. Gestion Différenciée des Mises à Jour

#### 4.1 Configuration pour les Clients
- Affecter les machines clientes au groupe **Clients** dans WSUS.
- Activer le mode de mise à jour automatique via GPO.
- Vérifier la réception des mises à jour via la console WSUS.

#### 4.2 Configuration pour les Serveurs
- Affecter les serveurs au groupe **Serveurs** dans WSUS.
- Paramétrer un déploiement manuel des mises à jour après validation.
- Utiliser **WSUS Approval Rules** pour différencier les applications.

#### 4.3 Configuration pour les Contrôleurs de Domaine (DC)
- Créer une politique spécifique pour les DC.
- Tester les mises à jour en préproduction avant déploiement en production.
- Assurer un suivi via les journaux d’événements WSUS.

### 5. Vérifications et Maintenance

#### 5.1 Vérification des Mises à Jour Appliquées
- Ouvrir la **Console WSUS** et consulter l’état des mises à jour.
- Exécuter sur un poste client :
```powershell
wuauclt /reportnow
wuauclt /detectnow
```
- Vérifier dans l’Observateur d’événements sous **Applications et Services Logs > Microsoft > Windows > WindowsUpdateClient**.

#### 5.2 Planification et Maintenance
- Planifier la synchronisation des mises à jour.
- Nettoyer régulièrement la base de données WSUS avec :
```powershell
wsusutil.exe cleanup
```
- Surveiller l’espace disque et l’intégrité du service WSUS.

Fin du guide.

# Guide d'Installation : AD - Rôles FSMO, DHCP, Routage et PFSENSE

## 1. Installation et Configuration d'Active Directory avec Rôles FSMO

### 1.1 Pré-requis
- Trois serveurs Windows Server (2016/2019/2022)
- Un réseau fonctionnel
- Accès administrateur sur les serveurs

### 1.2 Installation des Domain Controllers (DC)
1. Installer Windows Server sur chaque machine.
2. Renommer chaque serveur avec un nom unique (ex: `DC1`, `DC2`, `DC3`).
3. Configurer une adresse IP statique sur chaque serveur.
4. Installer le rôle AD DS sur chaque serveur via PowerShell :
   ```powershell
   Install-WindowsFeature -Name AD-Domain-Services -IncludeManagementTools
   ```
5. Promouvoir le premier serveur en tant que contrôleur de domaine principal :
   ```powershell
   Install-ADDSForest -DomainName "mondomaine.local"
   ```
6. Ajouter les autres serveurs au domaine :
   ```powershell
   Add-Computer -DomainName "mondomaine.local" -Credential (Get-Credential)
   Restart-Computer
   ```
7. Promouvoir `DC2` et `DC3` en tant que contrôleurs de domaine supplémentaires :
   ```powershell
   Install-ADDSDomainController -DomainName "mondomaine.local"
   ```

### 1.3 Répartition des rôles FSMO
Lister les rôles actuels :
```powershell
netdom query fsmo
```
Déplacer les rôles FSMO vers les différents DC :
```powershell
Move-ADDirectoryServerOperationMasterRole -Identity "DC2" -OperationMasterRole PDCEmulator, RIDMaster
Move-ADDirectoryServerOperationMasterRole -Identity "DC3" -OperationMasterRole InfrastructureMaster
```
Vérifier la nouvelle répartition :
```powershell
netdom query fsmo
```

## 2. Configuration d'un Serveur DHCP (VyOS ou Windows Server)

### 2.1 Configuration DHCP sous VyOS
1. Connectez-vous à VyOS et entrez en mode configuration :
   ```bash
   configure
   ```
2. Définissez le pool DHCP :
   ```bash
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 range 0 start 192.168.1.100
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 range 0 stop 192.168.1.200
   ```
3. Spécifiez la passerelle et le DNS :
   ```bash
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 default-router 192.168.1.1
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 dns-server 8.8.8.8
   commit
   save
   exit
   ```

## 3. Configuration du Routage avec VyOS ou PFSENSE

### 3.1 Routage sous VyOS
1. Activer le forwarding IPv4 :
   ```bash
   set system ipv6 disable
   set system forwarding
   ```
2. Définir les routes statiques :
   ```bash
   set protocols static route 192.168.2.0/24 next-hop 192.168.1.2
   ```
3. Enregistrer et appliquer :
   ```bash
   commit
   save
   exit
   ```

### 3.2 Configuration du Pare-feu et Routage sous PFSENSE
1. Installer et configurer PFSENSE.
2. Créer les interfaces WAN, LAN et DMZ.
3. Configurer les règles de pare-feu entre les zones :
   - Autoriser le trafic interne LAN↔DMZ.
   - Restreindre les accès depuis la DMZ vers le LAN.
   - Autoriser le trafic sortant WAN sous conditions.
4. Tester les connexions et ajuster les règles.

## Conclusion
Avec ces étapes, vous disposez d'un environnement AD robuste avec une répartition des rôles FSMO, un serveur DHCP fonctionnel et un routage sécurisé via VyOS ou PFSENSE.
