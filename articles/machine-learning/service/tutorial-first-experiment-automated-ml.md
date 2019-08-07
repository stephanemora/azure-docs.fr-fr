---
title: Créer votre première expérience de Machine learning automatisé
titleSuffix: Azure Machine Learning service
description: Découvrez comment entraîner et déployer un modèle de classification avec le Machine Learning automatisé dans le portail Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: tsikiksr
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 07/23/2019
ms.openlocfilehash: 7c7d90d4ca1625edecc9d84e1ff7beec50032884
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/24/2019
ms.locfileid: "68445079"
---
# <a name="tutorial-train-and-deploy-a-classification-model-with-automated-machine-learning-in-the-azure-portal-preview"></a>Didacticiel : Entraîner et déployer un modèle de classification avec le Machine Learning automatisé dans le portail Azure (préversion)

Dans ce tutoriel, vous allez découvrir comment créer votre première expérience de Machine Learning automatisé dans le portail Azure. Cet exemple crée un modèle de classification pour prédire si un client souscrit à un compte de dépôt à terme auprès de la banque. 

Quand vous utilisez les fonctionnalités de Machine Learning automatisé du service et le portail Azure, vous lancez le processus de Machine Learning automatisé, et la sélection de l’algorithme et le réglage de l’hyperparamètre sont effectués pour vous. La technique de Machine Learning automatisé itère de nombreuses combinaisons d’algorithmes et d’hyperparamètres jusqu’à ce qu’elle trouve le meilleur modèle en fonction de vos critères, tout ceci sans que vous ayez une seule ligne de code à écrire.

Dans ce tutoriel, vous avez appris à effectuer les tâches suivantes :

> [!div class="checklist"]
> * Configurer un espace de travail pour Azure Machine Learning service
> * Créer une expérience
> * Entraîner automatiquement un modèle de classification
> * Afficher les détails de l’exécution de l’entraînement
> * Déployer le modèle.

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez dès aujourd'hui la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

