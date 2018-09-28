---
title: Plan de contrôle Office 365 dans Azure Virtual WAN
description: Apprenez-en plus sur le plan de contrôle Office 365 dans Virtual WAN.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992672"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Plan de contrôle Office 365 dans Virtual WAN

Les clients Virtual WAN équipés de certains appareils SDWAN peuvent configurer les stratégies de « breakout » Internet d’O365 pour sécuriser le trafic dans le portail Azure. Vous pouvez ainsi :
- Permettre au trafic O365 d’entrer sur le réseau Microsoft à proximité de l’utilisateur pour optimiser son expérience.
- Éviter le trafic de retour et le renvoi d’appel en épingle, diminuant les coûts liés au WAN.
- Appliquer les principes de connectivité O365.

## <a name="faqs"></a>FAQ
### <a name="what-is-the-customer-benefit"></a>Quel est l’avantage pour le client ?
En utilisant cette fonctionnalité dans Virtual WAN, les clients peuvent maintenant spécifier les catégories de trafic Office 365 auxquels ils se fient pour le « breakout » Internet direct. Ce trafic O365 approuvé ignore les proxies et achemine directement le trafic du site de l’utilisateur vers le serveur POP Microsoft le plus proche. Cela permet d’éviter le trafic de retour et le renvoi d’appel en épingle, de proposer une expérience optimale à l’utilisateur et de diminuer les coûts liés au WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Quelles sont les catégories de trafic Office 365 ?
Les points de terminaison Office 365 représentent les sous-réseaux et les adresses réseau. Les points de terminaison peuvent être des URL, des adresses IP ou des plages IP. Les URL peuvent être un nom de domaine complet (FQDN) comme *account.office.net*, ou une URL générique comme **. office365.com*. Les points de terminaison sont répartis en trois catégories en fonction de leur niveau de gravité : **Optimiser**, **Autoriser** et **Par défaut**. Pour plus d’informations sur les catégories des points de terminaison, cliquez [ici](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Quelle catégorie de trafic Office 365 est recommandée par Microsoft pour le « breakout » Internet direct ?
La catégorie **Optimiser** regroupe les points de terminaison réseau les plus critiques et est nécessaire pour contourner le saut SSL et inspecter les autres appareils de sécurité réseau. Elle doit fournir des sorties Internet directes proches des utilisateurs. Les points de terminaison de cette catégorie représentent les scénarios Office 365 les plus sensibles aux performances, à la latence et à la disponibilité du réseau. Cette catégorie comprend un petit ensemble d’URL clés (de l’ordre de 10 environ) et un ensemble défini de sous-réseaux IP dédiés aux charges de travail Office 365 principales comme Exchange Online, SharePoint Online, Skype Entreprise Online et Microsoft Teams. 

La catégorie **Autoriser** est également recommandée pour les sorties Internet directes. L’autorisation du trafic réseau peut toutefois tolérer une certaine latence réseau. Les points de terminaison dans les catégories Optimiser et Autoriser sont tous hébergés dans des centres de données Microsoft et gérés dans le cadre d’Office 365. La catégorie Par défaut peut être dirigée vers un emplacement de sortie Internet par défaut et ne nécessite pas de sortie Internet directe, ni de contourner le saut SSL et d’inspecter les appareils.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Comment définir mes stratégies O365 via Virtual WAN ?
Vous pouvez activer des stratégies via **Virtual WAN virtuel** -> **Paramètres** -> **onglet Configuration**. Là, vous pouvez spécifier vos catégories par défaut de trafic O365 pour le « breakout » Internet direct.

![Configurer le plan de contrôle Office 365 dans Virtual WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Comment cela fonctionne-t-il ?

1.  Le trafic O365 entre sur le réseau Microsoft à proximité de l’utilisateur, ce qui optimise son expérience.
2.  Le SDWAN utilise les stratégies de routage. Il ignore les proxies de sécurité pour les catégories approuvées, puis effectue un « breakout » direct local pour ces catégories.
3.  Le trafic de retour et le renvoi d’appel en épingle sont évités, ce qui diminue les coûts liés au WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Quels appareils des partenaires prennent en charge cette solution via Virtual WAN ?
Actuellement, Citrix prend en charge ces stratégies via Virtual WAN.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Qu’advient-il des catégories restantes du trafic Office 365 (non approuvé) ?
Le trafic O365 restant suit le chemin de trafic Internet par défaut des clients.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Que se passe-t-il si j’ai déjà spécifié mes stratégies O365 via mon fournisseur de SDWAN ?
Si vous spécifiez des stratégies via l’expérience utilisateur du SDWAN et Azure Virtual WAN, les stratégies définies dans Virtual WAN sont prioritaires.

## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur [Virtual WAN](virtual-wan-about.md).