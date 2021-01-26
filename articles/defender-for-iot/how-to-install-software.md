---
title: Installation de Defender pour IoT
description: Découvrez comment installer un capteur et la console de gestion locale pour Azure Defender pour IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/2/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 49a0129ff26d4a12392066aa6304317d71fdb0f1
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247588"
---
# <a name="defender-for-iot-installation"></a>Installation de Defender pour IoT

Cet article explique comment installer les éléments suivants d’Azure Defender pour IoT :

- **Capteur** : Les capteurs Defender pour IoT collectent le trafic de partage de connexion Internet (ICS) à l’aide de la surveillance passive (sans agent). Passifs et non intrusifs, les capteurs n’ont aucun impact sur les réseaux et appareils OT et IoT. Le capteur se connecte à un port SPAN ou à un TAP réseau et commence immédiatement à surveiller votre réseau. Les détections s’affichent dans la console du capteur. Dans cette console, vous pouvez les visualiser, les examiner et les analyser dans une carte du réseau, un inventaire des appareils et un large éventail de rapports. Les exemples incluent des rapports d’évaluation des risques, des requêtes d’exploration de données et des vecteurs d’attaque. Pour plus d’informations sur les capacités des capteurs, consultez le [guide de l’utilisateur relatif aux capteurs Defender pour IoT (téléchargement direct)](./getting-started.md).

- **Console de gestion locale** : La console de gestion locale vous permet de gérer les périphériques, les risques et les vulnérabilités. Vous pouvez également l’utiliser pour surveiller les menaces et répondre aux incidents dans l’ensemble de votre entreprise. Elle offre une vue unifiée de tous les périphériques réseau, des principaux indicateurs de risque IoT et OT et des alertes détectés dans les installations où les capteurs sont déployés. Utilisez la console de gestion locale pour visualiser et gérer les capteurs dans les réseaux hermétiques.

Cet article aborde les informations d’installation suivantes :

  - **Matériel :** Détails des appliances physiques Dell et HPE.

  - **Logiciels :** Installation du logiciel de la console de gestion locale et du capteur.

  - **Appliances virtuelles :** Détails des machines virtuelles et installation du logiciel.

Après l’installation, connectez votre capteur à votre réseau.

## <a name="about-defender-for-iot-appliances"></a>À propos des appliances Defender pour IoT 

Les sections suivantes fournissent des informations sur les appliances de détection Defender pour IoT et l’appliance pour la console de gestion locale de Defender pour IoT.

### <a name="physical-appliances"></a>Appliances physiques

Le capteur de l’appliance Defender pour IoT se connecte à un port SPAN ou à un TAP réseau et commence immédiatement à collecter le trafic ICS à l’aide de la surveillance passive (sans agent). Ce processus n’a aucun impact sur les réseaux et appareils OT, car il n’est pas placé sous le chemin d’accès aux données et n’analyse pas activement les appareils OT.

Les appliances de montage en rack suivantes sont disponibles :

| **Type de déploiement** | **Entreprise** | **Entreprise** | **SMB** |  |
|--|--|--|--|--|
| **Modèle** | HPE ProLiant DL360 | Dell PowerEdge R340 XL | HPE ProLiant DL20 | HPE ProLiant DL20 |
| **Ports de surveillance** | jusqu’à 15 RJ45 ou 8 OPT | jusqu’à 9 RJ45 ou 6 OPT | jusqu’à 8 RJ45 ou 6 OPT | 4 RJ45 |
| **Bande passante maximale\** _ | 3 Go/s | 1 Go/s | 1 Go/s | 100 Mo/s |
| _ *Nombre maximal d’appareils protégés** | 30,000 | 10 000 | 15,000 | 1 000 |

*La capacité maximale de la bande passante peut varier en fonction de la distribution du protocole.

### <a name="virtual-appliances"></a>Appliances virtuelles

Les appliances virtuelles suivantes sont disponibles :

| **Type de déploiement** | **Entreprise** | **SMB** | **Ligne** |
|--|--|--|--|
| **Description** | Appliance virtuelle pour les déploiements d’entreprise | Appliance virtuelle pour les déploiements SMB | Appliance virtuelle pour les déploiements de ligne |
| **Bande passante maximale\** _ | 150 Mo/s | 15 Mo/s | 3 Mo/s |
| _ *Nombre maximal d’appareils protégés** | 3 000 | 300 | 100 |
| **Type de déploiement** | Enterprise | SMB | Lignes |
| **Description** | Appliance virtuelle pour les déploiements d’entreprise | Appliance virtuelle pour les déploiements SMB | Appliance virtuelle pour les déploiements de ligne |

*La capacité maximale de la bande passante peut varier en fonction de la distribution du protocole.

### <a name="hardware-specifications-for-the-on-premises-management-console"></a>Spécifications matérielles de la console de gestion locale

 | Élément | Description |
 |----|--|
 **Description** | Dans une architecture à plusieurs niveaux, la console de gestion locale offre une visibilité et un contrôle sur des sites répartis géographiquement. Elle s’intègre aux piles de sécurité du centre des opérations de sécurité (SOC), notamment les SIEM, aux systèmes de tickets, aux pare-feu de nouvelle génération, aux plateformes sécurisées d’accès à distance et au bac à sable de programmes malveillants ICS Defender pour IoT. |
 **Type de déploiement** | Enterprise |
 **Type d’appliance**  | Machine virtuelle Dell R340 |
 **Nombre de capteurs gérés** | Illimité |

## <a name="prepare-for-the-installation"></a>Préparation de l’installation

### <a name="access-the-iso-installation-image"></a>Accéder à l’image d’installation ISO

L’image d’installation est accessible à partir du portail Defender pour IoT.

Pour accéder au fichier :

1. Connectez-vous à votre compte Defender pour IoT.

2. Accédez à la page **Capteur réseau** ou **Console de gestion locale** et sélectionnez une version à télécharger.

### <a name="install-from-dvd"></a>Installer à partir d’un DVD

Avant l’installation, vérifiez que vous disposez des éléments suivants :

- Un lecteur de DVD portable avec connecteur USB.

- Une image du programme d’installation ISO.

Pour effectuer l’installation :

1. Gravez l’image sur un DVD ou préparez un disque sur une clé. Connectez un lecteur de DVD portable à votre ordinateur, cliquez avec le bouton droit sur l’image ISO, puis sélectionnez **Graver sur le disque**.

1. Connectez le DVD ou le disque sur clé et configurez l’appliance pour qu’elle démarre à partir du DVD ou du disque sur clé.

### <a name="install-from-disk-on-a-key"></a>Installer à partir d’un disque sur clé

