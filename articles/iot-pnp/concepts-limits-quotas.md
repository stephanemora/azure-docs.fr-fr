---
title: Limites et quotas de la préversion d’IoT Plug-and-Play | Microsoft Docs
description: Comprenez les limites, les quotas et les limitations qui s’appliquent lorsque vous utilisez la préversion d’IoT Plug-and-Play.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337396"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limites, quotas et limitations de la préversion d’IoT Plug-and-Play

Cet article explique les limites, quotas et limitations spécifiques à la préversion publique d’IoT Plug-and-Play. Il existe des [quotas et des limitations IOT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) qui s’appliquent également.

## <a name="iot-hub"></a>IoT Hub

Pour la préversion publique, les limites et quotas suivants s’appliquent à un hub IoT :

| Limites, restrictions et limitations | Valeur | Notes |
|-----|-----|-----|
| Nombre d’interfaces pouvant être inscrites par hub | 1500 ||
| Taille maximale d’un nom de composant | 1-64 caractères | Caractères autorisés : a-z, A-Z, 0-9 (hors premier caractère) et trait de soulignement (hors premier et dernier caractère). |
| Taille maximale du nom d’une propriété | 1-64 caractères | Caractères autorisés : a-z, A-Z, 0-9 (hors premier caractère) et trait de soulignement (hors premier et dernier caractère). |
| Taille maximale d’une valeur de propriété | Identique à la [Propriété](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) Digital Twins Definition Language | Profondeur de cinq niveaux, pas nécessairement un tableau ni un schéma complexe comportant un tableau. |
| Taille maximale d’un nom de commande | 1-64 caractères | Caractères autorisés : a-z, A-Z, 0-9 (hors premier caractère) et trait de soulignement (hors premier et dernier caractère).|
| Taille de jumeau d’appareil | Identique à [Limites IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||

## <a name="parser-library"></a>Bibliothèque de l’analyseur

La bibliothèque de l’analyseur suit les limites qui s’appliquent à [Digital Twins Definition Language](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Étapes suivantes

Nous vous recommandons maintenant de consulter [Architecture IoT Plug-and-Play](concepts-architecture.md).