* Fichier de données **bankmarketing_train. csv**. Téléchargez-le [ici](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

## <a name="create-a-workspace"></a>Créer un espace de travail

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-an-experiment"></a>Création d'une expérience

1. Accédez au volet gauche de votre espace de travail. Sélectionnez **Machine Learning automatisé** sous la section **Création (préversion)** .

    ![Volet de navigation du portail Azure](media/tutorial-1st-experiment-automated-ml/nav-pane.png)

    Comme il s’agit de votre première expérience avec le Machine Learning automatisé, vous verrez l’écran **Bienvenue dans le Machine Learning automatisé**. 

1.  Sélectionnez **Créer une expérience**. Entrez **my-1st-automl-experiment** comme nom de l’expérience.

1. Sélectionnez **Créer un nouveau calcul** et configurez votre contexte de calcul pour cette expérience.

    Champ| Valeur
    ---|---
    Nom du calcul| Entrez un nom unique qui identifie votre contexte de calcul. Pour cet exemple, nous utilisons **automl-compute**.
    Taille de la machine virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul. Nous utilisons **Standard_DS12_V2**.
    Paramètres supplémentaires| *Nœuds min* : 1. Pour activer le profilage des données, vous devez disposer d'un nœud minimum. <br> *Nœuds max* : 6. 

    Pour commencer à créer votre nouveau calcul, sélectionnez **Créer**. Cette opération peut prendre quelques instants. 

    Une fois la création terminée, sélectionnez votre nouveau calcul dans la liste déroulante, puis sélectionnez **Suivant**.

1. Pour ce tutoriel, nous utilisons le compte de stockage et le conteneur par défaut créés avec votre nouveau calcul. Ces informations sont insérées automatiquement dans le formulaire.

1. Sélectionnez **Charger** pour choisir le fichier **bankmarketing_train.csv** sur votre ordinateur local et le charger dans le conteneur par défaut. La préversion publique prend uniquement en charge les chargements de fichiers locaux et les comptes Stockage Blob Azure. Une fois le chargement terminé, sélectionnez le fichier dans la liste. 

    [![Sélectionner le fichier de données](media/tutorial-1st-experiment-automated-ml/select-data-file.png)](media/tutorial-1st-experiment-automated-ml/select-data-file-expanded.png#lightbox)

1. L’onglet **Aperçu** nous permet de configurer plus précisément nos données pour cette expérience.

    Sous l’onglet Aperçu, indiquez que les données incluent des en-têtes. Par défaut, le service inclut toutes les caractéristiques (colonnes) pour l’entraînement. Pour cet exemple, faites défiler vers la droite et **Ignorez** la caractéristique **day_of_week**.

    ![Configuration de l’onglet Aperçu](media/tutorial-1st-experiment-automated-ml/preview-tab-config.gif)


    >[!NOTE]
    > Le profilage des données n’est pas disponible avec les calculs qui ont 0 comme nombre minimal de nœud.

1. Sélectionnez **Classification** comme tâche de prédiction.

1. Sélectionnez **y** comme colonne cible, la colonne sur laquelle nous voulons effectuer des prédictions. Cette colonne indique si le client a souscrit à un compte de dépôt à terme.

1. Développez les **Paramètres avancés** et renseignez les champs comme suit.

    Paramètres avancés|Valeur
    ------|------
    Métrique principale| AUC_weighted 
    Critères de sortie| Lorsqu’un de ces critères est satisfait, le travail de formation se termine avant la fin. <br> *Délai du travail de formation (minutes)*  : 5.  <br> *Nombre maximal d’itérations* : 10 
    Prétraitement| Activez le prétraitement effectué par le Machine Learning automatisé. Cela comprend le nettoyage automatique des données, la préparation et la transformation pour générer des caractéristiques synthétiques.
    Validation| Sélectionnez la validation croisée « K-fold » et 2 comme nombre de validations croisées. 
    Accès concurrentiel| Sélectionnez 5 comme nombre maximal d’itérations simultanées.

   >[!NOTE]
   > Pour cette expérience, nous ne définissons pas de seuil de métriques ou d’itérations maximales, et nous n’empêchons pas le test des algorithmes.

1. Cliquez sur **Démarrer** pour exécuter l’expérience.

   Une fois l’expérience démarrée, un écran **Détails de l’exécution** vide s’affiche avec l’état suivant en haut. Le processus de préparation de l'expérience prend quelques minutes. Une fois le processus de préparation terminé, le message d’état devient **L’exécution est en cours d’exécution**.

      ![Préparation de l’exécution](media/tutorial-1st-experiment-automated-ml/run-preparing.png)

##  <a name="view-experiment-details"></a>Afficher les détails de l'expérience

À mesure que l’expérience progresse, l’écran **Détails de l’exécution** met à jour le graphique d’itération et la liste avec les différentes itérations (modèles) qui sont exécutées. La liste des itérations est triée par score de métrique et, par défaut, le modèle qui obtient la valeur la plus élevée d’après notre métrique **AUC_weighted** figure en haut de la liste.

>[!TIP]
> Les travaux d’entraînement peuvent prendre plusieurs minutes, le temps que l’exécution de chaque pipeline se termine.

[![Tableau de bord des détails de l’exécution](media/tutorial-1st-experiment-automated-ml/run-details.png)](media/tutorial-1st-experiment-automated-ml/run-details-expanded.png#lightbox)

## <a name="deploy-model"></a>Déployer le modèle

Pour cette expérience, **VotingEnsemble** est considéré comme le meilleur modèle d’après la métrique **AUC_weighted**. Avec le Machine Learning automatisé dans le portail Azure, nous pouvons déployer ce modèle en tant que service web pour effectuer des prédictions sur les nouvelles données d’un seul clic. 

1. Dans la page **Détails de l’exécution**, sélectionnez le bouton **Déployer le meilleur modèle**.

1. Renseignez le volet **Déployer le meilleur modèle** comme suit :

    Champ| Valeur
    ----|----
    Nom du déploiement| my-automl-deploy
    Description du déploiement| Déploiement de ma première expérience de Machine Learning automatisé
    Script de scoring| Générer automatiquement
    Script d’environnement| Générer automatiquement
    
1. Sélectionnez **Déployer**. Le déploiement peut prendre environ 20 minutes.

    Le message suivant s’affiche une fois le déploiement terminé.

    ![Déploiement terminé](media/tutorial-1st-experiment-automated-ml/deploy-complete-status.png)
    
    Et voilà ! Vous disposez d'un service web opérationnel pour générer des prédictions.

## <a name="clean-up-resources"></a>Supprimer des ressources

Les fichiers de déploiement sont plus volumineux que les fichiers de données et d’expérimentation. Le coût de leur stockage est donc plus élevé. Supprimez uniquement les fichiers de déploiement pour réduire les coûts associés à votre compte, et si vous souhaitez conserver vos fichiers d’expérience et d’espace de travail. Dans le cas contraire, supprimez l’intégralité du groupe de ressources, si vous n’envisagez pas d’utiliser les fichiers.  

### <a name="delete-deployment-instance"></a>Supprimer l’instance de déploiement

Supprimez uniquement l’instance de déploiement du portail Azure si vous souhaitez conserver le groupe de ressources et l’espace de travail pour d’autres tutoriels et à des fins d’exploration. 

1. Accédez au volet **Ressources** à gauche et sélectionnez **Déploiements**. 

1. Sélectionnez le déploiement à supprimer et sélectionnez **Supprimer**. 

1. Sélectionnez **Continuer**.

### <a name="delete-resource-group"></a>Supprimer un groupe de ressources

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel de Machine Learning automatisé, vous avez utilisé le portail Azure pour créer et déployer un modèle de classification. Pour plus d’informations et pour connaître les étapes suivantes, consultez ces articles.

+ Guide pratique pour [consommer un service web](how-to-consume-web-service.md).
+ En savoir plus sur le [prétraitement](how-to-create-portal-experiments.md#preprocess).
+ En savoir plus sur le [profilage des données](how-to-create-portal-experiments.md#profile).
+ En savoir plus sur le [Machine Learning automatisé](concept-automated-ml.md).

>[!NOTE]
> Ce jeu de données Bank Marketing est disponible sous licence [Creative Commons (CCO : domaine public)](https://creativecommons.org/publicdomain/zero/1.0/). Tous les droits du contenu individuel de la base de données sont concédés sous [licence du contenu de base de données](https://creativecommons.org/publicdomain/zero/1.0/) et sont disponibles sur [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset). Ce jeu de données est initialement disponible dans la [base de données Machine Learning UCI](https://archive.ics.uci.edu/ml/datasets/bank+marketing).<br><br>
>  Veuillez citer le travail suivant : <br> [Moro et al., 2014] S. Moro, P. Cortez et P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. Decision Support Systems, Elsevier, 62:22-31, Juin 2014.