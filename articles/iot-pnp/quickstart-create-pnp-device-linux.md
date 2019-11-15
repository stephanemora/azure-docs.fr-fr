---
title: Créer un appareil Azure IoT Plug-and-Play (Linux) (préversion) | Microsoft Docs
description: Utilisez un modèle de fonctionnalité d’appareil pour créer un code d’appareil. Puis, exécutez le code de l’appareil et observez l’appareil se connecter à votre IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 203725ba109922a8704c0e31a6e61dc6eadf6bd9
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585925"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-preview-device-linux"></a>Démarrage rapide : Utiliser un modèle de fonctionnalité d’appareil pour créer un appareil IoT Plug-and-Play Preview (Linux)

Un _modèle de fonctionnalité d’appareil_ décrit les fonctionnalités d’un appareil IoT Plug-and-Play. Un modèle de fonctionnalité d’appareil est souvent associé à une référence SKU de produit. Les fonctionnalités définies dans le modèle de fonctionnalité d’appareil sont organisées en interfaces réutilisables. Vous pouvez générer le code d’appareil squelette à partir d’un modèle de fonctionnalité d’appareil. Ce démarrage rapide vous montre comment utiliser VS Code sous Ubuntu Linux pour créer un appareil IoT Plug-and-Play à l’aide d’un modèle de fonctionnalité d’appareil.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide suppose que vous utilisez Ubuntu Linux avec un environnement de bureau. Les étapes de ce didacticiel ont été testées à l’aide d’Ubuntu 18.04.

Pour suivre ce démarrage rapide, vous devez installer les logiciels suivants sur votre machine locale Linux :

* Installez **GCC**, **Git**, **cmake** et toutes les dépendances à l’aide de la commande `apt-get` :

    ```sh
    sudo apt-get update
    sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
    ```

    Vérifiez que la version de `cmake` est supérieure à **2.8.12** et que la version de **GCC** est supérieure à **4.4.7**.

    ```sh
    cmake --version
    gcc --version
    ```

* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installer Azure IoT Tools

Pour installer le pack d’extension [Azure IoT Tools pour VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), procédez comme suit :

1. Dans VS Code, sélectionnez l’onglet **Extensions**.
1. Recherchez **Azure IoT Tools**.
1. Sélectionnez **Installer**.

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obtenir la chaîne de connexion pour le référentiel de modèles de votre entreprise

Vous pouvez trouver la _chaîne de connexion du référentiel de modèles de l’entreprise_ dans le [portail Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com) quand vous vous connectez avec un compte professionnel ou scolaire Microsoft, ou votre ID de partenaire Microsoft si vous avez un. Une fois que vous êtes connecté, sélectionnez **Company repository (Référentiel d’entreprise)** , puis **Chaînes de connexion**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Pour suivre ce démarrage rapide, vous devez disposer d'un hub Azure IoT dans votre abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Si vous utilisez Azure CLI localement, la version de `az` doit être **2.0.75** ou une version ultérieure ; Azure Cloud Shell utilise la version la plus récente. Utilisez la commande `az --version` pour vérifier la version installée sur votre ordinateur.

Ajoutez l’extension Microsoft Azure IoT pour Azure CLI :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Les étapes de ce guide de démarrage rapide nécessitent la version **0.8.5** ou une version ultérieure de l’extension. Utilisez la commande `az extension list` pour vérifier la version que vous avez installée, et la commande `az extension update` pour mettre à jour, si nécessaire.

En l'absence de hub IoT, créez-en un à l’aide des commandes suivantes, en remplaçant `{YourIoTHubName}` par un nom unique de votre choix. Si vous exécutez ces commandes localement, commencez par vous connecter à votre abonnement Azure à l’aide de `az login`. Si vous exécutez ces commandes dans Azure Cloud Shell, vous êtes automatiquement connecté :

  ```azurecli-interactive
  az group create --name pnpquickstarts_rg --location centralus
  az iot hub create --name {YourIoTHubName} \
    --resource-group pnpquickstarts_rg --sku S1
  ```

Les commandes précédentes créent un groupe de ressources appelé `pnpquickstarts_rg`, ainsi qu'un hub IoT dans la région USA Centre.

> [!IMPORTANT]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

Exécutez la commande suivante pour créer une identité d’appareil appelée `mypnpdevice` dans votre hub IoT. Remplacez l’espace réservé `{YourIoTHubName}` par le nom de votre hub IoT :

```azurecli-interactive
az iot hub device-identity create --hub-name {YourIoTHubName} --device-id mypnpdevice
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion_ de l’appareil que vous venez d’inscrire. Vous aurez besoin de cette chaîne de connexion plus loin dans ce démarrage rapide :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id mypnpdevice --output table
```

## <a name="author-your-model"></a>Créer votre modèle

Dans ce démarrage rapide, vous utilisez un exemple de modèle de fonctionnalité d’appareil existant et les interfaces associées.

1. Créez un répertoire `pnp_app` dans votre répertoire de base. Vous utilisez ce dossier pour les fichiers de modèle d’appareil et le stub de code d’appareil.

    ```bash
    cd ~
    mkdir pnp_app
    ```

1. Téléchargez le modèle de fonctionnalité d’appareil et les exemples de fichiers d'interface dans le dossier `pnp_app`.

    ```bash
    cd pnp_app
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/SampleDevice.capabilitymodel.json
    curl -O -L https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensor.interface.json
    ```

