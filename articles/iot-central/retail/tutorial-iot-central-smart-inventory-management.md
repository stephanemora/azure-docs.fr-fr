---
title: 'Tutoriel : gestion intelligente des stocks Azure IoT | Microsoft Docs'
description: Ce tutoriel vous montre comment déployer et utiliser le modèle d’application de gestion intelligente des stocks pour IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 08/17/2021
ms.openlocfilehash: 747bcaffd1e24937580dcf95f352a34f66c3314e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437505"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-inventory-management-application-template"></a>Tutoriel : déployer et découvrir un modèle d’application de gestion intelligente des stocks

Utilisez le modèle d’application de *gestion intelligente des stocks* IOT central et les conseils de cet article pour développer une solution de gestion intelligente des stocks de bout en bout.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-architecture.png" alt-text="Gestion intelligente des stocks.":::

1. Ensemble de capteurs IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Les données sont acheminées vers le service Azure souhaité pour manipulation
4. Les services Azure que sont ASA ou Azure Functions permettent de reformater les flux de données et de les envoyer aux comptes de stockage souhaités 
5. Les données traitées sont stockées dans un stockage à chaud pour des actions en quasi temps réel ou dans un stockage à froid pour des améliorations d’insights supplémentaires basées sur l’analyse par lots ou par ML. 
6. Logic Apps peut être utilisé pour alimenter divers flux de travail d’entreprise dans les applications d’entreprise pour utilisateurs finaux

### <a name="details"></a>Détails

La section suivante décrit chaque partie de l’architecture conceptuelle d’ingestion de données de télémétrie à partir de balises d’identification par radiofréquence (RFID, radio frequency identification) et Bluetooth basse énergie (BLE, Bluetooth low energy)

### <a name="rfid-tags"></a>Balises RFID

Les balises RFID transmettent les données relatives à un élément via des ondes radio. Les balises RFID n’ont généralement pas de batterie, sauf indication contraire. Les balises reçoivent de l’énergie des ondes radio générées par le lecteur et retransmettent un signal au lecteur RFID.

### <a name="ble-tags"></a>Balises BLE

Une balise d’énergie diffuse des paquets de données à intervalles réguliers. Les données de la balise sont détectées par des lecteurs BLE ou des services installés sur les smartphones, puis transmises au cloud.

### <a name="rfid--ble-readers"></a>Lecteurs RFID et BLE

Un lecteur RFID convertit les ondes radio en une forme de données plus exploitables. Les informations collectées à partir des balises sont ensuite stockées dans un serveur Edge local ou envoyées au cloud via JSON-RPC 2.0 sur MQTT.
Les lecteurs BLE, également appelés point d’accès, sont similaires aux lecteurs RFID. Ils servent à détecter les signaux Bluetooth proches et à relayer leurs messages vers un service Azure IoT Edge local ou le cloud via JSON-RPC 2.0 sur MQTT.
De nombreux lecteurs sont capables de lire des signaux RFID et de balises et d’offrir une capacité de capteur supplémentaire liée à la température, à l’humidité, à l’accéléromètre et au gyroscope.

### <a name="azure-iot-edge-gateway"></a>Passerelle Azure IoT Edge

Le serveur Azure IoT Edge Server fournit un emplacement pour prétraiter les données localement avant de les envoyer au cloud. Nous pouvons aussi déployer des charges de travail cloud d’intelligence artificielle, des services Azure et tiers, et une logique métier utilisant des conteneurs standard.

### <a name="device-management-with-iot-central"></a>Gestion des appareils avec IoT Central 

Azure IoT Central est une plateforme de développement de solutions qui simplifie la connectivité, la configuration et la gestion des appareils IoT. La plateforme réduit considérablement la charge et les coûts liés à la gestion des appareils IoT, aux opérations et aux développements connexes. Les clients et partenaires peuvent créer des solutions d’entreprise de bout en bout pour obtenir une boucle de retour numérique dans une gestion d’inventaire.

### <a name="business-insights--actions-using-data-egress"></a>Insights métier et actions utilisant la sortie de données 

La plateforme IoT Central offre des options d’extensibilité riches via l’exportation continue des données (CDE) et des API. Les perspectives d’affaires basées sur le traitement des données de télémétrie ou la télémétrie brute sont généralement exportées vers une application métier choisie. Cela peut être réalisé en utilisant un webhook, Service Bus, un hub d’événements ou un stockage Blob de façon à générer, entraîner et déployer des modèles Machine Learning et enrichir les insights.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Créer une application de gestion intelligente des stocks 
> * Examiner l’application 

## <a name="prerequisites"></a>Prérequis

* Aucune configuration requise spécifique n’est requise pour déployer cette application
* Il est recommandé d’avoir un abonnement Azure, mais vous pouvez essayer sans

## <a name="create-smart-inventory-management-application"></a>Créer une application de gestion intelligente des stocks

Créez une application à l’aide de la procédure suivante :

