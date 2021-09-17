---
title: 'Tutoriel : centre de microtraitement Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application du centre de microtraitement Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 09/01/2021
ms.openlocfilehash: 9eb70a08679427af9e91ffd6df3cd8f827cc76cd
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123476301"
---
# <a name="tutorial-deploy-and-walk-through-the-micro-fulfillment-center-application-template"></a>Tutoriel : déployer et parcourir un modèle d’application du centre de microtraitement

Utilisez le modèle d’application du *centre de microtraitement* IOT Central et les conseils de cet article pour développer une solution de centre de microtraitement de bout en bout.

![Analytique en magasin Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/micro-fulfillment-center-architecture-frame.png)

1. Ensemble de capteurs IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Exportation continue des données à destination du service Azure souhaité à des fins de manipulation
4. Les données peuvent être structurées dans le format souhaité et envoyées à un service de stockage
5. Les applications métier peuvent interroger des données et générer des insights pour les opérations de vente au détail

### <a name="robotic-carriers"></a>Robots de transport

Une solution de centre de microtraitement dispose généralement d’un grand nombre de robots porteurs qui génèrent différentes sortes de signaux de télémétrie. Ces données peuvent être ingérées par un appareil de passerelle, puis agrégées et envoyées à IoT Central, comme vous pouvez le voir sur la gauche du diagramme d’architecture.  

### <a name="condition-monitoring-sensors"></a>Capteurs de surveillance de l’état

Une solution IoT commence par un ensemble de capteurs qui capturent des données significatives au sein de votre centre de traitement. Il est représenté par différentes sortes de capteurs à l’extrême gauche du diagramme d’architecture ci-dessus.

### <a name="gateway-devices"></a>Appareils de passerelle

De nombreux capteurs IoT peuvent alimenter directement les signaux bruts vers le cloud ou un appareil de passerelle à proximité. L’appareil de passerelle effectue l’agrégation des données en périphérie avant d’envoyer un récapitulatif des insights à une application IoT Central. Le cas échéant, les appareils de passerelle sont également chargés de relayer les opérations de commande et de contrôle vers les capteurs. 

### <a name="iot-central-application"></a>Application IoT Central

L’application Azure IoT Central ingère les données de différentes sortes de capteurs et robots IoT et d’appareils de passerelle situés dans l’environnement du centre de traitement. Ces données permettent d’obtenir des insights intéressants.

Azure IoT Central offre également une expérience personnalisée au responsable du magasin, en lui permettant de surveiller et de gérer à distance les appareils de l'infrastructure.

### <a name="data-transform"></a>Transformation des données

Au sein d’une solution, l’application Azure IoT Central peut être configurée pour exporter des insights bruts ou agrégés vers un ensemble de services Azure PaaS capables de manipuler les données et d’enrichir ces insights avant de les transférer à une application métier. 

### <a name="business-application"></a>Applications métier

Les données IoT peuvent être utilisées pour alimenter différentes sortes d’applications métier déployées dans un environnement de vente au détail. Un responsable ou un employé du centre de traitement peut utiliser ces applications pour visualiser les insights métier et prendre des mesures significatives en temps réel. Pour savoir comment créer un tableau de bord Power BI en temps réel pour votre équipe de vendeurs, suivez [ce tutoriel](./tutorial-in-store-analytics-create-app.md).

Dans ce tutoriel, vous allez voir comment :

> [!div class="checklist"]

> * Déployer le modèle d’application
> * Utiliser le modèle d’application

## <a name="prerequisites"></a>Prérequis

* Le déploiement de cette application ne nécessite aucun prérequis.
* Vous pouvez utiliser le plan tarifaire gratuit ou utiliser un abonnement Azure.

## <a name="create-micro-fulfillment-application"></a>Créer une application de microtraitement

Créez une application à l’aide de la procédure suivante :

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Générer** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Distribution** :

   :::image type="content" source="media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png" alt-text="Capture d’écran montrant comment créer une application.":::

1. Sélectionnez **Créer une application** sous **centre de microtraitement**.

## <a name="walk-through-the-application"></a>examiner l’application 

Les sections suivantes décrivent les principales fonctionnalités de l’application :

