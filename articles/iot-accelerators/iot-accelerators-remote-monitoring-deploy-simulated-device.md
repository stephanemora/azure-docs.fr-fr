---
title: Déploiement IoT d’appareils simulés personnalisés - Azure | Microsoft Docs
description: Ce guide montre comment déployer des appareils simulés personnalisés sur l’accélérateur de solution de supervision à distance.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/15/2018
ms.topic: conceptual
ms.openlocfilehash: 7cbab38db859935c9f4490d79a131d6c9a7e302b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "66427572"
---
# <a name="deploy-a-new-simulated-device"></a>Déployer un nouvel appareil simulé

Les accélérateurs de solution Surveillance à distance et Simulation d’appareil vous permettent tous deux de définir vos propres appareils simulés. Cet article montre comment déployer un type d’appareil personnalisé Réfrigérateur et un nouveau type d’appareil Ampoule sur l’accélérateur de solution de supervision à distance.

Les étapes décrites dans cet article supposent que vous avez suivi les procédures décrites dans le guide [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md) et disposez des fichiers qui définissent le type d’appareil personnalisé Réfrigérateur et le nouveau type d’appareil Ampoule.

Les étapes décrites dans ce guide montrent comment :

1. Utiliser SSH pour accéder au système de fichiers de la machine virtuelle hébergeant l’accélérateur de solution de supervision à distance.

1. Configurer Docker pour charger les modèles d’appareils à partir d’un emplacement extérieur au conteneur Docker.

1. Exécuter l’accélérateur de solution de supervision à distance à l’aide de fichiers de modèles d’appareils personnalisés.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pour effectuer les étapes décrites dans ce guide pratique, vous avez besoin d’un abonnement Azure actif.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Conditions préalables requises

Pour effectuer les procédures de ce guide, vous avez besoin des éléments suivants :

- Une instance déployée de l’[accélérateur de solution de supervision à distance](https://www.azureiotsolutions.com/Accelerators#solutions/types/RM2).
- Un interpréteur de commandes **bash** local pour exécuter les commandes `ssh` et `scp`. Sur Windows, une manière simple d’installer **bash** consiste à installer [git](https://git-scm.com/download/win).
- Vos fichiers de modèles d’appareils personnalisés, tels que ceux décrits dans [Créer et tester un appareil simulé](iot-accelerators-remote-monitoring-create-simulated-device.md).

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="configure-docker"></a>Configurer Docker

Dans cette section, vous allez configurer Docker pour charger les fichiers de modèles d’appareils dans la machine virtuelle à partir du dossier **/tmp/devicemodels** plutôt qu’à partir de l’intérieur du conteneur Docker. Exécutez les commandes spécifiées dans cette section dans un interpréteur de commandes **bash** sur votre ordinateur local :

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
    devicesimulation:
      image: azureiotpcs/device-simulation-dotnet:1.0.0
      networks:
        - default_net
      depends_on:
        - storageadapter
      environment:
        - PCS_KEYVAULT_NAME
        - PCS_AAD_APPID
        - PCS_AAD_APPSECRET
      # How one could mount custom device models
      volumes:
        - /tmp/devicemodels:/app/webservice/data/devicemodels:ro
    ```

    Enregistrez les modifications.

1. Copiez les fichiers de modèles d’appareils existants du conteneur vers le nouvel emplacement. Commencez par chercher l’ID de conteneur pour le conteneur de simulation d’appareil :

    ```sh
    sudo docker ps
    ```

    Copiez ensuite les fichiers de modèles d’appareils vers le dossier **tmp** dans la machine virtuelle. La commande suivante suppose que l’ID de conteneur est c378d6878407. Remplacez cette valeur par votre ID de conteneur de simulation d’appareil :

    ```sh
    sudo docker cp c378d6878407:/app/webservice/data/devicemodels /tmp
    sudo chown -R azureuser /tmp/devicemodels/
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
    sudo docker ps
    ```

    Si vous voulez voir le journal du conteneur de simulation d’appareil, exécutez la commande suivante. Remplacez l’ID de conteneur par l’ID de votre conteneur de simulation d’appareil :

    ```sh
    sudo docker logs -f 5d3f3e78822e
    ```

## <a name="run-simulation"></a>Exécuter une simulation

Vous pouvez désormais utiliser vos modèles d’appareils personnalisés dans la solution de supervision à distance :

1. Lancez votre tableau de bord Surveillance à distance à partir de [Accélérateurs de solution Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#dashboard).

1. Utilisez la page **Appareils** pour ajouter des appareils simulés. Lorsque vous ajoutez un nouvel appareil simulé, vous pouvez choisir parmi vos nouveaux modèles d’appareils.

1. Vous pouvez utiliser le tableau de bord pour afficher la télémétrie de l’appareil et appeler des méthodes de l’appareil.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous envisagez d’explorer davantage, laissez l’accélérateur de solution de supervision à distance déployé.

Si vous n’avez plus besoin de l’accélérateur de solution, supprimez-le de la page [Solutions approvisionnées](https://www.azureiotsolutions.com/Accelerators#dashboard) en le sélectionnant et en cliquant sur **Supprimer la solution**.

## <a name="next-steps"></a>Étapes suivantes

Ce guide vous a montré comment déployer des modèles d’appareils personnalisés sur l’accélérateur de solution de supervision à distance. Nous vous suggérons à présent de découvrir comment [connecter un appareil réel à votre solution de supervision à distance](iot-accelerators-connecting-devices-node.md).
