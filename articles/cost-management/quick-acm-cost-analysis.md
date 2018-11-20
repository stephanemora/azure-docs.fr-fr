---
title: Démarrage rapide - Explorer les coûts Azure avec Analyse du coût | Microsoft Docs
description: Ce guide de démarrage rapide vous aide à utiliser l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/09/2018
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 40f3f56c15956a93176a753f7c66f66df1b8f5f4
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515724"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Démarrage rapide : Explorer et analyser les coûts avec Analyse du coût

Pour pouvoir contrôler et optimiser vos coûts Azure correctement, vous devez comprendre d’où proviennent les coûts au sein de votre organisation. Il est également utile de connaître le coût de vos services, notamment en fonction des environnements et des systèmes. La visibilité de la totalité des coûts est essentielle pour comprendre avec précision les modèles de dépenses de l’organisation. Les modèles de dépenses peuvent être utilisés pour appliquer des mécanismes de maîtrise des coûts, comme les budgets.

Dans ce guide de démarrage rapide, vous utilisez l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation. Vous pouvez voir les coûts agrégés par l’organisation afin de comprendre d’où ils viennent et d’identifier les tendances de dépenses. Vous pouvez aussi voir les coûts cumulés au fil du temps pour évaluer des tendances mensuelles, trimestrielles ou même annuelles par rapport à un budget. Un budget permet de faire respecter des contraintes financières. Il est aussi utilisé pour voir les coûts de chaque jour ou de chaque mois afin d’isoler les irrégularités dans les dépenses. De plus, vous pouvez télécharger les données du rapport actuel en vue de les analyser de manière plus approfondie ou de les utiliser dans un système externe.

Dans ce guide de démarrage rapide, vous apprenez à :

- Passer en revue les coûts dans l’analyse du coût
- Personnaliser les vues des coûts
- Télécharger les données de l’analyse du coût


## <a name="prerequisites"></a>Prérequis

L’analyse du coût est accessible à tous les clients bénéficiant d’un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/). Vous devez au moins disposer d’un accès en lecture à une ou plusieurs des étendues suivantes pour afficher les données de coût. Pour plus d’informations sur l’attribution de l’accès aux données Cost Management, consultez [Assigner l’accès aux données](assign-access-acm-data.md).

- Compte de facturation
- department
- Compte d’inscription
- Groupe d’administration
- Abonnement
- Groupe de ressources

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur http://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Passer en revue les coûts dans l’analyse du coût

Pour passer en revue vos coûts dans l’analyse des coûts, dans le portail Azure, accédez à **Gestion des coûts + facturation** &gt; **Cost Management** &gt; **Modifier l’étendue**, choisissez une étendue, puis cliquez sur **Sélectionner**.

L’étendue que vous sélectionnez est utilisée à travers Cost Management pour consolider les données et contrôler l’accès aux informations sur les coûts. Quand vous utilisez des étendues, vous n’opérez pas une sélection multiple. Vous sélectionnez plutôt une étendue plus grande qui en englobe d’autres, puis vous appliquez un filtre pour trouver ce qui vous intéresse. Il est important de bien comprendre ceci, car certaines personnes ne doivent pas avoir accès à une étendue parente englobant des étendues enfants.

Cliquez sur **Ouvrir l’analyse des coûts**.

L’affichage initial de l’analyse du coût inclut les zones suivantes :

**Total** : indique le total des coûts pour le mois actuel.

**Budget** : indique la limite de dépenses planifiée pour l’étendue sélectionnée, si disponible.

**Coût cumulé** : indique le total de dépenses quotidiennes cumulées, à partir du début du mois. Après avoir [créé un budget](tutorial-acm-create-budgets.md) pour votre compte de facturation ou votre abonnement, vous pouvez rapidement voir la tendance de vos dépenses par rapport au budget. Placez le curseur sur une date pour voir le coût cumulé de cette journée.

