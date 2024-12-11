# Administration Réseau EcoTechSolutions - Semaine 3

## Table des matières

1. [Introduction](#1-introduction)
2. [Objectifs de la semaine](#2-objectifs-de-la-semaine)
3. [Détail des actions réalisées](#3-détail-des-actions-réalisées)
    - [3.1. Description des actions entreprises](#31-description-des-actions-entreprises)
    - [3.2. Résultats obtenus](#32-résultats-obtenus)
6. [Bilan des objectifs de la semaine](#6-bilan-des-objectifs-de-la-semaine)

---

## 1. Introduction

### Contexte du projet :
L’entreprise **EcoTechSolutions**, basée à Bordeaux, met en place une infrastructure réseau hybride comprenant des serveurs et des machines client sous Windows et Linux. Ce projet vise à créer un réseau fiable et sécurisé pour les collaborateurs et prestataires dans les différentes localisations de l'entreprise qui se trouvent à Bordeaux, Paris et Nantes.

### Objectifs du projet :
- Mise en place et gestion des services via une configuration en **Active Directory** (AD), **DNS** et **DHCP**.
- Sécurisation de l'infrastructure avec **réplication des serveurs** et **gestion des permissions**.
- Déploiement et gestion des machines clientes sous **Windows** et **Ubuntu**.
- **Automatisation** des tâches par scripts.

### Objectif du rapport hebdomadaire :
Ce document est destiné à rendre compte des actions réalisées chaque semaine dans le cadre de l'administration du réseau, en suivant une méthodologie commune. Il évoluera au fil de l’avancement du projet et s’adaptera aux tâches spécifiques de chaque semaine.

---

## 2. Objectifs de la semaine

### Résumé des tâches à réaliser :
- Gestion de l'arborescence AD avec script PowerShell : création d'OU.
- Création automatisée des utilisateurs à partir d'un fichier CSV.
- Création d'une VM client Windows 10 Pro et d'une VM client Ubuntu.
- Configuration de GPO de sécurité :
  - gestion du pare-feu
  - blocage complet ou partiel du panneau de configuration
  - verrouillage du compte après 3 erreurs de mot de passe
  - écran de veille avec mot de passe en sortie
- Configuration de GPO standard :
  - fond d'écran
  - gestion de l'alimentation
  - mappage de lecteur

---

## 3. Détail des actions réalisées

### 3.1. Description des actions entreprises

#### Action 1 : Création et gestion des Unités d'Organisation (OU) via PowerShell
- **Objectif** : Organiser les objets dans Active Directory en créant des Unités d'Organisation (OU) spécifiques pour les départements et utilisateurs.
- **Procédure** :
  1. Ouvrir une session PowerShell en tant qu'administrateur sur le serveur.
  2. Créer une nouvelle Unité d'Organisation (OU) en utilisant la commande suivante pour le département "Marketing" :
     ```powershell
     New-ADOrganizationalUnit -Name "Marketing" -Path "DC=ecoTech,DC=local"
     ```
     Remarque : Remplacez "Marketing" par le nom du département ou de l'équipe pour chaque OU à créer.
  3. Pour vérifier la création des OU, utilisez cette commande :
     ```powershell
     Get-ADOrganizationalUnit -Filter * -SearchBase "DC=ecoTech,DC=local"
     ```
  4. Ouvrir **Active Directory Users and Computers** pour visualiser les OU nouvellement créées et confirmer qu'elles apparaissent sous le domaine correct.
  5. Si des erreurs de permission apparaissent, assurez-vous que votre compte PowerShell a les droits nécessaires pour créer des objets dans AD.

#### Action 2 : Création automatisée des utilisateurs via fichier CSV
- **Objectif** : Automatiser la création d'un grand nombre d'utilisateurs dans Active Directory à partir d'un fichier CSV.
- **Procédure** :
  1. Prévoir un fichier CSV avec les informations des utilisateurs à ajouter
  2. Utiliser un script PowerShell pour importer le fichier CSV et créer automatiquement les utilisateurs dans Active Directory :
     ```powershell
     Import-Csv "C:\Users.csv" | ForEach-Object {
         New-ADUser -SamAccountName $_.Username -GivenName $_.FirstName -Surname $_.LastName -UserPrincipalName "$($_.Username)@ecoTech.local" -AccountPassword (ConvertTo-SecureString $_.Password -AsPlainText -Force) -Enabled $true -PassThru
     }
     ```
     Assurez-vous que les chemins et les informations dans le fichier CSV sont corrects. Si une erreur se produit (par exemple, un doublon d'utilisateur), PowerShell renverra un message d'erreur indiquant le problème.
  3. Vérifier dans **Active Directory Users and Computers** que tous les utilisateurs ont été créés et sont visibles avec les bons attributs.
  4. Effectuer un test de connexion avec l'un des utilisateurs créés pour vérifier que tout fonctionne correctement.

#### Action 3 : Création de machines virtuelles (VM) client Windows 10 Pro et Ubuntu
- **Objectif** : Créer des machines virtuelles sous Windows 10 Pro et Ubuntu pour les tests d'intégration au domaine.
- **Procédure** :
  1. Créer une machine virtuelle Windows 10 Pro en utilisant le template existant dans Proxmox :
     - Sélectionner "Cloner" dans l'interface Proxmox.
     - Choisir "Windows 10 Pro" comme template.
     - Configurer les ressources (CPU, RAM, Disque) selon les besoins de votre projet.
     - Attribuer une adresse IP statique pour la machine (ex. 192.168.1.78).
     - Configurer le nom d'hôte et intégrer la VM au domaine via l'interface **Système**.
  2. Créer une machine virtuelle Ubuntu en suivant la même procédure, mais cette fois en choisissant le template Ubuntu.
     - Configurer également une IP statique et vérifier la connexion réseau.
     - Installer les mises à jour Ubuntu via la commande `sudo apt-get update && sudo apt-get upgrade`.
  3. Vérifier la connexion réseau des machines et leur capacité à rejoindre le domaine Active Directory.

#### Action 4 : Configuration des GPOs de sécurité (Verrouillage de compte, Pare-feu, etc.)
- **Objectif** : Configurer et appliquer des stratégies de sécurité via les GPO pour sécuriser les machines et les utilisateurs.
- **Procédure** :
  1. **Création d'une GPO pour le verrouillage du compte** :
     1. Ouvrir la console **Group Policy Management (GPMC)**.
     2. Créer une nouvelle GPO : **"Verrouillage de compte"**.
     3. Clic droit sur la GPO et choisir **Editer**.
     4. Sous **Computer Configuration** → **Policies** → **Windows Settings** → **Security Settings** → **Account Lockout Policy** :
        - Définir le **"Account lockout threshold"** à **3** tentatives.
        - Définir le **"Account lockout duration"** à **30 minutes**.
        - Définir le **"Reset account lockout counter after"** à **300 secondes**.
     5. Appliquer et tester la GPO sur un poste client pour vérifier qu'après trois tentatives infructueuses, le compte est effectivement verrouillé.
  2. **Configuration du pare-feu via GPO** :
     1. Créer une nouvelle GPO pour la gestion du pare-feu.
     2. Sous **Computer Configuration** → **Policies** → **Windows Settings** → **Security Settings** → **Windows Firewall with Advanced Security** :
        - Configurer les règles de pare-feu selon les besoins (par exemple, bloquer les connexions entrantes non sollicitées).
        - Appliquer la GPO et tester sur un poste client pour vérifier que les règles de pare-feu sont appliquées correctement.
  3. **Application des restrictions de panneau de configuration et écran de veille avec mot de passe** :
     1. Créer une GPO qui restreint l'accès au panneau de configuration.
     2. Sous **User Configuration** → **Policies** → **Administrative Templates** → **Control Panel** → **Prohibit access to Control Panel** :
        - Activer la politique pour interdire l'accès.
     3. Configurer l'écran de veille avec un mot de passe sous **User Configuration** → **Policies** → **Administrative Templates** → **Control Panel** → **Display** :
        - Activer l'écran de veille et spécifier un délai avant l'activation.

---

### 3.2. Résultats obtenus

#### Action 1 : Création des Unités d'Organisation (OU)
- **Résultat** : Les OU ont été créées avec succès. La hiérarchie Active Directory est maintenant bien structurée pour les différents départements, facilitant la gestion des utilisateurs et des groupes.
- **Vérification** : Toutes les OU sont visibles dans **Active Directory Users and Computers**, et les utilisateurs ont été placés dans les OU appropriées.

#### Action 2 : Création automatisée des utilisateurs via fichier CSV
- **Résultat** : Tous les utilisateurs ont été créés correctement sans erreur. La méthode CSV a permis de gagner du temps dans la gestion des utilisateurs.
- **Vérification** : Les utilisateurs sont visibles dans **Active Directory Users and Computers** et peuvent se connecter avec leurs identifiants sans problème.

#### Action 3 : Création des machines virtuelles
- **Résultat** : La machine virtuelle Windows 10 a été créée avec succès et intégrée au domaine. La machine Ubuntu reste à être intégrée au domaine mais la procédure reste à meettre en oeuvre.
- **Vérification** : La machine virtuelle Windows 10 peut se connecter au domaine et l'accès au réseau est stable.

#### Action 4 : Configuration des GPOs de sécurité
- **Résultat** : La GPO pour le verrouillage des comptes a été appliquée et fonctionne comme prévu (verrouillage après 3 tentatives échouées).
- **Vérification** : Le pare-feu a été configuré correctement, et les restrictions sur le panneau de configuration ainsi que l'écran de veille avec mot de passe fonctionnent comme prévu.

---

### 3.3. Modifications apportées

- **Modification des paramètres de sécurité du mot de passe** : La politique de mot de passe a été modifiée pour forcer l'utilisation de mots de passe complexes (min. 12 caractères).
- **Ajout d’un utilisateur administrateur supplémentaire** : Un compte administrateur a été créé pour les opérations de maintenance et la gestion des GPO.
- **Amélioration de la configuration des machines virtuelles** : Les VM ont été configurées pour utiliser des adresses IP statiques afin d'améliorer la gestion réseau.

---

## 4. Bilan des objectifs de la semaine

- **Objectifs atteints** : La majorité des actions prévues ont été réalisées avec succès, notamment la création des OU et des utilisateurs, ainsi que la mise en place des GPO de sécurité.
- **Problèmes rencontrés** : Des conflits de GPO ont été observés lorsque des GPO existantes ont été écrasées par de nouvelles configurations, ce qui a nécessité des ajustements. Des tests supplémentaires ont été réalisés pour résoudre ces conflits.
