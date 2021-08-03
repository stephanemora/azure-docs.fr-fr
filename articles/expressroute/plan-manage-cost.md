---
title: Planifier la gestion des coûts pour Azure ExpressRoute
description: Découvrez comment planifier et gérer les coûts pour Azure ExpressRoute avec l’analyse du coût dans le portail Azure.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 05/18/2021
ms.openlocfilehash: c4a2113a5aa95c1bacf7be0f4b376b9377412371
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111971685"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planifier et gérer les coûts pour Azure ExpressRoute

Cet article explique comment planifier et gérer les coûts d’Azure ExpressRoute. Vous devez d’abord utiliser la calculatrice de prix Azure pour planifier les coûts ExpressRoute avant d’ajouter des ressources pour le service aux coûts estimés. Ensuite, lorsque vous ajoutez les ressources Azure, passez en revue les coûts estimés. 

Une fois que vous avez commencé à utiliser des ressources Azure ExpressRoute, utilisez les fonctionnalités de Cost Management pour définir des budgets et surveiller les coûts. Vous pouvez également passer en revue les coûts prévus et déterminer les tendances des dépenses pour identifier les domaines où vous pourriez agir. Les coûts pour Azure ExpressRoute ne représentent qu’une partie des coûts mensuels sur votre facture Azure. Cet article explique comment planifier et gérer les coûts d’Azure ExpressRoute. Cependant, vous êtes facturé pour tous les services et ressources Azure utilisés dans votre abonnement Azure, y compris les services tiers.

## <a name="prerequisites"></a>Prérequis