Une fois le modèle d’application déployé, le **tableau de bord du centre de microtraitement de Northwind Traders** s’affiche. Northwind Trader est un détaillant fictif dont le centre de microtraitement est géré dans cette application Azure IoT Central. Dans ce tableau de bord figurent des informations et autres données de télémétrie sur les appareils de ce modèle ainsi qu’un ensemble de commandes, de tâches et d’actions que vous pouvez effectuer. Le tableau de bord se divise logiquement en deux parties. À gauche, vous pouvez superviser les conditions ambiantes au sein de la structure de traitement et à droite, vous pouvez superviser l’intégrité d’un robot porteur au sein de l’installation.  

À partir de ce tableau de bord, vous pouvez :
   * Consulter les données de télémétrie des appareils, par exemple le nombre de prélèvements, le nombre de commandes traitées et certaines propriétés comme l’état du système de la structure.  
   * Examiner le plan d’étage et l’emplacement des robots porteurs au sein de la structure de traitement.
   * Déclencher des commandes pour réinitialiser le système de contrôle, mettre à jour le microprogramme du porteur et reconfigurer le réseau, par exemple.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-1.png" alt-text="Capture d’écran montrant la moitié supérieure du tableau de bord du centre de microtraitement de Northwind Traders.":::
   * Consulter un exemple du tableau de bord dont peut se servir un opérateur pour superviser les conditions au sein du centre de traitement.
   * Superviser l’intégrité des charges utiles qui s’exécutent sur l’appareil de passerelle au sein du centre de traitement.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/mfc-dashboard-2.png" alt-text="Capture d’écran montrant la moitié inférieure du tableau de bord du centre de microtraitement de Northwind Traders.":::


### <a name="device-template"></a>Modèle d’appareil

Si vous sélectionnez l’onglet Modèles d’appareil, vous constatez qu’il existe deux types d’appareils dans le modèle : 
   * **Robotic Carrier** (Robot porteur) : ce modèle d’appareil correspond à la définition d’un robot porteur opérationnel qui a été déployé dans la structure de traitement et qui assure des opérations utiles de stockage et de récupération. Si vous sélectionnez le modèle, vous voyez que le robot envoie diverses données d’appareil, comme la température et la position des axes, ainsi que des propriétés comme l’état du robot porteur. 
   * **Structure Condition Monitoring** (Supervision des conditions de la structure) : Ce modèle d’appareil représente un ensemble d’appareils qui vous permet de superviser les conditions ambiantes, et l’appareil de passerelle qui héberge diverses charges de travail de périphérie sur lesquelles repose votre centre de traitement. L’appareil envoie des données de télémétrie, telles que la température, le nombre de prélèvements et le nombre de commandes. Il envoie aussi des informations sur l’état et l’intégrité des charges de travail de calcul s’exécutant dans votre environnement. 

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/device-templates.png" alt-text="Modèles d’appareil de centre de microtraitement.":::



Si vous sélectionnez l’onglet Groupes d’appareils, vous constatez aussi que des groupes d’appareils sont créés automatiquement pour ces modèles d’appareil.

### <a name="rules"></a>Règles

Sous l’onglet **Règles**, vous voyez que le modèle d’application comporte un exemple de règle destiné à superviser les conditions de température pour le robot porteur. Vous pouvez utiliser cette règle pour alerter l’opérateur qu’un certain robot au sein de l’installation est en surchauffe et qu’il doit être mis hors connexion et révisé. 

Inspirez-vous de l’exemple de règle pour définir des règles adaptées à votre activité.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/rules.png" alt-text="Capture d’écran de l’onglet Règles.":::

### <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous ne comptez pas continuer à utiliser cette application, supprimez le modèle d’application. Accédez à **Administration** > **Paramètres d’application** et sélectionnez **Supprimer**.

  :::image type="content" source="media/tutorial-micro-fulfillment-center-app/delete.png" alt-text="Capture d’écran de la page des paramètres d’application du centre de microtraitement.":::

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus :

> [!div class="nextstepaction"]
> [Architecture de la solution du centre de microtraitement](./architecture-micro-fulfillment-center.md)
