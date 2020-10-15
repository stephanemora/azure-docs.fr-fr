---
title: FAQ sur Azure Peering Service
description: Découvrez le FAQ sur Microsoft Azure Peering Service
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871359"
---
# <a name="peering-service-faq"></a>FAQ sur Peering Service

Cet article énumère les questions les plus fréquemment posées concernant les connexions Microsoft Azure Peering Service.


**Q. Qui sont les clients cibles ?**

R. Les clients cibles sont des entreprises qui se connectent au cloud Microsoft via Internet.

**Q. Les clients peuvent-ils s’inscrire à Peering Service avec plusieurs fournisseurs ?** 

R. Oui, les clients peuvent s’inscrire à Peering Service avec plusieurs fournisseurs dans la même région ou dans des régions différentes, mais pas pour le même préfixe.

**Q. Les clients peuvent-ils sélectionner un fournisseur de services Internet unique pour leurs sites par région géographique ?**

R. Oui, ils le peuvent. Sélectionnez le fournisseur de services Internet partenaire par région qui répond à vos besoins commerciaux et opérationnels.

**Q. Qu’est-ce qu’un Microsoft Edge PoP ?**

R. Il s’agit d’un emplacement physique où Microsoft s’interconnecte avec d’autres réseaux. L’emplacement Microsoft Edge PoP héberge des services tels qu’Azure Front Door et Azure CDN. Pour plus d’informations, consultez [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features).

## <a name="peering-service-unique-characteristics"></a>Peering Service : Caractéristiques uniques

**Q. En quoi Peering Service diffère-t-il d’un accès à Internet normal ?**

R. Les partenaires inscrits auprès de Microsoft Peering Service collaborent avec Microsoft pour offrir un routage optimisé et une connectivité fiable aux services Microsoft.  

**Q. En quoi Peering Service diffère-t-il d’ExpressRoute ?**

R. Azure ExpressRoute est une connexion privée et dédiée à partir d’un ou de plusieurs emplacements clients. Si Peering Service offre une connectivité publique optimisée et ne prend en charge aucune connectivité privée, il offre également une connectivité optimisée pour les répartitions Internet locales.

## <a name="next-steps"></a>Étapes suivantes

- Pour découvrir Peering Service, consultez [Vue d’ensemble de Peering Service](about.md).
- Pour rechercher un fournisseur de services, consultez [Partenaires et emplacements de Peering Service](location-partners.md).
- Pour intégrer une connexion Peering Service, consultez [intégration de Peering Service](onboarding-model.md).
- Pour savoir comment inscrire une connexion Peering Service, consultez [Inscrire une connexion Peering Service – Portail Azure](azure-portal.md).
- Pour mesurer les données de télémétrie, consultez [Mesurer les données de télémétrie des connexions](measure-connection-telemetry.md).