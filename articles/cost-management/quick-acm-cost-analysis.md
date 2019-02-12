---
title: Démarrage rapide - Explorer les coûts Azure avec Analyse du coût | Microsoft Docs
description: Ce guide de démarrage rapide vous aide à utiliser l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2019
ms.topic: quickstart
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 931732c047a5ffe22ad456a115c36d7c882d01bc
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55769849"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Démarrage rapide : Explorer et analyser les coûts avec l’analyse du coût

Pour pouvoir contrôler et optimiser vos coûts Azure correctement, vous devez comprendre d’où proviennent les coûts au sein de votre organisation. Il est également utile de connaître le coût de vos services, notamment en fonction des environnements et des systèmes. La visibilité de la totalité des coûts est essentielle pour comprendre avec précision les modèles de dépenses de l’organisation. Les modèles de dépenses peuvent être utilisés pour appliquer des mécanismes de maîtrise des coûts, comme les budgets.

Dans ce guide de démarrage rapide, vous utilisez l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation. Vous pouvez voir les coûts agrégés par l’organisation afin de comprendre d’où ils viennent et d’identifier les tendances de dépenses. Vous pouvez aussi voir les coûts cumulés au fil du temps pour évaluer des tendances mensuelles, trimestrielles ou même annuelles par rapport à un budget. Un budget permet de faire respecter des contraintes financières. Il est aussi utilisé pour voir les coûts de chaque jour ou de chaque mois afin d’isoler les irrégularités dans les dépenses. De plus, vous pouvez télécharger les données du rapport actuel en vue de les analyser de manière plus approfondie ou de les utiliser dans un système externe.

Dans ce guide de démarrage rapide, vous apprenez à :

- Passer en revue les coûts dans l’analyse du coût
- Personnaliser les vues des coûts
- Télécharger les données de l’analyse du coût


## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge divers types de compte Azure. Pour afficher la liste complète des types de comptes pris en charge, consultez [Comprendre les données Cost Management](understand-cost-mgt-data.md). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure.

Pour les clients [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), vous devez disposer d’au moins un accès en lecture à une ou plusieurs des étendues suivantes pour afficher les données de coût.

- Compte de facturation
- department
- Compte d’inscription
- Groupe d’administration
- Abonnement
- Groupe de ressources

