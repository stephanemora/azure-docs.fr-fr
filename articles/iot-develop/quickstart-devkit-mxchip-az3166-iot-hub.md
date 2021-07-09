---
title: Connecter un appareil MXCHIP AZ3166 à Azure IoT Hub – Démarrage rapide
description: Le logiciel intégré Azure RTOS permet de connecter un appareil MXCHIP AZ3166 à Azure IoT Hub et d’envoyer des données de télémétrie.
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.devlang: c
ms.topic: quickstart
ms.date: 06/09/2021
ms.openlocfilehash: a14f00585eede96814627c941050c3179436ab06
ms.sourcegitcommit: 23040f695dd0785409ab964613fabca1645cef90
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112059721"
---
# <a name="quickstart-connect-an-mxchip-az3166-devkit-to-iot-hub"></a>Démarrage rapide : Connecter un Devkit MXCHIP AZ3166 à IoT Hub

**S’applique à** : [Développement d’appareils intégrés](about-iot-develop.md#embedded-device-development)<br>
**Durée totale d’exécution** : 30 minutes

[![Parcourir le code](media/common/browse-code.svg)](https://github.com/azure-rtos/getting-started/tree/master/MXChip/AZ3166)

Dans le cadre de ce démarrage rapide, vous utilisez Azure RTOS pour connecter un DevKit IoT MXCHIP AZ3166 (ci-après DevKit MXCHIP) à Azure IoT. 

Vous allez également utiliser IoT Explorer et IoT Plug-and-Play pour gérer le DevKit MXCHIP. La technologie IoT Plug-and-Play fournit un modèle d’appareil ouvert qui permet aux applications d’interroger par programme les fonctionnalités d’un appareil et d’interagir avec celui-ci. Un appareil utilise ce modèle pour diffuser ses fonctionnalités vers une application prenant en charge la technologie IoT Plug-and-Play. Ce modèle vous permet de simplifier et d’améliorer les tâches d’ajout, de configuration et de gestion des appareils. Pour plus d’informations, consultez la [documentation sur la technologie IoT Plug-and-Play](/azure/iot-pnp).

Vous allez effectuer les tâches suivantes :

* Installer un ensemble d’outils de développement intégrés pour la programmation du DevKit MXChip en C
* Créer une image et la flasher sur le DevKit MXCHIP
* Utiliser Azure CLI pour créer et gérer un Azure IoT Hub auquel le DevKit MXCHIP se connectera en toute sécurité
* Utiliser Azure IoT Explorer pour inscrire un appareil auprès de votre IoT Hub, afficher les propriétés de l’appareil, afficher la télémétrie de l’appareil, et appeler des commandes directes sur l’appareil

## <a name="prerequisites"></a>Prérequis

* PC exécutant Microsoft Windows 10
* Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
* [Git](https://git-scm.com/downloads) pour cloner le référentiel
* Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    * Utilisez Azure Cloud Shell, un interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](/azure/cloud-shell/quickstart) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    * Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Si l’interface de ligne de commande Azure est déjà installée, exécutez `az upgrade` pour la mettre à niveau, ainsi que les extensions, vers la version actuelle. Pour installer l’interface de ligne de commande Azure, consultez [Installer l’interface de ligne de commande Azure](/cli/azure/install-azure-cli).

* [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer/releases) : utilitaire multiplateforme pour surveiller et gérer Azure IoT 
* Matériel

    * [DevKit IoT MXCHIP AZ3166](https://aka.ms/iot-devkit) (DevKit MXCHIP)
    * Wi-Fi 2,4 GHz
    * USB 2.0, câble mâle-mâle micro USB

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

Pour configurer votre environnement de développement, commencez par cloner un dépôt GitHub contenant toutes les ressources dont vous avez besoin pour le démarrage rapide. Ensuite, vous installez un ensemble d’outils de programmation.

### <a name="clone-the-repo-for-the-quickstart"></a>Cloner le dépôt pour le démarrage rapide

Clonez le référentiel suivant pour télécharger tous les exemples de code d’appareil, les scripts d’installation et les versions hors connexion de la documentation. Si vous avez déjà cloné ce dépôt dans le cadre d’un autre démarrage rapide, vous n’avez pas besoin de le refaire.

Pour cloner le référentiel, exécutez la commande suivante :

```shell
git clone --recursive https://github.com/azure-rtos/getting-started.git
```

### <a name="install-the-tools"></a>Installer les outils

Le référentiel cloné contient un script d’installation qui installe et configure les outils requis. Si vous avez installé ces outils dans le cadre d’un autre démarrage rapide d’appareil intégré, vous n’avez pas besoin de le refaire.

> [!NOTE]
> Le script d’installation installe les outils suivants :
> * [CMake](https://cmake.org) : générer
> * [ARM GCC](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm) : compiler
> * [Termite](https://www.compuphase.com/software_termite.htm) : surveiller la sortie du port série pour les ressources d’appareils connectés

Pour installer les outils :

1. Dans l’Explorateur de fichiers, accédez au chemin d’accès suivant dans le référentiel et exécutez le script d’installation nommé *get-toolchain.bat* :

    *getting-started\tools\get-toolchain.bat*

1. Après l’installation, ouvrez une nouvelle fenêtre de console pour identifier les modifications de configuration apportées par le script d’installation. Utilisez cette console pour accomplir les tâches de programmation restantes décrites dans ce démarrage rapide. Vous pouvez utiliser Windows CMD, PowerShell ou Git Bash pour Windows.
1. Exécutez le code suivant pour vérifier que CMake version 3.14 ou ultérieure est installé.

    ```shell
    cmake --version
    ```

## <a name="create-the-cloud-components"></a>Créer les composants cloud

### <a name="create-an-iot-hub"></a>Créer un hub IoT

Vous pouvez utiliser Azure CLI pour créer un hub IoT qui gère les événements et la messagerie pour votre appareil.

Pour créer un hub IoT :

1. Lancez votre application CLI. Pour exécuter les commandes CLI dans le reste de ce guide de démarrage rapide, copiez la syntaxe de la commande, collez-la dans votre application CLI, modifiez les valeurs des variables et appuyez sur Entrée.
    - Si vous préférez utiliser Cloud Shell, cliquez avec le bouton droit sur le lien [Cloud Shell](https://shell.azure.com/bash) et sélectionnez l’option pour ouvrir sous un nouvel onglet.
    - Si vous utilisez Azure CLI localement, démarrez votre application console CLI et connectez-vous à Azure CLI.

1. Exécutez la commande [az extension add](/cli/azure/extension?view=azure-cli-latest#az_extension_add&preserve-view=true) pour installer ou mettre à niveau l’extension *azure-iot* vers la version actuelle.

    ```azurecli-interactive
    az extension add --upgrade --name azure-iot
    ```

1. Exécutez la commande [az group create](/cli/azure/group#az-group-create) pour créer un groupe de ressources. La commande suivante crée un groupe de ressources nommé *MyResourceGroup* dans la région *centralus*.

    > [!NOTE] 
    > Vous pouvez éventuellement définir un autre `location`. Pour afficher les emplacements disponibles, exécutez la commande [az account list-locations](/cli/azure/account#az-account-list-locations).

    ```azurecli
    az group create --name MyResourceGroup --location centralus
    ```

1. Exécutez la commande [az iot hub create](/cli/azure/iot/hub#az-iot-hub-create) pour créer un hub IoT. La création de votre hub IoT peut prendre plusieurs minutes.

    *YourIotHubName*. Remplacez l’espace réservé ci-dessous par le nom que vous avez choisi pour votre hub IoT. Le nom du hub IoT doit être globalement unique dans Azure. Cet espace réservé est utilisé dans le reste de ce démarrage rapide pour représenter votre nom de hub IoT unique.

    Le paramètre `--sku F1` crée le hub IoT de niveau gratuit. Les hubs de niveau gratuit disposent d’un ensemble limité de fonctionnalités, et sont utilisés pour des applications de preuve de concept. Pour plus d’informations sur les niveaux les fonctionnalités et la tarification d’IoT Hub, consultez [Tarification d’Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub). 

    ```azurecli
    az iot hub create --resource-group MyResourceGroup --name {YourIoTHubName} --sku F1 --partition-count 2
    ```

1. Une fois le hub IoT créé, affichez la sortie JSON dans la console, puis copiez la valeur `hostName` que vous allez utiliser dans une étape ultérieure. La valeur `hostName` ressemble à l’exemple suivant :

    `{Your IoT hub name}.azure-devices.net`

### <a name="configure-iot-explorer"></a>Configurer IoT Explorer

Dans la suite de ce démarrage rapide, vous allez utiliser IoT Explorer pour inscrire un appareil auprès de votre hub IoT, afficher les propriétés et la télémétrie de l’appareil, et envoyer des commandes à celui-ci. Dans cette section, vous allez configurer IoT Explorer pour qu’il se connecte au hub IoT que vous venez de créer et lise les modèles Plug-and-Play à partir du référentiel de modèle public. 

Pour ajouter une connexion à votre hub IoT :

1. Dans votre application CLI, exécutez la commande [az iot hub connection-string show](/cli/azure/iot/hub/connection-string#az_iot_hub_connection_string_show) pour obtenir la chaîne de connexion à votre hub IoT.

    ```azurecli
    az iot hub connection-string  show --hub-name {YourIoTHubName}
    ```

1. Copiez la chaîne de connexion sans les guillemets qui l’entourent.
1. Dans Azure IoT Explorer, sélectionnez **Hubs IoT** dans le menu de gauche, puis **+ Ajouter une connexion**.
1. Collez la chaîne de connexion dans la zone **Chaîne de connexion**.
1. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-connection.png" alt-text="Capture d’écran de l’ajout d’une connexion dans IoT Explorer":::

1. Si la connexion aboutit, IoT Explorer bascule vers l’affichage **Appareils**.

Pour ajouter le référentiel de modèle public :

1. Dans IoT Explorer, sélectionnez **Accueil** pour revenir à la vue d’accueil.
1. Dans le menu de gauche, sélectionnez **Paramètres IoT Plug-and-Play**, puis **+ Ajouter** et **Référentiel public** dans le menu déroulant.
1. Une entrée s’affiche pour le référentiel de modèle public à l’emplacement `https://devicemodels.azure.com`.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-add-public-repository.png" alt-text="Capture d’écran de l’ajout du référentiel de modèle public dans IoT Explorer":::

1. Sélectionnez **Enregistrer**.

### <a name="register-a-device"></a>Inscrire un appareil

Dans cette section, vous allez créer une instance d’appareil et l’inscrire auprès du hub IoT que vous avez créé. Vous allez utiliser les informations de connexion de l’appareil que vous venez d’inscrire pour connecter votre appareil physique en toute sécurité dans une section ultérieure.

Pour inscrire un appareil :

1. À partir de la vue d’accueil dans IoT Explorer, sélectionnez **Hubs IoT**.
1. La connexion que vous avez ajoutée précédemment devrait apparaître. Sous les propriétés de connexion, sélectionnez **Afficher les appareils dans ce hub**.
1. Sélectionnez **+ Nouveau**, puis entrez un ID d’appareil pour votre appareil, par exemple, *mydevice*. Conservez toutes les autres propriétés en l’état.
1. Sélectionnez **Create** (Créer).

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-created.png" alt-text="Capture d’écran d’identité d’appareil Azure IoT Explorer":::

1. Utilisez les boutons de copie pour copier et noter les champs **ID de l’appareil** et **Clé primaire**.

Avant de passer à la section suivante, vérifiez que vous avez copié les valeurs suivantes :

* `hostName`
* `deviceId`
* `primaryKey`

## <a name="prepare-the-device"></a>Préparer l’appareil

Pour connecter le DevKit MXCHIP à Azure, vous allez modifier un fichier de configuration pour les paramètres Wi-Fi et Azure IoT, regénérer l’image et la flasher sur l’appareil.

### <a name="add-configuration"></a>Ajouter une configuration

1. Ouvrez le fichier suivant dans un éditeur de texte :

    *getting-started\MXChip\AZ3166\app\azure_config.h*

1. Commentez la ligne suivante en haut du fichier, comme illustré :

    ```c
    // #define ENABLE_DPS
    ```

1. Affectez aux constantes Wi-Fi les valeurs suivantes à partir de votre environnement local.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`WIFI_SSID` |{*Votre SSID Wi-Fi*}|
    |`WIFI_PASSWORD` |{*Votre mot de passe Wi-Fi*}|
    |`WIFI_MODE` |{*Une des valeurs de mode de Wi-Fi énumérées dans le fichier*}|

1. Définissez les constantes d’informations de l’appareil Azure IoT sur les valeurs que vous avez enregistrées après avoir créé les ressources Azure.

    |Nom de la constante|Valeur|
    |-------------|-----|
    |`IOT_HUB_HOSTNAME` |{*Valeur du nom d’hôte de votre hub IoT*}|
    |`IOT_DPS_REGISTRATION_ID` |{*Valeur d’ID de votre appareil*}|
    |`IOT_DEVICE_SAS_KEY` |{*Valeur de votre clé primaire*}|

1. Enregistrez et fermez le fichier.

### <a name="build-the-image"></a>Créer l’image

1. Dans votre console ou dans l’Explorateur de fichiers, exécutez le script *rebuild.bat* à l’emplacement suivant pour générer l’image :

    *getting-started\MXChip\AZ3166\tools\rebuild.bat*

2. Une fois la génération terminée, vérifiez que le fichier binaire a été créé dans le chemin d’accès suivant :

    *getting-started\MXChip\AZ3166\build\app\mxchip_azure_iot.bin*

### <a name="flash-the-image"></a>Flasher l’image

1. Dans le DevKit MXCHIP, recherchez le bouton **Réinitialiser** et le port micro USB. Vous pouvez utiliser ces composants dans les étapes suivantes. Les deux sont mis en surbrillance dans l’image suivante :

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/mxchip-iot-devkit.png" alt-text="Localiser les composants clés sur la carte du DevKit MXChip":::

1. Branchez le câble micro USB au port micro USB du DevKit MXCHIP, puis branchez-le à votre ordinateur.
1. Dans l’Explorateur de fichiers, recherchez le fichier binaire que vous avez créé dans la section précédente.
1. Copiez le fichier binaire *mxchip_azure_iot. bin*.
1. Dans l’Explorateur de fichiers, recherchez l’appareil DevKit MXCHIP connecté à votre ordinateur. L’appareil apparaît en tant que lecteur sur votre système avec l’intitulé de lecteur **AZ3166**.
1. Collez le fichier binaire dans le dossier racine du DevKit MXCHIP. Le processus de flash démarre automatiquement et se termine en quelques secondes.

    > [!NOTE]
    > Au cours de ce processus, un voyant vert s’allume pour le DevKit MXCHIP.

### <a name="confirm-device-connection-details"></a>Vérifier les détails de connexion de l’appareil

Vous pouvez utiliser l’application **Termite** pour surveiller la communication et vérifier que votre appareil est correctement configuré.

1. Démarrez **Termite**.
    > [!TIP]
    > Si vous ne parvenez pas à connecter Termite à votre devkit, installez le [pilote ST-LINK](https://my.st.com/content/ccc/resource/technical/software/driver/files/stsw-link009.zip), puis réessayez. Pour obtenir des étapes supplémentaires, consultez [Résolution des problèmes](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).
1. Sélectionnez **Paramètres**.
1. Dans la boîte de dialogue **Paramètres du port série**, vérifiez les paramètres suivants et mettez à jour si nécessaire :
    * **Vitesse (en bauds)**  : 115 200
    * **Port** : port auquel votre DevKit MXCHIP est connecté. Si plusieurs options de port sont disponibles dans la liste déroulante, vous pouvez trouver le port approprié à utiliser. Ouvrez le **Gestionnaire d'appareils** Windows et affichez les **Ports** pour identifier le port à utiliser.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/termite-settings.png" alt-text="Capture d’écran des paramètres de port série dans l’application Termite":::

1. Sélectionnez OK.
1. Appuyez sur le bouton **Réinitialiser** de l’appareil. Le bouton est étiqueté sur l’appareil et situé près du connecteur micro USB.
1. Dans l’application **Termite**, vérifiez les valeurs de point de contrôle suivantes pour confirmer que l’appareil est initialisé et connecté à Azure IoT.

    ```output
    Starting Azure thread

    Initializing WiFi
        MAC address: C8:93:46:8A:4C:43
        Connecting to SSID 'iot'
    SUCCESS: WiFi connected to iot

    Initializing DHCP
        IP address: 192.168.0.18
        Mask: 255.255.255.0
        Gateway: 192.168.0.1
    SUCCESS: DHCP initialized

    Initializing DNS client
        DNS address: 75.75.75.75
    SUCCESS: DNS client initialized

    Initializing SNTP client
        SNTP server 0.pool.ntp.org
        SNTP IP address: 157.245.166.169
        SNTP time update: Jun 8, 2021 18:16:50.807 UTC
    SUCCESS: SNTP initialized

    Initializing Azure IoT Hub client
        Hub hostname: ***.azure-devices.net
        Device id: mydevice
        Model id: dtmi:azurertos:devkit:gsgmxchip;1
    Connected to IoT Hub
    SUCCESS: Azure IoT Hub client initialized
    ```

Laissez Termite ouvert pour surveiller la sortie de l’appareil dans les étapes suivantes.

## <a name="view-device-properties"></a>Voir les propriétés de l’appareil

Vous pouvez utiliser Azure IoT Explorer pour afficher et gérer les propriétés de vos appareils. Dans cette section et les sections suivantes, vous allez utiliser les fonctionnalités Plug-and-Play proposées dans IoT Explorer pour gérer le DevKit MXCHIP et interagir avec celui-ci. Ces fonctionnalités s’appuient sur le modèle d’appareil publié pour le DevKit MXCHIP dans le référentiel de modèle public. Vous avez configuré IoT Explorer pour rechercher des modèles d’appareil dans ce référentiel plus tôt dans ce démarrage rapide. Dans de nombreux cas, vous pouvez effectuer la même action sans utiliser la technologie Plug-and-Play en sélectionnant la même action dans le menu de gauche du volet de votre appareil dans IoT Explorer. Toutefois, l’utilisation de la technologie Plug-and-Play offre souvent une expérience améliorée. En effet, IoT Explorer peut lire le modèle d’appareil spécifié par un appareil Plug-and-Play et présenter des informations spécifiques de celui-ci.  

Pour accéder aux composants IoT Plug-and-Play pour l’appareil dans IoT Explorer :

1. À partir de la vue d’accueil dans IoT Explorer, sélectionnez **Hubs IoT**, puis **Afficher les appareils dans ce hub**.
1. Sélectionnez votre appareil.
1. Sélectionnez **Composants IoT Plug-and-Play**.
1. Sélectionnez le **composant par défaut**. IoT Explorer affiche les composants IoT Plug-and-Play implémentés sur votre appareil.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-default-component-view.png" alt-text="Capture d’écran du composant DevKit MXCHIP par défaut dans IoT Explorer":::

1. Sous l’onglet **interface**, affichez le contenu JSON dans la **Description** du modèle d’appareil. Le JSON contient les détails de configuration de chaque composant IoT Plug-and-Play dans le modèle d’appareil.

    Chaque onglet dans IoT Explorer correspond à l’un des composants IoT Plug-and-Play dans le modèle d’appareil.

    | Onglet | Type | Nom | Description |
    |---|---|---|---|
    | **Interface** | Interface | `MXCHIP Getting Started Guide` | Exemple de modèle pour le DevKit MXCHIP |
    | **Propriétés (lecture seule)** | Propriété | -- | Le modèle n’a actuellement aucune propriété en lecture seule |
    | **Propriétés (accessibles en écriture)** | Propriété | `telemetryInterval` | Intervalle pendant lequel l’appareil envoie de la télémétrie |
    | **Commandes** | Commande | `setLedState` | Activer ou désactiver la LED |
    | **Télémétrie** | Télémétrie | `temperature` | Température en degrés Celsius |

Pour afficher les propriétés d’un appareil à l’aide d’Azure IoT Explorer :

1. Sélectionnez l’onglet **Propriétés (lecture seule)** . Actuellement, le modèle d’appareil n’expose aucune propriété en lecture seule.
1. Sélectionnez l’onglet **Propriétés (accessibles en écriture)** . Celui-ci affiche l’intervalle d’envoi de la télémétrie.
1. Remplacez la valeur de `telemetryInterval` par *5*, puis sélectionnez **Mettre à jour la valeur souhaitée**. Votre appareil utilise désormais cet intervalle pour envoyer la télémétrie.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-set-telemetry-interval.png" alt-text="Capture d’écran de la définition de l’intervalle de télémétrie sur le DevKit MXCHIP dans IoT Explorer":::

1. IoT Explorer répond avec une notification. Vous pouvez également observer la mise à jour dans Termite.
1. Réaffectez la valeur 10 à l’intervalle de télémétrie.
 
Pour utiliser Azure CLI pour afficher les propriétés de l’appareil :

1. Exécutez la commande [az iot hub device-identity show](/cli/azure/iot/hub/device-identity#az_iot_hub_device_identity_show).

    ```azurecli
    az iot hub device-identity show --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Examinez les propriétés de votre appareil dans la sortie de la console.

## <a name="view-telemetry"></a>Afficher les données de télémétrie

Avec IoT Central, vous pouvez afficher le flux de télémétrie de votre appareil vers le cloud. Si vous le souhaitez, vous pouvez effectuer la même tâche à l’aide d’Azure CLI.

Pour afficher la télémétrie dans Azure IoT Explorer :

1. Dans le volet **Composants IoT Plug-and-Play** (composant par défaut) de votre appareil dans IoT Explorer, sélectionnez l’onglet **Télémétrie**. Vérifiez que l’option **Utiliser l’Event Hub intégré** est définie sur *Oui*.
1. Sélectionnez **Démarrer**.
1. Affichez la télémétrie à mesure que l’appareil envoie des messages au cloud.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-device-telemetry.png" alt-text="Capture d’écran de télémétrie d’appareil dans IoT Explorer":::

    > [!NOTE]
    > Vous pouvez également surveiller la télémétrie à partir de l’appareil à l’aide de l’application Termite.

1. Activez la case à cocher **Afficher les événements modélisés** pour voir les événements dans le format de données spécifié par le modèle d’appareil.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-show-modeled-events.png" alt-text="Capture d’écran d’événements de télémétrie modélisés dans IoT Explorer":::

1. Sélectionnez **Arrêter** pour cesser de recevoir des événements.

Pour utiliser Azure CLI pour afficher la télémétrie d’un appareil :

1. Exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events). Utilisez les noms que vous avez créés précédemment dans Azure IoT pour votre appareil et le hub IoT.

    ```azurecli
    az iot hub monitor-events --device-id mydevice --hub-name {YourIoTHubName}
    ```

1. Affichez la sortie JSON dans la console

    ```json
    {
        "event": {
            "origin": "mydevice",
            "module": "",
            "interface": "dtmi:azurertos:devkit:gsgmxchip;1",
            "component": "",
            "payload": "{\"humidity\":41.21,\"temperature\":31.37,\"pressure\":1005.18}"
        }
    }
    ```

1. Sélectionnez CTRL+C pour cesser la surveillance.


## <a name="call-a-direct-method-on-the-device"></a>Appeler une méthode directe sur l’appareil

Vous pouvez également utiliser Azure IoT Explorer pour appeler une méthode directe que vous avez implémentée sur votre appareil. Les méthodes directes portent un nom et peuvent éventuellement avoir une charge utile JSON, une connexion configurable et un délai d’expiration de méthode. Dans cette section, vous appelez une méthode qui allume ou éteint une LED. Si vous le souhaitez, vous pouvez effectuer la même tâche à l’aide d’Azure CLI.

Pour appeler une méthode dans Azure IoT Explorer :

1. Dans le volet **Composants IoT Plug-and-Play** (composant par défaut) de votre appareil dans IoT Explorer, sélectionnez l’onglet **Commandes**.
1. Pour la commande **setLedState**, définissez **state** sur **true**.
1. Sélectionnez **Envoyer la commande**. Une notification devrait s’afficher dans IoT Explorer et le témoin LED USER de couleur jaune devrait s’allumer sur l’appareil.

    :::image type="content" source="media/quickstart-devkit-mxchip-az3166-iot-hub/iot-explorer-invoke-method.png" alt-text="Capture d’écran d’appel de la méthode setLedState dans IoT Explorer":::

1. Définissez **state** sur **false**, puis sélectionnez **Envoyer la commande**. Le témoin LED USER de couleur jaune devrait s’éteindre.
1. Si vous le souhaitez, vous pouvez afficher la sortie dans Termite pour surveiller l’état des méthodes.

Pour utiliser Azure CLI pour appeler une méthode :

1. Exécutez la commande [az iot hub invoke-device-method](/cli/azure/iot/hub#az_iot_hub_invoke_device_method) et spécifiez le nom et la charge utile de la méthode. Pour cette méthode, la définition de `method-payload` sur `true` a pour effet d’allumer la LED, et sa définition sur `false` de l’éteindre.

    ```azurecli
    az iot hub invoke-device-method --device-id mydevice --method-name setLedState --method-payload true --hub-name {YourIoTHubName}
    ```

    La console CLI affiche l’état de votre appel de méthode sur l’appareil, où `204` indique la réussite.

    ```json
    {
        "payload": {},
        "status": 200
    }    
    ```

1. Examiner votre appareil pour vérifier l’état de la LED.

1. Affichez le terminal Termite pour vérifier les messages de sortie :

    ```output
    Receive direct method: setLedState
        Payload: true
    LED is turned ON
    Device twin property sent: {"ledState":true}
    ```

## <a name="troubleshoot-and-debug"></a>Résoudre les problèmes et déboguer

Si vous rencontrez des problèmes pour générer le code d’appareil, flasher l’appareil ou le connecter, consultez [Résolution des problèmes](https://github.com/azure-rtos/getting-started/blob/master/docs/troubleshooting.md).

Pour déboguer l’application, consultez [Débogage avec Visual Studio Code](https://github.com/azure-rtos/getting-started/blob/master/docs/debugging.md).

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources Azure créées dans ce démarrage rapide, vous pouvez utiliser Azure CLI pour supprimer le groupe de ressources.

> [!IMPORTANT] 
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects.

Pour supprimer un groupe de ressources par nom :

1. Exécutez la commande [az group delete](/cli/azure/group#az-group-delete). Cela supprime le groupe de ressources, le hub IoT et l’inscription de l’appareil que vous avez créée.

    ```azurecli-interactive
    az group delete --name MyResourceGroup
    ```

1. Exécutez la commande [az group list](/cli/azure/group#az-group-list) pour confirmer la suppression du groupe de ressources.  

    ```azurecli-interactive
    az group list
    ```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez créé une image personnalisée contenant un exemple de code Azure RTOS, puis flashé l’image sur l’appareil DevKit MXCHIP. Vous avez également utilisé Azure CLI et/ou IoT Explorer pour créer des ressources Azure, connecter le DevKit MXCHIP en toute sécurité à Azure, afficher la télémétrie et envoyer des messages.

L’étape suivante consiste à explorer les articles ci-dessous pour savoir comment connecter des appareils à Azure IoT à l’aide des kits de développement logiciel (SDK) d’appareil IoT. 

> [!div class="nextstepaction"]
> [Connecter un DevKit MXCHIP AZ3166 à IoT Central](quickstart-devkit-mxchip-az3166.md)
> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Central](quickstart-send-telemetry-central.md)
> [!div class="nextstepaction"]
> [Connecter un appareil simulé à IoT Hub](quickstart-send-telemetry-iot-hub.md)

> [!IMPORTANT]
> Azure RTOS fournit aux fabricants OEM les composants permettant de sécuriser la communication et de créer du code et l’isolation des données à l’aide des mécanismes sous-jacents de protection matérielle MCU/MPU. Toutefois, chaque fabricant OEM est tenu de garantir que son appareil répond aux exigences de sécurité en constante évolution.

