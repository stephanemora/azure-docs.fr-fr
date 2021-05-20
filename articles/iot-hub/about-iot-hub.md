---
title: Qu’est-ce qu’Azure IoT Hub ? | Microsoft Docs
description: Cet article explique les utilisations d’Azure IoT Hub. Il vous permet de lire des données d’une manière évolutive et de gérer des appareils en toute sécurité.
author: robinsh
ms.author: robinsh
ms.date: 05/03/2021
ms.topic: overview
ms.custom:
- mvc
- amqp
- mqtt
- 'role: Direction'
- 'role: System Architecture'
- contentperf:fy21q3
ms.service: iot-hub
services: iot-hub
ms.openlocfilehash: 95247a1485bac7282ffe95ddcc2ddd37883738dd
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109783817"
---
# <a name="what-is-azure-iot-hub"></a>Qu’est-ce qu’Azure IoT Hub ?

IoT Hub est un service managé, hébergé dans le cloud, qui joue le rôle de hub de messages central pour la communication bidirectionnelle entre une application IoT et ses appareils attachés. Vous pouvez connecter des millions d’appareils et leurs solutions back-end de manière fiable et sécurisée. Presque tous les appareils peuvent être connectés à un hub IoT. 

Plusieurs modèles de messagerie sont pris en charge, notamment la télémétrie appareil-à-cloud, le chargement de fichiers à partir d’appareils et les méthodes demande-réponse pour contrôler vos appareils à partir du cloud. IoT Hub prend également en charge la supervision pour vous aider à effectuer le suivi de la création des appareils, de leur connexion et de leurs défaillances.

Les fonctionnalités d’IoT Hub vous permettent de générer des solutions IoT évolutives et complètes, notamment de gérer l’équipement industriel utilisé dans la fabrication, de suivre des ressources précieuses dans le secteur de la santé et de superviser l’utilisation de bâtiments de bureau.

## <a name="scale-your-solution"></a>Mettre à l’échelle votre solution

