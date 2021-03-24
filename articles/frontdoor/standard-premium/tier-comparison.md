---
title: Documentation Azure Front Door Standard/Premium
description: Cet article fournit une vue d’ensemble des SKU Azure Front Door Standard et Premium et montre leurs différences.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: amsriva
ms.openlocfilehash: 1753f2bb649e73d7a5fe6c1cc32361a418ea7f63
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181803"
---
# <a name="overview-of-azure-front-door-standardpremium-sku-preview"></a>Vue d’ensemble des SKU Azure Front Door Standard/Premium (préversion)

> [!Note]
> Cette documentation est destinée à Azure Front Door Standard/Premium (préversion). Vous recherchez des informations sur Azure Front Door ? Affichez [ici](../front-door-overview.md).

Azure Front Door est proposé pour 3 références SKU différentes, [Azure Front Door](../front-door-overview.md),Azure Front Door Standard (préversion) et Azure Front Door Premium (préversion). Les références SKU Azure Front Door Standard/Premium associent les fonctionnalités d’Azure Front Door, Azure CDN Standard de Microsoft, Azure WAF dans une plateforme CDN de Cloud sécurisé unique avec protection intelligente contre les menaces.

> [!IMPORTANT]
> Azure Front Door Standard/Premium (préversion) est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [**Conditions supplémentaires relatives à l’utilisation des préversions de Microsoft Azure**](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

* **Les SKU Azure Front Door Standard** sont :

    * Optimisation de la distribution de contenu
    * Offre d’une accélération du contenu statique et dynamique
    * Équilibrage de charge global
    * Déchargement SSL
    * Gestion des domaines et des certificats
    * Analyse du trafic améliorée 
    * Fonctionnalités de sécurité de base

* La **référence SKU Azure Front Door Premium** développe les fonctionnalités de la référence SKU Standard et ajoute :

    * Des fonctionnalités de sécurité étendues dans WAF
    * Une protection bot
    * Une prise Private Link
    * Une intégration au renseignement sur les menaces et l’analytique de sécurité Microsoft. 

![Diagramme montrant une comparaison entre les références SKU Front Door.](../media/tier-comparison/tier-comparison.png)

## <a name="feature-comparison"></a>Comparaison des fonctionnalités

| Fonctionnalité |      Standard      |  Premium |
|----------|:-------------:|------:|
| Domaines personnalisés | Oui | Oui |
| Déchargement SSL | Oui | Oui |
| Mise en cache |  Oui  | Oui |
| Compression | Oui | Oui   |
| Équilibrage de charge global | Oui  | Oui |
| Routage de couche 7 | Oui | Oui |
| Réécrire URL | Oui | Oui |
| Moteur de règles | Oui | Oui |
| Origine privée (Private Link) | Non | Oui |
| WAF | Règles personnalisées uniquement | Oui |
| Protection bot | Non | Oui |
| Métriques et diagnostics améliorés | Oui | Oui |
| Rapport sur le trafic | Oui | Oui |
| Rapport de sécurité | Non | Oui | 

## <a name="next-steps"></a>Étapes suivantes

Découvrir comment [créer un service Front Door](create-front-door-portal.md)