1. Accédez au site de [création d’applications Azure IoT Central](https://aka.ms/iotcentral). Ensuite, connectez-vous avec un compte Microsoft personnel, scolaire ou professionnel. Sélectionnez **Build** dans la barre de navigation de gauche, puis sélectionnez l’onglet **Vente au détail** : :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc-retail-home-page.png" alt-text="Capture d’écran montrant comment créer une application à partie du modèle d’application de gestion intelligente des stocks":::

1. Sélectionnez **Créer une application** sous **gestion intelligente des stocks**.

Pour plus d’informations, consultez [Créer une application IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>examiner l’application

Les sections suivantes décrivent les principales fonctionnalités de l’application :

### <a name="dashboard"></a>tableau de bord 

Après le déploiement réussi du modèle d’application, votre tableau de bord par défaut est un portail dédié à l’opérateur de gestion intelligente des stocks. Northwind Trader est un fournisseur de stocks intelligent fictif qui gère l’entrepôt en utilisant la technologie Bluetooth Low Energy (BLE) et un magasin de vente au détail avec la technologie d’identification radiofréquence (RFID). Dans ce tableau de bord, deux passerelles différentes fournissent des données de télémétrie sur les stocks ainsi que les commandes, tâches et actions associées que vous pouvez effectuer. Ce tableau de bord est préconfiguré pour présenter l’activité des opérations de l’appareil de gestion intelligente des stocks.
Le tableau de bord distingue logiquement deux opérations de gestion des appareils de passerelle différentes : 
   * L’entrepôt est déployé avec une passerelle BLE fixe et des balises BLE sur les palettes pour assurer le suivi des stocks dans un site plus important
   * Le magasin de vente au détail est doté d’une passerelle RFID fixe et de balises RFID sur chaque article, pour assurer le suivi des stocks dans un point de vente
  * Visualiser l’[emplacement](../core/howto-use-location-data.md) l’état et les détails associés de la passerelle 

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-1.png" alt-text="Capture d’écran montrant la moitié supérieure du tableau de bord de gestion intelligente des stocks.":::

    * Vous pouvez facilement effectuer le suivi du nombre total de passerelles, des balises actives et inconnues.
    * Vous pouvez effectuer des opérations de gestion des appareils telles que la mise à jour des microprogrammes, la désactivation et l’activation des capteurs, la modification du seuil des capteurs, la modification des intervalles de télémétrie ou la modification des contrats de service des appareils.
    * Les appareils de passerelle peuvent effectuer une gestion des stocks à la demande avec une analyse complète ou incrémentielle.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-dashboard-2.png" alt-text="Capture d’écran montrant la moitié inférieure du tableau de bord de gestion intelligente des stocks.":::

### <a name="device-template"></a>Modèle d'appareil

Cliquez sur l’onglet Modèles d’appareils pour voir le modèle de capacité de la passerelle. Un modèle de capacité est structuré autour de deux interfaces différentes, **Propriétés et télémétrie de la passerelle** et **Commandes de la passerelle**

**Propriétés et télémétrie de la passerelle** – Cette interface représente toutes les données de télémétrie liées aux capteurs, à la localisation, aux informations d’appareil et à la capacité de propriété de jumeau d’appareil telle que les seuils et les intervalles de mise à jour de la passerelle.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-1.png" alt-text="Capture d’écran montrant le modèle d’appareil de la passerelle de des stocks dans l’application.":::

**Commandes de la passerelle** : cette interface organise toutes les fonctionnalités de commande de la passerelle

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-device-template-2.png" alt-text="Capture d’écran montrant l’interface des commandes de la passerelle dans le modèle d’appareil de la passerelle des stocks.":::

### <a name="rules"></a>Règles

Sélectionnez l’onglet Règles pour afficher deux règles différentes qui existent dans ce modèle d’application. Ces règles sont configurées pour envoyer des notifications par e-mail aux opérateurs pour des investigations supplémentaires.

**Passerelle en mode hors connexion** : Cette règle se déclenche si la passerelle ne se signale pas au cloud pendant une période prolongée. L’absence de réponse de la passerelle peut être due à une batterie faible, à une perte de connectivité ou encore à l’intégrité de l’appareil.

**Balises inconnues** : Il est essentiel d’effectuer le suivi de toutes les balises RFID et BLE associées à une ressource. Si la passerelle détecte un trop grand nombre de balises inconnues, cela indique que les applications d’approvisionnement des balises rencontrent des problèmes de synchronisation.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-rules.png" alt-text="Capture d’écran montrant la liste des règles dans l’application de gestion intelligente des stocks.":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’envisagez pas de continuer à utiliser cette application, supprimez le modèle d’application en accédant à **Administration** > **Paramètres de l’application**, puis cliquez sur **supprimer**.

   :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-cleanup.png" alt-text="Capture d’écran montrant comment supprimer l’application lorsque vous n’en avez plus besoin.":::

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la gestion intelligente des stocks :

> [!div class="nextstepaction"]
> [Concept de la gestion intelligente des stocks](./architecture-smart-inventory-management.md)
