---
title: 'Tutoriel : centre de distribution numérique Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser un modèle d’application du centre de distribution numérique IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 71d16e08501a345fcc6ae8435f0609660be9a18a
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195248"
---
# <a name="tutorial-deploy-and-walk-through-the-digital-distribution-center-application-template"></a>Tutoriel : déployer et parcourir un modèle d’application du centre de distribution numérique

Utilisez le modèle d’application du *centre de distribution numérique* IOT Central et les conseils de cet article pour développer une solution de centre de distribution numérique de bout en bout.

   :::image type="content" source="media/tutorial-iot-central-ddc/digital-distribution-center-architecture.png" alt-text="centre de distribution numérique.":::

1. Ensemble de capteurs IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Les données sont acheminées vers le service Azure souhaité pour manipulation
4. Les services Azure que sont ASA ou Azure Functions permettent de reformater les flux de données et de les envoyer aux comptes de stockage souhaités
5. Les données traitées sont stockées dans un stockage à chaud pour des actions en quasi temps réel ou dans un stockage à froid pour des améliorations d’insights supplémentaires basées sur l’analyse par lots ou par ML. 
6. Logic Apps peut être utilisé pour alimenter divers flux de travail d’entreprise dans les applications d’entreprise pour utilisateurs finaux

### <a name="video-cameras"></a>Caméras vidéo 

Les caméras vidéo sont les capteurs principaux de cet écosystème à l’échelle de l’entreprise connecté numériquement. Les avancées en matière de Machine Learning et d’intelligence artificielle permettent aux vidéos d’être transformées en données structurées et de les traiter à la périphérie avant de les envoyer au cloud. Nous pouvons utiliser des caméras IP pour capturer des images, les compresser sur l’appareil, puis envoyer les données compressées sur le calcul en périphérie pour le pipeline d’analyse vidéo, ou utiliser des caméras de vision GigE pour capturer des images sur le capteur, puis envoyer ces images directement à Azure IoT Edge , qui compresse ensuite la vidéo avant de la traiter dans le pipeline d’analyse vidéo. 

### <a name="azure-iot-edge-gateway"></a>Passerelle Azure IoT Edge

Les « caméras en tant que capteurs » et les charges de travail de périphérie sont gérées localement par Azure IoT Edge, et le flux de caméras est traité par le pipeline analytique. Le pipeline de traitement analytique de vidéos sur Azure IoT Edge apporte de nombreux avantages, notamment un temps de réponse réduit et une faible consommation de bande passante, ce qui se traduit par une faible latence pour un traitement rapide des données. Seules les métadonnées, les informations ou les actions les plus essentielles sont envoyées au cloud à des fins d’action ou d’investigation ultérieures. 

### <a name="device-management-with-iot-central"></a>Gestion des appareils avec IoT Central
 
Azure IoT Central est une plateforme de développement de solutions qui simplifie la connectivité, la configuration et la gestion des appareils IoT et de la passerelle Azure IoT Edge. La plateforme réduit considérablement la charge et les coûts liés à la gestion des appareils IoT, aux opérations et aux développements connexes. Les clients et partenaires peuvent créer des solutions d’entreprise de bout en bout pour obtenir une boucle de retour numérique dans les centres de distribution.

### <a name="business-insights-and-actions-using-data-egress"></a>Insights métier et actions utilisant la sortie de données 

La plateforme IoT Central offre des options d’extensibilité riches via l’exportation continue des données (CDE) et des API. Les insights métier basés sur le traitement des données de télémétrie ou la télémétrie brute sont généralement exportés vers une application métier choisie. Cela peut être réalisé par webhook, Service Bus, hub d’événements ou stockage Blob pour générer, entraîner et déployer des modèles Machine Learning et enrichir les insights.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer une application pour un centre de distribution numérique.
> * Examiner l’application.

## <a name="prerequisites"></a>Prérequis

* Aucune configuration requise spécifique n’est requise pour déployer cette application
* Il est recommandé d’avoir un abonnement Azure, mais vous pouvez essayer sans

