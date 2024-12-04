# Convention de Nommage pour l'Infrastructure 

---

## **1. Nom de Domaine** 
- FQDN (Nom de Domaine Entièrement Qualifié) : `ecotechsolutions.lan`  


---

## **2. Unités Organisationnelles (OU)** 
### **Structure** :
- **Niveaux** :  ou 3 niveaux
  - Niveau 1 : Par Types (ex. : `Ordinateur``Utilisateur` ) 
  - Niveau 2 : Par Département (ex. : `Finance`, `Communication`, `Développement`)
  - Niveau 3 : Par Localisation (ex. : `Paris`)
  - Niveau 4 : Optionnel pour les services ou sous-fonctions (ex. : `Relation Médias`, `Rédacteur`)
  

### **Exemple de Hiérarchie**
- **Par Département** : 
```css
Bordeaux
  ├── Direction
  ├── Directtion des Ressources Humaines
  ├── DSI
  ├── Communication
  ├── Developpement
  ├── Finance et Comptabilité
  └── Service Commercial
  
```


---

## **3. Groupes de Sécurité** 
### **Convention de Nommage** :
- **Format** : `[Scope]_[Type]_[Department/Function]`
  - Scope : `L` (Local), `G` (Global)
  - Type : `US` (Utilisateurs), `PC` (Ordinateurs)
  - Department/Function : Identifier le département ou la fonction associée.

### **Exemples** :
- `L_PC_SCOM` : Groupe global pour les utilisateurs du service commercial.
- `G_US_DEV` : Groupe local pour les ordinateurs du département Développement.

---

## **4. Ordinateurs** 
### **Placement** :
- Organiser les ordinateurs dans l'AD selon :
  - Localisation (ex. : `Paris\PCs`)
  - Département (ex. : `Finance\Laptops`)

### **Convention de Nommage** 
- **Format** : `[Lieux]_[Department]_[Type][ID]`
  - Localisation (Ville Paris, Bordeaux, Nantes)
  - Département (ex. : `COM : Communication`)
  - Type :
    - `LT` : Laptops
    - `DT` : Desktops
    - `SRV` : Serveurs suivi du type ( DHCP, DNS, ADDS, NAS ...)
    - `RT` : Routeurs ( PRIN et SERV)
    - `IMP` : Imprimante 
  - ID : Nom de PC 

### **Exemples** :
- `BOR_COM_LPT_PA11094` : Laptop PA11094 de la communication à Bordeaux.
- `BOR_SRV_DHCP` : Serveur DHCP à Bordeaux.
- `BOR_RT_PRIN` : Routeur principal à Bordeaux.
- `BOR_SCOM_IMP` : Imprimante du Service Commercial à Bordeaux.
---

## **5. Utilisateurs** 
### **Placement** :
- Grouper par :
  - Département (ex. : `Paris\Utilisateurs`)
  - Fonction (ex. : `Marketing\Utilisateurs`)

### **Convention de Nommage** :
- **Format** : `[FirstInitialLastName]_[Department]`
  - Standard : `ATorres_COMM` : Ahmed Torres dans Développement.
  - Homonymes : Ajouter deuxième lettre du prénom (ex. : `AHTorres_DEV`).
  - Temporaire : Préfixe `TMP_` (ex. : `TMP_ARahman`).

---

## **6. Objets de Stratégie de Groupe (GPO)** 
### **Convention de Nommage** :
- **Format** : `[Target]_[Department]_[Function]_[Version/Date]`
  - Target : `USER` (Utilisateurs), `ORDI` (Ordinateurs).
  - Department : Département associé.
  - Function : Fonction ou objectif de la GPO.
  - Version/Date : Révision ou version de la GPO.

### **Exemples** :
- `ORDI_DEV_GESTAL_2024` : GPO de configuration pour l’environnement de test dans Développement.
- `USER_RH_GMDP_2024` : GPO utilisateur pour la gestion des mots de passses.
---
## **7. Liste et Nommage du matériel** 


