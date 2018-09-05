---
title: Explorer les coûts Azure avec l’analyse du coût | Microsoft Docs
description: Cet article vous aide à utiliser l’analyse du coût pour explorer et analyser vos coûts d’organisation Azure.
services: billing
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 08/16/2018
ms.topic: conceptual
ms.service: billing
manager: dougeby
ms.custom: ''
ms.openlocfilehash: db06956e01ed38ce1f8ee0ce92526be16733fd73
ms.sourcegitcommit: 58c5cd866ade5aac4354ea1fe8705cee2b50ba9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2018
ms.locfileid: "42818122"
---
# <a name="explore-and-analyze-costs-with-cost-analysis"></a>Explorer et analyser les coûts avec l’analyse du coût

Pour pouvoir contrôler et optimiser vos coûts Azure correctement, vous devez comprendre d’où proviennent les coûts au sein de votre organisation. Il est également utile de connaître le coût de vos services, notamment en fonction des environnements et des systèmes. La visibilité de la totalité des coûts est essentielle pour comprendre avec précision les modèles de dépenses de l’organisation, ce qui peut être utile pour appliquer des mécanismes de contrôle du coût, tels que des budgets.

Dans cet article, vous utilisez l’analyse du coût pour explorer et analyser vos coûts d’organisation Azure. Vous observez les coûts agrégés par votre organisation afin de comprendre leur origine et d’identifier les tendances de dépenses. Vous observez les coûts cumulés au fil du temps pour évaluer des tendances de coûts mensuelles, trimestrielles ou même annuelles par rapport à un budget. Un budget aide à respecter les contraintes financières ou à afficher les coûts quotidiens ou mensuels pour isoler des irrégularités dans les dépenses. De plus, vous pouvez télécharger les données du rapport actuel en vue de les analyser de manière plus approfondie ou de les utiliser dans un système externe.

## <a name="requirements"></a>Configuration requise

L’analyse du coût est disponible pour tous les clients détenant un Accord Entreprise (EA). Vous devez disposer d’un accès en lecture à au moins une des étendues suivantes pour afficher les données de coût.

- Compte de facturation Azure EA (inscription)
- Abonnement Azure EA
- Groupe de ressources d’abonnement Azure EA

## <a name="review-costs-in-cost-analysis"></a>Passer en revue les coûts dans l’analyse du coût

Pour passer en revue vos coûts avec l’analyse du coût, ouvrez le portail Azure, puis accédez à **Gestion des coûts + facturation** &gt; **Comptes de facturation** &gt; sélectionnez le compte de facturation lié à votre Accord Entreprise &gt; sous Gestion des coûts, sélectionnez **Analyse du coût**.

L’affichage initial de l’analyse du coût inclut les zones suivantes :

**Total** : indique le total des coûts pour le mois actuel.

**Budget** : indique la limite de dépenses planifiée pour l’étendue sélectionnée, si disponible.

