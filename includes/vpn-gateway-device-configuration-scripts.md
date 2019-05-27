---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 01/09/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 78dfd57fba6365f9c8937b30b5cf96b840749c68
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66157617"
---
| **Fournisseur** | **Famille de périphériques** | **Version du microprogramme** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (préversion)|
|Cisco | ASA | RouteBased ASA ( * ) (BGP No IKEv2) pour les versions ASA antérieures à 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - No BGP) pour ASA 9.8 ou versions postérieures |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x BGP RouteBased |
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> ( * ) Obligatoire : NarrowAzureTrafficSelectors (activer l’option UsePolicyBasedTrafficSelectors) et CustomAzurePolicies (IKE/IPsec)
>
