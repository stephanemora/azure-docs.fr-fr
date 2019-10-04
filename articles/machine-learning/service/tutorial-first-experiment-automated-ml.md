---
title: Créer votre première expérience de Machine learning automatisé
titleSuffix: Azure Machine Learning
description: Découvrez comment entraîner et déployer un modèle de classification avec le Machine Learning automatisé dans la page d’accueil de l’espace de travail d’Azure Machine Learning (préversion).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: tzvikei
author: tsikiksr
ms.reviewer: nibaccam
ms.date: 09/26/2019
ms.openlocfilehash: 38c319fb89e8c763f8231c18cbb59bef099193e2
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2019
ms.locfileid: "71259325"
---
# <a name="tutorial-create-your-first-classification-model-with-automated-machine-learning"></a>Didacticiel : Créer votre premier modèle de classification avec Machine Learning automatisé

Dans ce tutoriel, vous allez découvrir comment créer votre première expérience de Machine Learning automatisé dans la page d’accueil de l’espace de travail (préversion) sans écrire une seule ligne de code. Cet exemple crée un modèle de classification pour prédire si un client souscrit à un compte à terme auprès d’une institution financière.

Avec le machine learning automatisé, vous pouvez automatiser des tâches fastidieuses. Le machine learning automatisé itère rapidement sur de nombreuses combinaisons d’algorithmes et d’hyperparamètres pour vous aider à trouver le meilleur modèle basé sur une métrique de réussite de votre choix.

Dans ce tutoriel, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créez un espace de travail Machine Learning.
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

1. Sélectionnez **Prise en main**.

1. Dans le volet gauche, sélectionnez **Automated ML** dans la section **Authoring** (Création).

   Puisqu’il s’agit de votre premier essai d’Automated ML, l’écran Getting started (Bien démarrer) s’affiche.

   ![Azure Machine Learning Studio](media/tutorial-1st-experiment-automated-ml/get-started.png)

1. Sélectionnez **Créer une expérience**. 

1. Entrez le nom suivant pour l’expérience : `my-1st-automl-experiment`

1. Sélectionnez **Create a new compute** (Créer un nouveau calcul), puis configurez la cible de calcul. Une cible de calcul est un environnement de ressources local ou cloud utilisé pour exécuter votre script d’entraînement ou pour héberger votre déploiement de service. Pour cette expérience, nous utilisons un calcul cloud. 

   Champ | Description | Valeur pour le tutoriel
   ----|---|---
   Nom du calcul |Nom unique qui identifie votre contexte de calcul.|automl-compute
   Taille de la&nbsp;machine&nbsp;virtuelle| Sélectionnez la taille de la machine virtuelle pour votre calcul.|Standard_DS12_V2
   Nœuds min./max. (dans les paramètres avancés)| Pour profiler des données, vous devez spécifier un ou plusieurs nœuds.|Nœuds min. : 1<br>Nœuds max. : 6

   >[!NOTE]
   >Pour ce tutoriel, vous utiliserez le compte de stockage et le conteneur par défaut créés avec votre nouveau calcul. Ces informations sont insérées automatiquement dans le formulaire.
    
1. Sélectionnez **Créer** pour accéder à la cible de calcul. 

   **Quelques minutes sont nécessaires pour achever l’opération**. 

1. Une fois la création terminée, sélectionnez votre nouvelle cible de calcul dans la liste déroulante, puis sélectionnez **Next** (Suivant).

1. Sélectionnez **Upload from local file** (Charger à partir d’un fichier local) pour commencer la création d’un jeu de données. 

    1. Sélectionnez **Parcourir**.
    
    1. Choisissez le fichier **bankmarketing_train.csv** sur votre ordinateur local. Il s’agit du fichier que vous avez téléchargé en tant que [prérequis](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv).

    1. Donnez un nom unique à votre jeu de données et indiquez éventuellement une description. 

    1. En bas à gauche, sélectionnez **Next** (Suivant) pour le charger dans le conteneur par défaut qui a été configuré automatiquement lors de la création de votre espace de travail. La préversion publique prend en charge uniquement les chargements de fichiers locaux. 
    
       Une fois le chargement terminé, le formulaire Settings and preview (Paramètres et aperçu) est prérenseigné en fonction du type de fichier. 
       
    1. Vérifiez que le formulaire **Settings and preview** est renseigné comme ci-dessous, puis sélectionnez **Next** (Suivant).
        
        Champ|Valeur pour le tutoriel
        ---|---
        Format de fichier| Delimited
        Délimiteur| Comma
        Encodage| UTF-8
        En-têtes de colonne| Tous les fichiers ont les mêmes en-têtes
        Ignorer les lignes | Aucun
    
    1. Le formulaire **Schema** permet de configurer davantage vos données pour cette expérience. Pour cet exemple, sélectionnez le bouton bascule correspondant à la caractéristique **day_of_week**, afin de ne pas l’inclure pour cette expérience. Sélectionnez **Done** (Terminer) pour terminer le chargement de fichier et la création du jeu de données pour votre expérience.

        ![Configuration de l’onglet Aperçu](media/tutorial-1st-experiment-automated-ml/schema-tab-config.gif)