## <a name="create-digital-distribution-center-application-template"></a>Créer un modèle d’application pour un centre de distribution numérique

Créez une application à l’aide de la procédure suivante :

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Distribution** :

   :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-home-page.png" alt-text="Capture d’écran montrant comment créer une application.":::

1. Sélectionnez **Créer une application** sous le **centre de distribution numérique**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application 

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord

Le tableau de bord par défaut est un portail centré sur l’opérateur du centre de distribution. Northwind Trader est un fournisseur de solutions de centres de distribution fictifs qui gère les systèmes de transport. 

Dans ce tableau de bord, vous verrez une passerelle et une caméra faisant office d’appareil IoT. La passerelle fournit des données de télémétrie sur les packages tels que valide, non valide, non identifié et taille, ainsi que les propriétés de jumeaux d’appareil associées. Toutes les commandes en aval sont exécutées sur des appareils IoT, tels qu’une caméra. Ce tableau de bord est préconfiguré pour présenter l’activité des opérations de l’appareil du centre de distribution critique.

Le tableau de bord est structuré de manière logique pour afficher les fonctionnalités de gestion des périphériques de la passerelle Azure IoT et de l’appareil IoT.  

* Vous pouvez exécuter des tâches de commande et de contrôle de la passerelle
* Gérez toutes les caméras qui font partie de la solution.
* Gérez toutes les caméras qui font partie de la solution.
* Gérez toutes les caméras qui font partie de la solution.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-dashboard.png" alt-text="Capture d’écran montrant le tableau de bord du centre de distribution numérique.":::

### <a name="device-template"></a>Modèle d'appareil

Cliquez sur l’onglet Modèles d’appareils pour voir le modèle de capacité de la passerelle. Un modèle de fonctionnalités est structuré autour de deux interfaces différentes **Caméra** et **Passerelle de distribution numérique**

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Capture d’écran montrant le modèle d’appareil de la passerelle de distribution numérique dans l’application.":::

**Caméra** : cette interface organise toutes les fonctionnalités de commande propres à la caméra 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Capture d’écran montrant l’interface de l’appareil photo dans le modèle d’appareil de la passerelle de distribution numérique.":::

**Passerelle de distribution numérique** : cette interface représente toutes les données de télémétrie provenant de la caméra, les propriétés du jumeau d'appareil définies dans le cloud et les informations sur la passerelle.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-devicetemplate1.png" alt-text="Capture d’écran montrant l’interface de la passerelle de distribution numérique dans le modèle d’appareil de la passerelle de distribution numérique.":::

### <a name="gateway-commands"></a>Commandes de la passerelle

Cette interface organise toutes les fonctionnalités de commande de la passerelle

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-camera.png" alt-text="Capture d’écran montrant l’interface des commandes de la passerelle dans le modèle d’appareil de la passerelle de distribution numérique.":::

### <a name="rules"></a>Règles

Sélectionnez l’onglet Règles pour afficher deux règles différentes qui existent dans ce modèle d’application. Ces règles sont configurées pour envoyer des notifications par e-mail aux opérateurs pour des investigations supplémentaires.

 **Un trop grand nombre d’alertes de packages non valides** : cette règle est déclenchée lorsque la caméra détecte un nombre élevé de packages non valides circulant dans le système de transport.

**Packages volumineux** : cette règle est déclenchée si la caméra détecte un paquet volumineux qui ne peut pas être inspecté pour la qualité. 

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-rules.png" alt-text="Capture d’écran montrant la liste des règles dans l’application du centre de distribution numérique.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette application, supprimez le modèle d’application en accédant à **Administration** > **Paramètres de l’application**, puis cliquez sur **supprimer**.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-cleanup.png" alt-text="Capture d’écran montrant comment supprimer l’application lorsque vous n’en avez plus besoin.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’architecture de la solution du centre de distribution numérique :

> [!div class="nextstepaction"]
> [Concept du centre de distribution numérique](./architecture-digital-distribution-center.md)
