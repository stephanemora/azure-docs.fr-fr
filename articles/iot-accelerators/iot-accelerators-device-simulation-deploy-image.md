---
title: Déployer une image personnalisée de Device Simulation - Azure | Microsoft Docs
description: Dans ce guide pratique, vous allez apprendre à déployer une image Docker personnalisée de la solution Device Simulation sur Azure.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61448395"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>Déployer une image docker Device Simulation

Vous pouvez modifier la solution Device Simulation pour ajouter des fonctionnalités personnalisées. Par exemple, l’article [Sérialiser les données de télémétrie avec Protocol Buffers](iot-accelerators-device-simulation-protobuf.md) vous montre comment ajouter un appareil personnalisé à la solution qui utilise Protocol Buffers (Protobuf) pour envoyer la télémétrie. Une fois que vous avez testé vos modifications en local, l’étape suivante consiste à déployer les modifications apportées à votre instance Device Simulation dans Azure. Pour effectuer cette tâche, vous devez créer et déployer une image Docker qui contient votre service modifié.

Les étapes décrites dans ce guide pratique montrent comment :

1. Préparer un environnement de développement
1. Générer une nouvelle image Docker
1. Configurer Device Simulation pour utiliser votre nouvelle image Docker
1. Exécuter une simulation à l’aide de la nouvelle image

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre les étapes décrites dans ce guide pratique, vous devez disposer des éléments suivants :

* Une instance déployée de [Device Simulation d’appareil](quickstart-device-simulation-deploy.md).
* Docker. Téléchargez [Docker Community Edition](https://www.docker.com/products/docker-engine#/download) pour votre plateforme.
* Un [compte Docker Hub](https://hub.docker.com/) où vous pouvez charger vos images Docker. Dans votre compte Docker Hub, créez un référentiel public appelé **device-simulation**.
* Une [solution Device Simulation](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) modifiée et testée sur votre ordinateur local. Par exemple, vous pouvez modifier la solution pour [sérialiser les données de télémétrie avec Protocol Buffers](iot-accelerators-device-simulation-protobuf.md).
* Un interpréteur de commandes qui peut exécuter SSH. Si vous installez Git pour Windows, vous pouvez utiliser l’interpréteur de commandes **bash** qui fait partie de l’installation. Vous pouvez également utiliser votre [Azure Cloud Shell](https://shell.azure.com/).

Les instructions figurant dans cet article supposent que vous utilisez Windows. Si vous utilisez un autre système d’exploitation, vous devrez changer certains chemins de fichiers et certaines commandes en fonction de votre environnement.

## <a name="create-a-new-docker-image"></a>Créer une image Docker

Pour déployer vos propres modifications sur le service Device Simulation, vous devez modifier les scripts de création et de déploiement dans le dossier **scripts\docker** pour charger les conteneurs dans votre compte Docker Hub.

### <a name="modify-the-docker-scripts"></a>Modifier les scripts Docker

Modifiez les scripts Docker **build.cmd**, **publish.cmd** et **run.cmd** dans le dossier **scripts\docker** en ajoutant les informations de votre référentiel Hub Docker. Ces étapes supposent que vous avez créé un référentiel public appelé **device-simulation** :

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

Modifiez le fichier **docker-compose.yml** comme suit :

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>Configurer la solution pour inclure les nouveaux fichiers

Si vous avez ajouté de nouveaux fichiers de modèle d’appareil, vous devez explicitement les inclure dans la solution. Pour chaque fichiers à inclure, ajoutez une entrée dans le fichier **services\services.csproj**. Par exemple, si vous avez terminé le guide pratique [Sérialiser les données de télémétrie avec Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), ajoutez les entrées suivantes :

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>Générer de nouvelles images Docker et les envoyer à Docker Hub

Publiez la nouvelle image Docker dans Docker Hub avec votre compte Docker Hub :

1. Ouvrez une invite de commandes et accédez à votre copie locale du référentiel Device Simulation.

1. Accédez au dossier **docker** :

    ```cmd
    cd scripts\docker
    ```

1. Pour générer l’image Docker, exécutez la commande suivante :

    ```cmd
    build.cmd
    ```

1. Exécutez la commande suivante pour publier l’image Docker dans votre référentiel Docker Hub. Connectez-vous à Docker avec vos informations d’identification Docker Hub :

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>Mettre à jour le service

Pour mettre à jour le conteneur Device Simulation pour qu’il utilise votre image personnalisée, procédez comme suit :

* Utilisez SSH pour vous connecter à la machine virtuelle qui héberge votre instance Device Simulation. Utilisez l’adresse IP et le mot de passe que vous avez notés dans la section précédente :

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* Accédez au répertoire **/app** :

    ```sh
    cd /app
    ```

* Modifiez le fichier **docker-compose.yml** :

    ```sh
    sudo nano docker-compose.yml
    ```

    Modifiez l’**image** pour qu’elle pointe sur l’image personnalisée **device-simulation** que vous avez chargée dans votre référentiel Docker Hub :

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    Enregistrez vos modifications.

* Exécutez la commande suivante pour redémarrer les microservices :

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>Exécuter votre simulation

Vous pouvez à présent exécuter une simulation avec votre solution Device Simulation personnalisée :

1. Lancez votre interface utilisateur web Simulation d’appareil à partir de [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilisez l’interface utilisateur web pour configurer et exécuter une simulation. Si vous avez précédemment effectué [Sérialiser les données de télémétrie avec Protocol Buffers](iot-accelerators-device-simulation-protobuf.md), vous pouvez utiliser votre modèle d’appareil personnalisé.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez appris comment déployer une image personnalisée de Device Simulation, vous souhaitez peut-être savoir comment [Utiliser un hub IoT existant avec l’accélérateur de solution Device Simulation](iot-accelerators-device-simulation-choose-hub.md).