**(Graphiques croisés dynamiques (anneau)** – fournissent des tableaux croisés dynamiques où le coût total est détaillé par un ensemble commun de propriétés standard. Ils montrent les coûts cumulés, du plus élevé au plus faible, pour le mois en cours. Vous pouvez changer les graphiques croisés dynamiques à tout moment en sélectionnant un critère de croisement différent. Les coûts sont classés par : service (catégorie comptage), localisation (région) et étendue enfant par défaut. Par exemple, les comptes d’inscription sous les comptes de facturation, les groupes de ressources sous les abonnements et les ressources sous les groupes de ressources.

![Affichage initial de l’analyse du coût](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personnaliser les vues des coûts

L’affichage par défaut fournit des réponses rapides aux questions courantes telles que :

- Combien ai-je dépensé ?
- Est-ce que je vais respecter mon budget ?

Toutefois, il existe de nombreux cas où vous avez besoin d’une analyse plus approfondie. La personnalisation démarre en haut de la page, avec la sélection de la date.

L’analyse du coût affiche les données du mois en cours par défaut. Utilisez le sélecteur de date pour passer rapidement au mois dernier, au mois actuel, au trimestre civil actuel, à l’année civile actuelle ou à une plage de dates personnalisée de votre choix. La sélection du dernier mois est le moyen le plus rapide pour analyser votre dernière facture Azure et effectuer facilement le rapprochement des frais. Les options du trimestre actuel et de l’année actuelle facilitent le suivi des coûts par rapport aux budgets à long terme. Vous pouvez également sélectionner une autre plage de dates. Par exemple, vous pouvez sélectionner un seul jour, les sept derniers jours ou même des jours qui remontent jusqu’à un an.

![Sélecteur de date](./media/quick-acm-cost-analysis/date-selector.png)

L’analyse du coût montre les coûts **cumulés** par défaut. Les coûts cumulés incluent tous les coûts de chaque jour plus des jours précédents, pour obtenir une vue toujours plus complète de vos coûts quotidiens cumulés. Cette vue est optimisée pour montrer les tendances par rapport à un budget pendant l’intervalle de temps sélectionné.

Il existe aussi la vue **quotidienne** qui montre les coûts de chaque jour. La vue quotidienne n’affiche pas de tendance géométrique. Elle est conçue pour montrer les irrégularités comme les pics ou les chutes de dépenses de chaque jour. Si vous avez sélectionné un budget, la vue quotidienne indique aussi une estimation de ce à quoi peut ressembler votre budget quotidien. Quand vos coûts quotidiens sont constamment au-dessus du budget quotidien estimé, vous pouvez vous attendre à exploser votre budget mensuel. Le budget quotidien estimé est simplement un moyen pour vous aider à visualiser votre budget à un niveau inférieur. Si les coûts quotidiens présentent des fluctuations, la comparaison du budget quotidien estimé au budget mensuel est moins précise.

![Affichage quotidien](./media/quick-acm-cost-analysis/daily-view.png)

Vous pouvez **effectuer un regroupement** pour sélectionner une catégorie de groupe afin de changer l’affichage des données dans le graphique en aires Total supérieur. Le regroupement vous permet de voir rapidement la façon dont vos dépenses sont classées par type de ressource. Voici une vue des coûts des services Azure pour le mois dernier.

![Affichage cumulé quotidien regroupé](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Les graphiques croisés dynamiques sous la vue Total supérieure montrent les vues des différentes catégories de regroupement et de filtre. Quand vous sélectionnez une catégorie de groupe, l’ensemble complet des données pour l’affichage total se trouve en bas de l’affichage. Voici un exemple de groupes de ressources.

![Données complètes pour l’affichage actuel](./media/quick-acm-cost-analysis/full-data-set.png)

L’image précédente montre les noms de groupe de ressources. L’affichage des étiquettes des ressources n’est pas disponible dans aucune des vues de l’analyse de coût, ni aucun des filtres ou regroupements.

Lorsque vous regroupez les coûts en fonction d’un attribut spécifique, les dix principaux contributeurs aux coûts s’affichent dans l’ordre décroissant. S’il existe plus de dix groupes, les neuf principaux contributeurs aux coûts apparaissent en même temps qu’un groupe **Autres** qui réunit tous les groupes restants.

Les ressources de machines virtuelles, de mise en réseau et de stockage *Classic* (Azure Service Management ou ASM) ne partagent pas de données de facturation détaillées. Elles sont fusionnées sous forme de **services Classic** lors du regroupement des coûts.


## <a name="download-cost-analysis-data"></a>Télécharger les données de l’analyse du coût

Vous pouvez **télécharger** les informations de l’analyse du coût pour générer un fichier CSV de toutes les données affichées dans le portail Azure. Les filtres ou le regroupement que vous appliquez sont dans le fichier. Les données sous-jacentes du graphique Total supérieur qui ne sont pas activement affichées sont dans le fichier CSV.

## <a name="next-steps"></a>Étapes suivantes

Passez au premier tutoriel pour apprendre à créer et à gérer des budgets.

> [!div class="nextstepaction"]
> [Créer et gérer des budgets](tutorial-acm-create-budgets.md)