| Site      | Département                   | Nom de PC | Nommage                  |
|-----------|-------------------------------|-----------|--------------------------|
| Bordeaux  | Communication                 | PA11094   | BOR_COM_LPT_PA11094      |
| Bordeaux  | Communication                 | PA19149   | BOR_COM_LPT_PA19149      |
| Bordeaux  | Communication                 | PA19149   | BOR_COM_LPT_PA19149      |
| Bordeaux  | Communication                 | PA88356   | BOR_COM_LPT_PA88356      |
| Bordeaux  | Communication                 | PA10218   | BOR_COM_LPT_PA10218      |
| Bordeaux  | Communication                 | PA92969   | BOR_COM_LPT_PA92969      |
| Bordeaux  | Communication                 | PA76764   | BOR_COM_LPT_PA76764      |
| Bordeaux  | Communication                 | PA68385   | BOR_COM_LPT_PA68385      |
| Nantes    | Communication                 | -         | NAN_COM_LPT_EXT1         |
| Bordeaux  | Développement                 | PA82471   | BOR_DEV_LPT_PA82471      |
| Bordeaux  | Développement                 | PA41987   | BOR_DEV_LPT_PA41987      |
| Bordeaux  | Développement                 | PA90613   | BOR_DEV_LPT_PA90613      |
| Bordeaux  | Développement                 | PA93655   | BOR_DEV_LPT_PA93655      |
| Bordeaux  | Développement                 | PA27339   | BOR_DEV_LPT_PA27339      |
| Bordeaux  | Développement                 | PA69037   | BOR_DEV_LPT_PA69037      |
| Bordeaux  | Développement                 | PA83940   | BOR_DEV_LPT_PA83940      |
| Bordeaux  | Développement                 | PA33002   | BOR_DEV_LPT_PA33002      |
| Bordeaux  | Développement                 | PA48530   | BOR_DEV_LPT_PA48530      |
| Paris     | Développement                 | -         | BOR_DEV_LPT_EXT1         |
| Paris     | Développement                 | -         | BOR_DEV_LPT_EXT2         |
| Paris     | Développement                 | -         | BOR_DEV_LPT_EXT3         |
| Paris     | Développement                 | -         | BOR_DEV_LPT_EXT4         |
| Paris     | Développement                 | -         | BOR_DEV_LPT_EXT5         |
| Bordeaux  | Direction                     | PA12959   | BOR_DIR_LPT_PA12959      |
| Bordeaux  | Direction                     | PA75792   | BOR_DIR_LPT_PA75792      |
| Bordeaux  | Direction des Ressources Humaines | PA31784 | BOR_DIR_LPT_PA31784      |
| Bordeaux  | Direction des Ressources Humaines | PA66766 | BOR_DIR_LPT_PA66766      |
| Bordeaux  | Direction des Ressources Humaines | PA20618 | BOR_DIR_LPT_PA20618      |
| Bordeaux  | DSI                           | PA21577   | BOR_DSI_LPT_PA21577      |
| Bordeaux  | DSI                           | PA76820   | BOR_DSI_LPT_PA76820      |
| Bordeaux  | DSI                           | PA85785   | BOR_DSI_LPT_PA85785      |
| Paris     | DSI                           | -         | PAR_DSI_LPT_EXT1         |
| Paris     | DSI                           | -         | PAR_DSI_LPT_EXT2         |
| Paris     | DSI                           | -         | PAR_DSI_LPT_EXT3         |
| Paris     | DSI                           | -         | PAR_DSI_LPT_EXT4         |
| Paris     | DSI                           | -         | PAR_DSI_LPT_EXT5         |
| Bordeaux  | Finance et Comptabilité       | PA57512   | BOR_FIN_LPT_PA57512      |
| Bordeaux  | Finance et Comptabilité       | PA12959   | BOR_FIN_LPT_PA12959      |
| Bordeaux  | Finance et Comptabilité       | PA34551   | BOR_FIN_LPT_PA34551      |
| Bordeaux  | Finance et Comptabilité       | PA31320   | BOR_FIN_LPT_PA31320      |
| Bordeaux  | Finance et Comptabilité       | PA68447   | BOR_FIN_LPT_PA68447      |
| Bordeaux  | Finance et Comptabilité       | PA68867   | BOR_FIN_LPT_PA68867      |
| Bordeaux  | Finance et Comptabilité       | PA30790   | BOR_FIN_LPT_PA30790      |
| Bordeaux  | Finance et Comptabilité       | PA56314   | BOR_FIN_LPT_PA56314      |
| Bordeaux  | Service Commercial            | PA16564   | BOR_SCOM_LPT_PA16564     |
| Bordeaux  | Service Commercial            | PA46917   | BOR_SCOM_LPT_PA46917     |
| Bordeaux  | Service Commercial            | PA59874   | BOR_SCOM_LPT_PA59874     |
| Bordeaux  | Service Commercial            | PA16013   | BOR_SCOM_LPT_PA16013     |
| Bordeaux  | Service Commercial            | PA16353   | BOR_SCOM_LPT_PA16353     |
| Bordeaux  | Service Commercial            | PA19149   | BOR_SCOM_LPT_PA19149     |
| Bordeaux  | Service Commercial            | PA82880   | BOR_SCOM_LPT_PA82880     |
| Bordeaux  | Service Commercial            | PA90176   | BOR_SCOM_LPT_PA90176     |
| Bordeaux  | Service Commercial            | PA90233   | BOR_SCOM_LPT_PA90233     |
| Bordeaux  | Service Commercial            | PA37602   | BOR_SCOM_LPT_PA37602     |
| Bordeaux  | Service Commercial            | PA12427   | BOR_SCOM_LPT_PA12427     |
| Bordeaux  | Service Commercial            | PA44614   | BOR_SCOM_LPT_PA44614     |
| Bordeaux  | Service Commercial            | PA79817   | BOR_SCOM_LPT_PA79817     |
| Bordeaux  | Service Commercial            | PA64810   | BOR_SCOM_LPT_PA64810     |
| Bordeaux  | Service Commercial            | PA39395   | BOR_SCOM_LPT_PA39395     |
| Bordeaux  | Routeur Principal             | N/A       | BOR_RT_PRIN              |
| Bordeaux  | Routeur Serveur               | N/A       | BOR_RT_SERV              |
| Bordeaux  |Imprimante                     | N/A       | BOR_SCOMM_IMP             |
| Paris     | N/A                           | N/A       | Rout_Par1                |
| Paris     | N/A                           | N/A       | Swit_Par1                |
| Nantes    | N/A                           | N/A       | Rout_Nan1                |
|           | N/A                           | N/A       | PRN_PAR_                 |
| Bordeaux  | N/A                           | N/A       | SRV_BOR_DHCP_PC          |
| Bordeaux  | N/A                           | N/A       | SRV_BOR_DNSAD_PC         |
| Bordeaux  | N/A                           | N/A       | SRV_BOR_NAS_PC           |
| Bordeaux  | N/A                           | N/A       | SRV_BOR_NAS_PC           |

