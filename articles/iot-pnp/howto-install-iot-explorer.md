---
title: Installer et utiliser l’explorateur Azure IoT | Microsoft Docs
description: Installer l’outil d’explorateur Azure IoT et l’utiliser pour interagir avec les appareils IoT Plug-and-Play en préversion connectés à mon hub IoT.
author: miagdp
ms.author: miag
ms.date: 07/02/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: c7d8b946fc587d58f13e16c1d7d806390e712104
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878921"
---
# <a name="install-and-use-azure-iot-explorer"></a>Installer et utiliser l’explorateur Azure IoT

L’explorateur Azure IoT est un outil graphique permettant d’interagir avec vos appareils IoT Plug-and-Play en préversion et de les tester. Après avoir installé l’outil sur votre ordinateur local, vous pouvez l’utiliser pour vous connecter à un appareil. Vous pouvez utiliser l’outil pour afficher les données de télémétrie que l’appareil envoie, utiliser les propriétés de l’appareil et appeler des commandes.

Cet article vous montre comment procéder.

- Installer et configurer l’explorateur Azure IoT.
- Utiliser l’outil pour interagir avec vos appareils et les tester.

## <a name="prerequisites"></a>Prérequis

Pour utiliser l’outil qu’est l’explorateur Azure IoT, vous avez besoin des éléments suivants :

