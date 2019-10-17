---
title: Comprendre l’adresse IP de votre hub IoT | Microsoft Docs
description: Apprenez à interroger l’adresse IP de votre hub IoT et ses propriétés. L’adresse IP de votre hub IoT peut changer dans certains scénarios, par exemple dans le cadre d’une reprise d’activité ou d’un basculement régional.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841527"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Comprendre l’adresse IP de votre hub IoT

L’adresse IP de votre hub IoT est un point de terminaison public à charge équilibrée pour votre hub et non une adresse IP dédiée. L’adresse est déterminée par la plage d’adresses réseau de la région Azure où il est déployé. Cette adresse IP peut être modifiée sans préavis. Les mises à jour de réseau de centre de données, une reprise d’activité de centre de données ou un basculement régional d’un hub IoT peuvent modifier l’adresse IP de votre hub IoT. Pour plus d’informations sur la disponibilité et le basculement régional d’un hub IoT Azure, consultez [Haute disponibilité et récupération d’urgence IoT Hub](iot-hub-ha-dr.md).

L’adresse IP de votre hub IoT change après un basculement vers une autre région. Vous pouvez tester cette fonctionnalité en suivant le tutoriel [Perform a manual failover of an IoT hub](tutorial-manual-failover.md) (Effectuer le basculement manuel d’un hub IoT).

## <a name="discover-your-iot-hub-ip-address"></a>Découvrir l’adresse IP de votre hub IoT

Vous pouvez découvrir l’adresse IP de votre hub IoT à l’aide d’une recherche DNS inversée sur le CNAME ([*nom-hub-iot*].azure-devices.net). Vous pouvez utiliser **nslookup** pour vérifier l’adresse IP d’une instance de hub IoT :

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Cette adresse IP peut être modifiée sans préavis. Dans un scénario de basculement ou de reprise d’activité, vous devez interroger l’adresse IP de votre hub IoT dans la région secondaire.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Règles de pare-feu de trafic sortant pour un hub IoT

Essayez de créer des règles de pare-feu et un filtrage basés sur le domaine ou le nom d’hôte du hub IoT. Si vous pouvez uniquement autoriser le trafic sortant vers des adresses spécifiques, interrogez régulièrement l’adresse IP de votre hub IoT et mettez à jour vos règles de pare-feu.

## <a name="support-for-ipv6"></a>Prise en charge d’IPv6 

IPv6 n’est pas pris en charge sur IoT Hub.