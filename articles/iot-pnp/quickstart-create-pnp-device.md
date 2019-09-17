---
title: Créer un appareil IoT Plug-and-Play (préversion) | Microsoft Docs
description: Utilisez un modèle de fonctionnalité d’appareil pour créer un code d’appareil. Puis, exécutez le code de l’appareil et observez l’appareil se connecter à votre IoT Hub.
author: miagdp
ms.author: miag
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 6e5e08df444f66f2c5500d968c805552d20901c5
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861205"
---
# <a name="quickstart-use-a-device-capability-model-to-create-an-iot-plug-and-play-device"></a>Démarrage rapide : Utiliser un modèle de fonctionnalité d’appareil pour créer un appareil IoT Plug-and-Play

Un _modèle de fonctionnalité d’appareil_ décrit les fonctionnalités d’un appareil IoT Plug-and-Play. Un modèle de fonctionnalité d’appareil est souvent associé à une référence SKU de produit. Les fonctionnalités définies dans le modèle de fonctionnalité d’appareil sont organisées en interfaces réutilisables. Vous pouvez générer le code d’appareil squelette à partir d’un modèle de fonctionnalité d’appareil. Ce démarrage rapide vous montre comment utiliser VS Code pour créer un appareil IoT Plug-and-Play à l’aide d’un modèle de fonctionnalité d’appareil.

## <a name="prerequisites"></a>Prérequis

Pour suivre ce démarrage rapide, vous devez installer les logiciels suivants sur votre machine locale :

