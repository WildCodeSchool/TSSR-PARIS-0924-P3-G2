# **TSSR-0924-P3-G2-EcoTechSolutions**

## Sommaire

1) Utilisation de base : Comment utiliser les fonctionnalités clés
   a) Guide d'Utilisation de WSUS
   b) Guide Utilisateur : Utilisation d'Active Directory, DHCP, Routage et PFSENSE


## Guide d'Utilisation de WSUS

### 1. Accéder à la Console WSUS
- Ouvrir **Server Manager**.
- Aller dans **Outils** > **Windows Server Update Services**.

### 2. Gestion des Groupes et Liaison avec Active Directory

#### 2.1 Vérifier la liaison avec AD
- Ouvrir la console **WSUS**.
- Aller dans **Ordinateurs** > **Groupes d'ordinateurs**.
- Vérifier que les groupes (Clients, Serveurs, DC) sont bien créés.
- Confirmer que les ordinateurs apparaissent correctement via la GPO appliquée.

#### 2.2 Vérifier les Groupes WSUS liés aux OU AD
- Ouvrir **GPMC (Gestion des Stratégies de Groupe)**.
- Vérifier que les stratégies de mise à jour Windows sont bien appliquées aux différentes OU (Clients, Serveurs, DC).
- Modifier la stratégie si nécessaire sous **Configuration ordinateur > Modèles d'administration > Composants Windows > Windows Update**.

### 3. Gérer les Mises à Jour selon le Type de Machine

#### 3.1 Approuver et Déployer les Mises à Jour
- Ouvrir **WSUS Console**.
- Aller dans **Mises à jour**.
- Sélectionner les mises à jour en attente.
- Approuver les mises à jour pour les groupes cibles :
  - **Clients** : Approuver immédiatement les mises à jour critiques et de sécurité.
  - **Serveurs** : Tester les mises à jour en préproduction avant déploiement.
  - **DC** : Déployer uniquement après validation sur d’autres serveurs.

#### 3.2 Forcer un Poste à Récupérer les Mises à Jour
Exécuter la commande suivante sur un client :
```powershell
wuauclt /detectnow
wuauclt /reportnow
```

#### 3.3 Vérifier l'État des Mises à Jour
- Ouvrir **WSUS Console** > **Rapports**.
- Consulter l’état des mises à jour appliquées.
- Vérifier les échecs et ajuster si nécessaire.

### 4. Maintenance et Nettoyage WSUS

#### 4.1 Nettoyer la Base WSUS
```powershell
wsusutil.exe cleanup
```

#### 4.2 Planifier la Synchronisation Automatique
- Aller dans **Options** > **Planification de la synchronisation**.
- Définir la fréquence des mises à jour.

Fin du guide.

# Guide Utilisateur : Utilisation d'Active Directory, DHCP, Routage et PFSENSE

## Introduction
Ce guide explique comment utiliser efficacement les services Active Directory, DHCP, VyOS et PFSENSE après leur installation et configuration. Il est destiné aux administrateurs et utilisateurs ayant besoin de gérer ces systèmes au quotidien.

---

## 1. Utilisation d'Active Directory (AD)
### 1.1 Gestion des utilisateurs et groupes
1. **Créer un nouvel utilisateur** :
   - Ouvrir **Utilisateurs et ordinateurs Active Directory**
   - Clic droit sur l'OU souhaitée > **Nouveau** > **Utilisateur**
   - Renseigner les informations et définir un mot de passe

2. **Attribuer un utilisateur à un groupe** :
   - Aller dans l’onglet **Membres** de l’utilisateur
   - Ajouter l’utilisateur aux groupes nécessaires (ex: `Administrateurs`, `Utilisateurs du domaine`)

3. **Réinitialiser un mot de passe** :
   - Clic droit sur un utilisateur > **Réinitialiser le mot de passe**
   - Définir un nouveau mot de passe et cocher **L'utilisateur doit changer son mot de passe à la prochaine connexion**

### 1.2 Gestion des rôles FSMO
- Vérifier les rôles FSMO :
  ```powershell
  netdom query fsmo
  ```
- Transférer un rôle FSMO à un autre contrôleur de domaine si nécessaire via **NTDSUTIL** ou **PowerShell**

---

## 2. Utilisation du Serveur DHCP (VyOS ou Windows Server)
### 2.1 Vérifier les baux actifs
- **Sous VyOS** :
  ```bash
  show dhcp server leases
  ```
- **Sous Windows Server** :
  - Ouvrir **Gestionnaire DHCP**
  - Naviguer vers **Étendue active** > **Baux actifs**

### 2.2 Ajouter une réservation DHCP
1. Trouver l'adresse MAC du client
2. Ajouter une réservation via :
   ```bash
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 static-mapping PC1 mac-address 00:1A:2B:3C:4D:5E
   set service dhcp-server shared-network-name LAN subnet 192.168.1.0/24 static-mapping PC1 ip-address 192.168.1.50
   commit
   save
   ```

---

## 3. Gestion du Routage et de la Sécurité avec PFSENSE
### 3.1 Ajouter une règle de pare-feu
1. Se connecter à l’interface web de PFSENSE
2. Aller dans **Firewall > Rules**
3. Sélectionner l’interface appropriée (LAN, WAN, DMZ)
4. Cliquer sur **Add** et configurer la règle :
   - Source/Destination : IP ou réseau spécifique
   - Port : Ex. 80 pour HTTP, 443 pour HTTPS
   - Action : **Pass** pour autoriser, **Block** pour bloquer
5. Enregistrer et appliquer les changements

### 3.2 Surveiller le trafic réseau
- Utiliser l’outil **Status > System Logs > Firewall** pour analyser les connexions bloquées
- Afficher les sessions actives avec **Diagnostics > States**

### 3.3 Gestion des VLANs
1. Accéder à **Interfaces > VLANs**
2. Ajouter un nouveau VLAN avec un ID et une interface parent
3. Affecter le VLAN à une interface et définir les règles associées

---

## 4. Dépannage et Maintenance
### 4.1 Problèmes courants et solutions
| Problème | Cause possible | Solution |
|----------|---------------|----------|
| Un utilisateur ne peut pas se connecter | Mot de passe incorrect ou compte verrouillé | Déverrouiller le compte dans AD ou réinitialiser le mot de passe |
| Un appareil ne reçoit pas d’IP via DHCP | Plage d’adresses épuisée ou service DHCP arrêté | Vérifier l’état du service DHCP et les baux actifs |
| Impossible d’accéder à Internet via PFSENSE | Mauvaise règle de pare-feu ou problème de NAT | Vérifier les règles et les logs de PFSENSE |

---

## Conclusion
Ce guide couvre les principales opérations d’administration et d’utilisation des services AD, DHCP et PFSENSE. Une gestion proactive permet d’assurer une infrastructure réseau stable et sécurisée.
