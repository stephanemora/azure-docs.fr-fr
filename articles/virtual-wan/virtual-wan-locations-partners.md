---
title: Localisations des partenaires d’Azure Virtual WAN | Microsoft Docs
description: Cet article contient une liste des localisations des partenaires et hubs Azure Virtual WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: b7a3d443b64943c097a51eeae4be6253a0466e18
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76933710"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Localisation des partenaires et hub virtuel Virtual WAN

Cet article fournit des informations sur les partenaires et les régions Virtual WAN prises en charge pour la connectivité dans le Hub virtuel.

Un WAN virtuel Azure est un service de mise en réseau qui offre une connectivité de branche à branche optimisée et automatisée via Azure. Le WAN virtuel vous permet de vous connecter et de configurer des appareils de branche pour communiquer avec Azure. Cela peut se faire manuellement ou à l’aide d’appareils d’un fournisseur via un WAN virtuel partenaire. L’utilisation des appareils de partenaires permet une utilisation facile, une simplification de la connectivité et une gestion de la configuration.

La connectivité est établie de manière automatisée de l’appareil en local vers le Hub virtuel. Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Vous ne pouvez disposer que d’un seul hub par région.

## <a name="automation"></a>Automation provenant des partenaires de connectivité

Les appareils qui se connectent à Azure Virtual WAN disposent, pour ce faire, d’une automation intégrée. Celle-ci est généralement configurée à distance dans l’interface utilisateur (ou équivalent) de gestion des appareils, qui configure la connectivité et la gestion de la configuration du périphérique VPN de branche vers un point de terminaison VPN Azure Virtual Hub (passerelle VPN).

L’automation générale suivante est configurée dans la console de l’appareil/le centre de gestion :

* Autorisations appropriées pour que l’appareil accède au groupe de ressources Azure Virtual WAN
* Chargement de l’appareil de branche dans Azure Virtual WAN
* Téléchargement automatique des informations de connectivité Azure
* Configuration de l’appareil de branche sur site 

Certains partenaires de connectivité peuvent étendre l’automation pour inclure la création du réseau virtuel Azure Virtual Hub et la passerelle VPN. Pour en savoir plus sur l’automation, consultez [Configurer l’Automation - Partenaires WAN](virtual-wan-configure-automation-providers.md).

## <a name="partners"></a>Connectivité via les partenaires

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Les partenaires suivants sont prévus prochainement sur notre feuille de route : Arista, Aruba HPE, Cisco Systems, F5 Networks, Open Systems, Oracle Talari, SharpLink et VMWare Velocloud.

## <a name="locations"></a>Emplacements

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur Virtual WAN, consultez la [FAQ sur Virtual WAN](virtual-wan-faq.md).

* Pour plus d’informations sur l’automation de la connectivité à Azure Virtual WAN, consultez [Partenaires Virtual WAN - Comment automatiser](virtual-wan-configure-automation-providers.md).
