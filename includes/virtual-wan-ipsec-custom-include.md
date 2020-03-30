---
title: Fichier Include
description: Fichier Include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168384"
---
Quand vous utilisez des stratégies IPsec personnalisées, gardez à l’esprit les exigences suivantes :

* **IKE** - Pour IKE, vous pouvez sélectionner n’importe quel paramètre de chiffrement IKE, d’intégrité IKE et de groupe DH.
* **IPsec** - Pour IPsec, vous pouvez sélectionner n’importe quel paramètre de chiffrement IPsec, d’intégrité IPsec et de PFS. Si GCM est utilisé dans les paramètres de chiffrement IPsec ou d’intégrité IPsec, alors il doit être utilisé pour le chiffrement et l’intégrité.

>[!NOTE]
> Les stratégies IPsec personnalisées n’impliquent aucun concept de répondeur et d’initiateur (contrairement aux stratégies IPsec par défaut). Les deux côtés (passerelle locale et passerelle VPN Azure) utilisent les mêmes paramètres pour IKE Phase 1 et IKE Phase 2. Les protocoles IKEv1 et IKEv2 sont pris en charge. Azure ne peut pas jouer le rôle de répondeur uniquement.
>

**Paramètres et configurations disponibles**

| Paramètre | Paramètres |
|--- |--- |
| Chiffrement IKE | AES256, AES192, AES128 |
| Intégrité IKE | SHA384, SHA256, SHA1 |
| Groupe DH | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| Chiffrement IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| Intégrité IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| Groupe PFS | PFS24, ECP384, ECP256, PFS2048, PFS2 |