1. Sélectionnez **Classification** comme tâche de prédiction.

1. Sélectionnez **y** comme colonne cible, ce que vous souhaitez prédire. Cette colonne indique si le client a souscrit à un compte de dépôt à terme.

1. Développez **Paramètres avancés** et renseignez les champs comme suit.

   >[!NOTE]
   > Dans ce tutoriel, vous n’allez pas définir un score de métrique ni un nombre de cœurs maximal par itération. Vous n’allez pas non plus bloquer le test des algorithmes.
   
   Paramètres&nbsp;avancés|Description|Valeur&nbsp;pour&nbsp;le tutoriel
   ------|---------|---
   Métrique principale| Métrique d’évaluation selon laquelle l’algorithme de Machine Learning sera mesuré.|AUC_weighted
   Critères de sortie| Lorsqu’une condition est remplie, la tâche d’entraînement est arrêtée. |Durée&nbsp;de la tâche&nbsp;d’entraînement : 5. <br> <br> &nbsp;#&nbsp; max d’&nbsp;itérations : 10
   Prétraitement| Active le prétraitement effectué par le Machine Learning automatisé. Cela comprend le nettoyage automatique des données, la préparation et la transformation pour générer des caractéristiques synthétiques.| Activer
   Type de validation | Choisissez un type de validation croisée.|Validation croisée k-fold
   Nombre de validations | Nombre de tests. | 2 validations croisées 
   Accès concurrentiel| Nombre maximal d’itérations simultanées.|5\.
   
1. Sélectionnez **Démarrer** pour exécuter l’expérience. Au début de la préparation de l’expérience, un message d’état s’affiche.

>[!IMPORTANT]
> La préparation nécessaire à l’exécution de l’expérience prend **10 à 15** minutes. Une fois que l’exécution a commencé, **2-3 minutes supplémentaires sont nécessaires pour chaque itération**.  
>
> Dans un environnement de production, cette durée est un peu plus longue. Toutefois, dans le cadre de ce tutoriel, nous vous suggérons de commencer à explorer les résultats des itérations à mesure que celles-ci se terminent, pendant que les autres s’exécutent. 

##  <a name="explore-iteration-results"></a>Explorer les résultats des itérations

À mesure que l’expérience progresse, l’écran met à jour le graphique d’itération (**Iteration chart**) et la liste d’itération (**Iteration list**) avec les différentes itérations (modèles) créées à mesure qu’elles se terminent, puis il les classe en fonction de leur score de métrique. Par défaut, le modèle qui obtient le score le plus élevé d’après la métrique **AUC_weighted** choisie figure en haut de la liste.

En attendant que toutes les itérations d’expérience se terminent, sélectionnez le nom (**Name**) d’une itération terminée pour explorer ses performances en détail. 
   
L’exemple suivant montre les graphiques et les métriques d’exécution générés pour chaque itération, tels qu’une courbe de précision et de rappel, une matrice de confusion, des scores de précision pondérée, etc. 

![Détails sur l’exécution de l’itération](media/tutorial-1st-experiment-automated-ml/run-detail.gif)

## <a name="deploy-the-model"></a>Déployer le modèle

Dans la page d’accueil de l’espace de travail, le Machine Learning automatisé vous permet de déployer le meilleur modèle en tant que service web en quelques étapes. Le déploiement consiste à intégrer le modèle pour qu’il puisse prédire de nouvelles données et identifier les domaines potentiels d’opportunités. Dans le cadre de cette expérience, le déploiement sur un service web signifie que l’établissement financier dispose désormais d’une solution web itérative et scalable pour identifier les clients potentiels d’un compte à terme. 

Une fois l’exécution terminée, revenez aux pages de détails **Iteration chart** et **Iterations list**. 

Dans ce contexte d’expérience, **VotingEnsemble** est considéré comme le meilleur modèle d’après la métrique **AUC_weighted**.  Nous déployons ce modèle, mais nous vous informons que le déploiement prend 20 minutes environ. Le processus de déploiement comporte plusieurs étapes, notamment l’inscription du modèle, la génération de ressources et leur configuration pour le service web.

1. Sélectionnez le bouton **Deploy Best Model** (Déployer le meilleur modèle) en haut à droite.

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
