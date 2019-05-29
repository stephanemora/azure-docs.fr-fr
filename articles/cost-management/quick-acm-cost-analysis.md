---
title: Démarrage rapide - Explorer les coûts Azure avec Analyse du coût | Microsoft Docs
description: Ce guide de démarrage rapide vous aide à utiliser l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: b4302713188237b97ffbe8473f6a37edd6741b36
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65793089"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Démarrage rapide : Explorer et analyser les coûts avec l’analyse du coût

Pour pouvoir contrôler et optimiser vos coûts Azure correctement, vous devez comprendre d’où proviennent les coûts au sein de votre organisation. Il est également utile de connaître le coût de vos services, notamment en fonction des environnements et des systèmes. La visibilité de la totalité des coûts est essentielle pour comprendre avec précision les modèles de dépenses de l’organisation. Les modèles de dépenses peuvent être utilisés pour appliquer des mécanismes de maîtrise des coûts, comme les budgets.

Dans ce guide de démarrage rapide, vous utilisez l’analyse du coût pour explorer et analyser les coûts Azure de votre organisation. Vous pouvez voir les coûts agrégés par l’organisation afin de comprendre d’où ils viennent et d’identifier les tendances de dépenses. Vous pouvez aussi voir les coûts cumulés au fil du temps pour évaluer des tendances mensuelles, trimestrielles ou même annuelles par rapport à un budget. Un budget permet de faire respecter des contraintes financières. Il est aussi utilisé pour voir les coûts de chaque jour ou de chaque mois afin d’isoler les irrégularités dans les dépenses. De plus, vous pouvez télécharger les données du rapport actuel en vue de les analyser de manière plus approfondie ou de les utiliser dans un système externe.

Dans ce guide de démarrage rapide, vous apprenez à :

- Passer en revue les coûts dans l’analyse du coût
- Personnaliser les vues des coûts
- Télécharger les données de l’analyse du coût


## <a name="prerequisites"></a>Prérequis

L’analyse des coûts prend en charge différents types de comptes Azure. Pour accéder à la liste complète des types de comptes pris en charge, voir [Comprendre les données de Cost Management](understand-cost-mgt-data.md). Pour afficher les données de coût, vous avez au minimum besoin d’un accès en lecture pour votre compte Azure.

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

Pour examiner vos coûts via la fonctionnalité d’analyse des coûts, ouvrez l’étendue dans le portail Azure et sélectionnez **Analyse du coût** dans le menu. Par exemple, accédez à **Abonnements**, sélectionnez un abonnement dans la liste, puis sélectionnez **Analyse du coût** dans le menu. Utilisez le paramètre **Étendue** pour passer à une autre étendue dans l’analyse des coûts. Pour plus d’informations sur les étendues, consultez [Comprendre et utiliser les étendues](understand-work-scopes.md).

L’étendue que vous sélectionnez est utilisée à travers Cost Management pour consolider les données et contrôler l’accès aux informations sur les coûts. Quand vous utilisez des étendues, vous n’opérez pas une sélection multiple. Au lieu de cela, vous sélectionnez une étendue plus grande qui en regroupe d'autres, puis vous filtrez jusqu'aux portées imbriquées dont vous avez besoin. Il est important de comprendre cette approche, car il est possible que certaines personnes n’aient pas accès à une étendue parente unique qui couvre plusieurs étendues imbriquées.

L’affichage initial de l’analyse du coût inclut les zones suivantes :

**Total** : indique le total des coûts pour le mois actuel.

**Budget** : indique la limite de dépenses planifiée pour l’étendue sélectionnée, si disponible.

**Coûts cumulés** : indique le total des dépenses quotidiennes cumulées à partir du début du mois. Après avoir [créé un budget](tutorial-acm-create-budgets.md) pour votre compte de facturation ou votre abonnement, vous pouvez rapidement voir la tendance de vos dépenses par rapport au budget. Placez le curseur sur une date pour voir le coût cumulé de cette journée.

