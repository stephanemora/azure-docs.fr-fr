---
title: Fichier include
description: Fichier include
author: timlt
ms.service: iot-develop
ms.topic: include
ms.date: 05/05/2021
ms.author: timlt
ms.custom: include file
ms.openlocfilehash: e096e1259b6f58476d55cd2811ccdbb87d1fd46e
ms.sourcegitcommit: 38d81c4afd3fec0c56cc9c032ae5169e500f345d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2021
ms.locfileid: "109518270"
---
## <a name="prerequisites"></a>Prérequis
- Si vous n’avez pas d’abonnement Azure, [créez-en un gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- [Git](https://git-scm.com/downloads).
- Azure CLI. Vous avez le choix entre deux options pour exécuter les commandes Azure CLI dans ce guide de démarrage rapide :
    - Utilisez Azure Cloud Shell, interpréteur de commandes interactif qui exécute des commandes CLI dans votre navigateur. Cette option est recommandée, car vous n’avez pas besoin d’installer quoi que ce soit. Si vous utilisez Cloud Shell pour la première fois, connectez-vous au [portail Azure](https://portal.azure.com). Suivez les étapes décrites dans [Démarrage rapide de Cloud Shell](/azure/cloud-shell/quickstart) pour **démarrer Cloud Shell** et **sélectionner l’environnement Bash**.
    - Si vous le souhaitez, exécutez Azure CLI sur votre ordinateur local. Ce guide de démarrage rapide requiert Azure CLI version 2.0.76 ou ultérieure. Pour vérifier la version, exécutez `az --version`. Suivez les étapes décrites dans [Installer Azure CLI]( /cli/azure/install-azure-cli) pour installer ou mettre à niveau Azure CLI, l’exécuter et vous connecter. Si vous y êtes invité, installez les extensions Azure CLI lors de la première utilisation.

Installez les composants requis restants pour votre système d’exploitation.

### <a name="linux"></a>Linux
Les étapes de ce tutoriel ont été testées à l’aide d’Ubuntu Linux 18.04.

Pour suivre ce guide de démarrage rapide sur Linux, vous devez installer les logiciels suivants sur votre environnement Linux :

Installez **GCC**, **Git**, **cmake** et les dépendances nécessaires à l’aide de la commande `apt-get` :

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

```sh
cmake --version
gcc --version
```

### <a name="windows"></a>Windows
Pour suivre ce guide de démarrage rapide sur Windows, installez Visual Studio 2019 et ajoutez les composants requis pour le développement C et C++.

1. Pour les nouveaux utilisateurs, installez [Visual Studio (Community, Professional ou Enterprise) 2019](https://visualstudio.microsoft.com/downloads/). Téléchargez l’édition que vous souhaitez installer et démarrez le programme d’installation.
    > [!NOTE]
    > Pour les utilisateurs de Visual Studio 2019 existants, sélectionnez **Démarrer** dans Windows, tapez *Visual Studio Installer*, puis démarrez le programme d’installation.
1. Sous l’onglet **Charges de travail** du programme d’installation, sélectionnez la charge de travail **Développement Desktop en C++** .
1. Exécutez l’installation.

[!INCLUDE [iot-hub-include-create-hub-cli](iot-hub-include-create-hub-cli.md)]

## <a name="run-a-simulated-device"></a>Exécuter un appareil simulé
Dans cette section, vous allez utiliser le kit SDK C pour envoyer des messages de votre appareil simulé à votre hub IoT.

### <a name="build-the-sample"></a>Générer l’exemple
1. Ouvrez une console pour installer le kit SDK C d’appareil Azure IoT et exécuter l’exemple de code. Pour Windows, sélectionnez **Démarrer**, tapez *Invite de commandes développeur pour VS 2019*, puis ouvrez la console. Pour Linux, ouvrez Bash.
    > [!NOTE]
    > Vous devez maintenant avoir deux fenêtres de console ouvertes : celle que vous venez d’ouvrir et la console Cloud Shell ou CLI que vous avez utilisée précédemment pour entrer les commandes CLI.

1. Dans votre console C, clonez le kit SDK C d’appareil Azure IoT sur votre ordinateur local :
    ```console
    git clone https://github.com/Azure/azure-iot-sdk-c.git
    ```
1. Accédez au dossier racine du kit SDK et exécutez la commande suivante pour mettre à jour les dépendances :
    ```console
    cd azure-iot-sdk-c
    git submodule update --init
    ```
    Cette opération prend quelques minutes.
1. Exécutez les commandes suivantes pour générer le kit SDK et les exemples :

    ```console
    cmake -Bcmake -Duse_prov_client=ON -Dhsm_type_symm_key=ON -Drun_e2e_tests=OFF
    cmake --build cmake
    ```
1. Définissez les variables d’environnement suivantes pour permettre à votre appareil simulé de se connecter à Azure IoT.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_CONNECTION_STRING`. Pour la valeur de la variable, utilisez la chaîne de connexion de l’appareil que vous avez enregistrée dans la section précédente.
    * Définissez une variable d’environnement appelée `IOTHUB_DEVICE_SECURITY_TYPE`. Pour la variable, utilisez la valeur de chaîne littérale `connectionString`.

    **CMD**

    ```console
    set IOTHUB_DEVICE_CONNECTION_STRING=<your connection string here>
    set IOTHUB_DEVICE_SECURITY_TYPE=connectionString
    ```
    > [!NOTE]
    > Pour Windows CMD, il n’y a pas de guillemets avant et après les valeurs de chaîne pour chaque variable.

    **Bash**

    ```bash
    export IOTHUB_DEVICE_CONNECTION_STRING="<your connection string here>"
    export IOTHUB_DEVICE_SECURITY_TYPE="connectionString"
    ```

### <a name="run-the-code"></a>Exécuter le code
1. Dans votre application CLI, exécutez la commande [az iot hub monitor-events](/cli/azure/iot/hub#az_iot_hub_monitor_events) pour commencer à surveiller les événements sur votre appareil IoT simulé.  Les messages d’événement sont imprimés dans le terminal au fur et à mesure de leur arrivée.

    ```azurecli-interactive
    az iot hub monitor-events --output table --hub-name {YourIoTHubName}
    ```
1. Exécutez l’exemple de code à l’aide de la commande appropriée pour votre console :

    **CMD**
    ```console
    cmake\iothub_client\samples\pnp\pnp_temperature_controller\Debug\pnp_temperature_controller.exe
    ```

    **Bash**
    ```bash
    cmake/iothub_client/samples/pnp/pnp_temperature_controller/Debug/pnp_temperature_controller
    ```
    > [!NOTE]
    > Cet exemple de code utilise Azure IoT Plug-and-Play, qui vous permet d’intégrer des appareils intelligents dans vos solutions sans aucune configuration manuelle.  Par défaut, la plupart des exemples de cette documentation utilisent IoT Plug-and-Play. Pour en savoir plus sur les avantages et les cas d’utilisation d’IoT PnP, consultez [Qu’est ce qu’IoT Plug-and-Play ?](../articles/iot-pnp/overview-iot-plug-and-play.md).

    Une fois que votre appareil simulé se connecte à votre application IoT Central, il se connecte à l’instance d’appareil que vous avez créée dans l’application et commence à envoyer de la télémétrie. Les détails de la connexion et la sortie de la télémétrie sont affichés dans votre console : 
    
    ```output
    Starting event monitor, use ctrl-c to stop...
    event:
      component: ''
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: myDevice
      payload: '{"workingSet":1251}'
    
    event:
      component: thermostat1
      interface: dtmi:com:example:TemperatureController;1
      module: ''
      origin: myDevice
      payload: '{"temperature":22.00}'
    ```