Pour plus d’informations sur l’attribution de l’accès aux données Cost Management, consultez [Assigner l’accès aux données](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Connexion à Azure

- Connectez-vous au portail Azure sur https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Passer en revue les coûts dans l’analyse du coût

Pour passer en revue vos coûts avec l’analyse des coûts, dans le portail Azure, accédez à **Cost Management + facturation** &gt; **Analyse du coût**. Sélectionner **Étendue : _ScopeName_**, choisissez une étendue, puis cliquez sur **Sélectionner**.

L’étendue que vous sélectionnez est utilisée à travers Cost Management pour consolider les données et contrôler l’accès aux informations sur les coûts. Quand vous utilisez des étendues, vous n’opérez pas une sélection multiple. À la place, vous sélectionnez une étendue plus grande qui englobe d’autres étendues, puis vous appliquez un filtre pour trouver ce qui vous intéresse. Il est important de bien comprendre ceci, car certaines personnes ne doivent pas avoir accès à une étendue parente englobant des étendues enfants.

Cliquez sur **Ouvrir l’analyse des coûts**.

L’affichage initial de l’analyse du coût inclut les zones suivantes :

**Total** : indique le total des coûts pour le mois actuel.

**Budget** : indique la limite de dépenses planifiée pour l’étendue sélectionnée, si disponible.

**Coût cumulé** : indique le total de dépenses quotidiennes cumulées, à partir du début du mois. Après avoir [créé un budget](tutorial-acm-create-budgets.md) pour votre compte de facturation ou votre abonnement, vous pouvez rapidement voir la tendance de vos dépenses par rapport au budget. Placez le curseur sur une date pour voir le coût cumulé de cette journée.

**(Graphiques croisés dynamiques (anneau)** – fournissent des tableaux croisés dynamiques où le coût total est détaillé par un ensemble commun de propriétés standard. Ils montrent les coûts cumulés, du plus élevé au plus faible, pour le mois en cours. Vous pouvez changer les graphiques croisés dynamiques à tout moment en sélectionnant un critère de croisement différent. Les coûts sont classés par : service (catégorie comptage), localisation (région) et étendue enfant par défaut. Par exemple, les comptes d’inscription sous les comptes de facturation, les groupes de ressources sous les abonnements et les ressources sous les groupes de ressources.

![Vue initiale de l’analyse des coûts dans le Portail Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personnaliser les vues des coûts

L’affichage par défaut fournit des réponses rapides aux questions courantes telles que :

- Combien ai-je dépensé ?
- Est-ce que je vais respecter mon budget ?

Toutefois, il existe de nombreux cas où vous avez besoin d’une analyse plus approfondie. La personnalisation démarre en haut de la page, avec la sélection de la date.

L’analyse du coût affiche les données du mois en cours par défaut. Utilisez le sélecteur de date pour passer rapidement au mois dernier, au mois actuel, au trimestre civil actuel, à l’année civile actuelle ou à une plage de dates personnalisée de votre choix. La sélection du dernier mois est le moyen le plus rapide pour analyser votre dernière facture Azure et effectuer facilement le rapprochement des frais. Les options du trimestre actuel et de l’année actuelle facilitent le suivi des coûts par rapport aux budgets à long terme. Vous pouvez également sélectionner une autre plage de dates. Par exemple, vous pouvez sélectionner un seul jour, les sept derniers jours ou même des jours qui remontent jusqu’à un an.

![Sélecteur de date affichant un exemple de sélection pour ce mois](./media/quick-acm-cost-analysis/date-selector.png)

L’analyse du coût montre les coûts **cumulés** par défaut. Les coûts cumulés incluent tous les coûts de chaque jour plus des jours précédents, pour obtenir une vue toujours plus complète de vos coûts quotidiens cumulés. Cette vue est optimisée pour montrer les tendances par rapport à un budget pendant l’intervalle de temps sélectionné.

Il existe aussi la vue **quotidienne** qui montre les coûts de chaque jour. La vue quotidienne n’affiche pas de tendance géométrique. Elle est conçue pour montrer les irrégularités comme les pics ou les chutes de dépenses de chaque jour. Si vous avez sélectionné un budget, la vue quotidienne indique aussi une estimation de ce à quoi peut ressembler votre budget quotidien. Quand vos coûts quotidiens sont constamment au-dessus du budget quotidien estimé, vous pouvez vous attendre à exploser votre budget mensuel. Le budget quotidien estimé est simplement un moyen pour vous aider à visualiser votre budget à un niveau inférieur. Si les coûts quotidiens présentent des fluctuations, la comparaison du budget quotidien estimé au budget mensuel est moins précise.

En général, attendez-vous à voir des données ou des notifications correspondant aux ressources utilisées dans les huit heures.

![Vue quotidienne présentant un exemple des coûts quotidiens pour le mois actuel](./media/quick-acm-cost-analysis/daily-view.png)

Vous pouvez **effectuer un regroupement** pour sélectionner une catégorie de groupe afin de changer l’affichage des données dans le graphique en aires Total supérieur. Le regroupement vous permet de voir rapidement comment vos dépenses sont classées par propriétés de ressources et d’utilisation communes, comme des étiquettes de ressources ou de groupes de ressources. Pour regrouper par étiquettes, sélectionnez la clé d’étiquette d’après laquelle vous souhaitez effectuer le regroupement. Vous verrez les coûts répartis selon la valeur de cette étiquette, avec un segment supplémentaire pour les ressources auxquelles cette étiquette n’a pas été appliquée.

La plupart des [ressources Azure prennent en charge l’étiquetage](../azure-resource-manager/tag-support.md), mais certaines étiquettes ne sont pas disponibles pour la gestion des coûts et la facturation. Par ailleurs, les étiquettes de groupe de ressources ne sont pas prises en charge. Cost Management prend uniquement en charge les étiquettes de ressources à compter de la date à laquelle les étiquettes sont appliquées directement à la ressource.

Voici une vue des coûts des services Azure pour le mois dernier.

![Vue cumulée quotidienne groupée présentant un exemple de coûts du service Azure pour le mois dernier](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Les graphiques croisés dynamiques sous le graphique principal affichent différents regroupements pour vous donner une vue d’ensemble de vos coûts globaux pour la période et les filtres sélectionnés. Sélectionnez une propriété ou une étiquette pour afficher les coûts agrégés d’après n’importe quelle dimension.


![Données complètes pour la vue actuelle présentant les noms de groupe de ressources](./media/quick-acm-cost-analysis/full-data-set.png)

L’image précédente montre les noms de groupe de ressources. Bien que vous puissiez regrouper par étiquette pour afficher le total des coûts par étiquette, l’affichage de toutes les étiquettes par ressource ou par groupe de ressources n’est disponible dans aucune vue d’analyse des coûts.

Lorsque vous regroupez les coûts en fonction d’un attribut spécifique, les dix principaux contributeurs aux coûts s’affichent dans l’ordre décroissant. S’il y a plus de dix groupes, les neuf principaux contributeurs aux coûts sont affichés. Un groupe **Autres**, qui rassemble tous les groupes restants, s’affiche également. Lors du regroupement par étiquettes, vous pouvez aussi voir un groupe **Non étiqueté** pour les coûts auxquels la clé d’étiquette n’a pas été appliquée. **Non étiqueté** est toujours mentionné en dernier, même s’il existe plus de coûts non étiquetés que de coûts étiquetés. S’il y a dix valeurs d’étiquette ou plus, les coûts non étiquetés sont inclus dans le groupe **Autres**.

Les ressources de machines virtuelles, de réseau et de stockage *Classic* (Azure Service Management ou ASM) ne partagent pas de données de facturation détaillées. Elles sont fusionnées dans les **services Classic** lors du regroupement des coûts.

Vous pouvez afficher le jeu de données complet pour n’importe quelle vue. Les sélections ou les filtres que vous appliquez ont une incidence sur les données présentées. Pour voir le jeu de données complet, cliquez sur la liste **Type de graphique**, puis cliquez sur l’affichage **Tableau**.

![Données de l’affichage actuel présentées dans un tableau](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Télécharger les données de l’analyse du coût

Vous pouvez **télécharger** les informations de l’analyse du coût pour générer un fichier CSV de toutes les données affichées dans le portail Azure. Les filtres ou le regroupement que vous appliquez sont dans le fichier. Les données sous-jacentes du graphique Total supérieur qui ne sont pas activement affichées sont dans le fichier CSV.

## <a name="next-steps"></a>Étapes suivantes

Passez au premier tutoriel pour apprendre à créer et à gérer des budgets.

> [!div class="nextstepaction"]
> [Créer et gérer des budgets](tutorial-acm-create-budgets.md)
