# Firewall PFSense & Routeur VYOS DHCP


## Sommaire

1) Guide d'installation et de configuration pfSense avec 3 interfaces réseau (DMZ, WAN, LAN)
2) Guide d'installation et configuration d'un routeur VyOS avec DHCP et 4 interfaces


Ce guide détaillé vous accompagnera dans la configuration d'un pare-feu pfSense avec trois interfaces réseau : DMZ, WAN et LAN.

## Prérequis

1. **Hardware requis** :
   - Un ordinateur ou une machine virtuelle pour installer pfSense.
   - 3 cartes réseau (une pour chaque interface : WAN, LAN et DMZ).

2. **Matériel réseau** :
   - Un routeur ou un modem pour la connexion WAN.
   - Un switch ou des câbles pour la connexion LAN et DMZ.

3. **Accès physique et réseau** à votre infrastructure.

## Étape 1 : Installation de pfSense

1. **Téléchargez l'image pfSense** :
   - Rendez-vous sur [https://www.pfsense.org/download/](https://www.pfsense.org/download/), et choisissez la version compatible avec votre architecture (x86 ou x64) et le type d'installation (CD/DVD ou USB).

2. **Créer un support d'installation** :
   - Si vous utilisez une clé USB, utilisez un outil comme Rufus pour graver l'image sur la clé USB.
   - Si vous utilisez une machine virtuelle, montez directement l'ISO téléchargé.

3. **Démarrage de l'installation** :
   - Insérez votre support d'installation dans votre serveur et démarrez-le.
   - Suivez les instructions à l'écran pour installer pfSense sur le disque dur de la machine.

## Étape 2 : Configuration initiale de pfSense

1. **Accédez à pfSense** :
   - Une fois l'installation terminée, pfSense démarrera et vous devriez voir un menu avec des options.
   - L'interface graphique pfSense est accessible via l'adresse `https://<adresse_ip_du_pfSense>`, mais vous devez d'abord effectuer une configuration de base à partir de l'interface console.

2. **Assignation des interfaces réseau** :
   - Lorsque vous accédez à la console pfSense (via un terminal ou un écran directement connecté), vous serez invité à configurer les interfaces réseau.
   - Vous aurez besoin d'assigner chaque carte réseau :
     - **WAN** : La carte qui sera connectée à l'internet (par exemple, votre modem ou votre routeur).
     - **LAN** : La carte qui sera connectée à votre réseau local.
     - **DMZ** : La carte qui sera connectée à votre zone démilitarisée.

3. **Configuration IP de base** :
   - Une fois les interfaces assignées, configurez une adresse IP statique pour le **LAN**. Par exemple :
     - Adresse IP : `192.168.1.1`
     - Masque : `255.255.255.0`
   - Configurez également une adresse IP pour le **WAN**. Si vous utilisez un DHCP, vous pouvez obtenir l'adresse IP automatiquement. Sinon, configurez-la manuellement en fonction de votre fournisseur d'accès.

## Étape 3 : Configuration du pare-feu et des règles de base

1. **Accédez à l'interface Web** :
   - Connectez-vous à l'interface Web pfSense en accédant à l'adresse IP du LAN via un navigateur : `https://192.168.1.1`.

2. **Connexion initiale** :
   - Lors de votre première connexion, vous serez invité à créer un mot de passe administrateur pour pfSense.

3. **Configurer le WAN** :
   - Allez dans `Interfaces` > `WAN`.
   - Configurez l'interface WAN en fonction de votre connexion Internet (DHCP ou IP statique).

4. **Configurer le LAN** :
   - Allez dans `Interfaces` > `LAN`.
   - Configurez l'interface LAN pour qu'elle soit sur un réseau privé (par exemple, `192.168.1.0/24`).

5. **Configurer la DMZ** :
   - Allez dans `Interfaces` > `DMZ`.
   - Configurez l'interface DMZ avec un sous-réseau distinct (par exemple, `10.0.0.0/24`).

6. **Règles de pare-feu** :
   - Allez dans `Firewall` > `Rules`.
   - Ajoutez des règles pour chaque interface (WAN, LAN, DMZ). Voici des exemples basiques :
     - **WAN** : Autorisez uniquement le trafic sortant et certaines connexions spécifiques entrantes.
     - **LAN** : Autorisez tout le trafic sortant, mais restreignez l'accès entrant si nécessaire.
     - **DMZ** : Autorisez le trafic entrant spécifique (serveurs web, etc.) mais bloquez tout autre accès.

## Étape 4 : Configuration de la NAT

1. **Configurer la NAT** :
   - Allez dans `Firewall` > `NAT`.
   - Pour la zone WAN, configurez la NAT pour permettre la communication sortante depuis le LAN et la DMZ vers l'extérieur.
   - Vous pouvez également configurer la redirection de port (port forwarding) si vous souhaitez exposer des services depuis la DMZ.

## Étape 6 : Sauvegarde et maintenance

1. **Sauvegarde de la configuration** :
   - Allez dans `Diagnostics` > `Backup & Restore` pour sauvegarder votre configuration pfSense.

2. **Révision des logs** :
   - Vérifiez régulièrement les logs via `Status` > `System Logs` pour détecter d'éventuels problèmes ou alertes.

## Conclusion

Vous avez maintenant une configuration de base pfSense fonctionnelle avec des interfaces pour le WAN, LAN et DMZ. Assurez-vous de personnaliser les règles du pare-feu en fonction de vos besoins et de maintenir régulièrement la sécurité de votre infrastructure.


# Guide d'installation et configuration d'un routeur VyOS avec DHCP et 4 interfaces

Ce guide vous guidera à travers l'installation et la configuration d'un routeur VyOS avec quatre interfaces réseau : une vers le serveur ADDS, une vers le pfSense, et deux pour les VLAN des utilisateurs. Il inclut la configuration du service DHCP.

## Prérequis

1. **Matériel requis** :
   - Un serveur ou une machine virtuelle avec VyOS installé.
   - 4 cartes réseau physiques ou virtuelles (une vers le serveur ADDS, une vers pfSense, deux vers les VLAN des utilisateurs).

2. **Infrastructure réseau** :
   - Assurez-vous que votre réseau est correctement câblé pour connecter le routeur VyOS à chaque composant :
     - Interface 1 vers le **serveur ADDS**.
     - Interface 2 vers **pfSense** (rôle de la passerelle).
     - Interfaces 3 et 4 pour les **VLAN utilisateurs**.

3. **Accès à la machine VyOS** :
   - Vous devez pouvoir vous connecter à VyOS via SSH ou en utilisant un terminal local.

## Étape 1 : Installation de VyOS

1. **Téléchargez VyOS** :
   - Rendez-vous sur [VyOS Downloads](https://vyos.io/download/) et téléchargez la version appropriée (généralement la version stable la plus récente).

2. **Installation de VyOS sur le matériel** :
   - Si vous utilisez une machine virtuelle, montez l'ISO de VyOS et suivez les instructions pour l'installer sur le disque dur.
   - Si vous utilisez du matériel physique, gravez l'image ISO sur un CD ou une clé USB, puis démarrez à partir de ce support pour installer VyOS.

3. **Accédez à VyOS via SSH** :
   - Après l'installation, connectez-vous à VyOS via SSH avec l'adresse IP par défaut, ou en utilisant un terminal local si vous êtes physiquement devant la machine.

## Étape 2 : Configuration initiale des interfaces réseau

1. **Accédez à VyOS en mode configuration** :
   - Une fois connecté, passez en mode configuration avec la commande :
     ```bash
     configure
     ```

2. **Configurer les interfaces réseau** :
   - Identifiez vos interfaces réseau en utilisant la commande suivante pour afficher les interfaces disponibles :
     ```bash
     show interfaces
     ```
   - Vous devriez voir vos interfaces réseau listées comme `eth0`, `eth1`, `eth2`, et `eth3`. Configurez-les comme suit :
     - **eth0** : Interface vers le serveur ADDS (réseau interne).
     - **eth1** : Interface vers pfSense (réseau de passerelle).
     - **eth2** : Interface vers le VLAN utilisateur 1.
     - **eth3** : Interface vers le VLAN utilisateur 2.

3. **Attribuer des adresses IP aux interfaces** :
   - **Interface ADDS (eth0)** :
     ```bash
     set interfaces ethernet eth0 address '192.168.1.1/24'
     ```
   - **Interface pfSense (eth1)** :
     ```bash
     set interfaces ethernet eth1 address '192.168.2.1/24'
     ```
   - **Interface VLAN 1 (eth2)** :
     ```bash
     set interfaces ethernet eth2 address '10.0.1.1/24'
     ```
   - **Interface VLAN 2 (eth3)** :
     ```bash
     set interfaces ethernet eth3 address '10.0.2.1/24'
     ```

4. **Appliquer la configuration** :
   - Une fois les interfaces configurées, appliquez les modifications :
     ```bash
     commit
     save
     ```

## Étape 3 : Configurer le DHCP

1. **Activer le service DHCP pour le VLAN 1 et VLAN 2** :
   - **VLAN 1 (eth2)** :
     ```bash
     set service dhcp-server shared-network-name LAN subnet 10.0.1.0/24 range LAN-range start 10.0.1.100 stop 10.0.1.200
     set service dhcp-server shared-network-name LAN subnet 10.0.1.0/24 default-router '10.0.1.1'
     set service dhcp-server shared-network-name LAN subnet 10.0.1.0/24 dns-server '192.168.1.1'  # Serveur DNS (ADDS)
     ```
   - **VLAN 2 (eth3)** :
     ```bash
     set service dhcp-server shared-network-name VLAN2 subnet 10.0.2.0/24 range VLAN2-range start 10.0.2.100 stop 10.0.2.200
     set service dhcp-server shared-network-name VLAN2 subnet 10.0.2.0/24 default-router '10.0.2.1'
     set service dhcp-server shared-network-name VLAN2 subnet 10.0.2.0/24 dns-server '192.168.1.1'  # Serveur DNS (ADDS)
     ```

2. **Activer le service DHCP** :
   - Appliquez la configuration du serveur DHCP :
     ```bash
     commit
     save
     ```

## Étape 4 : Configurer les routes et les règles de pare-feu

1. **Configurer la route vers le serveur ADDS** :
   - Ajoutez une route statique pour acheminer le trafic vers le serveur ADDS :
     ```bash
     set protocols static route 192.168.1.0/24 next-hop '192.168.2.2'  # Adresse du pfSense sur eth1
     ```

2. **Configurer les règles de pare-feu de base** :
   - Ajoutez une règle pour autoriser le trafic entre les VLANs et vers Internet via pfSense.
   - Par exemple, pour autoriser le trafic du VLAN 1 et VLAN 2 vers Internet, ajoutez des règles sur VyOS :
     ```bash
     set firewall name LAN-to-WAN default-action 'drop'
     set firewall name LAN-to-WAN rule 10 action 'accept'
     set firewall name LAN-to-WAN rule 10 source address '10.0.1.0/24'
     set firewall name LAN-to-WAN rule 10 destination address '0.0.0.0/0'

     set firewall name VLAN2-to-WAN default-action 'drop'
     set firewall name VLAN2-to-WAN rule 10 action 'accept'
     set firewall name VLAN2-to-WAN rule 10 source address '10.0.2.0/24'
     set firewall name VLAN2-to-WAN rule 10 destination address '0.0.0.0/0'
     ```

3. **Appliquer les règles** :
   - Appliquez les modifications des règles de pare-feu :
     ```bash
     commit
     save
     ```

## Étape 5 : Vérification et tests

1. **Vérifiez les interfaces** :
   - Vérifiez les interfaces et les configurations réseau :
     ```bash
     show interfaces
     ```

2. **Test DHCP** :
   - Connectez un appareil sur les VLANs (VLAN 1 ou VLAN 2) et vérifiez qu'il reçoit une adresse IP du serveur DHCP.

3. **Test de la connectivité** :
   - Testez la connectivité depuis un appareil sur un VLAN vers le serveur ADDS et Internet (via pfSense) pour confirmer que tout fonctionne correctement.

## Conclusion

Vous avez maintenant configuré un routeur VyOS avec DHCP sur deux VLANs utilisateurs, une interface vers un serveur ADDS et une interface vers pfSense pour la gestion de la passerelle et de l'Internet. Ce routeur est prêt à gérer votre réseau avec des services de base pour la gestion des VLANs et des adresses IP dynamiques.



