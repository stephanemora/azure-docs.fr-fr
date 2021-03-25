---
title: Fichier include
description: Fichier include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 83f0ce27172879a37de9488499e46de30b8e112c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98147360"
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
| Chiffrement IKE | GCMAES256, GCMAES128, AES256, AES128 |
| Intégrité IKE | SHA384, SHA256 |
| Groupe DH | ECP384, ECP256, DHGroup24, DHGroup14 |
| Chiffrement IPsec | GCMAES256, GCMAES128, AES256, AES128, aucun |
| Intégrité IPsec | GCMAES256, GCMAES128, SHA256 |
| Groupe PFS | ECP384, ECP256, PFS24, PFS14, aucun |
| Durée de vie de l’AS |entier ; min. 300 / 27000 secondes par défaut |