**(Graphiques croisés dynamiques (anneau)** – fournissent des tableaux croisés dynamiques où le coût total est détaillé par un ensemble commun de propriétés standard. Ils montrent les coûts, du plus élevé au plus faible, pour le mois en cours. Vous pouvez changer les graphiques croisés dynamiques à tout moment en sélectionnant un critère de croisement différent. Les coûts sont classés par : service (catégorie comptage), localisation (région) et étendue enfant par défaut. Par exemple, les comptes d’inscription sous les comptes de facturation, les groupes de ressources sous les abonnements et les ressources sous les groupes de ressources.

![Vue initiale de l’analyse des coûts dans le Portail Azure](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Personnaliser les vues des coûts

L’analyse des coûts dispose de quatre affichages intégrés, optimisés pour les objectifs les plus courants :

Affichage | Répondre aux questions comme...
--- | ---
Coûts cumulés | Combien ai-je dépensé jusqu’ici ce mois-ci ? Est-ce que je vais respecter mon budget ?
Coûts quotidiens | Y a-t-il eu une augmentation des coûts par jour pour les 30 derniers jours ?
Coût par service | Comment mon utilisation mensuelle a-t-elle varié sur les 3 dernières factures ?
Coût par ressource | Quelles ressources ont été les plus onéreuses jusqu’ici ce mois-ci ?

![Sélecteur d’affichage montrant un exemple de sélection pour ce mois](./media/quick-acm-cost-analysis/view-selector.png)

Toutefois, il existe de nombreux cas où vous avez besoin d’une analyse plus approfondie. La personnalisation démarre en haut de la page, avec la sélection de la date.

L’analyse du coût affiche les données du mois en cours par défaut. Utilisez le sélecteur de date pour passer rapidement à des plages de dates communes. Quelques exemples incluent les sept derniers jours, le mois dernier, l’année en cours ou une plage de dates personnalisée. Les abonnements de paiement à l’utilisation incluent également des plages de dates en fonction de votre période de facturation (qui n’est pas liée au mois calendaire), telles que la période de facturation actuelle ou la dernière facture. Utilisez les liens **< PRÉCÉDENT** et **SUIVANT >** en haut du menu pour accéder respectivement à la période précédente ou suivante. Par exemple, **< PRÉCÉDENT** bascule des sept derniers jours à 8 à 14 jours, puis à 15 à 21 jours.

![Sélecteur de date affichant un exemple de sélection pour ce mois](./media/quick-acm-cost-analysis/date-selector.png)

L’analyse du coût montre les coûts **cumulés** par défaut. Les coûts cumulés incluent tous les coûts de chaque jour plus des jours précédents, pour obtenir une vue en évolution constante de vos coûts quotidiens cumulés. Cette vue est optimisée pour montrer les tendances par rapport à un budget pendant l’intervalle de temps sélectionné.

Il existe aussi la vue **quotidienne** qui montre les coûts de chaque jour. La vue quotidienne n’affiche pas de tendance géométrique. Elle est conçue pour montrer les irrégularités comme les pics ou les chutes de dépenses de chaque jour. Si vous avez sélectionné un budget, la vue quotidienne indique aussi une estimation de ce à quoi peut ressembler votre budget quotidien. Quand vos coûts quotidiens sont constamment au-dessus du budget quotidien estimé, vous pouvez vous attendre à exploser votre budget mensuel. Le budget quotidien estimé est simplement un moyen pour vous aider à visualiser votre budget à un niveau inférieur. Si les coûts quotidiens présentent des fluctuations, la comparaison du budget quotidien estimé au budget mensuel est moins précise.

En général, attendez-vous à voir des données ou des notifications correspondant aux ressources utilisées dans les 8 à 12 heures suivantes.

![Vue quotidienne présentant un exemple des coûts quotidiens pour le mois actuel](./media/quick-acm-cost-analysis/daily-view.png)

**Regrouper par** propriétés communes pour ventiler les coûts et d’identifier les principaux contributeurs. Pour regrouper par balises de ressources, par exemple, sélectionnez la clé de balise d’après laquelle vous souhaitez effectuer le regroupement. Les coûts sont ventilés selon la valeur de chaque balise, avec un segment supplémentaire pour les ressources auxquelles cette balise n’a pas été appliquée.

La plupart des [ressources Azure prennent en charge l’étiquetage](../azure-resource-manager/tag-support.md), mais certaines étiquettes ne sont pas disponibles pour la gestion des coûts et la facturation. Par ailleurs, les étiquettes de groupe de ressources ne sont pas prises en charge. Cost Management prend uniquement en charge les étiquettes de ressources à compter de la date à laquelle les étiquettes sont appliquées directement à la ressource. Regardez la vidéo [Comment examiner les stratégies de balises avec Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) pour découvrir comment utiliser la stratégie de balises Azure pour améliorer la visibilité des données de coûts.

Voici une vue des coûts des services Azure pour le mois dernier.

![Vue cumulée quotidienne groupée présentant un exemple de coûts du service Azure pour le mois dernier](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Les graphiques croisés dynamiques sous le graphique principal affichent différents regroupements pour vous donner une vue d’ensemble de vos coûts globaux pour la période et les filtres sélectionnés. Sélectionnez une propriété ou une étiquette pour afficher les coûts agrégés d’après n’importe quelle dimension.


![Données complètes pour la vue actuelle présentant les noms de groupe de ressources](./media/quick-acm-cost-analysis/full-data-set.png)

L’image précédente montre les noms de groupe de ressources. Bien que vous puissiez regrouper par étiquette pour afficher le total des coûts par étiquette, l’affichage de toutes les étiquettes par ressource ou par groupe de ressources n’est disponible dans aucune vue d’analyse des coûts.

Lorsque vous regroupez les coûts en fonction d’un attribut spécifique, les 10 principaux contributeurs aux coûts s’affichent dans l’ordre décroissant. S’il y en a plus de 10, les neuf principaux contributeurs aux coûts sont affichés avec un groupe **Autres** qui réunit tous les groupes restants. Lors du regroupement par étiquettes, vous pouvez aussi voir un groupe **Non étiqueté** pour les coûts auxquels la clé d’étiquette n’a pas été appliquée. **Sans balise** est toujours mentionné en dernier, même si les coûts sans balise sont plus élevés que ceux avec balise. Les coûts sans balise font partie du groupe **Autres** s’il y a 10 valeurs de balise ou plus.

Les ressources de machines virtuelles, de réseau et de stockage *classiques* ne partagent pas de données de facturation détaillées. Elles sont fusionnées dans les **services Classic** lors du regroupement des coûts.

Vous pouvez afficher le jeu de données complet pour n’importe quelle vue. Les sélections ou les filtres que vous appliquez ont une incidence sur les données présentées. Pour voir le jeu de données complet, cliquez sur la liste **Type de graphique**, puis cliquez sur l’affichage **Tableau**.

![Données de l’affichage actuel présentées dans un tableau](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="download-cost-analysis-data"></a>Télécharger les données de l’analyse du coût

Vous pouvez **télécharger** les informations de l’analyse du coût pour générer un fichier CSV de toutes les données affichées dans le portail Azure. Les filtres ou le regroupement que vous appliquez sont dans le fichier. Les données sous-jacentes du graphique Total supérieur qui ne sont pas activement affichées sont dans le fichier CSV.

## <a name="next-steps"></a>Étapes suivantes

Passez au premier tutoriel pour apprendre à créer et à gérer des budgets.

> [!div class="nextstepaction"]
> [Créer et gérer des budgets](tutorial-acm-create-budgets.md)
