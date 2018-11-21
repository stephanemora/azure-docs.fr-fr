---
title: Inscrire un nouvel appareil Azure IoT Edge (portail) | Microsoft Docs
description: Utilisez le portail Azure pour inscrire un nouvel appareil IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6657203c76bc03a262fbcbd30b5bf74b5be140eb
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/13/2018
ms.locfileid: "51577496"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Inscrire un nouvel appareil Azure IoT Edge à partir du portail Azure

Avant d’utiliser vos appareils IoT avec Azure IoT Edge, vous devez les inscrire auprès de votre hub IoT. Une fois que vous avez inscrit un appareil, vous recevez une chaîne de connexion qui vous permet de le configurer pour les charges de travail Edge. 

Cet article explique comment inscrire un nouvel appareil IoT Edge à partir du portail Azure.

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure. 

## <a name="create-a-device"></a>Créer un appareil

Dans le portail Azure, les appareils IoT Edge sont créés et gérés séparément des appareils qui se connectent à votre hub IoT, mais qui ne sont pas compatibles avec Edge. 

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub. 
2. Sélectionnez **IoT Edge** dans le menu.
3. Sélectionnez **Ajouter un appareil IoT Edge**. 
4. Indiquez un ID d’appareil descriptif. 
5. Sélectionnez **Enregistrer**. 

## <a name="view-all-devices"></a>Voir tous les appareils

Tous les appareils compatibles avec Edge qui se connectent à votre hub IoT sont répertoriés dans la page **IoT Edge**. 

## <a name="retrieve-the-connection-string"></a>Récupérer la chaîne de connexion

Pour configurer votre appareil, vous avez besoin de la chaîne de connexion qui établit un lien entre votre appareil physique et son identité dans le hub IoT.

1. Dans la page **IoT Edge** du portail, cliquez sur l’ID de l’appareil dans la liste des appareils Edge. 
2. Copiez la valeur de **Chaîne de connexion (clé primaire)** ou **Chaîne de connexion (clé secondaire)**. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer des modules sur un appareil à partir du portail Azure](how-to-deploy-modules-portal.md).
