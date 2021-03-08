---
title: Stratégies IPsec point à site de Virtual WAN
titleSuffix: Azure Virtual WAN
description: Découvrez les stratégies de connectivité IPsec point à site d’Azure Virtual WAN.
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: wellee
ms.openlocfilehash: d64748bf7719af52b76c5e4141bfbbabe80bbae2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743239"
---
# <a name="point-to-site-ipsec-policies"></a>Stratégies IPsec point à site

Cet article présente les combinaisons de stratégies IPsec prises en charge pour la connectivité VPN point à site dans Azure Virtual WAN.

## <a name="default-ipsec-policies"></a>Stratégies IPsec par défaut

Le tableau suivant présente les paramètres IPsec par défaut pour les connexions VPN point à site. Ces paramètres sont automatiquement choisis par la passerelle VPN point à site de Virtual WAN lors de la création d’un profil point à site.

| Paramètre | Paramètres |
|--- |--- |
| Phase 1 Chiffrement IKE | AES256 |
| Phase 1 Intégrité IKE |  SHA256 |
| Groupe DH | DHGroup24 |
| Phase 2 Chiffrement IPsec | GCMAES256|
| Phase 2 Intégrité IPsec | GCMAES25 |
| Groupe PFS |PFS24|

## <a name="custom-ipsec-policies"></a>Stratégies IPsec personnalisées

Quand vous utilisez des stratégies IPsec personnalisées, gardez à l’esprit les exigences suivantes :

* **IKE** – Pour la Phase 1 IKE, vous pouvez sélectionner n’importe quel paramètre de chiffrement IKE, d’intégrité IKE et de groupe DH.
* **IPsec** – Pour la Phase 2 IPsec, vous pouvez sélectionner n’importe quel paramètre de chiffrement IPsec, d’intégrité IPsec et de PFS. Si l’un des paramètres pour Chiffrement IPsec ou Intégrité IPsec est GCM, le chiffrement et l’intégrité IPsec doivent utiliser le même algorithme. Par exemple, si GCMAES128 est choisi pour Chiffrement IPsec, GCMAES128 doit également être choisi pour Intégrité IPsec.  

Le tableau suivant présente les paramètres IPsec disponibles pour les connexions VPN point à site.

| Paramètre | Paramètres |
|--- |--- |
| Phase 1 Chiffrement IKE | AES128, AES256, GCMAES128, GCMAES256 |
| Phase 1 Intégrité IKE |  SHA256, SHA384 |
| Groupe DH | DHGroup14, DHGroup24, ECP256, ECP384 |
| Phase 2 Chiffrement IPsec | GCMAES128, GCMAES256, SHA256|
| Phase 2 Intégrité IPsec | GCMAES128, GCMAES256 |
| Groupe PFS |PFS14, PFS24, ECP256, ECP384|

## <a name="next-steps"></a>Étapes suivantes

Découvrez [Comment créer une connexion point à site](virtual-wan-point-to-site-portal.md)

Pour plus d’informations sur Virtual WAN, consultez la [FAQ sur Virtual WAN](virtual-wan-faq.md).
