---
title: Localisations des partenaires d’Azure Virtual WAN | Microsoft Docs
description: Cet article contient une liste des localisations des partenaires et hubs Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect find a Virtual WAN partner
ms.openlocfilehash: 76542392522ff14642ed7ccc6de8ed543d66513f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984991"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Localisation des partenaires et hub virtuel Virtual WAN

Cet article fournit des informations sur les régions prises en charge Virtual WAN et les partenaires préférés pour la connectivité dans le Hub virtuel.

Un WAN virtuel Azure est un service de mise en réseau qui offre une connectivité de branche à branche optimisée et automatisée via Azure. Le WAN virtuel vous permet de vous connecter et de configurer des appareils de branche pour communiquer avec Azure. Cela peut se faire manuellement ou à l’aide d’appareils de fournisseur de votre choix via un partenaire Virtual WAN préféré. L’utilisation des appareils de partenaires préférés permet une utilisation facile, une simplification de la connectivité et une gestion de la configuration. La connectivité est établie de manière automatisée de l’appareil en local vers le Hub virtuel. Un hub virtuel est un réseau virtuel géré par Microsoft. Le hub contient différents points de terminaison de service pour activer la connectivité à partir de votre réseau local (vpnsite). Vous ne pouvez disposer que d’un seul hub par région.

## <a name="regions"></a>Régions

Voici la liste des régions prises en charge et disponibles :

|Région géopolitique | Régions Azure|
|---|---|
|Amérique du Nord | USA Est, USA Ouest, USA Est 2, USA Ouest 2, USA Centre, USA Centre Sud, USA Centre Nord, USA Centre-Ouest, Canada Centre, Canada Est |
|Amérique du Sud |Brésil Sud |
| Europe | France Centre, France Sud, Europe Nord, Europe Ouest, Royaume-Uni Ouest, Royaume-Uni Sud |
| Asie | Asie Est, Asie Sud-Est |
| Japon  | Japon Ouest, Japon Est |
| Australie | Australie Sud-Est | Australie Est | 
| Gouvernement australien | Australie Centre, Australie Centre 2 |
| Inde | Inde Ouest, Inde Centre, Inde Sud |
| Corée du Sud | Centre de la Corée, Corée du Sud | 

## <a name="automation-from-connectivity-partners"></a>Automation provenant des partenaires de connectivité

Cette section décrit les détails généraux de l’automation provenant des fournisseurs de connectivité.

Les appareils qui se connectent à Azure Virtual WAN disposent, pour ce faire, d’une automation intégrée. Celle-ci est généralement configurée à distance dans l’interface utilisateur (ou équivalent) de gestion des appareils, qui configure la connectivité et la gestion de la configuration du périphérique VPN de branche vers un point de terminaison VPN Azure Virtual Hub (passerelle VPN).

L’automation générale suivante est configurée dans la console de l’appareil/le centre de gestion :

* Autorisations appropriées pour que l’appareil accède au groupe de ressources Azure Virtual WAN
* Chargement de l’appareil de branche dans Azure Virtual WAN
* Téléchargement automatique des informations de connectivité Azure 
* Configuration de l’appareil de branche sur site 

Certains partenaires de connectivité peuvent étendre l’automation pour inclure la création du réseau virtuel Azure Virtual Hub et la passerelle VPN. Pour en savoir plus sur l’automation, consultez [Configurer l’Automation - Partenaires WAN](virtual-wan-configure-automation-providers.md).

## <a name="connectivity-through-preferred-partners"></a>Connectivité via les partenaires préférés

Si votre partenaire d’appareil de branche n’est pas répertorié dans la section ci-dessous, veuillez contacter votre fournisseur d’appareil de branche et demandez-lui de nous contacter par courrier électronique à l’adresse azurevirtualwan@microsoft.com.

Vous pouvez consulter les liens ci-dessous pour recueillir des informations supplémentaires sur les services proposés par les partenaires préférés. 

|Partenaires préférés|
|---|
|[Barracuda Networks](https://www.barracuda.com/AzurevWAN)|
| [Check Point](https://www.checkpoint.com/solutions/microsoft-azure-virtual-wan/) |
| [Citrix](https://www.citrix.com/global-partners/microsoft/sd-wan-for-azure-virtual-wan.html)|
|[Netfoundry](https://netfoundry.io/solutions/netfoundry-for-microsoft-azure-virtual-wan/)|
|[Palo Alto Networks](https://researchcenter.paloaltonetworks.com/2018/09/azure-vwan-integration/) |
|[Riverbed Technology](https://www.riverbed.com/go/steelconnect-azurewan.html)|
|[128 Technology](https://www.128technology.com/partners/azure) |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Virtual WAN, consultez la [FAQ sur Virtual WAN](virtual-wan-faq.md).

Pour plus d’informations sur l’automation de la connectivité à Azure Virtual WAN, consultez [Partenaires Virtual WAN - Comment automatiser](virtual-wan-configure-automation-providers.md).
