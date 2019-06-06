---
title: Inscrire un nouvel appareil depuis le portail Azure - Azure IoT Edge | Microsoft Docs
description: Utiliser le portail Azure pour inscrire un nouvel appareil IoT Edge et récupérer la chaîne de connexion
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 16660fbed465cc70f16cde430024f33b8aa4350e
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66495360"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Inscrire un nouvel appareil Azure IoT Edge à partir du portail Azure

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous inscrivez un appareil, vous recevez une chaîne de connexion qui peut être utilisée pour configurer votre appareil pour les charges de travail IoT Edge.

Cet article explique comment inscrire un nouvel appareil IoT Edge à partir du portail Azure.

## <a name="prerequisites"></a>Conditions préalables

Gratuit pour l’édition standard [IoT hub](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.

## <a name="create-a-device"></a>Créer un appareil

Dans le portail Azure, les appareils IoT Edge sont créés et gérés séparément des appareils qui se connectent à votre hub IoT, mais qui ne sont pas compatibles avec Edge.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
2. Sélectionnez **IoT Edge** dans le menu.
3. Sélectionnez **Ajouter un appareil IoT Edge**.
4. Indiquez un ID d’appareil descriptif. Utilisez les paramètres par défaut pour générer automatiquement des clés d’authentification et connecter le nouvel appareil à votre hub.
5. Sélectionnez **Enregistrer**.

## <a name="view-all-devices"></a>Voir tous les appareils

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**.

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. À partir de la **IoT Edge** page dans le portail, cliquez sur l’ID d’appareil dans la liste des appareils IoT Edge.
2. Copiez la valeur de **Chaîne de connexion (clé primaire)** ou **Chaîne de connexion (clé secondaire)** .

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil avec le portail Azure](how-to-deploy-modules-portal.md).