IoT Hub peut supporter des millions d’appareils connectés simultanément et des millions d’événements par seconde pour prendre en charge vos charges de travail IoT. Pour plus d’informations sur la mise à l’échelle de votre IoT Hub, consultez [Mise à l’échelle IoT Hub](iot-hub-scaling.md). Pour en savoir plus sur les différents niveaux de service offerts par IoT Hub et sur la meilleure manière de s’adapter à vos besoins d’extensibilité, consultez la [page des tarifs](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="secure-your-communications"></a>Sécuriser vos communications

Vous pouvez envoyer des données de manière sécurisée en utilisant IoT Hub.

* L’authentification par appareil permet à chaque appareil de se connecter en toute sécurité à IoT Hub et d’être managé en toute sécurité.

* Vous avez un contrôle complet sur l’accès à l’appareil et pouvez contrôler les connexions au niveau de l’appareil.

* Le [Service IoT Hub Device Provisioning](../iot-dps/index.yml) provisionne automatiquement les appareils en fonction de l’IoT Hub correct lors de leur premier démarrage.

* Plusieurs types d’authentification permettent de prendre en charge un large éventail de fonctionnalités d’appareil :

  * L’authentification basée sur un jeton SAS vous permet de prendre en main rapidement votre solution IoT.

  * L’authentification par certificat X.509 individuel est disponible pour une authentification sécurisée basée sur des normes.

  * L’authentification par autorité de certification X.509 peut être utilisée pour une inscription simple basée sur des normes.

## <a name="route-device-data"></a>Acheminer les données d’appareil

La fonctionnalité de routage de messages intégrée vous offre la possibilité de configurer la distribution ramifiée automatique de messages en fonction de règles :

* Le [routage des messages](iot-hub-devguide-messages-d2c.md) est utilisé pour contrôler la destination à laquelle votre hub envoie les données de télémétrie de l’appareil.

* Aucuns frais supplémentaires ne sont appliqués pour acheminer les messages vers plusieurs points de terminaison.

* Des règles de routage peuvent être configurées pour diriger automatiquement les messages en fonction de leur contenu sans avoir à écrire de code.

## <a name="integrate-with-other-services"></a>Intégrer aux autres services

Vous pouvez intégrer IoT Hub avec d’autres services Azure pour créer des solutions complètes, de bout en bout. Par exemple, utilisez :

* [Azure Event Grid](../event-grid/index.yml) pour permettre à votre entreprise de réagir rapidement à des événements critiques de façon sûre, évolutive et sécurisée.

* [Azure Logic Apps](../logic-apps/index.yml) pour automatiser les processus métier.

* [Azure Machine Learning](iot-hub-weather-forecast-machine-learning.md) pour ajouter l’apprentissage automatique et les modèles AI à votre solution.

* [Azure Stream Analytics](../stream-analytics/index.yml) pour exécuter des calculs analytiques en temps réel sur le streaming de données à partir de vos appareils.

## <a name="configure-and-control-your-devices"></a>Configurer et contrôler vos appareils

Vous pouvez gérer vos appareils connectés à IoT Hub avec un ensemble de fonctionnalités intégrées.

* Vous pouvez stocker, synchroniser et interroger les informations de métadonnées et d’état de l’appareil pour tous vos appareils.

* Vous pouvez définir l’état de l’appareil par appareil ou en fonction de caractéristiques communes des appareils.

* Vous pouvez répondre automatiquement à un changement d’état de l’appareil signalé avec l’intégration de routage de message.

## <a name="make-your-solution-highly-available"></a>Rendre votre solution hautement disponible

Il existe un [contrat de niveau de service pour IoT Hub](https://azure.microsoft.com/support/legal/sla/iot-hub/) de 99,9 %. La version complète du [contrat SLA Azure](https://azure.microsoft.com/support/legal/sla/) explique la disponibilité garantie d’Azure dans son ensemble.

## <a name="connect-your-devices"></a>Connecter vos appareils

Utilisez les bibliothèques [Azure IoT device SDK](./iot-hub-devguide-sdks.md) pour créer des applications qui s’exécutent sur vos appareils et interagissent avec IoT Hub. Les plateformes prises en charge incluent plusieurs distributions Linux, Windows et les systèmes d’exploitation en temps réel. Les langages pris en charge incluent :

* C
* Embedded C
* C#
* Java
* Python
* Node.js.

IoT Hub et les Kits SDK d’appareil prennent en charge les protocoles suivants pour la connexion des appareils :

* HTTPS
* AMQP
* AMQP sur WebSockets
* MQTT
* MQTT sur WebSockets

IoT Hub et les SDK d’appareil prennent en charge les conventions [Azure IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md) pour la connexion des appareils. Les appareils IoT Plug-and-Play utilisent un modèle d’appareil pour exposer leurs fonctionnalités aux applications IoT Plug-and-Play. Le modèle d’appareil permet aux créateurs de solutions d’intégrer des appareils intelligents dans leurs solutions sans aucune configuration manuelle.

Si votre solution ne peut pas utiliser les bibliothèques d’appareils, les appareils peuvent utiliser les protocoles MQTT v3.1.1, HTTPS 1.1 ou AMQP 1.0 pour se connecter nativement à votre concentrateur.

Si votre solution ne peut pas utiliser un des protocoles pris en charge, vous pouvez étendre IoT Hub pour prendre en charge des protocoles personnalisés :

* Utilisez [Azure IoT Edge](../iot-edge/index.yml) pour créer une passerelle locale afin d’effectuer la traduction de protocole sur la périphérie.

* Personnalisez la [passerelle de protocole Azure IoT](https://github.com/Azure/azure-iot-protocol-gateway/blob/master/README.md) pour effectuer la traduction de protocole dans le cloud.

## <a name="quotas-and-limits"></a>Quotas et limites

Chaque abonnement Azure a des limites de quota par défaut pour empêcher tout abus du service. Ces limites peuvent impacter l’étendue de votre solution IoT. La limite actuelle est de 50 IoT Hubs par abonnement. Vous pouvez demander une augmentation de ce quota en contactant le support. Pour plus d’informations, consultez [Quotas et limitations IoT Hub](iot-hub-devguide-quotas-throttling.md). Pour plus d’informations sur les limites de quota, consultez l’un des articles suivants :

* [Limites du service d’abonnement Azure](../azure-resource-manager/management/azure-subscription-service-limits.md)

* [Limitation d’IoT Hub et vous](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/)

## <a name="iot-hub-on-azure-stack-hub-preview"></a>IoT Hub sur Azure Stack Hub (préversion)

IoT Hub sur Azure Stack Hub (préversion) vous permet de créer des solutions IoT hybrides. IoT Hub est un service managé qui fait office de hub de messages centralisé pour la communication bidirectionnelle entre votre application IoT et les appareils qu’elle gère. Vous pouvez utiliser IoT Hub sur Azure Stack Hub pour générer des solutions IoT avec des communications fiables et sécurisées entre des appareils IoT et vos solutions locales.

IoT Hub sur Azure Stack Hub est gratuit pendant la préversion publique. Pour plus d’informations, consultez la [Présentation d’IoT Hub sur Azure Stack Hub](/azure-stack/operator/iot-hub-rp-overview).

## <a name="next-steps"></a>Étapes suivantes

Pour tester une solution IoT de bout en bout, consultez les démarrages rapides relatifs à IoT Hub :

* [Démarrage rapide : Send telemetry from a device to an IoT hub (Envoyer des données de télémétrie d’un appareil à un IoT Hub)](quickstart-send-telemetry-node.md)

Pour en savoir plus sur la façon dont vous pouvez générer et déployer des solutions IoT avec Azure IoT, consultez la page :

* [Fondamentaux : Technologies et solutions Azure IoT](../iot-fundamentals/iot-services-and-technologies.md).