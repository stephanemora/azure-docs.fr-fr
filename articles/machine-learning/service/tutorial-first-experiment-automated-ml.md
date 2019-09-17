---
title: Créer votre première expérience de Machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Découvrez comment entraîner et déployer un modèle de classification avec le Machine Learning automatisé dans la page d’accueil de l’espace de travail d’Azure Machine Learning (préversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/09/2019
ms.openlocfilehash: 0dd4447736469644875dff914c6284b087be87d0
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910220"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Didacticiel : Créer votre premier modèle de classification avec Machine Learning automatisé

Dans ce tutoriel, vous allez découvrir comment créer votre première expérience de Machine Learning automatisé dans la page d’accueil de l’espace de travail (préversion) sans écrire une seule ligne de code. Cet exemple crée un modèle de classification pour prédire si un client souscrit à un compte à terme auprès d’une institution financière.

Avec le machine learning automatisé, vous pouvez automatiser des tâches fastidieuses. Le machine learning automatisé itère rapidement sur de nombreuses combinaisons d’algorithmes et d’hyperparamètres pour vous aider à trouver le meilleur modèle basé sur une métrique de réussite de votre choix.

Dans ce tutoriel, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un espace de travail de service Azure Machine Learning.
> * Exécuter une expérience de machine learning automatisé.
> * Voir les détails de l'expérience.
> * Déployer le modèle.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree).

* Téléchargez le fichier de données [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv). La colonne **y** indique si un client a souscrit à un compte à terme, qui est ensuite identifié comme colonne cible pour les prédictions de ce tutoriel. 

## <a name="create-a-workspace"></a>Créer un espace de travail

Un espace de travail Azure Machine Learning est une ressource fondamentale du cloud que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning. Il lie votre abonnement Azure et votre groupe de ressources à un objet facile à consommer dans le service. 

Vous créez un espace de travail par le biais du portail Azure, une console web pour la gestion de vos ressources Azure. 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Prenez note de votre **espace de travail** et de votre **abonnement**. Vous en aurez besoin pour être sûr de créer votre expérience au bon endroit. 

## <a name="create-and-run-the-experiment"></a>Créer et exécuter l’expérience

Vous effectuez les étapes de configuration et d’exécution de l’expérience suivantes dans la page d’accueil de l’espace de travail, une interface consolidée qui comprend des outils de Machine Learning pour implémenter des scénarios de science des données pour les praticiens de la science des données de tous niveaux de compétence.

