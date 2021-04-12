---
title: Configurer Azure IoT Hub pour déployer des mises à jour OTA
description: Découvrir comment configurer Azure IoT Hub pour déployer des mises à jour OTA sur le Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064118"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Comment configurer Azure IoT Hub pour déployer des mises à jour OTA sur votre DK Azure Percept

Utilisez des mises à jour OTA (over-the-air) pour garder votre DK Azure Percept sécurisé et à jour. En quelques étapes simples, vous allez configurer votre environnement Azure avec Device Update pour IoT Hub et déployer les dernières mises à jour sur votre DK Azure Percept.

## <a name="prerequisites"></a>Prérequis

- DK (devkit) Azure Percept
- [Abonnement Azure](https://azure.microsoft.com/free/)
- [Expérience de configuration d’Azure Percept DK](./quickstart-percept-dk-set-up.md) : vous avez connecté votre kit de développement à un réseau Wi-Fi, créé un IoT Hub et connecté votre kit de développement à celui-ci.

## <a name="create-a-device-update-account"></a>Créer un compte Device Update

1. Accédez au [portail Azure](https://portal.azure.com) et connectez-vous avec le compte Azure que vous utilisez avec Azure Percept.

1. Dans la barre de recherche en haut de la page, entrez **Device Update pour IoT Hub**.

1. Sélectionnez **Device Update pour IoT Hub** quand cela apparaît dans la barre de recherche.

1. Cliquez sur le bouton **+Ajouter** dans la partie supérieure gauche de la page.

1. Sélectionnez l’**Abonnement Azure** et le **Groupe de ressources** associés à votre appareil Azure Percept et à son IoT Hub.

1. Spécifiez un **Nom** et un **Emplacement** pour votre compte Device Update.

1. Révisez les détails, puis sélectionnez **Vérifier + créer**.

1. Une fois le déploiement terminé, cliquez sur **Accéder à la ressource**.

## <a name="create-a-device-update-instance"></a>Créer une instance Device Update

1. Dans votre ressource Device Update pour IoT Hub, cliquez sur **Instances** sous **Gestion des instances**.

1. Cliquez sur **+ Créer**, spécifiez un nom d’instance, puis sélectionnez l’IoT Hub associé à votre appareil Azure Percept. L’exécution de cette opération nécessite quelques minutes.

1. Cliquez sur **Créer**.

## <a name="configure-iot-hub"></a>Configurer IoT Hub

1. Dans la page **Instances** de Gestion des instances, attendez que votre instance Device Update passe à l’état **Réussi**. Cliquez sur l’icône **Actualiser** pour mettre à jour l’état.

1. Sélectionnez l’instance créée pour vous, puis cliquez sur **Configurer IoT Hub**. Dans le volet gauche, sélectionnez **J’accepte d’apporter ces modifications**, puis cliquez sur **Mettre à jour**.

1. Attendez que le processus se termine.

## <a name="configure-access-control-roles"></a>Configurer les rôles de contrôle d’accès

La dernière étape vous permet d’autoriser des utilisateurs à publier et à déployer des mises à jour.

1. Dans votre ressource Device Update pour IoT Hub, cliquez sur **Contrôle d’accès (IAM)** .

1. Sélectionnez **+Ajouter**, puis **Ajouter une attribution de rôle**.

1. Pour **Rôle**, sélectionnez **Administrateur Device Update**. Pour **Attribuer l’accès à**, sélectionnez **Utilisateur, groupe ou principal du service**. Pour **Sélectionner**, sélectionnez votre compte ou le compte de la personne qui va déployer les mises à jour. Cliquez sur **Enregistrer**.

> [!TIP]
> Si vous souhaitez accorder l’accès à d’autres personnes de votre organisation, vous pouvez répéter cette étape et faire de chacun de ces utilisateurs un **Administrateur Device Update**.

## <a name="next-steps"></a>Étapes suivantes

Vous êtes à présent prêt à [effectuer une mise à jour OTA de votre kit de développement Azure Percept](./how-to-update-over-the-air.md) avec Device Update pour IoT Hub.