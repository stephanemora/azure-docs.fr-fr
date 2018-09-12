---
title: Déploiement IoT d’appareils simulés personnalisés - Azure | Microsoft Docs
description: Ce guide montre comment déployer des appareils simulés personnalisés sur l’accélérateur de solution Simulation d’appareil.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/14/2018
ms.topic: conceptual
ms.openlocfilehash: e51d0330c3ed804bff8cdb06265bb8c39141733f
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2018
ms.locfileid: "43346330"
---
# <a name="deploy-a-new-simulated-device"></a>Déployer un nouvel appareil simulé

Les accélérateurs de solution Surveillance à distance et Simulation d’appareil vous permettent tous deux de définir vos propres appareils simulés. Cet article montre comment déployer un type d’appareil personnalisé Réfrigérateur et un nouveau type d’appareil Ampoule sur l’accélérateur de solution Simulation d’appareil.

Les étapes décrites dans cet article supposent que vous avez suivi les procédures décrites dans le guide [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md) et disposez des fichiers qui définissent le type d’appareil personnalisé Réfrigérateur et le nouveau type d’appareil Ampoule.

Les étapes décrites dans ce guide montrent comment :

1. Utiliser SSH pour accéder au système de fichiers de la machine virtuelle hébergeant l’accélérateur de solution Simulation d’appareil.

1. Configurer Docker pour charger les modèles d’appareils à partir d’un emplacement extérieur au conteneur Docker.

1. Exécuter une simulation en utilisant les fichiers du modèle d’appareil personnalisé.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pour effectuer les étapes décrites dans ce guide, vous devez disposer d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables

Pour suivre ce guide, vous devez disposer des éléments suivants :

- Une instance déployée de l’[accélérateur de solution Simulation d’appareil](https://www.azureiotsolutions.com/Accelerators#solutions/types/DS).
- Un interpréteur de commandes **bash** local pour exécuter les commandes `ssh` et `scp`. Sur Windows, une manière simple d’installer **bash** consiste à installer [git](https://git-scm.com/download/win).
- Vos fichiers de modèles d’appareils personnalisés, tels que ceux décrits dans [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurer Docker

Dans cette section, vous allez configurer Docker pour charger les fichiers de modèles d’appareils dans la machine virtuelle à partir du dossier **/tmp/devicemodels** plutôt qu’à partir de l’intérieur du conteneur Docker. Exécutez les commandes spécifiées dans cette section dans un interpréteur de commandes **bash** sur votre ordinateur local :

1. Utilisez SSH pour vous connecter à la machine virtuelle dans Azure à partir de votre machine locale. La commande suivante suppose que l’adresse IP publique de la machine virtuelle **vm-vikxv** est **104.41.128.108**. Remplacez cette valeur par l’adresse IP publique de votre machine virtuelle extraite de la section précédente :

   ```sh
    ssh azureuser@104.41.128.108
    ```

    Suivez les invites pour vous connecter à la machine virtuelle avec le mot de passe que vous définissez dans la section précédente.

1. Configurez le service de simulation d’appareil pour charger les modèles d’appareils à partir de l’extérieur du conteneur. Commencez par ouvrir le fichier de configuration de Docker :

    ```sh
    sudo nano /app/docker-compose.yml
    ```

    Localisez les paramètres pour le conteneur **devicesimulation**, et modifiez le paramètre **volumes** comme dans l’extrait de code suivant :

    ```yml
    # To mount custom device models, upload the files to the VM, edit and uncomment the following line:
          - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    # To mount a custom service configuration, create a custom file, edit and uncomment the following line:
    #     - /app/custom-device-simulation-appsettings.ini:/app/webservice/appsettings.ini:ro
    ```

    Enregistrez les modifications.

1. Créez les dossiers pour stocker les fichiers JSON et de script :

    ```sh
    sudo mkdir -p /tmp/devicemodels/scripts
    ```

    Gardez la fenêtre **bash** avec votre session SSH ouverte.

1. Copiez vos fichiers de modèles d’appareils personnalisés dans la machine virtuelle. Exécutez la commande suivante dans un autre interpréteur de commandes **bash** sur l’ordinateur que vous avez utilisé pour créer vos modèles d’appareils personnalisés. Commencez par accéder au dossier local contenant les fichiers JSON de votre modèle d’appareil. Les commandes suivantes supposent que l’adresse IP publique de la machine virtuelle est **104.41.128.108**. Remplacez cette valeur par l’adresse IP publique de votre machine virtuelle. Entrez le mot de passe de votre machine virtuelle quand vous y êtes invité :

    ```sh
    scp *json azureuser@104.41.128.108:/tmp/devicemodels
    cd scripts
    scp *js azureuser@104.41.128.108:/tmp/devicemodels/scripts
    ```

1. Redémarrez le conteneur Docker de simulation d’appareil pour utiliser les nouveaux modèles d’appareils. Exécutez les commandes suivantes dans l’interpréteur de commandes **bash** avec la session SSH ouverte sur la machine virtuelle :

    ```sh
    sudo /app/start.sh
    ```

    Si vous voulez afficher l’état et l’ID des conteneurs Docker en cours d’exécution, utilisez la commande suivante :

    ```sh
    docker ps
    ```

    Si vous voulez voir le journal du conteneur de simulation d’appareil, exécutez la commande suivante. Remplacez l’ID de conteneur par l’ID de votre conteneur de simulation d’appareil :

    ```sh
    docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Exécuter une simulation

Vous pouvez à présent exécuter une simulation en utilisant vos modèles d’appareils personnalisés :

1. Lancez votre interface utilisateur web Simulation d’appareil à partir de [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilisez l’interface utilisateur web pour configurer et exécuter une simulation en utilisant l’un de vos modèles d’appareils personnalisés.

1. Lorsque vous exécutez une simulation, cliquez sur **Afficher les métriques IoT Hub dans le portail Azure** pour la surveiller. Vous pouvez également utiliser la commande Azure CLI suivante pour surveiller l’appareil dans le trafic cloud. Remplacez le nom de l’IoT Hub par le nom de votre hub :

    ```azurecli-interactive
    az iot hub monitor-events -n contoso-simulation9dc75
    ```

## <a name="clean-up-resources"></a>Supprimer les ressources

Si vous prévoyez d’explorer davantage, laissez l’accélérateur de solution Simulation d’appareil déployé.

Si vous n’avez plus besoin de l’accélérateur de solution, supprimez-le de la page [Solutions approvisionnées](https://www.azureiotsolutions.com/Accelerators#dashboard) en le sélectionnant, puis en cliquant sur **Supprimer la solution**.

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment déployer des modèles d’appareils personnalisés sur l’accélérateur de solution Simulation d’appareil. L’étape suivante suggérée consiste à en savoir plus sur le [schéma de modèle d’appareil](iot-accelerators-device-simulation-device-schema.md).
