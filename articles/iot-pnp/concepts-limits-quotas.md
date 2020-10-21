---
title: Limites et quotas d’IoT Plug-and-Play | Microsoft Docs
description: Comprenez les limites, les quotas et les limitations qui s’appliquent lorsque vous utilisez IoT Plug-and-Play.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577983"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>Limites, quotas et limitations d’IoT Plug-and-Play

Cet article explique les limites, quotas et limitations spécifiques à IoT Plug-and-Play. Il existe des [quotas et des limitations IOT Hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) qui s’appliquent également.

## <a name="iot-hub"></a>IoT Hub

Les limites et quotas suivants s’appliquent à un hub IoT :

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
