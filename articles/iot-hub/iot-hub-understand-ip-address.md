---
title: Comprendre l’adresse IP de votre hub IoT | Microsoft Docs
description: Apprenez à interroger l’adresse IP de votre hub IoT et ses propriétés. L’adresse IP de votre hub IoT peut changer dans certains scénarios, par exemple dans le cadre d’une reprise d’activité ou d’un basculement régional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383914"
---
# <a name="iot-hub-ip-addresses"></a>Adresses IP IoT Hub

Les préfixes d’adresse IP de l’hub IoT sont publiés régulièrement sous la *balise de service* [AzureIoTHub](../virtual-network/service-tags-overview.md). Pour garantir un bon fonctionnement, vos appareils IoT doivent disposer d’une connectivité sortante pour résoudre les préfixes figurant sous la balise de service *AzureIoTHub*. Vos services d’application IoT doivent en outre disposer d’une connectivité sortante pour résoudre les préfixes figurant sous la balise de service *EventHub*.


## <a name="best-practices"></a>Bonnes pratiques

* Les préfixes d’adresse IP de l’hub IoT sont susceptibles d’être modifiés. Ces modifications sont publiées régulièrement par le biais de balises de service avant d’être prises en compte. Il est donc important que vous développiez des processus pour récupérer et utiliser régulièrement les balises de service les plus récentes. Ce processus peut être automatisé via l’[API de détection de balises de service](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Utilisez la balise *AzureIoTHub.[nom de région]* pour identifier les préfixes IP utilisés par les points de terminaison de l’hub IoT dans une région spécifique. Pour tenir compte de la récupération d’urgence d’un centre de données ou de [basculement régional](iot-hub-ha-dr.md), assurez-vous que la connectivité aux préfixes IP de la région de géolocalisation de votre IoT Hub est également activée.


## <a name="support-for-ipv6"></a>Prise en charge d’IPv6 

IPv6 n’est pas pris en charge sur IoT Hub.