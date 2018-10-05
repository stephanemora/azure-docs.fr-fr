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
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454446"
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
> (*) Requis : NarrowAzureTrafficSelectors (activer l’option UsePolicyBasedTrafficSelectors) et CustomAzurePolicies (IKE/IPsec)
>
