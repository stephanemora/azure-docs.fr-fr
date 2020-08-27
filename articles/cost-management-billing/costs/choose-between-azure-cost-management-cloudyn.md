---
title: Choisir entre Azure Cost Management et Cloudyn
description: Cet article vous permet de déterminer s’il est plus approprié de choisir Azure Cost Management ou Cloudyn pour satisfaire vos besoins en termes de gestion des coûts.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684964"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Choisir entre Azure Cost Management et Cloudyn

Cloudyn sera déprécié d’ici la fin de l’année 2020. Les fonctionnalités existantes de Cloudyn sont intégrées directement au portail Azure dans la mesure du possible. À l’exception des clients CSP, aucun nouveau client ne peut être intégré pour le moment. Le produit existant restera pris en charge jusqu’à ce qu’il soit complètement déprécié.

Microsoft a acquis Cloudyn et migre ses fonctionnalités de gestion des coûts à partir du portail Cloudyn en mode natif dans Azure. Pour utiliser les nouvelles fonctionnalités, connectez-vous au portail Azure et accédez à [Gestion des coûts et facturation](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) dans la liste des services Azure. Par rapport à Cloudyn, l’expérience native offre des performances améliorées et une latence pus faible des données d’environ huit heures.

La migration de fonctionnalités clés pour les catégories d’offres Accord Entreprise, Paiement à l’utilisation et MSDN vers Azure Cost Management est terminée. Les abonnements CSP sont en cours de migration vers Azure Cost Management.

Si vous avez une catégorie d’offres non encore migrée, vous devez continuer à utiliser le portail Cloudyn. Toute autre personne peut utiliser Azure Cost Management.

## <a name="recommended-services-by-offer"></a>Services recommandés par offre

| Offres Microsoft Azure | Service de gestion des coûts recommandé |
| --- | --- |
| Contrat Entreprise Azure | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Contrat client Microsoft | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Contrat client Microsoft pris en charge par les partenaires | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Fonctionnalités de Cost Management disponibles

Certaines des fonctionnalités suivantes sont disponibles dans Cloudyn, mais toutes sont désormais disponibles dans Azure Cost Management.

- API
- Recommandations d’optimisation des coûts Azure avec notamment :
    - Recommandations sur le dimensionnement approprié et l’arrêt des instances Azure
    - Recommandations pour Réservation Azure
- Budgets
- Analyse des coûts
- Exporter des données vers un compte de stockage Azure
- Latence plus faible
- Application modèle Power BI
- Prise en charge des étiquettes de ressources
- Prise en charge de l’analyse des coûts inter-cloud pour AWS

## <a name="next-steps"></a>Étapes suivantes
- Apprenez-en davantage sur [Azure Cost Management](../cost-management-billing-overview.md).