* [Visual Studio (Community, Professional ou Enterprise)](https://visualstudio.microsoft.com/downloads/) : assurez-vous d’inclure le composant **Gestionnaire de package NuGet** et la charge de travail **Développement Desktop en C++** lorsque vous installez Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).
* [Visual Studio Code](https://code.visualstudio.com/).

### <a name="install-azure-iot-tools"></a>Installer Azure IoT Tools

Pour installer le pack d’extension [Azure IoT Tools pour VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), procédez comme suit :

1. Dans VS Code, sélectionnez l’onglet **Extensions**.
1. Recherchez **Azure IoT Tools**.
1. Sélectionnez **Installer**.

### <a name="install-the-azure-iot-explorer"></a>Installer l’explorateur Azure IoT

Téléchargez et installez l’outil Explorateur Azure IoT à partir de la page sur la [version la plus récente](https://github.com/Azure/azure-iot-explorer/releases).

### <a name="get-the-connection-string-for-your-company-model-repository"></a>Obtenir la chaîne de connexion pour le référentiel de modèles de votre entreprise

Vous pouvez trouver la _chaîne de connexion du référentiel de modèles de l’entreprise_ dans le [portail Azure Certified pour IoT](https://preview.catalog.azureiotsolutions.com) quand vous vous connectez avec un compte professionnel ou scolaire Microsoft, ou votre ID de partenaire Microsoft si vous avez un. Une fois que vous êtes connecté, sélectionnez **Company repository (Référentiel d’entreprise)** , puis **Chaînes de connexion**.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Préparer un hub IoT

Vous avez également besoin d’un hub Azure IoT dans votre abonnement Azure pour suivre ce démarrage rapide. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

> [!NOTE]
> Dans le cadre de la préversion publique, les fonctionnalités IoT Plug-and-Play sont disponibles uniquement sur les hubs IoT créés dans les régions **USA Centre**, **Europe Nord** et **Japon Est**.

Ajoutez l’extension Microsoft Azure IoT pour Azure CLI :

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Exécutez la commande suivante pour créer une identité d’appareil dans votre hub IoT. Remplacez les espaces réservés **YourIoTHubName** et **YourDevice** par vos noms réels. Si vous n’avez pas de hub IoT, [suivez ces instructions pour en créer un](../iot-hub/iot-hub-create-using-cli.md) :

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion_ de l’appareil que vous venez d’inscrire :

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Exécutez les commandes suivantes pour obtenir la _chaîne de connexion IoT Hub_ pour votre hub :

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Préparer l’environnement de développement

### <a name="get-azure-iot-device-sdk-for-c"></a>Se procurer Azure IoT device SDK pour C

Dans ce démarrage rapide, vous préparez un environnement de développement pour cloner et générer le Azure IoT device SDK pour C.

1. Ouvrez une invite de commandes. Exécutez la commande suivante pour cloner le référentiel GitHub du [Kit de développement logiciel (SDK) Azure IoT pour C](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd/sh
    git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
    ```

    Attendez-vous à ce que cette opération prenne plusieurs minutes.

1. Créez un sous-répertoire `pnp_app` à la racine du clone local du référentiel. Vous utilisez ce dossier pour les fichiers de modèle d’appareil et le stub de code d’appareil.

    ```cmd/sh
    cd azure-iot-sdk-c
    mkdir pnp_app
    ```

## <a name="author-your-model"></a>Créer votre modèle

Dans ce démarrage rapide, vous utilisez un exemple de modèle de fonctionnalité d’appareil existant et les interfaces associées.

1. Téléchargez le [modèle de fonctionnalité d’appareil](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/SampleDevice.capabilitymodel.json) et l’[exemple d’interface](https://github.com/Azure/IoTPlugandPlay/blob/master/samples/EnvironmentalSensor.interface.json), puis enregistrez les fichiers dans le dossier `pnp_app`.

    > [!TIP]
    > Pour télécharger un fichier à partir de GitHub, accédez au fichier, cliquez avec le bouton droit sur **Brut**, puis sélectionnez **Enregistrer le lien sous**.

1. Ouvrez le dossier `pnp_app` avec VS Code. Vous pouvez afficher les fichiers avec IntelliSense :

    ![Modèle de fonctionnalité d’appareil](media/quickstart-create-pnp-device/dcm.png)

1. Dans les fichiers que vous avez téléchargés, remplacez `<YOUR_COMPANY_NAME_HERE>` dans les champs `@id` et `schema` par une valeur unique. Utilisez uniquement les caractères a-z, A-Z, 0-9 et le trait de soulignement. Pour plus d’informations, consultez la section [Digital Twin identifier format](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL#digital-twin-identifier-format) (Format d’identificateur pour les jumeaux numériques).

## <a name="generate-the-c-code-stub"></a>Générer le stub de code C

Maintenant que vous avez le modèle de fonctionnalité d’appareil et ses interfaces associées, vous pouvez générer le code de l’appareil qui implémente le modèle. Pour générer le stub de code C dans VS Code :

1. Avec le dossier contenant les fichiers DCM ouverts, utilisez **Ctrl + Maj + P** pour ouvrir la palette de commandes, entrez **IoT Plug-and-Play**, puis sélectionnez **Generate Device Code Stub (Générer le stub de code de l’appareil)** .

    > [!NOTE]
    > La première fois que vous utilisez l’outil de génération de code IoT Plug-and-Play, le téléchargement prend quelques secondes.

1. Choisissez le fichier DCM que vous souhaitez utiliser pour générer le stub de code de l’appareil.

1. Entrez le nom du projet, c’est-à-dire le nom de votre application d’appareil : **exemple_appareil**.

1. Choisissez **ANSI C** comme langage.

1. Choisissez **Projet CMake** comme type de projet.

1. Choisissez le mode de connexion **Via IoT Hub device connection string (Avec une chaîne de connexion d’appareil IoT Hub)** .

1. VS Code ouvre une nouvelle fenêtre avec les fichiers de stub de code d’appareil générés.
    ![Code d’appareil](media/quickstart-create-pnp-device/device-code.png)

## <a name="build-the-code"></a>Générer le code

Vous utilisez le SDK d’appareil pour générer le stub de code d’appareil généré. L’application que vous créez simule un appareil qui se connecte à un hub IoT. L’application envoie les données de télémétrie et les propriétés et reçoit des commandes.

1. Dans VS Code, ouvrez le dossier racine `CMakeLists.txt` du SDK d’appareil.

1. Ajoutez la ligne ci-dessous au bas du fichier `CMakeLists.txt` pour inclure le dossier stub de code de l’appareil lors de la compilation :

    ```txt
    add_subdirectory(pnp_app/sample_device)
    ```

1. Créez un sous-répertoire cmake dans le dossier racine du SDK d’appareil, puis accédez à ce dossier :

    ```cmd\sh
    mkdir cmake
    cd cmake
    ```

1. Exécutez les commandes suivantes pour générer le SDK d’appareil et le stub de code généré :

    ```cmd\sh
    cmake .. -Duse_prov_client=ON -Dhsm_type_symm_key:BOOL=ON
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Si cmake ne parvient pas à trouver votre compilateur C++, vous obtenez des erreurs de build lorsque vous exécutez la commande précédente. Si cela se produit, essayez d’exécuter cette commande dans [l’invite de commandes Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

1. Une fois que la génération a été effectuée correctement, exécutez votre application en transmettant la chaîne de connexion de l’appareil IoT Hub en tant que paramètre.

    ```cmd\sh
    cd azure-iot-sdk-c\cmake\pnp_app\sample_device\Release\
    sample_device.exe "[IoT Hub device connection string]"
    ```

1. L’application de l’appareil commence à envoyer des données à IoT Hub.

    ![Application d’appareil en cours d’exécution](media/quickstart-create-pnp-device/device-app-running.png)

## <a name="validate-the-code"></a>Valider le code

### <a name="publish-device-model-files-to-model-repository"></a>Publier les fichiers de modèle d’appareil vers le référentiel de modèles

Pour valider le code de l’appareil avec **Azure IoT Explorer**, vous devez publier les fichiers dans le référentiel de modèles.

1. Avec le dossier contenant les fichiers de modèle de fonctionnalité d’appareil ouverts, utilisez **Ctrl + Maj + P** pour ouvrir la palette de commandes, tapez et sélectionnez **IoT Plug & Play : Submit files to Model Repository (Envoyer les fichiers vers le référentiel de modèles)** .

1. Sélectionnez les fichiers `SampleDevice.capabilitymodel.json` et `EnvironmentalSensor.interface.json`.

1. Entrez la chaîne de connexion du référentiel de modèles d’entreprise.

    > [!NOTE]
    > La chaîne de connexion est requise uniquement la première fois que vous vous connectez au référentiel.

1. Dans la fenêtre de sortie et la notification VS Code, vous pouvez vérifier que les fichiers ont été correctement publiés.

    > [!NOTE]
    > Si vous recevez des erreurs lors de la publication des fichiers de modèle d’appareil, vous pouvez essayer d’utiliser la commande **IoT Plug-and-Play: Sign out Model Repository** pour vous déconnecter et suivre à nouveau les étapes.

### <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Utiliser l’Explorateur Azure IoT pour valider le code

1. Ouvrez l’Explorateur Azure IoT pour ouvrir la page **Configurations de l’application**.

1. Entrez votre chaîne de connexion IoT Hub, puis cliquez sur **Se connecter**.

1. Une fois que vous êtes connecté, la page de vue d’ensemble de l’appareil s’affiche.

1. Pour ajouter le référentiel de votre entreprise, sélectionnez **Paramètres**, puis **+Nouveau**, puis **Company repository (Référentiel de l’entreprise)** .

1. Ajoutez la chaîne de connexion du référentiel de modèles d’entreprise. Sélectionnez **Connecter**.

1. Sur la page de vue d’ensemble de l’appareil, recherchez l’identité d’appareil que vous avez créée précédemment et sélectionnez-la pour afficher plus de détails.

1. Développez l’interface avec l’ID **urn:azureiot:EnvironmentalSensor:1** pour voir les primitives IoT Plug and Play (propriétés, commandes et télémétrie).

1. Sélectionnez la page **Télémétrie** pour afficher les données de télémétrie envoyées par l’appareil.

1. Sélectionnez la page **Properties(non-writable)** (Propriétés (non accessibles en écriture)) pour afficher les propriétés non accessibles en écriture signalées par l’appareil.

1. Sélectionnez la page **Properties(writable)** (Propriétés (accessibles en écriture)) pour afficher les propriétés accessibles en écriture que vous pouvez mettre à jour.

1. Développez le **nom** de la propriété, mettez-le à jour avec un nouveau nom et sélectionnez **Update writable property (Mettre à jour la propriété accessible en écriture)** . 
2. Pour voir le nouveau nom s’afficher dans la colonne **Propriété rapportée**, cliquez sur le bouton **Actualiser** en haut de la page.

1. Sélectionnez la page **Commande** pour afficher toutes les commandes prises en charge par l’appareil.

1. Développez la commande **blink** et définissez un nouvel intervalle de clignotement. Sélectionnez **Envoyer la commande** pour appeler la commande sur l’appareil.

1. Accédez à l’appareil simulé pour vérifier que la commande s’est exécutée comme prévu.

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez appris à créer un appareil IoT Plug-and-Play à l’aide d’un modèle de fonctionnalité d’appareil.

Pour en savoir plus sur IoT Plug-and-Play, consultez le tutoriel suivant :

> [!div class="nextstepaction"]
> [Créer et tester un modèle de fonctionnalité d’appareil avec Visual Studio Code](tutorial-pnp-visual-studio-code.md)
