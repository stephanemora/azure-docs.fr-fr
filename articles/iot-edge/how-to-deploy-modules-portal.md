---
title: Déployer des modules à partir du Portail Azure – Azure IoT Edge | Microsoft Docs
description: Utiliser le portail Azure pour déployer des modules sur un appareil IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: fab85b74fb53bec07843abf31aac1a8495d0fcfb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53093979"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Déployer des modules Azure IoT Edge à partir du portail Azure

Une fois que vous avez créé des modules IoT Edge avec votre logique métier, vous pouvez les déployer sur vos appareils afin qu’ils opèrent à la périphérie. Si plusieurs modules fonctionnent ensemble pour collecter et traiter les données, vous pouvez les déployer tous à la fois et déclarer les règles de routage qui les connectent. 

Cet article explique comment le portail Azure vous aide à créer un manifeste de déploiement et à étendre le déploiement à un appareil IoT Edge. Pour plus d’informations sur la création d’un déploiement ciblant plusieurs appareils en fonction de leurs balises partagées, consultez [Déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure. 
* Un [appareil IoT Edge](how-to-register-device-portal.md) avec le runtime IoT Edge installé. 

## <a name="select-your-device"></a>Sélectionner votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
2. Sélectionnez **IoT Edge** dans le menu.
3. Cliquez sur l’ID de l’appareil cible dans la liste des appareils. 
4. Sélectionnez **Définir modules**.

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

Le portail Azure comprend un Assistant qui vous guide à travers la création du manifeste de déploiement, vous évitant de générer le document JSON manuellement. Il comporte trois étapes : **Ajouter des modules**, **Spécifier des routes** et **Vérifier le déploiement**. 

### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Paramètres de Registre** de la page, fournissez les informations d’identification permettant d’accéder à n’importe quels registres de conteneurs privés qui contiennent des images de module. 
2. Dans la section **Modules de déploiement** de la page, sélectionnez **Ajouter**. 
3. Examinez les types de modules dans la liste déroulante : 
   * **Module IoT Edge** : option par défaut.
   * **Module Azure Stream Analytics** : uniquement les modules générés à partir d’une charge de travail Azure Stream Analytics. 
4. Sélectionnez le **Module IoT Edge**.
5. Fournissez un nom pour le module, puis spécifiez l’image conteneur. Par exemple :  
   * **Nom** : tempSensor
   * **URI d’image** : mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
6. Renseignez les champs facultatifs si nécessaire. Pour plus d’informations sur les options de création de conteneur, la stratégie de redémarrage et l’état souhaité, consultez [Propriétés souhaitées pour EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Pour plus d’informations sur le jumeau de module, consultez [Définir ou mettre à jour les propriétés souhaitées](module-composition.md#define-or-update-desired-properties).
7. Sélectionnez **Enregistrer**.
8. Répétez les étapes 2 à 6 pour ajouter des modules à votre déploiement. 
9. Sélectionnez **Suivant** pour passer à la section des itinéraires.

### <a name="specify-routes"></a>Spécifier des routes

Par défaut, l’Assistant vous donne un itinéraire nommé **route** et défini sous la forme **FROM /\* INTO $upstream**, ce qui signifie que tous les messages issus des modules sont envoyés à votre hub IoT.  

Ajoutez ou mettez à jour les itinéraires avec des informations issues de [Déclarer des itinéraires](module-composition.md#declare-routes), puis sélectionnez **Suivant** pour passer à la section de vérification.

### <a name="review-deployment"></a>Vérifier le déploiement

La section de vérification vous montre le manifeste de déploiement JSON qui a été créé en fonction de vos sélections dans les deux sections précédentes. Notez qu’il existe deux modules déclarés que vous n’avez pas ajoutés : **$edgeAgent** et **$edgeHub**. Ces deux modules composent le [runtime IoT Edge](iot-edge-runtime.md) et sont des valeurs par défaut requises dans chaque déploiement. 

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**. 

## <a name="view-modules-on-your-device"></a>Afficher les modules sur votre appareil

Une fois les modules déployés sur votre appareil, vous pouvez les voir tous dans la page **Détails de l’appareil** du portail. Cette page affiche le nom de chaque module déployé, ainsi que des informations utiles telles que le code de sortie et l’état du déploiement. 

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
