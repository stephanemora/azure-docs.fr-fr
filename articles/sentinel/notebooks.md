---
title: Fonctionnalités de recherche à l’aide de blocs-notes Sentinel version préliminaire d’Azure | Microsoft Docs
description: Cet article décrit comment utiliser des blocs-notes avec les fonctionnalités de recherche Azure Sentinel.
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
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/07/2019
ms.locfileid: "65228637"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Utiliser des blocs-notes Jupyter pour rechercher les menaces de sécurité

> [!IMPORTANT]
> Azure Sentinel est actuellement disponible en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La Fondation Sentinel Azure est le magasin de données ; Il combine les hautes performances de requêtes, le schéma dynamique et s’adapte aux volumes de données importants. Le portail Azure Sentinel et tous les outils Azure Sentinel utilisent une API commune pour accéder à ce magasin de données. La même API est également disponible pour les outils externes tels que [Jupyter](https://jupyter.org/) notebooks et Python. Bien que de nombreuses tâches courantes peuvent être effectuées dans le portail, Jupyter étend la portée de ce que vous pouvez faire avec ces données. Il combine la programmabilité complète avec une vaste collection de bibliothèques pour l’analyse de la machine learning, de visualisation et de données. Ces attributs rendent Jupyter un outil incontournable pour l’enquête de sécurité et de chasse.

![Bloc-notes de l’exemple](./media/notebooks/sentinel-nb-mapandtimeline.png)

Nous avons intégré l’expérience de Jupyter dans le portail Azure Sentinel, ce qui rend facile créer et exécuter des ordinateurs portables pour analyser vos données. Le *Kqlmagic* bibliothèque fournit le lien qui vous permet de prendre des requêtes dans Azure Sentinel et les exécuter directement à l’intérieur d’un bloc-notes. Les requêtes utilisent le [langage de requête Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Plusieurs notebooks, développés par certains des analystes de sécurité de Microsoft, sont empaquetés avec Azure Sentinel. Certaines de ces ordinateurs portables sont pour un scénario spécifique et peut être utilisées en tant que-est. D’autres sont des exemples pour illustrer les techniques et les fonctionnalités que vous pouvez copier ou de s’adapter à utiliser vos propres blocs-notes. Autres blocs-notes peuvent également être importées à partir de la Communauté Azure Sentinel GitHub.

Utilise l’expérience intégrée de Jupyter [Azure Notebooks](https://notebooks.azure.com/) pour stocker, partager et exécuter des ordinateurs portables. Vous pouvez également exécuter ces blocs-notes localement (si vous avez un environnement Python et Jupyter sur votre ordinateur) ou dans d’autres environnements JupterHub tels que Azure Databricks.

Blocs-notes possèdent deux composants :

- l’interface basée sur navigateur où vous entrez et exécutez des requêtes et code, et où les résultats de l’exécution sont affichés.
- un *noyau* qui est responsable de l’analyse et l’exécution du code lui-même. 

Dans les blocs-notes Azure, ce noyau s’exécute sur Azure *de calcul Cloud gratuit et de stockage* par défaut. Si vos blocs-notes incluent des modèles d’apprentissage automatique complexes ou des visualisations vous devez envisager d’utiliser plus puissante, dédié des ressources de calcul tels que [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Les blocs-notes dans votre compte restent privées, sauf si vous choisissez de les partager.

Les blocs-notes Azure Sentinel utilisent de nombreuses bibliothèques Python populaires telles que pandas, matplotlib, bokeh et autres. Il existe un très grand nombre d’autres packages Python de votre choix, couvrant des domaines tels que :

- visualisations et des graphiques
- traitement des données et analyse
- statistiques et des calculs numériques
- apprentissage automatique et l’apprentissage profond

Nous avons également publié des outils de sécurité open source Jupyter dans un package nommé [msticpy](https://github.com/Microsoft/msticpy/). Ce package est utilisé dans la plupart des ordinateurs portables inclus. Msticpy outils conçus spécialement pour faciliter la création d’ordinateurs portables de chasse et enquête et nous travaillons activement à améliorations et nouvelles fonctionnalités.

Les blocs-notes initiales sont les suivantes :

- **Guidée par un examen - traiter les alertes**: Vous permet de trier rapidement les alertes en analysant l’activité sur l’ou les hôtes affecté.
- **Guidée chasse - l’Explorateur Windows hôte**: Vous permet d’Explorer les activités de compte, les exécutions de processus, l’activité réseau et autres événements sur un ordinateur hôte.  
- **Guidée chasse - Explorer-Office 365**: Recherche d’activité suspecte de Office 365 dans plusieurs jeux de données Office 365.

Le [Azure Sentinel Communauté référentiel](https://github.com/Azure/Azure-Sentinel) est l’emplacement de n’importe quel bloc-notes Azure Sentinel futures créée par Microsoft ou fourni par la Communauté.

## <a name="run-a-notebook"></a>Exécuter une instance notebook

Dans l’exemple suivant, nous créer un projet Azure Notebooks à partir du portail Azure Sentinel, remplissage du projet avec les blocs-notes. Avant d’utiliser ces ordinateurs portables, il est judicieux d’effectuer une copie de l’ordinateur portable et travailler sur la copie. Travail sur des copies vous permet d’en toute sécurité mettre à jour vers les versions ultérieures d’ordinateurs portables sans remplacer un de vos données.

1. Dans le portail Azure Sentinel, cliquez sur **blocs-notes** dans le menu de navigation. Pour créer un nouveau projet Azure Notebooks, cliquez sur **Clone Azure Sentinel Notebooks** ou cliquez sur les projets pour ouvrir vos blocs-notes existants **accédez à vos blocs-notes**.
  
   ![Sélectionnez les ordinateurs portables](./media/notebooks/sentinel-az-notebooks-home.png)

2. Si vous avez choisi **Clone Azure Sentinel Notebooks** à l’étape précédente, la boîte de dialogue suivante s’affiche. Cliquez sur **importation** pour cloner le référentiel GitHub dans votre projet Azure Notebooks. Si vous n’avez pas un compte Azure Notebooks existant, vous devrez en créer une et connectez-vous.

   ![Bloc-notes d’importation](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. Lorsque vous créez un nouveau projet, vous devez nommer le projet - utiliser le nom par défaut ou le type dans un autre. Ne cochez pas la **cloner récursivement** option - cette option fait référence à des dépôts GitHub liés. En cliquant sur **importation** démarre le clonage du contenu de GitHub, qui peut prendre quelques minutes.

   ![Bloc-notes d’importation](./media/notebooks/sentinel-create-nb-project.png)

4. Ouvrez le **blocs-notes** dossier pour afficher les ordinateurs portables. Chaque bloc-notes vous guide à travers les étapes pour effectuer une recherche ou d’une enquête. Bibliothèques et autres dépendances requises par l’ordinateur portable peuvent être installés à partir du bloc-notes lui-même ou via une procédure de configuration simple. Configuration qui lie votre projet portable à votre abonnement Azure Sentinel est automatiquement configurée dans les étapes précédentes. Vos blocs-notes sont prêts à exécuter par rapport à votre espace de travail Analytique de journal Azure Sentinel.

   ![Importation de dépôt](./media/notebooks/sentinel-open-notebook1.png)

5. Ouvrez un bloc-notes. Calcul gratuite est sélectionnée par défaut pour exécuter les blocs-notes (mis en surbrillance). Si vous avez configuré une machine virtuelle DSVM pour utiliser (voir ci-dessus), sélectionnez la machine virtuelle DSVM et s’authentifier avant d’ouvrir le bloc-notes en premier. Cliquez sur un ordinateur portable pour l’ouvrir.

   ![Sélectionnez le bloc-notes](./media/notebooks/sentinel-open-notebook2.png)

6. Sélectionner la version de Python. Lorsque vous ouvrez un bloc-notes pour la première fois, il peut vous invite à sélectionner une version de noyau. Si ce n’est pas le cas, sélectionnez le noyau à utiliser comme suit. Python 3.6 ou ultérieure doit être le noyau sélectionné (en haut à droite de la fenêtre du bloc-notes).

   ![Sélectionnez le bloc-notes](./media/notebooks/sentinel-select-kernel.png)

Pour une présentation rapide de l’interrogation des données dans Azure Sentinel, examinez le [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) bloc-notes dans le dossier principal de blocs-notes. Vous trouverez des exemples supplémentaires de bloc-notes dans le **des exemples de bloc-notes** sous-dossier. Les exemples de notebooks ont été enregistrées avec des données, afin qu’il soit plus facile de voir la sortie prévue (nous vous recommandons de les afficher dans [nbviewer](https://nbviewer.jupyter.org/)). Le **procédures** dossier contient des ordinateurs portables décrivant, par exemple : paramètre version de Python par défaut, configurez une machine virtuelle DSVM, création Sentinel Azure crée des signets à partir d’un ordinateur portable et les autres sujets.

Ces ordinateurs portables sont conçus en tant que les deux outils utiles et illustrations et les exemples de code que vous pouvez utiliser dans le développement de vos propres blocs-notes.

Nous apprécions vos commentaires, si des suggestions, des demandes de fonctionnalités, contribué blocs-notes, les rapports de bogues ou les améliorations et les ajouts aux blocs-notes existants. Accédez à la [Azure Sentinel Communauté GitHub](https://github.com/Azure/Azure-Sentinel) pour créer un problème ou une branche et de télécharger une contribution.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à bien démarrer avec les blocs-notes Jupyter dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- [Recherche de façon proactive les menaces](hunting.md)
- [Utiliser des signets pour enregistrer des informations intéressantes lors de la recherche](bookmarks.md)