1. Ouvrez le dossier `pnp_app` avec VS Code. Vous pouvez afficher les fichiers avec IntelliSense :

    ![Modèle de fonctionnalité d’appareil](media/quickstart-create-pnp-device-linux/dcm.png)

1. Dans les fichiers que vous avez téléchargés, remplacez `<YOUR_COMPANY_NAME_HERE>` dans les champs `@id` et `schema` par une valeur unique. Utilisez uniquement les caractères a-z, A-Z, 0-9 et le trait de soulignement. Pour plus d’informations, consultez la section [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format) (Format d’identificateur pour les jumeaux numériques).

## <a name="generate-the-c-code-stub"></a>Générer le stub de code C

Maintenant que vous avez le modèle de fonctionnalité d’appareil et ses interfaces associées, vous pouvez générer le code de l’appareil qui implémente le modèle. Pour générer le stub de code C dans VS Code :

1. Une fois le dossier `pnp_app` ouvert dans VS Code, utilisez **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play** et sélectionnez **Générer le stub de code de l'appareil**.

    > [!NOTE]
    > La première fois que vous utilisez l’outil de génération de code IoT Plug-and-Play, le téléchargement prend quelques secondes.

1. Choisissez le fichier **SampleDevice.capabilitymodel.json** à utiliser pour générer le stub de code de l’appareil.

1. Entrez le nom du projet, c’est-à-dire le nom de votre application d’appareil : **exemple_appareil**.

1. Choisissez **ANSI C** comme langage.

1. Choisissez le mode de connexion **Via IoT Hub device connection string (Avec une chaîne de connexion d’appareil IoT Hub)** .

1. Choisissez **Projet CMake sur Linux** en tant que modèle de projet.

1. Choisissez **Via le code source** pour inclure le kit de développement logiciel (SDK).

1. VS Code ouvre une nouvelle fenêtre avec les fichiers de stub de code d’appareil générés.
    ![Code d’appareil](media/quickstart-create-pnp-device-linux/device-code.png)

## <a name="build-and-run-the-code"></a>Générer et exécuter le code

Vous utilisez le code source du kit de développement logiciel (SDK) de l'appareil pour générer le stub de code d’appareil généré. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Exécutez les commandes suivantes pour télécharger le code source du kit de développement logiciel (SDK) de l'appareil :

    ```bash
    cd ~/pnp_app/sample_device
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

1. Créez un dossier de build **CMake** pour l’application **sample_device** :

    ```bash
    cd ~/pnp_app/sample_device
    mkdir cmake
    cd cmake
    ```

1. Exécutez CMake pour générer votre application avec le kit de développement logiciel (SDK) :

    ```bash
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON -Dskip_samples:BOOL=ON
    cmake --build .
    ```

1. Une fois que la génération a été effectuée correctement, exécutez votre application en transmettant la chaîne de connexion de l’appareil IoT Hub en tant que paramètre.

    ```sh
    cd ~/pnp_app/sample_device/cmake
    ./sample_device "{IoT Hub device connection string}"
    ```

1. L’application de l’appareil commence à envoyer des données à IoT Hub.

    ![Application d’appareil en cours d’exécution](media/quickstart-create-pnp-device-linux/device-app-running.png)

## <a name="validate-the-code"></a>Valider le code

### <a name="publish-device-model-files-to-model-repository"></a>Publier les fichiers de modèle d’appareil vers le référentiel de modèles

Pour valider le code de l’appareil avec la CLI **az**, vous devez publier les fichiers dans le référentiel de modèles.

1. Une fois le dossier `pnp_app` ouvert dans VS Code, utilisez **Ctrl+Maj+P** pour ouvrir la palette de commandes, entrez et sélectionnez **IoT Plug-and-Play : Submit files to Model Repository (Envoyer les fichiers vers le référentiel de modèles)** .

1. Sélectionnez les fichiers `SampleDevice.capabilitymodel.json` et `EnvironmentalSensor.interface.json`.

1. Entrez la chaîne de connexion du référentiel de modèles d’entreprise.

    > [!NOTE]
    > La chaîne de connexion est requise uniquement la première fois que vous vous connectez au référentiel.

1. Dans la fenêtre de sortie et la notification VS Code, vous pouvez vérifier que les fichiers ont été correctement publiés.

    > [!NOTE]
    > Si vous recevez des erreurs lors de la publication des fichiers de modèle d’appareil, vous pouvez essayer d’utiliser la commande **IoT Plug-and-Play: Sign out Model Repository** pour vous déconnecter et suivre à nouveau les étapes.

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Utiliser Azure IoT CLI pour valider le code

Une fois l’exemple de client d’appareil démarré, vous pouvez vérifier qu’il fonctionne avec Azure CLI.

Utilisez la commande suivante pour afficher les données de télémétrie que l’exemple d’appareil envoie. Vous devrez peut-être attendre une minute ou deux avant de voir les données de télémétrie dans la sortie :

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mypnpdevice
```

Utilisez la commande suivante pour afficher toutes les propriétés envoyées par l’appareil :

```azurecli-interactive
az iot dt list-properties --device-id mypnpdevice --hub-name {Your IoT hub name} --source private --repo-login "{Your company model repository connection string}"
```

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un appareil IoT Plug-and-Play à l’aide d’un modèle de fonctionnalité d’appareil.

Pour en savoir plus sur les modèles de fonctionnalités d’appareils et la façon de créer vos propres modèles, poursuivez avec le tutoriel :

> [!div class="nextstepaction"]
> [Tutoriel : Créer et tester un modèle de fonctionnalité d’appareil avec Visual Studio Code](tutorial-pnp-visual-studio-code.md)