Avant l’installation, vérifiez que vous disposez des éléments suivants :

  - Rufus installé.
  
  - Un disque sur clé avec USB 3.0 et versions ultérieures. La taille minimale est de 4 Go.

  - Une fichier image du programme d’installation ISO.

Le disque sur clé sera effacé au cours de ce processus.

Pour préparer un disque sur une clé :

1. Exécutez Rufus et sélectionnez **SENSOR ISO**.

2. Connectez le disque sur clé au panneau avant.

3. Définissez le BIOS du serveur de sorte qu’il démarre à partir de l’USB.

## <a name="dell-poweredger340xl-installation"></a>Installation de Dell PowerEdgeR340XL

Avant d’installer le logiciel sur l’appliance Dell, vous devez ajuster la configuration du BIOS de l’appliance :

  - Les sections [Panneau avant de Dell PowerEdge R340](#dell-poweredge-r340-front-panel) et [Panneau arrière de Dell PowerEdge R340](#dell-poweredge-r340-back-panel) contiennent la description des panneaux avant et arrière, ainsi que les informations requises pour l’installation, telles que les pilotes et les ports.

  - [Configuration du BIOS Dell](#dell-bios-configuration) fournit des informations sur la façon de se connecter à l’interface de gestion de l’appliance Dell et de configurer le BIOS.

  - [Installation du logiciel (Dell R340)](#software-installation-dell-r340) décrit la procédure requise pour installer le logiciel du capteur Defender pour IoT.

### <a name="dell-poweredge-r340xl-requirements"></a>Configuration requise pour Dell PowerEdge R340XL 

Pour installer l’appliance Dell PowerEdge R340XL, vous avez besoin des éléments suivants :

- Licence d’entreprise pour le contrôleur d’accès à distance Dell (iDrac)

- Fichier XML de configuration du BIOS

- Versions du microprogramme du serveur :

  - BIOS version 2.1.6

  - iDrac version 3.23.23.23

### <a name="dell-poweredge-r340-front-panel"></a>Panneau avant de Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-front-panel.jpg" alt-text="Panneau avant de Dell PowerEdge R340.":::

 1. Panneau de configuration gauche 
 2. Lecteur optique (en option) 
 3. Panneau de commande de droite 
 4. Plaquette d’information 
 5. Lecteurs  

### <a name="dell-poweredge-r340-back-panel"></a>Panneau arrière de Dell PowerEdge R340

:::image type="content" source="media/tutorial-install-components/view-of-dell-poweredge-r340-back-panel.jpg" alt-text="Panneau arrière de Dell PowerEdge R340.":::

1. Port série 
2. Port de carte NIC (Gb 1) 
3. Port de carte NIC (Gb 1) 
4. Logement de carte d’extension PCIe mi-hauteur 
5. Logement de carte d’extension PCIe pleine hauteur 
6. Bloc d’alimentation 1 
7. Bloc d’alimentation 2 
8. Bouton d’identification du système 
9. Port du câble du voyant d’état du système (CMA) 
10. Ports USB 3.0 (2) 
11. Port réseau dédié iDRAC9 
12. Port VGA 

### <a name="dell-bios-configuration"></a>Configuration du BIOS Dell

La configuration du BIOS Dell est requise pour adapter l’appliance Dell à l’utilisation du logiciel.

La configuration du BIOS s’effectue par le biais d’une configuration prédéfinie. Le fichier est accessible à partir du [centre d’aide](https://help.cyberx-labs.com/).

Importez le fichier config sur l’appliance Dell. Avant d’utiliser le fichier config, vous devez établir la communication entre l’appliance Dell et l’ordinateur de gestion.

L’appliance Dell est gérée par un iDRAC intégré avec contrôleur de cycle de vie. Le contrôleur de cycle de vie est incorporé à chaque serveur Dell PowerEdge et fournit des fonctionnalités qui vous permettent de déployer, mettre à jour, surveiller et gérer vos appliances Dell PowerEdge.

Pour établir la communication entre l’appliance Dell et l’ordinateur de gestion, vous devez définir l’adresse IP de l’iDRAC et l’adresse IP de l’ordinateur de gestion sur le même sous-réseau.

Lorsque la connexion est établie, le BIOS est configurable.

Pour configurer le BIOS Dell :

1. [Configurez l’adresse IP de l’iDRAC](#configure-idrac-ip-address).

2. [Importez le fichier config du BIOS](#import-the-bios-configuration-file).

#### <a name="configure-idrac-ip-address"></a>Configurer l’adresse IP de l’iDRAC

1. Mettez le capteur sous tension.

2. Si le système d’exploitation est déjà installé, sélectionnez la touche F2 pour entrer dans la configuration du BIOS.

3. Sélectionnez **Paramètres de l’iDRAC**.

4. Sélectionnez **Réseau**.

   > [!NOTE]
   > Pendant l’installation, vous devez configurer l’adresse IP et le mot de passe par défaut de l’iDRAC mentionnés dans les étapes suivantes. Après l’installation, vous modifierez ces définitions.

5. Remplacez l’adresse IPv4 statique par **10.100.100.250**.

6. Remplacez le masque de sous-réseau statique par **255.255.255.0**.

   :::image type="content" source="media/tutorial-install-components/idrac-network-settings-screen-v2.png" alt-text="Capture d’écran montrant le masque de sous-réseau statique.":::

7. Sélectionnez **Retour** > **Terminer**.

#### <a name="import-the-bios-configuration-file"></a>Importer le fichier config du BIOS

Cet article explique comment configurer le BIOS à l’aide du fichier config.

1. Branchez un PC avec une adresse IP préconfigurée statique **10.100.100.200** au port **iDRAC**.

   :::image type="content" source="media/tutorial-install-components/idrac-port.png" alt-text="Capture d’écran du port de l’adresse IP préconfigurée.":::

2. Ouvrez un navigateur et entrez **10.100.100.250** pour vous connecter à l’interface web de l’iDRAC.

3. Connectez-vous avec les privilèges d’administrateur par défaut de Dell :

   - Nom d’utilisateur : **root**

   - Mot de passe : **calvin**

4. Les informations d’identification de l’appliance sont :

   - Nom d’utilisateur : **XXX**

   - Mot de passe : **XXX**

     L’opération d’importation du profil du serveur est lancée.

     > [!NOTE]
     > Avant d’importer le fichier, vérifiez que :
     > - Vous êtes le seul utilisateur actuellement connecté à l’iDRAC.
     > - Le système n’est pas dans le menu BIOS.

5. Accédez à **Configuration** > **Profil de configuration du serveur**. Définissez les paramètres suivants :

   :::image type="content" source="media/tutorial-install-components/configuration-screen.png" alt-text="Capture d’écran montrant la configuration du profil de votre serveur.":::

   | Paramètre | Configuration |
   |--|--|
   | Type d'emplacement | Sélectionnez **Local**. |
   | Chemin d'accès au fichier | Sélectionnez **Choisir un fichier** et ajoutez le fichier XML de configuration. |
   | Importer des composants | Sélectionnez **BIOS, Carte réseau, RAID**. |
   | Durée d’attente maximale | Sélectionnez **20 minutes**. |

6. Sélectionnez **Importer**.

7. Pour surveiller le processus, accédez à **Maintenance** > **File d’attente des travaux**.

   :::image type="content" source="media/tutorial-install-components/view-the-job-queue.png" alt-text="Capture d’écran montrant File d’attente des travaux.":::

#### <a name="manually-configuring-bios"></a>Configurer manuellement le BIOS 

Vous devez configurer manuellement le BIOS de l’appliance dans les cas suivants :

- Vous n’avez pas acheté votre appliance auprès d’Arrow.

- Vous disposez d’une appliance, mais vous n’avez pas accès au fichier config XML.

Une fois que vous avez accès au BIOS, accédez à **Paramètres de l’appareil**.

Pour configurer manuellement :

1. Accédez au BIOS de l’appliance directement à l’aide d’un clavier et d’un écran, ou utilisez l’iDRAC.

   - Si l’appareil n’est pas une appliance Defender pour IoT, ouvrez un navigateur et accédez à l’adresse IP qui a été configurée précédemment. Connectez-vous avec les privilèges d’administrateur par défaut de Dell. Utilisez **root** comme nom d’utilisateur et **calvin** comme mot de passe.

   - Si l’appareil est une appliance Defender pour IoT, connectez-vous en utilisant **XXX** comme nom d’utilisateur et **XXX** comme mot de passe.

2. Une fois que vous avez accès au BIOS, accédez à **Paramètres de l’appareil**.

3. Choisissez la configuration contrôlée par RAID en sélectionnant **Contrôleur RAID intégré 1 : Utilitaire de configuration de Dell PERC\<PERC H330 Adapter\>** .

4. Sélectionnez **Gestion de la configuration**.

5. Sélectionnez **Créer un disque virtuel**.

6. Dans le champ **Sélectionner le niveau RAID**, sélectionnez **RAID5**. Dans le champ **Nom du disque virtuel**, entrez **ROOT** et sélectionnez **Disques physiques**.

7. Sélectionnez **Cocher tout** puis sélectionnez **Appliquer les modifications**.

8. Sélectionnez **OK**.

9. Faites défiler la page et sélectionnez **Créer un disque virtuel**.

10. Cochez la case **Confirmer** et sélectionnez **Oui**.

11. Sélectionnez **OK**.

12. Revenez à l’écran principal et sélectionnez **BIOS du système**.

13. Sélectionnez **Paramètres de démarrage**.

14. Pour l’option **Mode de démarrage**, sélectionnez **BIOS**.

15. Sélectionnez **Retour**, puis sélectionnez **Terminer** pour quitter les paramètres du BIOS.

### <a name="software-installation-dell-r340"></a>Installation du logiciel (Dell R340)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour effectuer l’installation :

1. Vérifiez que le support de la version est monté sur l’appliance de l’une des manières suivantes :

   - Connectez le CD externe ou le disque sur clé sur lequel se trouve la version.

   - Montez l’image ISO à l’aide de l’iDRAC. Après vous être connecté à l’iDRAC, sélectionnez la console virtuelle, puis sélectionnez **Support virtuel**.

2. Dans la section **Mapper le CD/DVD**, sélectionnez **Choisir un fichier**.

3. Choisissez le fichier image ISO de la version appropriée dans la boîte de dialogue qui s’ouvre.

4. Sélectionnez le bouton **Mapper l’appareil**.

   :::image type="content" source="media/tutorial-install-components/mapped-device-on-virtual-media-screen-v2.png" alt-text="Capture d’écran montrant un appareil mappé.":::

5. Le support est monté. Sélectionnez **Fermer**.

6. Démarrez l’appliance. Lorsque vous utilisez l’iDRAC, vous pouvez redémarrer les serveurs en sélectionnant le bouton **Contrôle Consul**. Ensuite, dans les **macros du clavier**, sélectionnez le bouton **Appliquer**, qui démarre la séquence Ctrl+Alt+Suppr.

7. Sélectionnez **Français**.

8. Sélectionnez **SENSOR-RELEASE-\<version\> Enterprise**.

   :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Capture d’écran montrant la sélection de la version.":::   

9. Définissez le profil de l’appareil et les propriétés du réseau :

   :::image type="content" source="media/tutorial-install-components/appliance-profile-screen-v2.png" alt-text="Capture d’écran montrant le profil de l’appliance.":::   

   | Paramètre | Configuration |
   |--|--|
   | **Profil matériel** | **enterprise** |
   | **Interface de gestion** | **eno1** |
   | **Paramètres réseau (fournis par le client)** | - |
   |**Adresse IP du réseau de gestion :** | - |
   | **Masque de sous-réseau :** | - |
   | **Nom d’hôte de l’appliance :** | - |
   | **DNS :** | - |
   | **Adresse IP de la passerelle par défaut :** | - |
   | **Interfaces d’entrée :** |  Le système génère la liste des interfaces d’entrée pour vous. Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule. Notez qu’il n’est pas nécessaire de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. |

10. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.  

11. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme la première fois que vous l’utiliserez.

12. Sélectionnez **Entrée** pour continuer.

## <a name="hpe-proliant-dl20-installation"></a>Installation de HPE ProLiant DL20

Cet article décrit le processus d’installation de HPE ProLiant DL20, qui comprend les étapes suivantes :

  - Activer l’accès à distance et mettre à jour le mot de passe administrateur par défaut.
  - Configurer les paramètres BIOS et RAID.
  - Installer le logiciel.

### <a name="about-the-installation"></a>À propos de l’installation

  - Les appliances Enterprise et SMB peuvent être installées. Le processus d’installation est identique pour les deux types d’appliances, à l’exception de la configuration des groupes.
  - Un utilisateur administratif par défaut est fourni. Nous vous recommandons de modifier le mot de passe lors du processus de configuration du réseau.
  - Pendant le processus de configuration du réseau, vous configurerez le port iLO sur le port réseau 1.
  - Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

### <a name="hpe-proliant-dl20-front-panel"></a>Panneau avant de HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-front-panel-v2.png" alt-text="Panneau avant de HPE ProLiant DL20.":::

### <a name="hpe-proliant-dl20-back-panel"></a>Panneau arrière de HPE ProLiant DL20

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl20-back-panel-v2.png" alt-text="Panneau arrière de HPE ProLiant DL20.":::

### <a name="enable-remote-access-and-update-the-password"></a>Activer l’accès à distance et mettre à jour le mot de passe

Utilisez la procédure suivante pour configurer les options réseau et mettre à jour le mot de passe par défaut.

Pour activer et mettre à jour le mot de passe :

1. Connectez un écran et un clavier à l’appliance HP, allumez l’appliance, puis appuyez sur **F9**.

    :::image type="content" source="media/tutorial-install-components/hpe-proliant-screen-v2.png" alt-text="Capture d’écran montrant la fenêtre HPE ProLiant.":::

2. Accédez à **Utilitaires système** > **Configuration du système** > **Utilitaires de configuration iLO 5** > **Options réseau**.

    :::image type="content" source="media/tutorial-install-components/system-configuration-window-v2.png" alt-text="Capture d’écran montrant la fenêtre Configuration du système.":::

    1.  Sélectionnez **Port réseau partagé : LOM** dans le champ **Carte réseau**.
    
    1.  Désactivez DHCP.
    
    1.  Entrez l’adresse IP, le masque de sous-réseau et l’adresse IP de la passerelle.

3. Sélectionnez **F10 : Enregistrer**.

4. Sélectionnez **Échap** pour revenir à **Utilitaires de configuration iLO 5**, puis sélectionnez **Gestion des utilisateurs**.

5. Sélectionnez **Modifier/supprimer un utilisateur**. L’administrateur est le seul utilisateur par défaut défini. 

6. Modifiez le mot de passe par défaut et sélectionnez **F10 : Enregistrer**.

### <a name="configure-the-hpe-bios"></a>Configurer le BIOS de HPE

La procédure suivante décrit comment configurer le BIOS de HPE pour les appliances Enterprise et SMB.

Pour configurer le BIOS de HPE :

1. Sélectionnez **Utilitaires système** > **Configuration du système** > **Configuration du BIOS/de la plateforme (RBSU)** .

2. Dans le formulaire **Configuration du BIOS/de la plateforme (RBSU)** , sélectionnez **Options de démarrage**.

3. Modifiez **Mode de démarrage** en **Mode BIOS hérité**, puis sélectionnez **F10 : Enregistrer**.

4. Sélectionnez **Échap** deux fois pour fermer le formulaire **Configuration du système**.

#### <a name="for-the-enterprise-appliance"></a>Pour l’appliance d’entreprise

1. Sélectionnez **RAID 1 incorporé : HPE Smart Array P408i-a SR de 10e génération** > **Configuration des groupes** > **Créer un groupe**.

2. Dans le formulaire **Créer un groupe**, sélectionnez toutes les options. Trois options sont disponibles pour l’appliance **Enterprise**.

#### <a name="for-the-smb-appliance"></a>Pour l’appliance SMB

1. Sélectionnez **RAID 1 incorporé : HPE Smart Array P208i-a SR de 10e génération** > **Configuration des groupes** > **Créer un groupe**.

2. Sélectionnez **Passer au formulaire suivant**.

3. Dans le formulaire **Définir le niveau RAID**, définissez le niveau sur **RAID 5** pour les déploiements d’entreprise et **RAID 1** pour les déploiements SMB.

4. Sélectionnez **Passer au formulaire suivant**.

5. Dans le formulaire **Étiquette de lecteur logique**, entrez **Lecteur logique 1**.

6. Sélectionnez **Soumettre les modifications**.

7. Dans le formulaire **Soumettre**, sélectionnez **Retour au menu principal**.

8. Sélectionnez **F10 : Enregistrer** puis appuyez deux fois sur **Échap**.

9. Dans la fenêtre **Utilitaires système**, sélectionnez **Menu de démarrage unique**.

10. Dans le formulaire **Menu de démarrage unique**, sélectionnez **Menu de démarrage unique BIOS hérité**.

11. Les fenêtres **Démarrage hérité** et **Substitution de démarrage** s’affichent. Choisissez une option de substitution de démarrage ; par exemple, sur un CD-ROM, un lecteur USB, un HDD ou un interpréteur de commandes UEFI.

    :::image type="content" source="media/tutorial-install-components/boot-override-window-one-v2.png" alt-text="Capture d’écran montrant la première fenêtre Substitution de démarrage.":::

    :::image type="content" source="media/tutorial-install-components/boot-override-window-two-v2.png" alt-text="Capture d’écran montrant la deuxième fenêtre Substitution de démarrage.":::
### <a name="software-installation-hpe-proliant-dl20-appliance"></a>Installation du logiciel (Appliance HPE ProLiant DL20)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour installer le logiciel :

1. Connectez l’écran et le clavier à l’appliance, puis connectez-vous à l’interface de ligne de commande.

2. Connectez un CD externe ou un disque sur clé sur lequel se trouve l’image ISO que vous avez téléchargée à partir de la page **Mises à jour** du portail Defender pour IoT.

3. Démarrez l’appliance.

4. Sélectionnez **Français**.

    :::image type="content" source="media/tutorial-install-components/select-english-screen.png" alt-text="Sélection du français dans la fenêtre de l’interface de ligne de commande.":::

5. Sélectionnez **SENSOR-RELEASE-<version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Capture d’écran de la sélection d’une version.":::

6. Dans l’Assistant Installation, définissez le profil de l’appliance et les propriétés du réseau :

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Capture d’écran montrant l’Assistant Installation.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Enterprise** ou **Office** pour les déploiements SMB. |
    | **Interface de gestion** | **eno2** |
    | **Paramètres réseau par défaut (en général, les paramètres sont fournis par le client)** | **Adresse IP du réseau de gestion :** <br/> <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Adresse IP de la passerelle par défaut :**|
    | **Interfaces d’entrée :** | Le système génère la liste des interfaces d’entrée pour vous.<br/><br/>Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule : **eno5, eno3, eno1, eno6, eno4**.<br/><br/>**Pour HPE DL20 : Ne pas répertorier eno1, enp1s0f4u4 (interfaces iLo)** .<br/><br/>**PONT** : Il n’est pas nécessaire de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. Appuyez sur **Entrée** pour continuer. |

7. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.

8. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin des informations d’identification pour accéder à la plateforme pour la première fois.

9. Sélectionnez **Entrée** pour continuer.

## <a name="hpe-proliant-dl360-installation"></a>Installation de HPE ProLiant DL360

  - Un utilisateur administratif par défaut est fourni. Nous vous recommandons de modifier le mot de passe lors de la configuration du réseau.

  - Au cours de la configuration du réseau, vous allez configurer le port iLO.

  - Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

### <a name="hpe-proliant-dl360-front-panel"></a>Panneau avant de HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-front-panel.png" alt-text="Panneau avant de HPE ProLiant DL360.":::

### <a name="hpe-proliant-dl360-back-panel"></a>Panneau arrière de HPE ProLiant DL360

:::image type="content" source="media/tutorial-install-components/hpe-proliant-dl360-back-panel.png" alt-text="Panneau arrière de HPE ProLiant DL360.":::

### <a name="enable-remote-access-and-update-the-password"></a>Activer l’accès à distance et mettre à jour le mot de passe

Reportez-vous aux sections précédentes pour l’installation de HPE ProLiant DL20 :

  - « Activer l’accès à distance et mettre à jour le mot de passe »

  - « Configurer le BIOS de HPE »

La configuration d’entreprise est identique.

> [!Note]
> Dans le formulaire du groupe, assurez-vous de sélectionner toutes les options.

### <a name="ilo-remote-installation-from-a-virtual-drive"></a>Installation à distance d’iLO (à partir d’un lecteur virtuel)

Cette procédure décrit l’installation d’iLO à partir d’un lecteur virtuel.

Pour effectuer l’installation :

1. Connectez-vous à la console iLO, puis cliquez avec le bouton droit sur l’écran des serveurs.

2. Sélectionnez **Console HTML5**.

3. Dans la console, sélectionnez l’icône de CD, puis choisissez l’option CD/DVD.

4. Sélectionnez **Fichier ISO local**.

5. Dans la boîte de dialogue, choisissez le fichier ISO approprié.

6. Accédez à l’icône de gauche, sélectionnez **Alimentation** puis **Réinitialiser**.

7. L’appliance redémarre et exécute le processus d’installation du capteur.

### <a name="software-installation-hpe-dl360"></a>Installation du logiciel (HPE DL360)

Le processus d’installation prend environ 20 minutes. Après l’installation, le système redémarre plusieurs fois.

Pour effectuer l’installation :

1. Connectez l’écran et le clavier à l’appliance, puis connectez-vous à l’interface de ligne de commande.

2. Connectez un CD externe ou un disque sur clé sur lequel se trouve l’image ISO que vous avez téléchargée à partir de la page **Mises à jour** du portail Defender pour IoT.

3. Démarrez l’appliance.

4. Sélectionnez **Français**.

5. Sélectionnez **SENSOR-RELEASE-<version> Enterprise**.

    :::image type="content" source="media/tutorial-install-components/sensor-version-select-screen-v2.png" alt-text="Capture d’écran qui montre la sélection de la version.":::

6. Dans l’Assistant Installation, définissez le profil de l’appliance et les propriétés du réseau.

    :::image type="content" source="media/tutorial-install-components/installation-wizard-screen-v2.png" alt-text="Capture d’écran montrant l’Assistant Installation.":::

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | Sélectionnez **Société**. |
    | **Interface de gestion** | **eno2** |
    | **Paramètres réseau par défaut (fournis par le client)** | **Adresse IP du réseau de gestion :** <br/>**Masque de sous-réseau :** <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Adresse IP de la passerelle par défaut :**|
    | **Interfaces d’entrée :**  | Le système génère une liste d’interfaces d’entrée pour vous.<br/><br/>Pour mettre en miroir les interfaces d’entrée, copiez tous les éléments présentés dans la liste en les séparant par une virgule.<br/><br/>Notez qu’il n’est pas nécessaire de configurer l’interface de pont. Cette option est utilisée uniquement dans des cas d’usage particuliers. |

7. Après environ 10 minutes, les deux jeux d’informations d’identification s’affichent. L’un est destiné à un utilisateur **CyberX**, et l’autre à un utilisateur **Support**.

8. Enregistrez l’ID et les mots de passe de l’appliance. Vous aurez besoin de ces informations d’identification pour accéder à la plateforme pour la première fois.

9. Sélectionnez **Entrée** pour continuer.

## <a name="sensor-installation-for-the-virtual-appliance"></a>Installation du capteur pour l’appliance virtuelle

Vous pouvez déployer la machine virtuelle pour le capteur Defender pour IoT dans les architectures suivantes :


| Architecture | Spécifications | Utilisation | Commentaires |
|---|---|---|---|
| **Entreprise** | Processeur : 8<br/>Mémoire : RAM 32 Go<br/>HDD : 1800 Go | Environnement de production | Architecture par défaut et la plus courante |
| **Small Business** | Processeur : 4 <br/>Mémoire : RAM 8 Go<br/>HDD : 500 Go | Environnements de test ou petits environnements de production | -  |
| **Office** | Processeur : 4<br/>Mémoire : RAM 8 Go<br/>HDD : 100 Go | Petits environnements de test | -  |

### <a name="prerequisites"></a>Prérequis

La console de gestion locale prend en charge les options de déploiement de VMware et Hyper-V. Avant de commencer l’installation, assurez-vous de disposer des éléments suivants :

  - Hyperviseur VMware (ESXi 5.5 ou version ultérieure) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel

  - Ressources matérielles disponibles pour la machine virtuelle

  - Fichier d’installation ISO pour le capteur Azure Defender pour IoT

Assurez-vous que l’hyperviseur fonctionne.

### <a name="create-the-virtual-machine-esxi"></a>Créer la machine virtuelle (ESXi)

1. Connectez-vous à ESXi, choisissez le **magasin de données** approprié et sélectionnez **Navigateur de magasin de données**.

2. **Chargez** l’image, puis sélectionnez **Fermer**.

3. Accédez à **Machines virtuelles**, puis sélectionnez **Créer/inscrire une machine virtuelle**.

4. Sélectionnez **Créer une nouvelle machine virtuelle**, puis sélectionnez **Suivant**.

5. Ajoutez un nom de capteur et choisissez :

   - Compatibilité : **&lt;version ESXi la plus récente&gt;**

   - Famille de système d’exploitation invité : **Linux**

   - Version du système d’exploitation invité : **Ubuntu Linux (64 bits)**

6. Sélectionnez **Suivant**.

7. Choisissez le magasin de données approprié et sélectionnez **Suivant**.

8. Modifiez les paramètres du matériel virtuel en fonction de l’architecture requise.

9. Pour **Lecteur CD/DVD 1**, sélectionnez **Fichier ISO de magasin de données** et choisissez le fichier ISO que vous avez chargé précédemment.

10. Sélectionnez **Suivant** > **Terminer**.

### <a name="create-the-virtual-machine-hyper-v"></a>Créer la machine virtuelle (Hyper-V)

Cette procédure décrit comment créer une machine virtuelle à l’aide d’Hyper-V.

Pour créer une machine virtuelle :

1. Créez un disque virtuel dans Hyper-V Manager.

2. Sélectionnez **format = VHDX**.

3. Sélectionnez **type = Développement dynamique**.

4. Entrez le nom et l’emplacement du disque dur virtuel.

5. Entrez la taille requise (en fonction de l’architecture).   

6. Vérifiez le résumé et sélectionnez **Terminer**.

7. Dans le menu **Actions**, créez une machine virtuelle.

8. Entrez un nom pour la machine virtuelle.

9. Sélectionnez **Spécifier la génération** > **Génération 1**.

10. Spécifiez l’allocation de mémoire (en fonction de l’architecture) et cochez la case de la mémoire dynamique.

11. Configurez la carte réseau en fonction de la topologie de réseau de votre serveur.

12. Connectez le VHDX créé précédemment à la machine virtuelle.

13. Vérifiez le résumé et sélectionnez **Terminer**.

14. Cliquez avec le bouton droit sur la nouvelle machine virtuelle, puis sélectionnez **Paramètres**.

15. Sélectionnez **Ajouter du matériel** et ajouter une nouvelle carte réseau.

16. Sélectionnez le commutateur virtuel qui sera connecté au réseau de gestion du capteur.

17. Allouez des ressources d’UC (selon l’architecture).

18. Connectez l’image ISO de la console de gestion à un lecteur de DVD virtuel.

19. Démarrez la machine virtuelle.

20. Dans le menu **Actions**, sélectionnez **Connexion** pour poursuivre l’installation du logiciel.

### <a name="software-installation-esxi-and-hyper-v"></a>Installation des logiciels (ESXi et Hyper-V)

Cette section décrit l’installation des logiciels ESXi et Hyper-V.

Pour effectuer l’installation :

1. Ouvrez la console de la machine virtuelle.

2. La machine virtuelle démarre à partir de l’image ISO et l’écran de sélection de la langue s’affiche. Sélectionnez **Français**.

3. Sélectionnez l’architecture requise.

4. Définissez le profil de l’appareil et les propriétés du réseau :

    | Paramètre | Configuration |
    | ----------| ------------- |
    | **Profil matériel** | &lt;architecture requise&gt; |
    | **Interface de gestion** | **ens192** |
    | **Paramètres réseau (fournis par le client)** | **Adresse IP du réseau de gestion :** <br/>**Masque de sous-réseau :** <br/>**Nom d’hôte de l’appliance :** <br/>**DNS :** <br/>**Passerelle par défaut :** <br/>**Interfaces d’entrée :**|
    | **Interfaces de pont :** | Il n’est pas nécessaire de configurer l’interface de pont. Cette option est réservée à des cas d’usage particuliers. |

5. Entrez **O** pour accepter les paramètres.

6. Les informations d’identification de connexion sont automatiquement générées et présentées. Copiez le nom d’utilisateur et le mot de passe dans un endroit sûr, car ils sont requis à des fins de connexion et d’administration.

   - **Support** : Utilisateur administratif pour la gestion des utilisateurs.

   - **CyberX** : Équivalent de la racine pour accéder à l’appliance.

7. L’appliance redémarre.

8. Accédez à la console de gestion à l’aide de l’adresse IP précédemment configurée : `https://ip_address`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Capture d’écran qui montre l’accès à la console de gestion.":::

## <a name="virtual-appliance-on-premises-management-console-installation"></a>Appliance virtuelle : installation de la console de gestion locale

La machine virtuelle de la console de gestion locale prend en charge les architectures suivantes :

| Architecture | Spécifications | Utilisation | 
|--|--|--|
| Enterprise <br/>(Par défaut et la plus courante) | Processeur : 8 <br/>Mémoire : RAM 32 Go<br/> HDD : 1,8 To | Environnements de production volumineux | 
| Enterprise | Processeur : 4 <br/> Mémoire : RAM 8 Go<br/> HDD : 500 Go | Environnements de production volumineux |
| Enterprise | Processeur : 4 <br/>Mémoire : RAM 8 Go <br/> HDD : 100 Go | Petits environnements de test | 
   
### <a name="prerequisites"></a>Prérequis

La console de gestion locale prend en charge les options de déploiement de VMware et Hyper-V. Avant de commencer l’installation, vérifiez que les conditions suivantes sont remplies :

- L’hyperviseur VMware (ESXi 5.5 ou version ultérieure) ou Hyper-V (Windows 10 Professionnel ou Entreprise) installé et opérationnel.

- Les ressources matérielles sont disponibles pour la machine virtuelle.

- Vous disposez du fichier d’installation ISO pour la console de gestion locale.
    
- L’hyperviseur fonctionne.

### <a name="create-the-virtual-machine-esxi"></a>Créer la machine virtuelle (ESXi)

Pour créer une machine virtuelle (ESXi) :

1. Connectez-vous à ESXi, choisissez le **magasin de données** approprié et sélectionnez **Navigateur de magasin de données**.

2. Chargez l’image, puis sélectionnez **Fermer**.

3. Accédez à **Machines virtuelles**.

4. Sélectionnez **Créer/inscrire une machine virtuelle**.

5. Sélectionnez **Créer une nouvelle machine virtuelle**, puis sélectionnez **Suivant**.

6. Ajoutez un nom de capteur et choisissez :

   - Compatibilité : \<latest ESXi version>

   - Famille de système d’exploitation invité : Linux

   - Version du système d’exploitation invité : Ubuntu Linux (64 bits)

7. Sélectionnez **Suivant**.

8. Choisissez le magasin de données approprié et sélectionnez **Suivant**.

9. Modifiez les paramètres du matériel virtuel en fonction de l’architecture requise.

10. Pour **Lecteur CD/DVD 1**, sélectionnez **Fichier ISO de magasin de données** et choisissez le fichier ISO que vous avez chargé précédemment.

11. Sélectionnez **Suivant** > **Terminer**.

### <a name="create-the-virtual-machine-hyper-v"></a>Créer la machine virtuelle (Hyper-V)

Pour créer une machine virtuelle à l’aide d’Hyper-V :

1. Créez un disque virtuel dans Hyper-V Manager.

2. Sélectionnez le format **VHDX**.

3. Sélectionnez **Suivant**.

4. Sélectionnez le type **Développement dynamique**.

5. Sélectionnez **Suivant**.

6. Entrez le nom et l’emplacement du disque dur virtuel.

7. Sélectionnez **Suivant**.

8. Entrez la taille requise (en fonction de l’architecture).

9. Sélectionnez **Suivant**.

10. Vérifiez le résumé et sélectionnez **Terminer**.

11. Dans le menu **Actions**, créez une machine virtuelle.

12. Sélectionnez **Suivant**.

13. Entrez un nom pour la machine virtuelle.

14. Sélectionnez **Suivant**.

15. Sélectionnez **Génération** et définissez-la sur **Génération 1**.

16. Sélectionnez **Suivant**.

17. Spécifiez l’allocation de mémoire (en fonction de l’architecture) et cochez la case de la mémoire dynamique.

18. Sélectionnez **Suivant**.

19. Configurez la carte réseau en fonction de la topologie de réseau de votre serveur.

20. Sélectionnez **Suivant**.

21. Connectez le VHDX créé précédemment à la machine virtuelle.

22. Sélectionnez **Suivant**.

23. Vérifiez le résumé et sélectionnez **Terminer**.

24. Cliquez avec le bouton droit sur la nouvelle machine virtuelle, puis sélectionnez **Paramètres**.

25. Sélectionnez **Ajouter du matériel** et ajouter un nouvel adaptateur pour **Carte réseau**.

26. Pour **Commutateur virtuel**, sélectionnez le commutateur qui sera connecté au réseau de gestion du capteur.

27. Allouez des ressources d’UC (selon l’architecture).

28. Connectez l’image ISO de la console de gestion à un lecteur de DVD virtuel.

29. Démarrez la machine virtuelle.

30. Dans le menu **Actions**, sélectionnez **Connexion** pour poursuivre l’installation du logiciel.

### <a name="software-installation-esxi-and-hyper-v"></a>Installation des logiciels (ESXi et Hyper-V)

Le démarrage de la machine virtuelle démarre le processus d’installation à partir de l’image ISO.

Pour installer le logiciel :

1. Sélectionnez **Français**.

2. Sélectionnez l’architecture requise pour votre déploiement.

3. Définissez l’interface réseau pour le réseau de gestion du capteur : interface, IP, sous-réseau, serveur DNS et passerelle par défaut.

4. Les informations d’identification de connexion sont automatiquement générées et présentées. Conservez ces informations d’identification dans un endroit sûr, car elles sont requises à des fins de connexion et d’administration.

  - **Support** : Utilisateur administratif pour la gestion des utilisateurs.

  - **CyberX** : Équivalent de la racine pour accéder à l’appliance.

5. L’appliance redémarre.

6. Accédez à la console de gestion à l’aide de l’adresse IP précédemment configurée : `<https://ip_address>`.

    :::image type="content" source="media/tutorial-install-components/defender-for-iot-management-console-sign-in-screen.png" alt-text="Capture d’écran montrant l’écran de connexion de la console de gestion.":::

## <a name="post-installation-validation"></a>Validation après installation

Pour valider l’installation d’une appliance physique, vous devez effectuer un certain nombre de tests. Le même processus de validation s’applique à tous les types d’appliances.

Effectuez la validation à l’aide de l’interface graphique utilisateur ou de l’interface de ligne de commande. La validation est disponible pour l’utilisateur **Support** et l’utilisateur **CyberX**.

La validation après installation doit inclure les tests suivants :

  - **Test de validité** : Vérifiez que le système fonctionne.

  - **Version** : Vérifiez que la version est correcte.

  - **ifconfig** : Vérifiez que toutes les interfaces d’entrée configurées pendant le processus d’installation fonctionnent.

### <a name="checking-system-health-by-using-the-gui"></a>Vérification de l’intégrité du système à l’aide de l’interface graphique utilisateur

:::image type="content" source="media/tutorial-install-components/system-health-check-screen.png" alt-text="Capture d’écran montrant la vérification de l’intégrité du système.":::

#### <a name="sanity"></a>Validité

- **Appliance** : Exécute la vérification de l’intégrité de l’appliance. Vous pouvez effectuer la même vérification à l’aide de la commande CLI `system-sanity`.

- **Version** : Affiche la version de l’appliance.

- **Propriétés réseau** : Affiche les paramètres réseau du capteur.

#### <a name="redis"></a>Redis

- **Mémoire** : Fournit une vue d’ensemble de l’utilisation de la mémoire, par exemple la quantité de mémoire utilisée et la quantité restante.

- **Clé la plus longue** : Affiche les clés les plus longues qui peuvent entraîner une utilisation intensive de la mémoire.

#### <a name="system"></a>Système

- **Journal principal** : Fournit les 500 dernières lignes du journal principal, ce qui vous permet de visualiser les lignes de journal récentes sans exporter l’intégralité du journal système.

- **Gestionnaire des tâches** : Traduit les tâches qui s’affichent dans le tableau des processus en couches comme suit : 
  
  - Couche persistante (Redis) 
  - Couche de cache (SQL)

- **Statistiques relatives au réseau** : Affiche les statistiques de votre réseau.

- **TOP** : Affiche la table des processus. Il s’agit d’une commande Linux qui fournit une vue dynamique en temps réel du système en cours d’exécution.

- **Vérification de la mémoire de sauvegarde** : Indique l’état de la mémoire de sauvegarde, en vérifiant les éléments suivants :
  - Emplacement du dossier de sauvegarde 
  - Taille du dossier de sauvegarde
  - Limitations du dossier de sauvegarde
  - Date et heure de la dernière sauvegarde
  - Espace disponible pour les fichiers de sauvegarde supplémentaires

- **ifconfig** : Affiche les paramètres des interfaces physiques de l’appliance.

- **CyberX nload** : Affiche le trafic et la bande passante en utilisant les tests de six secondes.

- **Erreurs de Core, log** : Affiche les erreurs du fichier journal principal.

Pour accéder à l’outil :

1. Connectez-vous au capteur en utilisant les informations d’identification de l’utilisateur **Support**.

2. Sélectionnez **Statistiques du système** dans la fenêtre **Paramètres du système**.

    :::image type="icon" source="media/tutorial-install-components/system-statistics-icon.png" border="false":::

### <a name="checking-system-health-by-using-the-cli"></a>Vérification de l’intégrité du système à l’aide de l’interface de ligne de commande

**Test 1 : Validité**

Vérifiez que le système est en état de marche :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

2. Entrez `system sanity`.

3. Vérifiez que tous les services sont en vert (en cours d’exécution).

    :::image type="content" source="media/tutorial-install-components/support-screen.png" alt-text="Capture d’écran montrant les services en cours d’exécution.":::

4. Vérifiez que **System is UP! (prod)** [Le système est opérationnel ! (prod)] s’affiche en bas.

**Test 2 : Vérification de la version**

Vérifiez que la version correcte est utilisée :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

2. Entrez `system version`.

3. Vérifiez que la version correcte s’affiche.

**Test 3 : Validation du réseau**

Vérifiez que toutes les interfaces d’entrée configurées pendant le processus d’installation fonctionnent :

1. Connectez-vous à l’interface de ligne de commande à l’aide du terminal Linux (p. ex. : PuTTY) et de l’utilisateur **Support**.

2. Entrez `network list` (l’équivalent de la commande Linux `ifconfig`).

3. Confirmez que les interfaces d’entrée requises s’affichent. Par exemple, si deux cartes réseau Cuivre 4 ports sont installées, la liste doit compter 10 interfaces.

    :::image type="content" source="media/tutorial-install-components/interface-list-screen.png" alt-text="Capture d’écran montrant la liste des interfaces.":::

**Test 4 : Accès de gestion à l’interface utilisateur**

Vérifiez que vous pouvez accéder à la GUI web de la console :

1. Connectez un ordinateur portable au port de gestion (**Gb1**) à l’aide d’un câble Ethernet.

2. Définissez l’adresse de la carte réseau de l’ordinateur portable pour qu’elle soit dans la même plage que celle de l’appliance.

    :::image type="content" source="media/tutorial-install-components/access-to-ui.png" alt-text="Capture d’écran montrant l’accès de gestion à l’interface utilisateur.":::

3. Effectuez un test ping sur l’adresse IP de l’appliance à partir de l’ordinateur portable pour vérifier la connectivité (par défaut : 10.100.10.1).

4. Ouvrez le navigateur Chrome sur l’ordinateur portable et entrez l’adresse IP de l’appliance.

5. Dans la fenêtre **Votre connexion n’est pas privée**, sélectionnez **Avancé** et continuez.

6. Le test est réussi lorsque l’écran de connexion Defender pour IoT s’affiche.

   :::image type="content" source="media/tutorial-install-components/defender-for-iot-sign-in-screen.png" alt-text="Capture d’écran qui montre l’accès à la console de gestion.":::

## <a name="troubleshooting"></a>Résolution des problèmes

### <a name="you-cant-connect-by-using-a-web-interface"></a>Vous ne pouvez pas vous connecter à l’aide d’une interface web

1. Vérifiez que l’ordinateur que vous essayez de connecter se trouve sur le même réseau que l’appliance.

2. Vérifiez que le réseau de l’interface graphique utilisateur est connecté au port de gestion.

3. Effectuez un test ping sur l’adresse IP de l’appliance. Si vous n’avez pas l’utilitaire Ping :

   1. Connectez un moniteur et un clavier à l’appliance.

   1. Utilisez l’utilisateur **Support** et son mot de passe pour vous connecter.

   1. Utilisez la commande `network list` pour afficher l’adresse IP actuelle.

      :::image type="content" source="media/tutorial-install-components/network-list.png" alt-text="Capture d’écran montrant la liste des réseaux.":::

4. Si les paramètres du réseau sont mal configurés, utilisez la procédure suivante pour les modifier :

   1. Utilisez la commande `network edit-settings`.

   1. Pour modifier l’adresse IP du réseau de gestion, sélectionnez **O**.

   1. Pour modifier le masque de sous-réseau, sélectionnez **O**.

   1. Pour modifier le DNS, sélectionnez **O**.

   1. Pour modifier l’adresse IP de la passerelle par défaut, sélectionnez **O**.

   1. Pour la modification de l’interface d’entrée (capteur uniquement), sélectionnez **N**.

   1. Pour appliquer les paramètres, sélectionnez **O**.

5. Après le redémarrage, connectez-vous avec les informations d’identification de l’utilisateur Support et utilisez la commande `network list` pour vérifier que les paramètres ont été modifiés.

6. Essayez d’effectuer un test ping et de vous reconnecter à partir de l’interface graphique utilisateur.

### <a name="the-appliance-isnt-responding"></a>L’appliance ne répond pas

1. Connectez un moniteur et un clavier à l’appliance, ou utilisez PuTTY pour vous connecter à distance à l’interface de ligne de commande.

2. Utilisez les informations d’identification de l’utilisateur **Support** pour vous connecter.

3. Utilisez la commande `system sanity` et vérifiez que tous les processus sont en cours d’exécution.

    :::image type="content" source="media/tutorial-install-components/system-sanity-screen.png" alt-text="Capture d’écran montrant la commande system sanity.":::

Pour tout autre problème, contactez [Support Microsoft](https://support.microsoft.com/en-us/supportforbusiness/productselection?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099).

## <a name="appendix-a-mirroring-port-on-vswitch-esxi"></a>Annexe A : Port de mise en miroir sur un commutateur virtuel (ESXi)

### <a name="configure-a-span-port-on-an-existing-vswitch"></a>Configurer un port SPAN sur un commutateur virtuel existant

Un commutateur virtuel ne dispose pas de capacités de mise en miroir, mais vous pouvez utiliser une simple solution de contournement pour implémenter un port SPAN.

Pour configurer un port SPAN :

1. Ouvrez les propriétés du commutateur virtuel.

2. Sélectionnez **Ajouter**.

3. Sélectionnez **Machine virtuelle** > **Suivant**.

4. Insérez une étiquette réseau **Réseau SPAN**, sélectionnez **ID VLAN** > **Tout**, puis sélectionnez **Suivant**.

5. Sélectionnez **Terminer**.

6. Sélectionnez **Réseau SPAN** > **Modifier*.

7. Sélectionnez **Sécurité** et vérifiez que la stratégie **Mode de proximité** est définie sur **Accepter le mode**.

8. Sélectionnez **OK**, puis sélectionnez **Fermer** pour fermer les propriétés du commutateur virtuel.

9. Ouvrez les propriétés de la **machine virtuelle XSense**.

10. Pour **Carte réseau 2**, sélectionnez le réseau **SPAN**.

11. Sélectionnez **OK**.

12. Connectez-vous au capteur et vérifiez que la mise en miroir fonctionne.

## <a name="appendix-b-access-sensors-from-the-on-premises-management-console"></a>Annexe B : Accéder aux capteurs à partir de la console de gestion locale

Vous pouvez renforcer la sécurité du système en empêchant l’accès direct des utilisateurs au capteur. Au lieu de cela, utilisez le tunneling de proxy pour permettre aux utilisateurs d’accéder au capteur à partir de la console de gestion locale avec une seule règle de pare-feu. Cette technique réduit le risque d’accès non autorisé à l’environnement réseau au-delà du capteur. L’expérience de l’utilisateur lorsqu’il se connecte au capteur reste la même.

:::image type="content" source="media/tutorial-install-components/sensor-system-graph.png" alt-text="Capture d’écran montrant l’accès au capteur.":::

Pour activer le tunneling :

1. Connectez-vous à l’interface de ligne de commande de la console de gestion locale en utilisant les informations d’identification de l’utilisateur **CyberX** ou **Support**.

2. Entrez `sudo cyberx-management-tunnel-enable`.

3. Sélectionnez **Enter** (Entrer).

4. Entrez `--port 10000`.

### <a name="next-steps"></a>Étapes suivantes

[Configurer votre réseau](how-to-set-up-your-network.md)