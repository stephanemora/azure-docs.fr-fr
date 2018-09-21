---
title: Créer une application Azure IoT Central | Microsoft Docs
description: Créez une nouvelle application Azure IoT Central pour gérer les appareils de distribution réfrigérés. Affichez les données de télémétrie générées à partir de vos appareils simulés.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: af06766d89804b2f3d0aaf061494fb836f6ec262
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46465603"
---
# <a name="create-an-azure-iot-central-application"></a>Créer une application Azure IoT Central

En tant que _générateur_, vous utilisez l’interface utilisateur d’Azure IoT Central pour définir votre application Microsoft Azure IoT Central. Ce démarrage rapide vous montre comment :

- Créez une application Azure IoT Central qui contient un exemple de _modèle d’appareil_ et des _appareils_ simulés.
- Affichez les fonctionnalités du modèle d’appareil **Machine de distribution réfrigérée** dans votre application.
- Affichez les données de télémétrie et l’analyse de vos appareils **Réfrigérateur** simulés.

Dans ce guide de démarrage rapide, vous affichez un appareil **Réfrigérateur** simulé à partir d’un modèle d’appareil. L’appareil simulé :

* Envoie des données de télémétrie, comme la température et la pression, à votre application.
* Rapporte des valeurs de propriété d’appareil, comme une alerte de mouvement, à votre application.
* Comprend des paramètres d’appareil, comme la vitesse du ventilateur, que vous pouvez définir dans l’application.

Lorsque vous créez un appareil simulé à partir d’un modèle d’appareil dans une application Azure IoT Central, l’appareil simulé vous permet de tester votre application avant de raccorder un appareil réel.

## <a name="create-the-application"></a>Création de l'application

Pour effectuer ce guide de démarrage rapide, vous devez créer une application Azure IoT Central à partir du modèle d’application **Sample Contoso** (Exemple Contoso).

Accédez à la page [Application Manager](https://aka.ms/iotcentral) (Gestionnaire d’applications) d’Azure IoT Central. Ensuite, entrez l’adresse e-mail et le mot de passe que vous utilisez pour accéder à votre abonnement Azure :

![Entrer votre compte d’entreprise](media/quick-deploy-iot-central/sign-in.png)

Pour commencer à créer une application Azure IoT Central, choisissez **Nouvelle application** :

![Page du Gestionnaire d’applications d’Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Pour créer une nouvelle application Azure IoT Central :

1. Choisissez le plan de paiement **Free Trial Application** (Version d’évaluation gratuite).
1. Choisissez un nom d’application convivial, tel que **Contoso IoT**. Azure IoT Central génère un préfixe d’URL unique pour vous. Vous pouvez remplacer ce préfixe d’URL par une chaîne plus facile à mémoriser.
1. Choisissez le modèle d’application **Sample Contoso** (Exemple Contoso).
1. Sélectionnez ensuite **Créer**.

![Page de création d’une application Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez créé une application Azure IoT Central prédéfinie qui contient un modèle d’appareil **Machine de distribution réfrigérée** et des appareils simulés. Consultez [Define a new device template in your application](tutorial-define-device-type.md) (Définir un nouveau modèle d’appareil dans votre application) pour en savoir plus, en tant que générateur, sur la définition de vos propres modèles d’appareil.
