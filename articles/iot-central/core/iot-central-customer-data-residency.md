---
title: Résidence des données client dans Azure IoT Central | Microsoft Docs
description: Cet article décrit la résidence des données client dans les applications Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2086ab1e899b85f52391187425c85eeb86b21c0e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481161"
---
# <a name="azure-iot-central-customer-data-residency"></a>Résidence des données client dans Azure IoT Central

IoT Central ne stocke pas les données client en dehors de la zone géographique spécifiée par le client, à l’exception des scénarios suivants :

- Lorsqu’un nouvel utilisateur est ajouté à une application IoT Central existante, l’ID d’e-mail de l’utilisateur peut être stocké en dehors de la zone géographique, jusqu’à ce que l’utilisateur invité accède à l’application pour la première fois.

- Les mosaïques du tableau de bord IoT Central utilisent [Azure Maps](../../azure-maps/about-azure-maps.md). Lorsque vous ajoutez une mosaïque à une application IoT Central existante, les données de localisation peuvent être traitées ou stockées conformément aux règles de géolocalisation du service Azure Maps.
