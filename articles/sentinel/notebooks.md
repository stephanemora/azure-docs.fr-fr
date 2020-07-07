---
title: Utiliser des notebooks avec Azure Sentinel pour la chasse sécuritaire
description: Cet article décrit comment utiliser des notebooks avec les fonctionnalités de recherche Azure Sentinel.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/25/2019
ms.openlocfilehash: bf63d5c8cb46fd791508af40dcefd7b39d4ba9de
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "83652030"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Utiliser Jupyter Notebook pour rechercher des menaces de sécurité

Azure Sentinel repose sur le magasin de données. Il associe des capacités d’interrogation hautes performances, un schéma dynamique et s’adapte aux volumes de données importants. Le portail Azure et tous les outils Azure Sentinel utilisent une API commune pour accéder à ce magasin de données. La même API est également disponible pour les outils externes tels que les notebooks [Jupyter](https://jupyter.org/) et Python. Bien que de nombreuses tâches courantes puissent être effectuées dans le portail, Jupyter étend l’étendue de ce que vous pouvez faire avec ces données. Il combine une programmabilité totale à une vaste collection de bibliothèques pour l’apprentissage automatique, la visualisation et l’analyse des données. Ces attributs transforment Jupyter en un outil essentiel pour les scénarios de recherche et d’enquête relatifs à la sécurité.

![exemple de notebook](./media/notebooks/sentinel-notebooks-map.png)

Nous avons intégré l’expérience de Jupyter dans le portail Azure , pour que vous puissiez créer et exécuter facilement des notebooks pour analyser vos données. La bibliothèque *Kqlmagic* fournit le ciment qui vous permet d’extraire des requêtes d’Azure Sentinel et de les exécuter directement à l’intérieur d’un notebook. Les requêtes utilisent le [langage de requête Kusto](https://kusto.azurewebsites.net/docs/query/index.html). Plusieurs notebooks, développés par certains des analystes de sécurité de Microsoft, sont intégrés à Azure Sentinel. Quelques-uns d’entre eux sont conçus pour un scénario spécifique et peuvent être utilisés en tant que tel. D’autres sont des exemples pour illustrer les techniques et les fonctionnalités que vous pouvez copier ou adapter pour les utiliser dans vos propres notebooks. D’autres notebooks peuvent également être importés à partir du GitHub de la communauté Azure Sentinel.

L’expérience Jupyter intégrée utilise [Azure Notebooks](https://notebooks.azure.com/) pour stocker, partager et exécuter des notebooks. Vous pouvez également exécuter ces notebooks localement (si vous avez un environnement Python et Jupyter sur votre ordinateur) ou dans d’autres environnements JupterHub tels qu’Azure Databricks.

Notebooks a deux composants :

- l’interface basée sur un navigateur où vous entrez et exécutez des requêtes et du code, et où les résultats de l’exécution sont affichés.
- Un *noyau* qui est responsable de l’analyse et de l’exécution du code. 

Dans Azure Notebooks, par défaut, ce noyau s’exécute sur le *stockage et calcul cloud gratuit* Azure. Si vos notebooks incluent des modèles Machine Learning complexes ou des visualisations, envisagez d’utiliser des ressources de calcul dédiées plus puissantes, telles que [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Les notebooks dans votre compte restent privés, sauf si vous choisissez de les partager.

Les notebooks Azure Sentinel utilisent de nombreuses bibliothèques Python populaires telles que pandas, matplotlib, bokeh et bien d’autres. Vous avez le choix entre un grand nombre d’autres packages Python, couvrant des domaines tels que ceux énoncés ci-dessous :

- visualisations et graphiques
- traitement de données et analyse
- statistiques et calculs numériques
- Machine Learning et Deep Learning

Nous avons également publié des outils de sécurité open source Jupyter dans un package nommé [msticpy](https://github.com/Microsoft/msticpy/). Ce package est utilisé dans la plupart des notebooks inclus. Les outils de msticpy sont conçus spécialement pour faciliter la création de notebooks pour la recherche et l’investigation, et nous travaillons activement pour vous proposer de nouvelles fonctionnalités et améliorations.

Les notebooks initiaux comprennent :

- **Investigation guidée - Traitement des alertes** : vous permet de trier rapidement les alertes en analysant l’activité sur les hôtes affectés.
- **Recherche guidée - Explorateur d’hôte Windows** : vous permet d’explorer l’activité de comptes, les exécutions de processus, l’activité réseau et d’autres événements sur un hôte.
- **Recherche guidée - Exploration d’Office 365** : recherche d’activité suspecte Office 365 dans plusieurs jeux de données Office 365.

Le [référentiel GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) est l’emplacement de tous les futurs notebooks Azure Sentinel créés par Microsoft ou fournis par la communauté.

Pour utiliser les notebooks, vous devez disposer d’un compte Azure Notebooks. Pour plus d’informations, consultez [Démarrage rapide : Connectez-vous et définissez un ID d’utilisateur](https://docs.microsoft.com/azure/notebooks/quickstart-sign-in-azure-notebooks) dans la documentation d’Azure Notebooks. Pour créer ce compte, vous pouvez utiliser l’option **S’inscrire à Azure Notebooks** à partir de la barre de commandes de **Azure Sentinel - Notebooks** :

> [!div class="mx-imgBorder"]
>![S’inscrire à l’option Azure Notebooks](./media/notebooks/sentinel-azure-sign-up-azure-notebooks.png)

Vous pouvez exécuter un notebook directement à partir d’Azure Sentinel ou cloner tous les notebooks Azure Sentinel dans un nouveau projet Azure Notebooks.

## <a name="run-a-notebook-from-azure-sentinel"></a>Exécuter un notebook depuis Azure Sentinel
 
1. À partir du portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Notebooks**, où vous pouvez voir les notebooks fournis par Azure Sentinel. 

2. Sélectionnez des notebooks individuels pour lire leurs descriptions, les types de données requis et les sources de données. Par exemple :
    
    > [!div class="mx-imgBorder"]
    > ![lancer le notebook](./media/notebooks/sentinel-azure-notebooks-launch.png)

3. Sélectionnez le notebook que vous souhaitez utiliser, puis sélectionnez **Lancer le notebook (préversion)** pour cloner et configurer le notebook dans un nouveau projet Azure Notebooks qui se connecte à votre espace de travail Azure Sentinel. Une fois le processus terminé, le notebook s’ouvre dans Azure Notebooks pour que vous l’exécutiez.

## <a name="clone-azure-sentinel-notebooks-to-a-new-azure-notebooks-project"></a>Cloner des notebooks Azure Sentinel dans un nouveau projet Azure Notebooks

Cette procédure crée un projet Azure Notebooks pour vous, contenant les notebooks Azure Sentinel. Vous pouvez ensuite exécuter les notebooks tels quels, ou leur apporter des modifications, puis les exécuter.

1. À partir du portail Azure, accédez à **Azure Sentinel** > **Gestion des menaces** > **Notebooks**, puis sélectionnez **Cloner des notebooks** à partir de la barre de commande :
  
    > [!div class="mx-imgBorder"]
    >![Option Cloner des notebooks](./media/notebooks/sentinel-azure-clone-notebooks.png)

2. Lorsque la boîte de dialogue suivante apparaît, sélectionnez **Importer** pour cloner le référentiel GitHub dans votre projet Azure Notebooks. Si vous n’avez pas de compte Azure Notebooks existant, vous êtes invité à en créer un et à vous connecter.

   ![Importer un notebook](./media/notebooks/sentinel-notebooks-clone.png)

3. Dans la boîte de dialogue **Charger un référentiel GitHub**, ne sélectionnez pas **Cloner de manière récursive**, car cette option fait référence aux dépôts GitHub liés. Pour le nom du projet, utilisez le nom par défaut ou tapez-en un nouveau. Cliquer ensuite sur **Importer** pour démarrer le clonage du contenu de GitHub, qui peut prendre quelques minutes.

   ![Importer un notebook](./media/notebooks/sentinel-create-project.png)

4. Ouvrez le projet que vous venez de créer, puis ouvrez le dossier **Notebooks** pour afficher les notebooks. Par exemple :

   ![Importer un référentiel](./media/notebooks/sentinel-open-notebook1.png)

Vous pouvez ensuite exécuter les notebooks à partir d’Azure Notebooks. Pour revenir à ces notebooks à partir d’Azure Sentinel, sélectionnez **Accéder à vos notebooks** à partir de la barre de commandes dans **Azure Sentinel - Notebooks** :

> [!div class="mx-imgBorder"]
>![Accéder à vos notebooks (option)](./media/notebooks/sentinel-azure-to-go-notebooks.png)


## <a name="using-notebooks-to-hunt"></a>Utiliser des notebooks pour chasser

Chaque notebook vous guide dans la procédure de recherche ou d’investigation. Les bibliothèques et autres dépendances requises par le notebook peuvent être installées à partir du notebook lui-même ou via une procédure de configuration simple. La configuration qui lie votre projet de notebook à votre abonnement Azure Sentinel est automatiquement définie dans les étapes précédentes.

1. Si vous n’êtes pas encore dans Azure Notebooks, vous pouvez utiliser l’option **Accéder à vos notebooks** à partir de la barre de commandes de **Azure Sentinel - Notebooks** :
    
    > [!div class="mx-imgBorder"]
    >![Accéder à vos notebooks (option)](./media/notebooks/sentinel-azure-to-go-notebooks.png)
    
    Dans Azure Notebooks, sélectionnez **Mes projets**, puis le projet qui contient les notebooks Azure Sentinel et enfin le dossier **Notebooks**.
    
2. Avant d’ouvrir un notebook, sachez que par défaut, le calcul gratuit est sélectionné pour exécuter les notebooks :
    
   ![sélectionner le notebook](./media/notebooks/sentinel-open-notebook2.png)
    
    Si vous avez configuré une machine virtuelle de science des données (DSVM) à utiliser comme expliqué dans l’introduction, sélectionnez la DSVM et authentifiez-vous avant d’ouvrir le premier notebook. 

3. Sélectionnez un notebook pour l’ouvrir.
    
    La première fois que vous ouvrez un notebook, vous pouvez être invité à sélectionner une version du noyau. Si vous n’y êtes pas invité, vous pouvez sélectionner la version du noyau à partir de **Noyau** >  **Modifier le noyau**, puis sélectionner une version (au moins 3.6). La version sélectionnée du noyau est affiché en haut à droite de la fenêtre du notebook :
    
   ![sélectionner le notebook](./media/notebooks/sentinel-select-kernel.png)

4. Avant d’apporter des modifications au notebook que vous avez téléchargé, il est judicieux de faire une copie du notebook d’origine et de travailler sur la copie. Pour ce faire, sélectionnez **Fichier** > **Créer une copie**. Travailler sur des copies vous permet de mettre à jour en toute sécurité vers les versions futures des notebooks sans remplacer vos données.
    
    Vous êtes maintenant prêt à exécuter ou à modifier le notebook sélectionné.

Recommandations :

- Pour une présentation rapide de l’interrogation des données dans Azure Sentinel, consultez le notebook [GetStarted](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/345cf9f7c8f6137f5af4593a3f9d7568acd6cbc2/DeprecatedNotebooks/Get%20Started.ipynb) le dossier principal **Notebooks**. 

- Vous trouverez des exemples supplémentaires de notebooks dans le sous-dossier **Sample-Notebooks**. Les exemples de notebooks ont été enregistrés avec des données pour que vous puissiez voir plus facilement le résultat prévu. Nous vous recommandons de les afficher dans [nbviewer](https://nbviewer.jupyter.org/). 

- Le dossier **HowTos** contient notebooks décrivant, par exemple : la configuration de votre version Python par défaut, la configuration d’une DSVM, la création de signets Azure Sentinel dans un notebook et bien d’autres sujets.

Les notebooks fournis sont conçus en tant qu’outils utiles, illustrations et exemples de code que vous pouvez utiliser dans le développement de vos propres notebooks.

Nous apprécions les commentaires, qu’il s’agisse de suggestions, de demandes de fonctionnalités, de contribution aux notebooks, de signalements de bogues ou d’améliorations et d’ajouts aux notebooks existants. Rendez-vous sur le [GitHub de la communauté Azure Sentinel](https://github.com/Azure/Azure-Sentinel) pour créer un problème ou dupliquer et charger une contribution.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à bien démarrer avec les notebooks Jupyter dans Azure Sentinel. Pour en savoir plus sur Azure Sentinel, voir les articles suivants :

- [Hunt for threats with in Azure Sentinel Preview](hunting.md) (Rechercher des menaces dans Azure Sentinel en préversion)
- [Keep track of data during hunting](bookmarks.md) (Suivre des données pendant la recherche)