Analyse des coûts dans Cost Management prend en charge la plupart des types de compte Azure, mais pas tous. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour un compte Azure. Pour plus d’informations sur l’attribution de l’accès aux données Azure Cost Management, consultez [Assigner l’accès aux données](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Local ou Stratégies de routage sur une machine virtuelle Standard ou Premium

Azure ExpressRoute a trois références SKU de circuit différentes : [*Local*](./expressroute-faqs.md#expressroute-local), *Standard* et [*Premium*](./expressroute-faqs.md#expressroute-premium). La façon dont vous êtes facturé pour votre utilisation d’ExpressRoute varie en fonction de ces trois types de références SKU. Avec la référence SKU Local, vous êtes automatiquement facturé avec un forfait de données illimité. Avec la référence SKU Standard et Premium, vous pouvez choisir entre un forfait de données limité ou illimité. Toutes les données d’entrée sont gratuites, sauf lors de l’utilisation du module complémentaire Global Reach. Il est important de comprendre les types de références SKU et le forfait de données qui conviennent le mieux à votre charge de travail pour optimiser les coûts et le budget.

## <a name="estimate-costs-before-using-azure-expressroute"></a>Estimer les coûts avant d’utiliser Azure ExpressRoute

Utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/) pour estimer les coûts avant de créer un circuit Azure ExpressRoute. 

1. Sur la gauche, sélectionnez **Mise en réseau**, puis **Azure ExpressRoute** pour commencer. 

1. Sélectionnez la *Zone* appropriée en fonction de votre emplacement de peering. Consultez [Fournisseurs de connectivité ExpressRoute](./expressroute-locations-providers.md#partners) pour sélectionner la *Zone* appropriée dans la liste déroulante. 

1. Sélectionnez ensuite la *référence SKU*, la *vitesse du circuit* et le *forfait de données* pour lesquels vous voulez obtenir une estimation. 

1. Dans *Transfert de données sortantes supplémentaires*, entrez une estimation en Go de la quantité de données sortantes que vous pouvez utiliser au cours d’un mois. 

1. Enfin, vous pouvez ajouter le *module complémentaire Global Reach* à l’estimation.

La capture d’écran suivante montre l’estimation des coûts effectuée par la calculatrice :

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Estimation des coûts ExpressRoute dans la calculatrice Azure":::

Pour plus d'informations, consultez [Tarification d'Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Coût estimé de la passerelle ExpressRoute

Si vous utilisez une passerelle ExpressRoute pour lier un réseau virtuel au circuit ExpressRoute, procédez comme suit pour estimer le coût de l’utilisation de la passerelle.

1. Sur la gauche, sélectionnez **Mise en réseau**, puis **Passerelle VPN** pour commencer. 

1. Sélectionnez la *région* pour la passerelle, puis modifiez le *type* sur **Passerelles ExpressRoute**.

1. Dans la liste déroulante, sélectionnez le *type de passerelle*.

1. Entrez les *heures de la passerelle*. (720 heures = 30 jours)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Comprendre le modèle de facturation complet pour ExpressRoute

Azure ExpressRoute s’exécute sur l’infrastructure Azure qui engendre des coûts en même temps qu’ExpressRoute lorsque vous déployez la nouvelle ressource. Il est important de comprendre qu’une infrastructure supplémentaire peut accumuler des frais. Vous devez gérer ce coût lorsque vous apportez des modifications aux ressources déployées. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Coûts qui s’accumulent généralement avec ExpressRoute

#### <a name="expressroute"></a>ExpressRoute

Lorsque vous créez un circuit ExpressRoute, vous pouvez choisir de créer une passerelle ExpressRoute pour relier vos réseaux virtuels au circuit. Les passerelles ExpressRoute sont facturées à un tarif horaire auquel s’ajoute le coût d’un circuit ExpressRoute. Consultez [Tarification d’ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) et sélectionnez Passerelles ExpressRoute pour afficher les tarifs des différentes références SKU de passerelle.

Le transfert de données entrantes est inclus dans le coût mensuel du circuit ExpressRoute pour les trois références SKU. Le transfert de données sortantes est uniquement inclus pour un plan de données illimité. Pour un plan de facturation à l’usage, le transfert de données sortantes est facturé par Go utilisé en fonction du numéro de zone de la [localisation du Peering](expressroute-locations-providers.md#partners).

#### <a name="expressroute-direct"></a>ExpressRoute Direct

ExpressRoute Direct a des frais de port mensuels qui incluent les frais de circuit pour les circuits ExpressRoute de la référence SKU Local et Standard. Pour les circuits de la référence SKU Premium, des frais de circuit supplémentaires sont facturés. Le transfert de données sortantes est facturé par Go utilisé en fonction du numéro de zone de la localisation du Peering. Les frais de transfert de données sortantes s’appliquent uniquement aux références SKU Standard et Premium.
 
#### <a name="expressroute-global-reach"></a>Service Global Reach d’ExpressRoute

ExpressRoute Global Reach est un module complémentaire que vous pouvez activer pour ExpressRoute et ExpressRoute Direct pour relier les circuits ExpressRoute entre eux. Le transfert de données entrantes et sortantes est facturé par Go utilisé en fonction du numéro de zone de la localisation du Peering.

### <a name="costs-might-accrue-after-resource-deletion"></a>Coûts qui peuvent s’accumuler après la suppression de la ressource

Si vous avez une passerelle ExpressRoute, après avoir supprimé le circuit ExpressRoute, vous êtes toujours facturé pour le coût jusqu’à ce que vous la supprimiez.

### <a name="using-azure-prepayment-credit"></a>Utilisation d’un crédit de paiement anticipé Azure

Vous pouvez payer les frais ExpressRoute avec votre crédit Paiement anticipé Azure (anciennement appelé « engagement financier »). Vous ne pouvez cependant pas utiliser le crédit Paiement anticipé Azure pour payer des frais pour des produits et services tiers, notamment ceux de la Place de marché Azure.

## <a name="monitor-costs"></a>Superviser les coûts

À mesure que vous utilisez des ressources Azure avec ExpressRoute, vous générez des coûts. Les coûts unitaires d’utilisation des ressources Azure varient selon les intervalles de temps (secondes, minutes, heures et jours) ou selon l’utilisation d’unités (octets, mégaoctets, etc.). Dès que l’utilisation d’ExpressRoute démarre, les coûts sont générés et vous pouvez voir les coûts dans [Analyse des coûts](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Quand vous utilisez l’analyse des coûts, vous affichez les coûts du circuit ExpressRoute dans des graphes et des tableaux pour différents intervalles de temps. Par exemple, par jour, par année ou pour le mois en cours ou le mois précédent. Vous pouvez aussi afficher les coûts par rapport aux budgets et aux coûts prévus. Passez à des vues pour des périodes plus longues pour identifier les tendances des dépenses. Ceci vous permet de voir où des dépassements ont pu se produire. Si vous avez créé des budgets, vous pouvez aussi facilement voir à quel moment ils ont été dépassés.

Pour voir les coûts ExpressRoute dans l’analyse des coûts :

1. Connectez-vous au portail Azure.

1. Accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse des coûts** dans le menu. Sélectionnez **Étendue** pour passer à une autre étendue dans l’analyse des coûts. Par défaut, le coût des services est affiché dans le premier graphique en anneau.

    Les coûts mensuels réels s’affichent lors de l’ouverture initiale de l’analyse des coûts. Voici un exemple qui montre tous les coûts d’utilisation mensuelle.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Exemple montrant les coûts cumulés pour un abonnement":::
    

1.  Pour limiter les coûts à un seul service, par exemple ExpressRoute, sélectionnez **Ajouter un filtre**, puis **Nom du service**. Sélectionnez ensuite **ExpressRoute**.

    Voici un exemple montrant les coûts d’ExpressRoute uniquement.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Exemple montrant les coûts cumulés pour ExpressRoute":::

Dans l’exemple précédent, vous avez pu voir le coût actuel du service. Les coûts par région Azure (emplacements) et les coûts ExpressRoute par groupe de ressources sont également présentés. À partir de là, vous pouvez explorer les coûts par vous-même.

## <a name="create-budgets-and-alerts"></a>Créer des budgets et des alertes

Vous pouvez créer des [budgets](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) pour gérer les coûts et des [alertes](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) permettant d’avertir automatiquement des parties prenantes en cas d’anomalies de dépenses et de risques de dépenses excessives. Les alertes sont basées sur les dépenses par rapport aux seuils de budget et de coût. Les budgets et les alertes sont créés pour les abonnements et les groupes de ressources Azure : ils sont donc utiles dans le cadre d’une stratégie globale de supervision des coûts. 

Vous pouvez créer des budgets avec des filtres pour des ressources ou des services spécifiques dans Azure si vous souhaitez disposer d’une plus grande granularité dans votre analyse. Les filtres vous permettent de vous assurer que vous ne créez pas accidentellement de nouvelles ressources entraînant un surcoût. Pour plus d’informations sur les options de filtre lorsque vous créez un budget, consultez [Options de regroupement et de filtre](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Exporter des données de coûts

Vous pouvez également [exporter vos données de coûts](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) vers un compte de stockage. C’est utile quand vous ou d’autres personnes avez besoin d’effectuer des analyses supplémentaires des données concernant les coûts. Par exemple, une équipe Finance peut analyser les données avec Excel ou Power BI. Vous pouvez exporter vos coûts selon une planification quotidienne, hebdomadaire ou mensuelle, et définir une plage de dates personnalisée. L’exportation des données des coûts est la méthode recommandée pour récupérer les jeux de données des coûts.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez plus en détail comment fonctionnent les tarifs avec Azure ExpressRoute. Voir [Vue d’ensemble de la tarification Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).
- Découvrez [comment optimiser votre investissement cloud avec Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Apprenez-en davantage sur la gestion des coûts avec l’[analyse du coût](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Découvrez comment [éviter des coûts imprévus](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Suivez le cours d’apprentissage guidé [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).