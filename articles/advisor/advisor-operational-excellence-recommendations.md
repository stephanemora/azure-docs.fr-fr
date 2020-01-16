---
title: Améliorer l’excellence opérationnelle pour vos abonnements Azure avec Azure Advisor
description: Utiliser Advisor pour optimiser l’excellence opérationnelle de vos abonnements Azure
ms.topic: article
ms.date: 10/24/2019
ms.openlocfilehash: f34284ba62bd5dea98345ebe73365b332d38ee78
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443071"
---
# <a name="achieve-operational-excellence-with-azure-advisor"></a>Atteindre l’excellence opérationnelle avec Azure Advisor

Les recommandations d’Azure Advisor en matière d’excellence opérationnelle aident les clients en lien avec l’efficacité des processus et des flux de travail, ainsi qu’avec les meilleures pratiques en matière de gestion et de déploiement des ressources. Vous pouvez obtenir ces recommandations du Advisor sous l’onglet **Excellence opérationnelle** du tableau de bord Advisor.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Créer des alertes Azure Service Health pour être averti lorsque des problèmes Azure vous concernent

Vous recommandons de configurer des alertes Azure Service Health pour recevoir une notification lorsque des problèmes Azure vous concernent. [Azure Service Health](https://azure.microsoft.com/features/service-health/) est un service gratuit qui offre conseils et support personnalisés lorsque vous êtes concerné par un problème de service Azure. Advisor identifie les abonnements qui n’ont pas d’alertes configurées et recommande de créer une.

## <a name="design-your-storage-accounts-to-prevent-hitting-the-maximum-subscription-limit"></a>Concevoir vos comptes de stockage de manière à éviter d’atteindre le nombre maximum d’abonnements

Une région Azure peut prendre en charge un maximum de 250 comptes de stockage par abonnement. Une fois cette limite atteinte, vous ne pourrez plus créer de comptes de stockage dans cette combinaison région/abonnement. Azure Advisor vérifie vos abonnements et affiche des suggestions pour vous aider à concevoir pour un moindre nombre de comptes de stockage lorsque vous êtes près d’atteindre la limite maximale.

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Assurez-vous d’avoir accès à des experts du cloud Azure chaque fois que vous en avez besoin

Lors de l’exécution d’une charge de travail critique, il est important d’avoir accès au support technique en cas de besoin. Advisor identifie les éventuels abonnements critiques dont le plan de support ne comprend aucun accès au support technique et recommande d’effectuer une mise à niveau pour choisir une option qui inclut le support technique.

## <a name="repair-invalid-log-alert-rules"></a>Réparer les règles d’alerte de journal invalides

Azure Advisor détectera les règles d’alerte comportant des requêtes non valides spécifiées dans leur section des conditions. Les règles d’alerte de journal sont créées dans Azure Monitor et sont utilisés pour exécuter des requêtes d’analytique à des intervalles spécifiés. Les résultats de la requête déterminent si une alerte doit être déclenchée. Les requêtes d’analytique peuvent devenir non valides au fil du temps en raison de modifications dans les ressources, les tables ou les commandes référencées. Advisor vous recommandera de corriger la requête dans la règle d’alerte pour éviter qu’elle ne se désactive automatiquement, ce qui garantit aussi une couverture de la supervision de vos ressources dans Azure. [En savoir plus sur la résolution des problèmes liés aux règles d’alerte](https://aka.ms/aa_logalerts_queryrepair)

## <a name="follow-best-practices-using-azure-policy"></a>Suivre les meilleures pratiques à l’aide d’Azure Policy

Azure Policy est un service d’Azure que vous utilisez pour créer, affecter et gérer des stratégies. Ces stratégies appliquent différentes règles et différents effets à vos ressources. Vous trouverez ci-dessous des recommandations en matière de stratégie Azure pour vous aider à atteindre l’excellence opérationnelle : 
1. Gérez les balises à l’aide d’Azure Policy : Cette stratégie ajoute ou remplace la balise spécifiée et la valeur lors de la création ou de la mise à jour d’une ressource. Il est possible de corriger des ressources existantes en déclenchant une tâche de correction. En outre, cela ne modifie pas les balises sur les groupes de ressources.
2. Appliquez les exigences de conformité géographique à l’aide d’Azure Policy : Cette stratégie vous permet de restreindre les emplacements que votre organisation peut spécifier lors du déploiement de ressources. 
3. Spécifiez les références SKU de machine virtuelle autorisées pour les déploiements : Cette stratégie vous permet de spécifier un ensemble de références de machine virtuelle que votre organisation peut déployer.
4. Appliquer la règle « Auditer les machines virtuelles qui n’utilisent pas de disques managés » à l’aide d’Azure Policy
5. Utilisez la règle « Hériter d’une étiquette de groupes de ressources » à l’aide d’Azure Policy : La stratégie ajoute ou remplace la balise spécifiée et la valeur du groupe de ressources parent lors de la création ou de la mise à jour d’une ressource. Il est possible de corriger des ressources existantes en déclenchant une tâche de correction.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les recommandations d’Advisor, consultez les ressources suivantes :
* [Présentation du conseiller](advisor-overview.md)
* [Bien démarrer](advisor-get-started.md)
* [Recommandations du conseiller en matière de coûts](advisor-cost-recommendations.md)
* [Recommandations du conseiller en matière de performances](advisor-performance-recommendations.md)
* [Recommandations du conseiller en matière de haute disponibilité](advisor-high-availability-recommendations.md)
* [Recommandations du conseiller en matière de sécurité](advisor-security-recommendations.md)
