---
title: Architecture de gestion des stocks intelligente IoT | Microsoft Docs
description: Architecture du modèle de gestion des stocks intelligente IoT pour Azure IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 6450169ae2b2d74006eedc66f35338494257594a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889088"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architecture du modèle de gestion des stocks intelligente IoT pour Azure IoT Central

Les partenaires et clients peuvent tirer parti du modèle d’application et des conseils suivants pour développer des solutions de **gestion des stocks intelligente** de bout en bout.

> [!div class="mx-imgBorder"]
> ![Gestion des stocks intelligente](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Ensemble de capteurs IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Les données sont acheminées vers le service Azure souhaité pour manipulation
4. Les services Azure tels qu’ASA ou Azure Functions peuvent être utilisés pour reformater les flux de données et les envoyer aux comptes de stockage souhaités 
5. Les données traitées sont stockées dans un stockage à chaud pour des actions en temps quasi-réel ou un stockage à froid pour des améliorations de données supplémentaires basées sur l’analyse par lots ou par ML. 
6. Logic Apps peut être utilisé pour alimenter divers flux de travail d’entreprise dans les applications d’entreprise pour utilisateurs finaux

## <a name="details"></a>Détails
La section suivante décrit chaque partie de l’architecture conceptuelle d’ingestion de données de télémétrie à partir de balises d’identification par radiofréquence (RFID, radio frequency identification) et Bluetooth basse énergie (BLE, Bluetooth low energy)

## <a name="rfid-tags"></a>Balises RFID
Les balises RFID transmettent les données relatives à un élément via des ondes radio. Les balises RFID n’ont généralement pas de batterie, sauf spécification contraire. Les balises reçoivent de l’énergie des ondes radio générées par le lecteur et retransmettent un signal au lecteur RFID.

## <a name="ble-tags"></a>Balises BLE
Une balise d’énergie diffuse des paquets de données à intervalles réguliers. Les données de la balise sont détectées par des lecteurs BLE ou des services installés sur les smartphones, puis transmises au cloud.

## <a name="rfid--ble-readers"></a>Lecteurs RFID et BLE
Un lecteur RFID convertit les ondes radio en une forme de données plus exploitables. Les informations collectées à partir des balises sont ensuite stockées dans un serveur Edge local ou envoyées au cloud via JSON-RPC 2.0 sur MQTT.
Les lecteurs BLE, également appelés point d’accès, sont similaires aux lecteurs RFID. Ils sont utilisés pour détecter des signaux Bluetooth proches et relayer leurs messages vers un service Azure IoT Edge local ou le cloud via JSON-RPC 2.0 sur MQTT.
De nombreux lecteurs sont capables de lire des signaux RFID et de balises, ainsi que de fournir des données de capteurs liées à la température, à l’humidité, à l’accéléromètre et au gyroscope.

## <a name="azure-iot-edge-gateway"></a>Passerelle Azure IoT Edge
Le serveur Azure IoT Edge Server fournit un emplacement pour prétraiter les données localement avant de les envoyer au cloud. Nous sommes également en mesure de déployer des charges de travail cloud d’intelligence artificielle, des services Azure et tiers, et une logique métier via des conteneurs standard.

## <a name="device-management-with-iot-central"></a>Gestion des appareils avec IoT Central 
Azure IoT Central est une plateforme de développement de solutions qui simplifie la connectivité, la configuration et la gestion des appareils IoT. La plateforme réduit considérablement la charge et les coûts liés à la gestion des appareils IoT, aux opérations et aux développements connexes. Les clients et partenaires peuvent créer des solutions d’entreprise de bout en bout pour obtenir une boucle de retour numérique dans une gestion d’inventaire.

## <a name="business-insights--actions-via-data-egress"></a>Perspectives d’affaires et actions via la sortie de données 
La plateforme IoT Central offre des options d’extensibilité riches par le biais de l’exportation continue des données (CDE) et des API. Les perspectives d’affaires basées sur le traitement des données de télémétrie ou la télémétrie brute sont généralement exportées vers une application métier choisie. Cela peut être réalisé par webhook, Service Bus, Event Hub ou stockage d’objets Blob pour créer, former et déployer des modèles Machine Learning et enrichir les informations.

## <a name="next-steps"></a>Étapes suivantes
* Découvrir comment déployer un [modèle de gestion intelligente d’inventaire](./tutorial-iot-central-smart-inventory-management-pnp.md)
* En savoir plus sur les [modèles de distribution IoT Central](./overview-iot-central-retail-pnp.md)
* En savoir plus sur IoT Central en lisant [Vue d’ensemble d’IoT Central](../preview/overview-iot-central.md)
