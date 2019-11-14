---
title: Architecture du centre de distribution numérique IoT Central | Microsoft Docs
description: Un modèle d’architecture d’application du centre de distribution numérique pour IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 2ab79cccaafeb1b48100285a5fd495340b545278
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615342"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Modèle d’application d’architecture du centre de distribution numérique IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Les partenaires et clients peuvent tirer parti du modèle d’application et des conseils suivants pour développer de bout en bout des solutions de **centre de distribution numérique**.

> [!div class="mx-imgBorder"]
> ![centre de distribution numérique](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Ensemble de capteurs IoT envoyant des données de télémétrie à un appareil de passerelle
2. Les appareils de passerelle envoient des données de télémétrie et des insights agrégés à IoT Central
3. Les données sont acheminées vers le service Azure souhaité pour manipulation
4. Les services Azure tels qu’ASA ou Azure Functions peuvent être utilisés pour reformater les flux de données et les envoyer aux comptes de stockage souhaités 
5. Les données traitées sont stockées dans un stockage à chaud pour des actions en temps quasi-réel ou un stockage à froid pour des améliorations de données supplémentaires basées sur l’analyse par lots ou par ML. 
6. Logic Apps peut être utilisé pour alimenter divers flux de travail d’entreprise dans les applications d’entreprise pour utilisateurs finaux

## <a name="details"></a>Détails
La section suivante décrit chaque partie de l’architecture conceptuelle

## <a name="video-cameras"></a>Caméras vidéo 
Les caméras vidéo sont les capteurs principaux de cet écosystème à l’échelle de l’entreprise connecté numériquement. Les avancées en matière de Machine Learning et d’intelligence artificielle permettent aux vidéos d’être transformées en données structurées et de les traiter à la périphérie avant de les envoyer au cloud. Nous pouvons utiliser des caméras IP pour capturer des images, les compresser sur l’appareil, puis envoyer les données compressées sur le calcul en périphérie pour le pipeline d’analyse vidéo, ou utiliser des caméras de vision GigE pour capturer des images sur le capteur, puis envoyer ces images directement à Azure IoT Edge , qui compresse ensuite la vidéo avant de la traiter dans le pipeline d’analyse vidéo. 

## <a name="azure-iot-edge-gateway"></a>Passerelle Azure IoT Edge
Les « caméras en tant que capteurs » et les charges de travail de périphérie sont gérées localement par Azure IoT Edge, et le flux de la caméra est traité par le pipeline d’analyse. Le pipeline de traitement d’analyse vidéo sur Azure IoT Edge apporte de nombreux avantages, dont un temps de réponse réduit et une faible consommation de bande passante pour une latence basse et un traitement rapide des données. Seules les métadonnées, les informations ou les actions les plus essentielles sont envoyées au cloud à des fins d’action ou d’investigation ultérieures. 

## <a name="device-management-with-iot-central"></a>Gestion des appareils avec IoT Central 
Azure IoT Central est une plateforme de développement de solutions qui simplifie la connectivité, la configuration et la gestion des appareils IoT et de la passerelle Azure IoT Edge. La plateforme réduit considérablement la charge et les coûts liés à la gestion des appareils IoT, aux opérations et aux développements connexes. Les clients et partenaires peuvent créer des solutions d’entreprise de bout en bout pour obtenir une boucle de retour numérique dans les centres de distribution.

## <a name="business-insights--actions-via-data-egress"></a>Perspectives d'affaires et actions via la sortie de données 
La plateforme IoT Central offre des options d’extensibilité riches par le biais de l’exportation continue des données (CDE) et des API. Les perspectives d'affaires basées sur le traitement des données de télémétrie ou la télémétrie brute sont généralement exportées vers une application métier choisie. Cela peut être réalisé par webhook, Service Bus, Event Hub ou stockage d’objets Blob pour créer, former et déployer des modèles Machine Learning et enrichir les informations.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment déployer un [modèle de centre de distribution numérique](./tutorial-iot-central-digital-distribution-center-pnp.md)
* En savoir plus sur les [modèles de distribution IoT Central](./overview-iot-central-retail-pnp.md)
* Pour plus d’informations sur IoT Central, voir la [vue d’ensemble d’IoT Central](../core/overview-iot-central-pnp.md)
