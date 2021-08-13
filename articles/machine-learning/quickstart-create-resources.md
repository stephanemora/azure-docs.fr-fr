---
title: 'Démarrage rapide : créer des ressources d’espace de travail'
titleSuffix: Azure Machine Learning
description: Créez un espace de travail Azure Machine Learning et des ressources cloud qui peuvent être utilisées pour l’apprentissage des modèles de Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 06/10/2021
adobe-target: true
ms.custom: FY21Q4-aml-seo-hack, contperf-fy21q4
ms.openlocfilehash: 0d4c85ec1075e3925fa5657783ffce618faaa6ea
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760141"
---
# <a name="quickstart-create-workspace-resources-you-need-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : créer les ressources d’espace de travail nécessaires pour commencer à utiliser Azure Machine Learning

Dans ce guide de démarrage rapide, vous allez créer un espace de travail auquel vous ajouterez des ressources de calcul. Vous aurez alors tout ce dont vous avez besoin pour commencer à utiliser Azure Machine Learning.  

L’espace de travail est la ressource de niveau supérieur pour vos activités de machine learning. Il offre un emplacement centralisé qui vous permet de voir et de gérer les artefacts que vous créez quand vous utilisez Azure Machine Learning. Les ressources de calcul fournissent un environnement cloud préconfiguré qui vous permet d’entraîner, de déployer, d’automatiser, de gérer et de suivre les modèles Machine Learning.


## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-workspace"></a>Créer l’espace de travail

Si vous avez déjà un espace de travail, ignorez cette section et passez à [Créer une instance de calcul](#instance).

Si vous n’avez pas encore d’espace de travail, créez-en un maintenant :

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

## <a name="create-compute-instance"></a><a name="instance"></a> Créer une instance de calcul

Vous pouvez installer Azure Machine Learning sur votre propre ordinateur.  Toutefois, dans ce guide de démarrage rapide, vous allez créer une ressource de calcul en ligne dont l’environnement de développement est déjà installé et prêt à l’emploi.  Vous allez utiliser cette machine en ligne, une *instance de calcul*, pour votre environnement de développement afin d’écrire et d’exécuter du code dans des scripts Python et des notebooks Jupyter.

Créez une *instance de calcul* afin d’utiliser cet environnement de développement pour le reste des tutoriels et des guides de démarrage rapide.

1. Si vous n’avez pas sélectionné **Accéder à l’espace de travail** dans la section précédente, connectez-vous au [studio Azure Machine Learning](https://ml.azure.com) maintenant, et sélectionnez votre espace de travail.
1. Sur le côté gauche, sous **Gérer**, sélectionnez **Calcul**.
1. Sélectionnez **+Nouveau** pour créer une instance de calcul.
1. Conservez toutes les valeurs par défaut sur la première page, puis sélectionnez **Suivant**.
1. Indiquez un nom, puis sélectionnez **Créer**.
 
Dans environ deux minutes, vous verrez l’**État** de l’instance de calcul passer de *Création en cours* à *En cours d’exécution*.  Il sera prêt à ce moment-là.  

## <a name="create-compute-clusters"></a><a name="cluster"></a> Créer des clusters de calcul

Vous allez ensuite créer un cluster de calcul.  Les clusters vous permettent de distribuer un processus d’entraînement ou d’inférence par lots sur un cluster de nœuds de calcul de processeur ou de GPU dans le cloud.

Créez un cluster de calcul qui doit effectuer une mise à l’échelle automatique de nœuds (entre zéro et quatre) :

1. Toujours dans la section **Calcul**, sous l’onglet supérieur, sélectionnez **Clusters de calcul**.
1. Sélectionnez **+Nouveau** pour créer un cluster de calcul.
1. Conservez toutes les valeurs par défaut sur la première page, puis sélectionnez **Suivant**.
1. Nommez le cluster **cpu-cluster**.  Si ce nom existe déjà, ajoutez vos initiales au nom pour le rendre unique.
1. Laissez la valeur de **Nombre minimal de nœuds** à 0.
1. Remplacez la valeur de **Nombre maximal de nœuds** par 4, si possible.  Selon les paramètres, vous pouvez avoir une limite inférieure.
1. Remplacez la valeur présente dans **Secondes d’inactivité avant le scale-down** par 2 400.
1. Conservez le reste des valeurs par défaut, puis sélectionnez **Créer**.

En moins d’une minute, l’**État** du cluster passe de *Création en cours* à *Opération réussie*.  La liste montre le cluster de calcul provisionné ainsi que le nombre de nœuds inactifs, de nœuds occupés et de nœuds non provisionnés.  Étant donné que vous n’avez pas encore utilisé le cluster, tous les nœuds ne sont pas provisionnés pour le moment. 

> [!NOTE]
> Quand le cluster est créé, aucun nœud n’y est provisionné. Le cluster n’implique *aucun* coût tant que vous n’avez pas soumis un travail. Ce cluster fera l’objet d’un scale-down après 2 400 secondes (40 minutes) d’inactivité.  Cela vous donnera le temps de l’utiliser dans quelques tutoriels, si vous le souhaitez, sans devoir attendre un nouveau scale-up.

## <a name="quick-tour-of-the-studio"></a><a name="studio"></a> Visite rapide du studio

Le studio est votre portail web pour Azure Machine Learning. Il combine des expériences sans code et Code First pour offrir une plateforme de science des données inclusive.

Passez en revue les différentes parties du studio dans la barre de navigation de gauche :

* La section **Auteur** du studio contient plusieurs méthodes pour commencer à créer des modèles Machine Learning.  Vous pouvez :

    * La section **Notebooks** vous permet de créer des notebooks Jupyter, de copier des exemples de notebooks ou d’exécuter des notebooks et des scripts Python.
    * **ML automatisé** vous permet de créer un modèle Machine Learning sans avoir à écrire du code.
    * **Concepteur** vous permet de créer des modèles par glisser-déposer à l’aide de modules prédéfinis.

* La section **Composants** du studio vous aide à suivre les composants que vous créez au fur et à mesure de l’exécution de vos travaux.  Si vous avez un nouvel espace de travail, ces sections ne contiennent rien.

* Vous avez déjà utilisé la section **Gérer** du studio pour créer vos ressources de calcul.  Cette section vous permet également de créer et de gérer des données ainsi que des services externes que vous liez à votre espace de travail.  

## <a name="clean-up-resources"></a><a name="clean-up"></a>Supprimer des ressources

Si vous comptez suivre à présent le prochain tutoriel, passez à [Étapes suivantes](#next-steps).

### <a name="stop-compute-instance"></a>Arrêter l’instance de calcul

Si vous ne comptez pas l’utiliser maintenant, arrêtez l’instance de calcul :

1. Dans le studio, sur la gauche, sélectionnez **Calcul**.
1. Sous les onglets supérieurs, sélectionnez **Instances de calcul**
1. Sélectionnez l’instance de calcul dans la liste.
1. Dans la barre d’outils supérieure, sélectionnez **Arrêter**.

### <a name="delete-all-resources"></a>Supprimer toutes les ressources

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous disposez maintenant d’un espace de travail Azure Machine Learning qui contient :

- Une instance de calcul à utiliser pour votre environnement de développement.
- Un cluster de calcul à utiliser pour soumettre des exécutions d’entraînement.

Utilisez ces ressources pour en savoir plus sur Azure Machine Learning et entraîner un modèle avec des scripts Python.

> [!div class="nextstepaction"]
> [En savoir plus sur les scripts Python](tutorial-1st-experiment-hello-world.md)
>
