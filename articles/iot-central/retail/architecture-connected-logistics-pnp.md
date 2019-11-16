---
title: Architecture de logistique connectée IoT | Microsoft Docs
description: Une architecture pour le modèle d’application de logistique connectée IoT pour IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 797488632f04af9a170f05590154e50310570b28
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890717"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architecture du modèle d’application de logistique connectée IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Les partenaires et clients peuvent tirer parti du modèle d’application et des conseils suivants pour développer de bout en bout des **solutions logistiques connectées**.

> [!div class="mx-imgBorder"]
> ![tableau de bord de logistique connectée](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Ensemble de balises IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Les données sont acheminées vers le service Azure souhaité pour manipulation
4. Les services Azure tels qu’ASA ou Azure Functions peuvent être utilisés pour reformater les flux de données et les envoyer aux comptes de stockage souhaités 
5. Divers flux de travail d’entreprise peuvent reposer sur des applications métier d’utilisateur final

## <a name="details"></a>Détails
La section suivante décrit chaque partie de l’architecture conceptuelle Ingestion de données de télémétrie à partir de balises et de passerelles IoT

## <a name="iot-tags"></a>Balises IoT
Les balises IoT fournissent des fonctionnalités de capteur de données physiques, ambiantes et environnementales, avec notamment la température, l’humidité, les chocs, l’inclinaison et l’éclairage. Les balises IoT se connectent généralement à l’appareil de passerelle via ZigBee (802.15.4). Les balises sont des capteurs peu coûteux ; par conséquent, elles peuvent être ignorées à la fin d’un parcours logistique typique pour éviter les difficultés liées à la logistique inversée.

## <a name="gateway"></a>Passerelle
Les passerelles peuvent également agir comme balises IoT avec leurs fonctionnalités de détection des conditions ambiantes. La passerelle permet une connectivité à Azure IoT Cloud (MQTT) en amont via des canaux Wi-Fi et cellulaires.  Les modes Bluetooth, NFC et réseau de capteur sans fil 802.15.4 (WSN) sont utilisés pour la communication en aval avec les balises IoT. Les passerelles fournissent une connectivité cloud sécurisée de bout en bout, le jumelage des balises IoT, l’agrégation des données de capteur, la rétention des données et la possibilité de configurer des seuils d’alarme.

## <a name="device-management-with-iot-central"></a>Gestion des appareils avec IoT Central 
Azure IoT Central est une plateforme de développement de solutions qui simplifie la connectivité, la configuration et la gestion des appareils IoT. La plateforme réduit considérablement la charge et les coûts liés à la gestion des appareils IoT, aux opérations et aux développements connexes. Les clients et partenaires peuvent créer des solutions d’entreprise de bout en bout pour obtenir une boucle de retour numérique en logistique.

## <a name="business-insights--actions-via-data-egress"></a>Perspectives d'affaires et actions via la sortie de données 
La plateforme IoT Central offre des options d’extensibilité riches par le biais de l’exportation continue des données (CDE) et des API. Les perspectives d’affaires basées sur le traitement des données de télémétrie ou la télémétrie brute sont généralement exportées vers une application métier choisie. Cela peut être réalisé par webhook, Service Bus, Event Hub ou stockage d’objets Blob pour créer, former et déployer des modèles Machine Learning et enrichir les informations.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment déployer un [modèle de solution logistique connectée](./tutorial-iot-central-connected-logistics-pnp.md)
* En savoir plus sur les [modèles de distribution IoT Central](./overview-iot-central-retail-pnp.md)
* En savoir plus sur IoT Central en lisant [Vue d’ensemble d’IoT Central](../preview/overview-iot-central.md)
