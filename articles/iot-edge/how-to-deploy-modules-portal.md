---
title: Déployer des modules à partir du Portail Azure – Azure IoT Edge | Microsoft Docs
description: Utiliser le portail Azure pour déployer des modules sur un appareil IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/25/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4c2adc8ef0d426617dc85dd507907d612bbdabaa
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964916"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Déployer des modules Azure IoT Edge à partir du portail Azure

Une fois que vous avez créé des modules IoT Edge avec votre logique métier, vous pouvez les déployer sur vos appareils afin qu’ils opèrent à la périphérie. Si plusieurs modules fonctionnent ensemble pour collecter et traiter les données, vous pouvez les déployer tous à la fois et déclarer les règles de routage qui les connectent.

Cet article explique comment le portail Azure vous aide à créer un manifeste de déploiement et à étendre le déploiement à un appareil IoT Edge. Pour plus d’informations sur la création d’un déploiement ciblant plusieurs appareils en fonction de leurs balises partagées, consultez [Déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).

## <a name="prerequisites"></a>Prérequis

* Un [hub IoT](../iot-hub/iot-hub-create-through-portal.md) dans votre abonnement Azure.
* Un [appareil IoT Edge](how-to-register-device.md#register-in-the-azure-portal) avec le runtime IoT Edge installé.

## <a name="select-your-device"></a>Sélectionner votre appareil

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre IoT Hub.
1. Sélectionnez **IoT Edge** dans le menu.
1. Cliquez sur l’ID de l’appareil cible dans la liste des appareils.
1. Sélectionnez **Définir modules**.

## <a name="configure-a-deployment-manifest"></a>Configurer un manifeste de déploiement

Un manifeste de déploiement est un document JSON qui décrit les modules à déployer, le flux des données entre les modules et les propriétés souhaitées des jumeaux de module. Pour plus d’informations sur le fonctionnement et la création des manifestes de déploiement, consultez [Comprendre comment les modules IoT Edge peuvent être utilisés, configurés et réutilisés](module-composition.md).

Le portail Azure comprend un Assistant qui vous guide à travers la création du manifeste de déploiement, vous évitant de générer le document JSON manuellement. Il comporte trois étapes : **Ajouter des modules**, **Spécifier des routes** et **Vérifier le déploiement**.

### <a name="add-modules"></a>Ajouter des modules

1. Dans la section **Paramètres de Container Registry** de la page, fournissez les informations d’identification permettant d’accéder à n’importe quels registres de conteneurs privés qui contiennent des images de module.

1. Dans la section **Modules de déploiement** de la page, sélectionnez **Ajouter**.

1. Examinez les types de modules dans la liste déroulante :

   * **Module IoT Edge** : option par défaut.
   * **Module Azure Stream Analytics** : uniquement les modules générés à partir d’une charge de travail Azure Stream Analytics.
   * **Module Azure Machine Learning** : uniquement les images de modèle générées à partir d’un espace de travail Azure Machine Learning.

1. Sélectionnez le **Module IoT Edge**.

1. Fournissez un nom pour le module, puis spécifiez l’image conteneur. Par exemple :

   * **Nom** – SimulatedTemperatureSensor
   * **URI d’image** : mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. Renseignez les champs facultatifs si nécessaire. Pour plus d’informations sur les options de création de conteneur, la stratégie de redémarrage et l’état souhaité, consultez [Propriétés souhaitées pour EdgeAgent](module-edgeagent-edgehub.md#edgeagent-desired-properties). Pour plus d’informations sur le jumeau de module, consultez [Définir ou mettre à jour les propriétés souhaitées](module-composition.md#define-or-update-desired-properties).

1. Sélectionnez **Enregistrer**.

1. Répétez les étapes 2 à 6 pour ajouter des modules à votre déploiement.

1. Sélectionnez **Suivant** pour passer à la section des itinéraires.

### <a name="specify-routes"></a>Spécifier des routes

Par défaut, l’Assistant vous donne un itinéraire nommé **route** et défini sous la forme **FROM /\* INTO $upstream**, ce qui signifie que tous les messages issus des modules sont envoyés à votre hub IoT.  

Ajoutez ou mettez à jour les itinéraires avec des informations issues de [Déclarer des itinéraires](module-composition.md#declare-routes), puis sélectionnez **Suivant** pour passer à la section de vérification.

### <a name="review-deployment"></a>Vérifier le déploiement

La section de vérification vous montre le manifeste de déploiement JSON qui a été créé en fonction de vos sélections dans les deux sections précédentes. Notez qu’il existe deux modules déclarés que vous n’avez pas ajoutés : **$edgeAgent** et **$edgeHub**. Ces deux modules composent le [runtime IoT Edge](iot-edge-runtime.md) et sont des valeurs par défaut requises dans chaque déploiement.

Passez en revue les informations de votre déploiement, puis sélectionnez **Envoyer**.

## <a name="view-modules-on-your-device"></a>Afficher les modules sur votre appareil

Une fois les modules déployés sur votre appareil, vous pouvez les voir tous dans la page **Détails de l’appareil** du portail. Cette page affiche le nom de chaque module déployé, ainsi que des informations utiles telles que le code de sortie et l’état du déploiement.

## <a name="deploy-modules-from-azure-marketplace"></a>Déployer des modules à partir de la Place de marché Azure

La Place de marché Azure est un marché d’applications et de services en ligne qui vous permet de naviguer au milieu d’un large éventail d’applications et de solutions d’entreprise certifiées et optimisées pour s’exécuter sur Azure, notamment les [modules IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Vous pouvez également accéder à la Place de marché Azure via le Portail Azure sous **Créer une ressource**.

Vous pouvez installer un module IoT Edge à partir de la Place de marché Azure ou du Portail Azure :

1. Recherchez un module et entamez le processus de déploiement.

   * Portail Azure : Recherchez un module et sélectionnez **Créer**.

   * Place de marché Azure :

     1. Recherchez un module et sélectionnez **Obtenir maintenant**.
     1. Acceptez les conditions d’utilisation et la politique de confidentialité du fournisseur en sélectionnant **Continuer**.

1. Choisissez votre abonnement et l’IoT Hub auquel l’appareil cible est joint.

1. Choisissez **Déployer sur un appareil**.

1. Entrez le nom de l’appareil ou sélectionnez **Rechercher un appareil** pour naviguer entre les appareils inscrits auprès du hub.

1. Sélectionnez **Créer** pour continuer le processus standard de la configuration d’un manifeste de déploiement, y compris l’ajout d’autres modules si vous le souhaitez. Les détails du nouveau module, tels que l’URI de l’image, les options de création et les propriétés souhaitées, sont prédéfinis mais peuvent être modifiés.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment [déployer et surveiller des modules IoT Edge à grande échelle](how-to-deploy-monitor.md).
