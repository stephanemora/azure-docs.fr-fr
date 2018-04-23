---
title: Fichier Include
description: Fichier Include
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9cdb608505e594e0020eb33abc869c6bf4b6b263
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2018
---
| **Fournisseur** | **Famille de périphériques** | **Version du microprogramme** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (préversion)|
|Cisco | ASA | RouteBased ASA (*) (BGP No IKEv2) pour les versions ASA antérieures à 9.8 |
|Cisco | ASA | ASA RouteBased (IKEv2 - No BGP) pour ASA 9.8 ou versions postérieures |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Requis : NarrowAzureTrafficSelectors et CustomAzurePolicies (IKE/IPsec)
>