- Un IoT Hub Azure. Il y a de nombreuses façons d’ajouter un hub IOT à votre abonnement Azure, par exemple la [Création d’un hub IOT à l’aide de l’interface de ligne de commande Azure](../iot-hub/iot-hub-create-using-cli.md). Vous avez besoin de la chaîne de connexion du hub IoT pour exécuter l’outil explorateur Azure IoT. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.
- Un appareil inscrit dans votre hub IoT. Vous pouvez utiliser la commande d’interface de ligne de commande Azure suivante pour inscrire un appareil. Veillez à remplacer les espaces réservés `{YourIoTHubName}` et `{YourDeviceID}` par vos valeurs :

    ```azurecli-interactive
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

## <a name="install-azure-iot-explorer"></a>Installez l’explorateur Azure IoT

Accédez aux [versions de l’explorateur Azure IOT](https://github.com/Azure/azure-iot-explorer/releases) et développez la liste des ressources pour la version la plus récente. Téléchargez et installez la version la plus récente de l’application.

## <a name="use-azure-iot-explorer"></a>Utilisez l’explorateur Azure IoT

Concernant l’appareil, vous pouvez connecter votre propre appareil ou utiliser l’un de nos exemples d’appareils simulés. Suivez [ces instructions](https://github.com/Azure/azure-iot-sdk-c/tree/public-preview/iothub_client/samples) pour exécuter l’exemple d’appareil simulé.

### <a name="connect-to-your-hub"></a>Connectez-vous à votre hub

La première fois que vous exécutez l’explorateur Azure IoT, vous êtes invité à entrer la chaîne de connexion de votre hub IoT. Après avoir ajouté la chaîne de connexion, sélectionnez **Connexion**. Vous pouvez utiliser les paramètres de l’outil pour basculer vers un autre hub IoT en mettant à jour la chaîne de connexion.

La définition de modèle pour un appareil Plug-and-Play IoT est stockée dans le référentiel public, dans un référentiel de société ou sur votre appareil connecté. Par défaut, l’outil recherche la définition de votre modèle dans le référentiel de modèles public et dans votre appareil connecté. Vous pouvez ajouter et supprimer des sources, ou configurer la priorité des sources dans **Paramètres** :

Pour ajouter une source :

1. Accédez à **Settings**.
1. Sélectionnez **Nouveau** et choisissez votre source.
1. Si vous ajoutez le référentiel de modèles de votre entreprise, indiquez la chaîne de connexion.

Pour supprimer une source :

1. Accédez à **Settings**.
1. Recherchez la source que vous souhaitez supprimer.
1. Sélectionnez **X** pour la supprimer. Vous ne pouvez pas supprimer le référentiel de modèles public, car les définitions d’interface communes proviennent de ce référentiel.

Modifiez les priorités sources :

Vous pouvez faire glisser et déposer l’une des sources de définition de modèle dans une position différente sur la liste. En cas de conflit, les sources de définition avec des classements supérieurs remplacent les sources avec des classements moins élevés.

### <a name="overview-page"></a>Page Vue d’ensemble

#### <a name="device-overview"></a>Vue d'ensemble de l’appareil

Une fois que l’outil se connecte à votre hub IoT, une page de vue d’ensemble qui répertorie toutes les identités d’appareils inscrits auprès de votre hub Azure IoT s’affiche. Sélectionnez un appareil pour afficher plus de détails.

#### <a name="device-management"></a>Gestion des appareils

- Pour inscrire un nouvel appareil à votre hub, sélectionnez **Ajouter**. Entrez un ID d'appareil. Utilisez les paramètres par défaut pour générer automatiquement les clés d’authentification et activez la connexion à votre hub.
- Pour supprimer une identité d’appareil, sélectionnez **Supprimer**. Passez en revue les détails de l’appareil avant d’effectuer cette action pour vous assurer que vous supprimez l’identité du bon appareil.
- L’outil prend en charge l'interrogation par `capabilityID` et `interfaceID`. Ajoutez votre `capabilityID` ou `interfaceID` en tant que paramètre pour interroger vos appareils.

## <a name="interact-with-a-device"></a>Interagir avec un appareil

Double-cliquez sur un appareil dans la page vue d’ensemble pour afficher le niveau de détails suivant. Il y a deux sections : **Appareil** et **Jumeau numérique.**

### <a name="device"></a>Appareil

Cette section inclut les onglets **Identité d'appareil**, **Données de télémétrie** et **Jumeau d'appareil**.

- Vous pouvez afficher et mettre à jour les informations d’identité de l'appareil sous l’onglet **Identité d'appareil**.
- Si un appareil est connecté et envoie activement des données, vous pouvez afficher les données de télémétrie dans l'onglet **Données de télémétrie**.
- Vous pouvez accéder aux informations sur le jumeau d'appareil dans l’onglet **Jumeau d'appareil**.

### <a name="digital-twin"></a>Jumeau numérique

Vous pouvez utiliser l’outil pour afficher une instance de jumeau numérique de l’appareil. Pour un appareil IoT Plug-and-Play, toutes les interfaces associées au modèle de capacité de l’appareil sont affichées dans cet article. Sélectionnez une interface pour développer ses primitives [IoT Plug-and-Play](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) correspondantes.

#### <a name="properties"></a>properties

Vous pouvez afficher les propriétés en lecture seule définies dans une interface sur la page **Propriétés**. Vous pouvez mettre à jour les propriétés inscriptibles définies dans une interface sur la page **Propriétés inscriptibles**.

1. Accédez à la page **Propriétés inscriptibles**.
1. Cliquez sur la propriété que vous souhaitez mettre à jour.
1. Entrez la nouvelle valeur de la propriété.
1. Affichez un aperçu de la charge utile à envoyer à l’appareil.
1. Envoyer la modification.

Après avoir envoyé une modification, vous pouvez suivre l’état de la mise à jour : **synchronisation**, **succès** ou **erreur**. Une fois la synchronisation terminée, la nouvelle valeur de votre propriété s’affiche dans la colonne **Propriété rapportée**. Si vous accédez à d’autres pages avant la fin de la synchronisation, l’outil vous avertit lorsque la mise à jour est terminée. Vous pouvez également utiliser le centre de notifications de l’outil pour afficher l’historique des notifications.

#### <a name="commands"></a>Commandes

Pour envoyer une commande à un appareil, accédez à la page **Commandes** :

1. Dans la liste des commandes, développez la commande que vous souhaitez déclencher.
1. Entrez les valeurs requises pour la commande.
1. Affichez un aperçu de la charge utile à envoyer à l’appareil.
1. Envoyez la commande.

#### <a name="telemetry"></a>Télémétrie

Pour afficher les données de télémétrie de l’interface sélectionnée, accédez à la page **Données de télémétrie**.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article de procédure, vous avez appris à installer et à utiliser l’explorateur Azure IoT pour interagir avec vos appareils IoT Plug-and-Play. L’étape suivante suggérée consiste à apprendre à [Gérer des modèles](./howto-manage-models.md).
