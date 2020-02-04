---
title: Vue d’ensemble de Cloudyn dans Azure | Microsoft Docs
description: Cloudyn est une solution de gestion des coûts multicloud qui vous aide à utiliser Azure et autres ressources cloud.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/24/2020
ms.topic: overview
ms.service: cost-management-billing
ms.reviewer: benshy
ms.custom: seodec18
ms.openlocfilehash: bfd00613a3949b29e2defcb6f97398a39091d0e6
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76774052"
---
# <a name="what-is-the-cloudyn-service"></a>Qu’est-ce que le service de Cloudyn ?

Cloudyn, affilié à Microsoft, vous permet de suivre l’utilisation du cloud et les dépenses liées à vos ressources Azure et celles d’autres fournisseurs de services cloud, notamment AWS et Google. Les rapports du tableau de bord vous aident à comprendre la répartition des coûts, de même que la rétrofacturation et la facturation interne. Cloudyn vous permet d’optimiser vos dépenses cloud en identifiant les ressources sous-utilisées que vous pouvez ainsi gérer et ajuster.

Pour regarder une vidéo d’introduction, consultez [Introduction to Azure Cloudyn](https://azure.microsoft.com/resources/videos/azure-cost-management-overview-and-demo).

Azure Cost Management offre des fonctionnalités similaires à Cloudyn. Azure Cost Management est une solution native de gestion des coûts Azure. Il vous permet d’analyser les coûts, de créer et de gérer des budgets, d’exporter des données, et de consulter des recommandations d’optimisation et d’agir en conséquence pour dépenser moins. Pour plus d’informations, consultez [Azure Cost Management](../cost-management-billing-overview.md).

Regardez la [vidéo Azure Cost Management et Cloudyn](https://www.youtube.com/watch?v=PmwFWwSluh8) pour savoir quand utiliser Azure Cost Management ou Cloudyn, en fonction des besoins de votre entreprise.

>[!VIDEO https://www.youtube.com/embed/PmwFWwSluh8]

## <a name="cloudyn-features-moving-to-azure-cost-management"></a>Fonctionnalités de Cloudyn migrant vers Azure Cost Management

Microsoft a acquis Cloudyn et migre ses fonctionnalités de gestion des coûts à partir du portail Cloudyn en mode natif dans Azure. Pour utiliser les nouvelles fonctionnalités, connectez-vous au portail Azure et accédez à [Gestion des coûts et facturation](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) dans la liste des services Azure. Par rapport à Cloudyn, l’expérience native offre des performances améliorées et une latence pus faible des données d’environ huit heures.

La migration de fonctionnalités clés pour les catégories d’offres Accord Entreprise, Paiement à l’utilisation et MSDN vers Azure Cost Management est terminée. Les abonnements CSP sont en cours de migration vers Azure Cost Management.

Si vous avez une catégorie d’offres non encore migrée, vous devez continuer à utiliser le portail Cloudyn. Toute autre personne peut utiliser Azure Cost Management.

| Offres et fonctionnalités Microsoft Azure | Service de gestion des coûts recommandé |
| --- | --- |
| Contrat Entreprise Azure | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |
| Prise en charge de l’analyse des coûts inter-cloud pour AWS (en préversion) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Recommandations AWS | [Cloudyn](https://azure.cloudyn.com) |

Certaines des fonctionnalités suivantes sont disponibles dans Cloudyn, mais toutes sont désormais disponibles dans Azure Cost Management.

- API
- Recommandations pour Calcul Azure
- Recommandations pour Réservation Azure
- Budgets
- Analyse des coûts
- Exporter des données vers un compte de stockage Azure
- Latence plus faible
- Pack de contenu et connecteur Power BI
- Prise en charge des étiquettes de ressources

## <a name="monitor-usage-and-spending"></a>Surveiller l’utilisation et les dépenses

Il est extrêmement important de surveiller l’utilisation et les dépenses en infrastructures cloud, car les organisations sont facturées sur les ressources qu’elles consomment dans le temps. Quand l’utilisation dépasse les seuils contractuels, les coûts peuvent augmenter de façon rapide et inattendue. Une supervision ad-hoc peut s’avérer difficile pour plusieurs raisons. Tout d’abord, la projection des coûts en fonction de l’utilisation moyenne suppose que votre consommation reste constante sur une période de facturation donnée. En second lieu, quand les coûts sont proches de votre budget ou le dépassent, il est important que vous en soyez averti à un stade précoce pour ajuster vos dépenses. Enfin, les fournisseurs de services cloud ne proposent pas nécessairement de rapports de projection des coûts par rapport aux seuils ou des rapports de comparaison de périodes.

Les rapports vous aident à surveiller les dépenses et ainsi d’analyser et suivre l’utilisation du cloud, les coûts et les tendances. Grâce aux rapports d’utilisation dans le temps, vous pouvez détecter des anomalies par rapport aux tendances normales. La mauvaise utilisation des ressources dans votre déploiement cloud est visible dans les rapports d’optimisation. Vous pouvez aussi identifier une mauvaise utilisation des ressources dans les rapports d’analyse des coûts.

## <a name="manage-costs"></a>Gérer les coûts

Les données historiques peuvent vous aider à gérer les coûts quand vous analysez l’utilisation et les coûts dans le temps pour identifier les tendances. Celles-ci permettent ensuite de prévoir les dépenses futures. Cloudyn propose aussi des rapports d’estimation des coûts très utiles.

La répartition des coûts permet de gérer les coûts en analysant vos coûts en fonction de votre stratégie de balisage. Vous pouvez utiliser des balises sur vos comptes, ressources et entités personnalisés pour affiner la répartition des coûts. Le Gestionnaire de catégories organise vos balises pour optimiser la gouvernance. Enfin, la répartition des coûts dans la rétrofacturation et la facturation interne met en évidence l’utilisation des ressources et les coûts associés dans le but d’influencer les comportements de consommation ou de facturer les clients du locataire.

Le contrôle d’accès facilite la gestion des coûts en autorisant les utilisateurs et les équipes à accéder uniquement aux données de gestion des coûts dont ils ont besoin. Vous vous servez de la structure des entités, de la gestion des utilisateurs et des rapports planifiés avec les listes de destinataires pour attribuer l’accès.

La gestion des coûts est aussi facilitée par les alertes, qui vous avertissent automatiquement en cas de dépenses inhabituelles ou excessives. Les alertes peuvent aussi être envoyées automatiquement à d’autres parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Plusieurs rapports prennent en charge les alertes basées sur les seuils budgétaires et de coûts. Toutefois, les alertes ne sont actuellement pas prises en charge pour les comptes ou abonnements de partenaires CSP.

## <a name="improve-efficiency"></a>Améliorer l’efficacité

Avec Cloudyn, vous pouvez déterminer quelle est l’utilisation optimale des machines virtuelles, identifier ou supprimer les machines virtuelles inactives, ainsi que les disques non attachées. Les informations fournies par les rapports d’optimisation du dimensionnement et les rapports de mauvaise utilisation des ressources permettent d’élaborer un plan visant à réduire la taille ou supprimer les machines virtuelles inactives. Toutefois, les rapports d’optimisation ne sont actuellement pas pris en charge pour les comptes ou abonnements de partenaires CSP.

Si vous avez approvisionné des instances réservées AWS, vous pouvez optimiser leur utilisation grâce aux rapports d’optimisation. Vous y trouverez des recommandations d’achat et ils vous permettront de modifier les réservations inutilisées et de planifier l’approvisionnement.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous en savez un peu plus sur Cloudyn, passez à l’étape suivante en inscrivant votre environnement cloud et commencez à explorer vos données.

- [Inscrire un abonnement Azure individuel](quick-register-azure-sub.md)
