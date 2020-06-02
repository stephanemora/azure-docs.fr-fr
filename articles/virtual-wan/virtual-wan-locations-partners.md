---
title: Partenaires Azure Virtual WAN et emplacements | Microsoft Docs
description: Cet article contient une liste des localisations des partenaires et hubs Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: e56da52823de169ccb1fc28a0bd7f3ac7efacf49
ms.sourcegitcommit: b396c674aa8f66597fa2dd6d6ed200dd7f409915
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2020
ms.locfileid: "82891030"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Localisation des partenaires et hub virtuel Virtual WAN

Cet article fournit des informations sur les partenaires et les régions Virtual WAN prises en charge pour la connectivité dans le Hub virtuel.

Un WAN virtuel Azure est un service de mise en réseau qui offre une connectivité de branche à branche optimisée et automatisée via Azure. Le WAN virtuel vous permet de vous connecter et de configurer des appareils de branche pour communiquer avec Azure. Cela peut se faire manuellement ou à l’aide d’appareils d’un fournisseur via un WAN virtuel partenaire. L’utilisation des appareils de partenaires permet une utilisation facile, une simplification de la connectivité et une gestion de la configuration.

La connectivité est établie de manière automatisée de l’appareil en local vers le Hub virtuel. Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Vous ne pouvez disposer que d’un seul hub par région.

## <a name="automation-from-connectivity-partners"></a><a name="automation"></a>Automation provenant des partenaires de connectivité

Les appareils qui se connectent à Azure Virtual WAN disposent, pour ce faire, d’une automation intégrée. Celle-ci est généralement configurée à distance dans l’interface utilisateur (ou équivalent) de gestion des appareils, qui configure la connectivité et la gestion de la configuration du périphérique VPN de branche vers un point de terminaison VPN Azure Virtual Hub (passerelle VPN).

L’automation générale suivante est configurée dans la console de l’appareil/le centre de gestion :

* Autorisations appropriées pour que l’appareil accède au groupe de ressources Azure Virtual WAN
* Chargement de l’appareil de branche dans Azure Virtual WAN
* Téléchargement automatique des informations de connectivité Azure
* Configuration de l’appareil de branche sur site 

Certains partenaires de connectivité peuvent étendre l’automation pour inclure la création du réseau virtuel Azure Virtual Hub et la passerelle VPN. Pour en savoir plus sur l’automatisation, consultez [Conseils d’automatisation pour les partenaires Virtual WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-partners"></a><a name="partners"></a>Connectivité via les partenaires

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Les partenaires suivants sont prévus prochainement sur notre feuille de route : 128 Technology, Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle SD-WAN et SharpLink.

## <a name="locations"></a><a name="locations"></a>Emplacements

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ sur Virtual WAN](virtual-wan-faq.md).

* Pour plus d’informations sur l’automatisation de la connectivité à Azure Virtual WAN, consultez [Conseils d’automatisation pour les partenaires Virtual WAN](virtual-wan-configure-automation-providers.md).
