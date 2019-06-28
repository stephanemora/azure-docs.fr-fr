---
title: Fonctionnalités de recherche à l’aide de notebooks dans Azure Sentinel en préversion | Microsoft Docs
description: Cet article décrit comment utiliser des notebooks avec les fonctionnalités de recherche Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ffe3ae5b6aa26d154928a74e51864a0574b82c68
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65228637"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Utiliser Jupyter Notebook pour rechercher des menaces de sécurité

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel repose sur le magasin de données. Il associe des capacités d’interrogation hautes performances, un schéma dynamique et s’adapte aux volumes de données importants. Le portail Azure Sentinel et tous les outils Azure Sentinel utilisent une API commune pour accéder à ce magasin de données. La même API est également disponible pour les outils externes tels que les notebooks [Jupyter](https://jupyter.org/) et Python. Bien que de nombreuses tâches courantes puissent être effectuées dans le portail, Jupyter étend l’étendue de ce que vous pouvez faire avec ces données. Il combine une programmabilité totale à une vaste collection de bibliothèques pour l’apprentissage automatique, la visualisation et l’analyse des données. Ces attributs transforment Jupyter en un outil essentiel pour les scénarios de recherche et d’enquête relatifs à la sécurité.

![exemple de notebook](./media/notebooks/sentinel-nb-mapandtimeline.png)

Nous avons intégré l’expérience de Jupyter dans le portail Azure Sentinel, pour que vous puissiez créer et exécuter facilement des notebooks pour analyser vos données. La bibliothèque *Kqlmagic* fournit le ciment qui vous permet d’extraire des requêtes d’Azure Sentinel et de les exécuter directement à l’intérieur d’un notebook. Les requêtes utilisent le [langage de requête Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Plusieurs notebooks, développés par certains des analystes de sécurité de Microsoft, sont intégrés à Azure Sentinel. Quelques-uns d’entre eux sont conçus pour un scénario spécifique et peuvent être utilisés en tant que tel. D’autres sont des exemples pour illustrer les techniques et les fonctionnalités que vous pouvez copier ou adapter pour les utiliser dans vos propres notebooks. D’autres notebooks peuvent également être importés à partir du GitHub de la communauté Azure Sentinel.

L’expérience Jupyter intégrée utilise [Azure Notebooks](https://notebooks.azure.com/) pour stocker, partager et exécuter des notebooks. Vous pouvez également exécuter ces notebooks localement (si vous avez un environnement Python et Jupyter sur votre ordinateur) ou dans d’autres environnements JupterHub tels qu’Azure Databricks.

Notebooks a deux composants :

- l’interface basée sur un navigateur où vous entrez et exécutez des requêtes et du code, et où les résultats de l’exécution sont affichés ;
- un *noyau* qui est responsable de l’analyse et de l’exécution du code. 

Dans Azure Notebooks, ce noyau s’exécute sur le *stockage et calcul cloud gratuit* Azure par défaut. Si vos notebooks incluent des modèles Machine Learning complexes ou des visualisations, envisagez d’utiliser des ressources de calcul dédiées plus puissantes, telles que [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Les notebooks dans votre compte restent privés, sauf si vous choisissez de les partager.

Les notebooks Azure Sentinel utilisent de nombreuses bibliothèques Python populaires telles que pandas, matplotlib, bokeh et bien d’autres. Vous avez le choix entre un grand nombre d’autres packages Python, couvrant des domaines tels que ceux énoncés ci-dessous :

- visualisations et graphiques ;
- traitement de données et analyse ;
- statistiques et calculs numériques ;
- Machine Learning et Deep Learning.

Nous avons également publié des outils de sécurité open source Jupyter dans un package nommé [msticpy](https://github.com/Microsoft/msticpy/). Ce package est utilisé dans la plupart des notebooks inclus. Les outils de msticpy sont conçus spécialement pour faciliter la création de notebooks pour la recherche et l’investigation, et nous travaillons activement pour vous proposer de nouvelles fonctionnalités et améliorations.

Les notebooks initiaux comprennent :

- **Investigation guidée - Traitement des alertes** : vous permet de trier rapidement les alertes en analysant l’activité sur les hôtes affectés.
- **Recherche guidée - Explorateur d’hôte Windows** : vous permet d’explorer l’activité de comptes, les exécutions de processus, l’activité réseau et d’autres événements sur un hôte.  
- **Recherche guidée - Exploration d’Office 365** : recherche d’activité suspecte Office 365 dans plusieurs jeux de données Office 365.

Le [référentiel GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) est l’emplacement de tous les futurs notebooks Azure Sentinel créés par Microsoft ou fournis par la communauté.

## <a name="run-a-notebook"></a>Exécuter un notebook

Dans l’exemple suivant, nous allons créer un projet Azure Notebooks à partir du portail Azure Sentinel, en complétant le projet avec des notebooks. Avant d’utiliser ces notebooks, il est judicieux d’effectuer une copie du notebook et de travailler sur cette copie. Travailler sur des copies vous permet de mettre à jour en toute sécurité vers les versions futures des notebooks sans remplacer vos données.

1. Dans le portail Azure Sentinel, cliquez sur **Notebooks** dans le menu de navigation. Pour créer un projet Azure Notebooks, cliquez sur **Clone Azure Sentinel Notebooks** (Cloner des notebooks Azure Sentinel) ; pour ouvrir vos projets de notebooks existants, cliquez sur **Go to your Notebooks** (Accéder à vos notebooks).
  
   ![sélectionner les notebooks](./media/notebooks/sentinel-az-notebooks-home.png)

2. Si vous avez choisi l’option **Clone Azure Sentinel Notebooks** (Cloner des notebooks Azure Sentinel) à l’étape précédente, la boîte de dialogue suivante s’affiche. Cliquez sur **Importer** pour cloner le référentiel GitHub dans votre projet Azure Notebooks. Si vous n’avez pas de compte Azure Notebooks existant, vous êtes invité à en créer un et à vous connecter.

   ![Importer un notebook](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Lorsque vous créez un projet, vous devez nommer le projet ; utilisez le nom par défaut ou saisissez-en un autre. Ne cochez pas l’option **Clone Recursively** (Cloner récursivement), puisqu’elle fait référence à des référentiels GitHub liés. Cliquer sur **Importer** démarre le clonage du contenu de GitHub, qui peut prendre quelques minutes.

   ![Importer un notebook](./media/notebooks/sentinel-create-nb-project.png)

4. Ouvrez le dossier **Notebooks** pour afficher les notebooks. Chaque notebook vous guide dans la procédure de recherche ou d’investigation. Les bibliothèques et autres dépendances requises par le notebook peuvent être installées à partir du notebook lui-même ou via une procédure de configuration simple. La configuration qui lie votre projet de notebook à votre abonnement Azure Sentinel est automatiquement définie dans les étapes précédentes. Vos notebooks sont prêts à s’exécuter dans votre espace de travail Log Analytics Azure Sentinel.

   ![Importer un référentiel](./media/notebooks/sentinel-open-notebook1.png)

5. Ouvrez un notebook. L’option Free Compute (Calcul gratuit) est sélectionnée par défaut pour exécuter les notebooks (surlignée). Si vous avez configuré une DSVM à utiliser (voir ci-dessus), sélectionnez la DSVM et authentifiez-vous avant d’ouvrir le premier notebook. Cliquez sur un notebook pour l’ouvrir.

   ![sélectionner le notebook](./media/notebooks/sentinel-open-notebook2.png)

6. Sélection de la version de Python. Lorsque vous ouvrez un notebook pour la première fois, vous pouvez être invité à sélectionner une version de noyau. Si ce n’est pas le cas, sélectionnez le noyau à utiliser comme suit. Python 3.6 ou version ultérieure doit être le noyau sélectionné (en haut à droite de la fenêtre du notebook).

   ![sélectionner le notebook](./media/notebooks/sentinel-select-kernel.png)

Pour une présentation rapide de l’interrogation des données dans Azure Sentinel, consultez le notebook [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) le dossier principal Notebooks. Vous trouverez des exemples supplémentaires de notebooks dans le sous-dossier **Sample-Notebooks**. Les exemples de notebooks ont été enregistrés avec des données pour que vous puissiez voir plus facilement le résultat prévu (nous vous recommandons de les afficher dans [nbviewer](https://nbviewer.jupyter.org/)). Le dossier **HowTos** contient des notebooks décrivant, par exemple, la configuration de votre version Python par défaut, la configuration d’une DSVM, la création de signets Azure Sentinel dans un notebook et bien d’autres sujets.

Ces notebooks sont conçus en tant qu’outils utiles, illustrations et exemples de code que vous pouvez utiliser dans le développement de vos propres notebooks.

Nous apprécions les commentaires, qu’il s’agisse de suggestions, de demandes de fonctionnalités, de contribution aux notebooks, de signalements de bogues ou d’améliorations et d’ajouts aux notebooks existants. Rendez-vous sur le [GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) pour créer un problème ou dupliquer et charger une contribution.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à bien démarrer avec les notebooks Jupyter dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)