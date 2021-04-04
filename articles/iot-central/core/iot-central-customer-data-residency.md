---
title: Résidence des données client dans Azure IoT Central | Microsoft Docs
description: Cet article décrit la résidence des données client dans les applications Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280653"
---
# <a name="azure-iot-central-customer-data-residency"></a>Résidence des données client dans Azure IoT Central

IoT Central ne stocke pas les données client en dehors de la zone géographique spécifiée par le client, à l’exception des scénarios suivants :

- Lorsqu’un nouvel utilisateur est ajouté à une application IoT Central existante, l’ID d’e-mail de l’utilisateur peut être stocké en dehors de la zone géographique, jusqu’à ce que l’utilisateur invité accède à l’application pour la première fois.

- Les mosaïques du tableau de bord IoT Central utilisent [Azure Maps](../../azure-maps/about-azure-maps.md). Lorsque vous ajoutez une mosaïque à une application IoT Central existante, les données de localisation peuvent être traitées ou stockées conformément aux règles de géolocalisation du service Azure Maps.