**Coût cumulé** : indique le total de dépenses quotidiennes cumulées, à partir du début du mois. Si vous avez [créé un budget](billing-cost-management-budget-scenario.md#create-the-azure-budget) pour votre compte de facturation ou abonnement, vous pouvez rapidement voir vos tendances de dépenses par rapport au budget. Placez le curseur sur une date pour afficher le coût cumulé jusqu’à ce jour-là.

**Graphiques croisés dynamiques (en anneau)** : fournissent des informations croisées dynamiques. Ils décomposent le coût total en fonction d’un ensemble commun de propriétés standard. Les graphiques montrent les coûts cumulés, du plus grand au plus petit, pour le mois actuel. Vous pouvez changer les graphiques croisés dynamiques à tout moment en sélectionnant un critère de croisement différent. Par défaut, les coûts sont répartis par service (catégorie du compteur), emplacement (région) et étendue enfant (par exemple, les comptes d’inscription sous les comptes de facturation, les groupes de ressources sous les abonnements et les ressources sous les groupes de ressources).

![Affichage initial de l’analyse du coût](./media/billing-cost-analysis/cost-analysis-01.png)



## <a name="customizing-cost-views"></a>Personnalisation des vues des coûts

L’affichage par défaut fournit des réponses rapides aux questions courantes telles que :

- Combien ai-je dépensé ?
- Est-ce que je vais respecter mon budget ?

Toutefois, il existe de nombreux cas où vous avez besoin d’une analyse plus approfondie. La personnalisation démarre en haut de la page, avec la sélection de la date.

Par défaut, l’analyse du coût affiche les données pour le mois actuel. Utilisez le sélecteur de date pour passer rapidement au dernier mois, au mois actuel, au trimestre civil actuel, à l’année civile actuelle ou à une plage de dates personnalisée de votre choix. La sélection du dernier mois est le moyen le plus rapide pour analyser votre dernière facture Azure et effectuer facilement le rapprochement des frais. Les options du trimestre actuel et de l’année actuelle facilitent le suivi des coûts par rapport aux budgets à long terme. Si vous le souhaitez, vous pouvez sélectionner une plage de dates plus précise ou plus large, allant d’un seul jour aux sept derniers jours, ou même jusqu’à un an avant le mois actuel.

![Sélecteur de date](./media/billing-cost-analysis/date-selector.png)

En outre, par défaut, l’analyse du coût montre les coûts **cumulés**. Les coûts cumulés incluent tous les coûts pour chaque jour en plus des jours précédents, enrichissant constamment l’affichage des coûts quotidiens cumulés. Cet affichage est optimisé pour montrer les tendances par rapport à un budget pour l’intervalle de temps sélectionné.

Il existe également l’affichage **quotidien**. Il montre les coûts pour chaque jour et n’affiche pas de tendance de croissance. L’affichage quotidien est optimisé pour montrer les irrégularités liées à une forte augmentation ou diminution des coûts d’un jour à l’autre. Quand vous sélectionnez un budget, l’affichage quotidien montre également une estimation de ce à quoi peut ressembler votre budget quotidien. Si vos coûts quotidiens sont constamment supérieurs au budget quotidien estimé, vous pouvez vous attendre à dépasser votre budget mensuel. Le budget quotidien estimé est simplement un moyen pour vous aider à visualiser votre budget à un niveau inférieur. Si les coûts quotidiens présentent des fluctuations, la comparaison du budget quotidien estimé au budget mensuel est moins précise.

![Affichage quotidien](./media/billing-cost-analysis/daily-view.png)

Vous pouvez **effectuer un regroupement** pour sélectionner une catégorie de groupe afin de changer l’affichage des données dans le graphique en aires Total supérieur. Le regroupement vous permet de voir rapidement la façon dont vos dépenses sont classées par type de ressource. Voici un aperçu des coûts des services Azure pour un affichage du dernier mois.

![Affichage cumulé quotidien regroupé](./media/billing-cost-analysis/grouped-daily-accum-view.png)

Les graphiques croisés dynamiques se trouvent sous l’affichage Total supérieur. Utilisez-les pour obtenir des affichages en fonction de différentes catégories de regroupement et de filtrage. Quand vous sélectionnez une catégorie de groupe, l’ensemble complet des données pour l’affichage total se trouve en bas de l’affichage. Voici un exemple de groupes de ressources.

![Données complètes pour l’affichage actuel](./media/billing-cost-analysis/full-data-set.png)

### <a name="download-cost-analysis-data"></a>Télécharger les données de l’analyse du coût

Quand vous **téléchargez** des informations à partir de l’analyse du coût, un fichier CSV est généré pour toutes les données affichées dans le portail Azure. Si vous avez appliqué des filtres ou des regroupements, ils sont inclus dans le fichier. Certaines données sous-jacentes pour le graphique Total supérieur qui ne sont pas activement affichées dans le portail sont également incluses dans le fichier CSV.

## <a name="next-steps"></a>Étapes suivantes

Consultez une autre [documentation sur la facturation et la gestion des coûts Azure](billing-cost-management-budget-scenario.md).