1. Connectez-vous à la [page d’accueil de l’espace de travail](https://ml.azure.com/workspaceportal/).

1. Sélectionnez votre abonnement et l’espace de travail que vous avez créé.

1. Sélectionnez **Get started** (Bien démarrer).

1.  Sélectionnez **Automated ML** dans la section **Authoring** (Création), dans le volet gauche.
L’écran **Getting started** (Bien démarrer) s’affiche, car il s’agit de votre première expérience avec le Machine Learning automatisé.

    ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Sélectionnez **Créer une expérience**. 

1. Entrez **my-1st-automl-experiment** comme nom de l’expérience.

1. Sélectionnez **Create a new compute** (Créer un calcul). 

    1. Configurez votre contexte de calcul pour cette expérience.
        
        Champ | Valeur
        ----|---
        Nom du calcul |  Entrez un nom unique qui identifie votre contexte de calcul. Pour cet exemple, utilisez **automl-compute**.
        Taille de la machine virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul. Utilisez la valeur par défaut, **Standard_DS12_V2**.
        Paramètres supplémentaires| *Nœuds min* : 1. Pour activer le profilage des données, vous devez disposer d’un ou de plusieurs nœuds. <br> *Nœuds max* : 6.
 
    1. Pour créer votre calcul, sélectionnez **Créer**. Quelques minutes sont nécessaires pour achever l’opération. 

    1. Une fois la création terminée, sélectionnez votre nouveau calcul dans la liste déroulante, puis sélectionnez **Suivant**.

    >[!NOTE]
    >Pour ce tutoriel, vous utiliserez le compte de stockage et le conteneur par défaut créés avec votre nouveau calcul. Ces informations sont insérées automatiquement dans le formulaire.

1. Sélectionnez **Upload from local file** (Charger à partir d’un fichier local). À partir de là, vous créez un jeu de données avec le fichier **bankmarketing_train.csv** que vous avez téléchargé pour ce tutoriel. 

    1. Sélectionnez **Browse** (Parcourir), puis sélectionnez le fichier **bankmarketing_train.csv** sur votre ordinateur local. 

    1. Donnez un nom unique à votre jeu de données et fournissez une description facultative. 

    1. Sélectionnez **Next** (Suivant) pour le charger dans le conteneur par défaut qui a été configuré automatiquement lors de la création de votre espace de travail. La préversion publique prend en charge uniquement les chargements de fichiers locaux. 

    1. Une fois le chargement terminé, le formulaire **Settings and preview** (Paramètres et aperçu) est renseigné intelligemment en fonction du type de fichier. Vérifiez que le formulaire est renseigné comme suit.
        
        Champ|Valeur
        ---|---
        Format de fichier| Delimited
        Délimiteur| Comma
        Encodage| UTF-8
        Column headers| Tous les fichiers ont les mêmes en-têtes            Skip rows | Aucune

        >[!NOTE]
        > Si l’un des paramètres de ce formulaire est mis à jour, l’aperçu est mis à jour en conséquence.

        Sélectionnez **Suivant**.
    

    1. Le formulaire **Schema** permet de configurer davantage vos données pour cette expérience. Pour cet exemple, sélectionnez le bouton bascule correspondant à la caractéristique **day_of_week**, afin de ne pas l’inclure pour cette expérience. Sélectionnez **Done** (Terminer) pour terminer le chargement de fichier et la création du jeu de données pour votre expérience.

        ![Configuration de l’onglet Aperçu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

        
1. Sélectionnez **Classification** comme tâche de prédiction.

1. Sélectionnez **y** comme colonne cible, ce que vous souhaitez prédire. Cette colonne indique si le client a souscrit à un compte de dépôt à terme.

1. Développez **Paramètres avancés** et renseignez les champs comme suit.

    Paramètres avancés|Valeur
    ------|------
    Métrique principale| AUC_weighted 
    Critères de sortie| Quand l’un de ces critères est satisfait, le travail d’entraînement se termine avant la fin : <br> *Délai du travail de formation (minutes)*  : 5.  <br> *Nombre maximal d’itérations* : 10 
    Prétraitement| Active le prétraitement effectué par le Machine Learning automatisé. Cela comprend le nettoyage automatique des données, la préparation et la transformation pour générer des caractéristiques synthétiques.
    Validation| Sélectionnez la validation croisée « K-fold » et **2** comme nombre de validations croisées. 
    Accès concurrentiel| Sélectionnez **5** comme nombre maximal d’itérations simultanées.

   >[!NOTE]
   > Pour cette expérience, vous ne définissez pas de seuil de métriques ni de nombre de cœurs maximal par itérations. Vous n’empêchez pas non plus le test des algorithmes.

1. Sélectionnez **Démarrer** pour exécuter l’expérience.

   Au démarrage de l’expérience, un écran vide apparaît avec un message d’état en haut.

Le processus de préparation de l’expérience prend plusieurs minutes. Une fois ce processus terminé, le message d’état devient **Run is Running** (L’exécution est en cours d’exécution).

##  <a name="view-experiment-details"></a>Afficher les détails de l'expérience

À mesure que l’expérience progresse, l’écran met à jour le graphique d’itération (**Iteration chart**) et la liste d’itération (**Iteration list**) avec les différentes itérations (modèles) qui sont exécutées. La liste des itérations est triée par score de métrique. Par défaut, le modèle qui obtient la valeur la plus élevée d’après notre métrique **AUC_weighted** figure en haut de la liste.

>[!WARNING]
> Les travaux d’entraînement prennent plusieurs minutes, le temps que l’exécution de chaque pipeline se termine.

[![Tableau de bord des détails de l’exécution](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-the-model"></a>Déployer le modèle

En utilisant le Machine Learning automatisé dans la page d’accueil de l’espace de travail, vous pouvez déployer le meilleur modèle en tant que service web pour effectuer des prédictions sur de nouvelles données et identifier les zones d’opportunité. Pour cette expérience, le déploiement signifie que l’établissement financier dispose à présent d’une solution itérative et scalable pour identifier les clients potentiels d’un compte à terme.

Dans ce contexte d’expérience, **VotingEnsemble** est considéré comme le meilleur modèle d’après la métrique **AUC_weighted**.  Nous déployons ce modèle, mais nous vous informons que le déploiement prend 20 minutes environ.

1. Dans la page **Run Detail** (Détails de l’exécution), sélectionnez le bouton **Deploy Best Model** (Déployer le meilleur modèle) en haut à droite.

1. Renseignez le volet **Déployer le meilleur modèle** comme suit :

    Champ| Valeur
    ----|----
    Nom du déploiement| my-automl-deploy
    Description du déploiement| Déploiement de ma première expérience de Machine Learning automatisé
    Script de scoring| Générer automatiquement
    Script d’environnement| Générer automatiquement
    
1. Sélectionnez **Déployer**.

    Un message complet de déploiement s’affiche une fois le déploiement terminé.
    
Vous disposez maintenant d’un service web opérationnel pour générer des prédictions.

## <a name="clean-up-resources"></a>Supprimer des ressources

Les fichiers de déploiement sont plus volumineux que les fichiers de données et d’expérimentation. Le coût de leur stockage est donc plus élevé. Supprimez uniquement les fichiers de déploiement pour réduire les coûts associés à votre compte, ou si vous souhaitez conserver vos fichiers d’expérience et d’espace de travail. Dans le cas contraire, supprimez l’intégralité du groupe de ressources, si vous n’envisagez pas d’utiliser les fichiers.  

### <a name="delete-the-deployment-instance"></a>Supprimer l’instance de déploiement

Supprimez uniquement l’instance de déploiement du portail Azure si vous souhaitez conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et à des fins d’exploration. 

1. Accédez au [portail Azure](https://portal.azure.com//). Accédez à votre espace de travail et, à gauche dans le volet **Assets** (Ressources), sélectionnez **Deployments** (Déploiements). 

1. Sélectionnez le déploiement à supprimer et sélectionnez **Supprimer**. 

1. Sélectionnez **Continuer**.

### <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel de Machine Learning automatisé, vous avez utilisé la page d’accueil de l’espace de travail pour créer et déployer un modèle de classification. Pour plus d’informations et pour connaître les étapes suivantes, consultez ces articles :

> [!div class="nextstepaction"]
> [Utiliser un service web](how-to-consume-web-service.md)

+ En savoir plus sur le [prétraitement](how-to-create-portal-experiments.md#preprocess).
+ En savoir plus sur le [profilage des données](how-to-create-portal-experiments.md#profile).
+ En savoir plus sur le [Machine Learning automatisé](concept-automated-ml.md).

>[!NOTE]
> Ce jeu de données Bank Marketing est disponible sous licence [Creative Commons (CCO : domaine public)](https://creativecommons.org/publicdomain/zero/1.0/). Tous les droits du contenu individuel de la base de données sont concédés sous [licence du contenu de base de données](https://creativecommons.org/publicdomain/zero/1.0/) et sont disponibles sur [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ce jeu de données était initialement disponible dans la [base de données Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez et P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, Juin 2014.
