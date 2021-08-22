---
title: Créer et gérer des ressources avec l’extension VS Code (préversion)
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et gérer des ressources Azure Machine Learning à l’aide de l’extension Visual Studio Code pour Azure Machine Learning.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 05/25/2021
ms.openlocfilehash: 8175fa121828b9c50911fee8bb811115a630e919
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/22/2021
ms.locfileid: "112460537"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>Gérer les ressources Azure Machine Learning avec l’extension VS Code (préversion)

Découvrez comment gérer les ressources Azure Machine Learning avec l’extension VS Code.

![Extension VS Code Azure Machine Learning](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>Prérequis

- Abonnement Azure. Si vous n’en avez pas, inscrivez-vous ou essayez la [version gratuite ou payante d’Azure Machine Learning](https://azure.microsoft.com/free/)
- Visual Studio Code. [Installez-le](https://code.visualstudio.com/docs/setup/setup-overview) si ce n’est déjà fait.
- Extension Azure Machine Learning. Suivez le [Guide d’installation de l’extension VS Code Azure Machine Learning](how-to-setup-vs-code.md) pour configurer l’extension.

## <a name="create-resources"></a>Créer des ressources

La façon la plus rapide de créer des ressources consiste à utiliser la barre d’outils de l’extension.

1. Ouvrez la vue Azure Machine Learning.
1. Sélectionnez **+** dans la barre d’activité.
1. Choisissez votre ressource dans la liste déroulante.
1. Configurez le fichier de spécification. Les informations requises dépendent du type de ressource que vous souhaitez créer.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez également créer une ressource à l’aide de la palette de commandes :

1. Ouvrez la palette de commandes **Affichage > Palette de commandes**.
1. Entrez `> Azure ML: Create <RESOURCE-TYPE>` dans la zone de texte. Remplacez `RESOURCE-TYPE` par le type de ressource que vous souhaitez créer.
1. Configurez le fichier de spécification.
1. Ouvrez la palette de commandes **Affichage > Palette de commandes**.
1. Entrez `> Azure ML: Create Resource` dans la zone de texte.

## <a name="version-resources"></a>Ressources de version

Certaines ressources, telles que les environnements, les jeux de données et les modèles, vous permettent d’apporter des modifications à une ressource et de stocker les différentes versions.

Pour créer une version d’une ressource :

1. Utilisez le fichier de spécification existant qui a créé la ressource ou suivez le processus de création de ressources pour créer un nouveau fichier de spécification.
1. Incrémentez le numéro de version dans le modèle.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Tant que le nom de la ressource mise à jour est identique à la version précédente, Azure Machine Learning récupère les modifications et crée une nouvelle version.

## <a name="workspaces"></a>Workspaces

Pour plus d’informations, consultez [Espaces de travail](concept-workspace.md).

### <a name="create-a-workspace"></a>Créer un espace de travail

1. Dans la vue Azure Machine Learning, cliquez avec le bouton droit sur le nœud de votre abonnement et sélectionnez **Créer un espace de travail**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Workspace` dans la palette de commandes.

### <a name="remove-workspace"></a>Supprimer l’espace de travail

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Cliquez avec le bouton doit sur l’espace de travail que vous souhaitez supprimer.
1. Indiquez si vous souhaitez supprimer :
    - *Uniquement l’espace de travail* : cette option supprime **uniquement** la ressource Azure de l’espace de travail. Le groupe de ressources, les comptes de stockage et toute autre ressource à laquelle l’espace de travail a été attaché sont toujours dans Azure.
    - *Avec les ressources associées* : cette option supprime l’espace de travail **et** toutes les ressources qui lui sont associées.

Vous pouvez aussi utiliser la commande `> Azure ML: Remove Workspace` dans la palette de commandes.

## <a name="datastores"></a>Magasins de données

L’extension prend actuellement en charge les magasins de données des types suivants :

- Objets blob Azure
- Azure Data Lake Storage Gen1
- Azure Data Lake Storage Gen2
- Fichier Azure

Pour plus d’informations, consultez [Magasins de données](concept-data.md#datastores).

### <a name="create-a-datastore"></a>Créer une banque de données

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud d’espace de travail sous lequel vous souhaitez créer le magasin de données.
1. Cliquez avec le bouton droit sur le nœud **Magasins de données** et sélectionnez **Créer un magasin de données**.
1. Choisissez le type de magasin de données.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Datastore` dans la palette de commandes.

### <a name="manage-a-datastore"></a>Gérer un magasin de données

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Magasins de données** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le magasin de données de votre choix pour :
    - *Désinscrire le magasin de données*. Supprime le magasin de données de votre espace de travail.
    - *Afficher le magasin de données*. Afficher les paramètres de magasin de données en lecture seule

Vous pouvez également utiliser les commandes `> Azure ML: Unregister Datastore` et `> Azure ML: View Datastore` dans la palette de commandes.

## <a name="datasets"></a>Groupes de données

L’extension prend actuellement en charge les types de jeux de données suivants :

- *Tabulaire* : Vous permet de matérialiser des données dans un DataFrame.
- *Fichier* : fichier ou collection de fichiers. Vous permet de télécharger ou de monter des fichiers dans votre capacité de calcul.

Pour plus d’informations, consultez [Jeux de données](concept-data.md#datasets).

### <a name="create-dataset"></a>Créer un jeu de données

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud d’espace de travail sous lequel vous souhaitez créer le jeu de données.
1. Cliquez avec le bouton droit sur le nœud **Jeux de données** et sélectionnez **Créer un jeu de données**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Dataset` dans la palette de commandes.

### <a name="manage-a-dataset"></a>Gérer un jeu de données

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Jeux de données**.
1. Cliquez avec le bouton droit sur le jeu de données de votre choix pour :
    - **Afficher les propriétés du jeu de données**. Vous permet d’afficher les métadonnées associées à un jeu de données spécifique. Si vous avez plusieurs versions d’un jeu de données, vous pouvez choisir d’afficher uniquement les propriétés de jeu de données d’une version spécifique en développant le nœud du jeu de données et en effectuant les étapes décrites dans cette section sur la version qui vous intéresse.
    - **Afficher l’aperçu d’un jeu de données**. Affichez votre jeu de données directement dans la visionneuse de données VS Code. Notez que cette option n’est disponible que pour les jeux de données tabulaires.
    - **Désinscrire le jeu de données**. Supprime un jeu de données et toutes ses versions de votre espace de travail.

Vous pouvez également utiliser les commandes `> Azure ML: View Dataset Properties` et `> Azure ML: Unregister Dataset` dans la palette de commandes.

## <a name="environments"></a>Environnements

Pour plus d’informations, consultez [Environnements](concept-environments.md).

### <a name="create-environment"></a>Créer un environnement

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud d’espace de travail sous lequel vous souhaitez créer le magasin de données.
1. Cliquez avec le bouton droit sur le nœud **Environnements** et sélectionnez **Créer un environnement**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Environment` dans la palette de commandes.

### <a name="view-environment-configurations"></a>Afficher les configurations de l’environnement

Pour afficher les dépendances et les configurations d’un environnement spécifique dans l’extension

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Environnements**.
1. Cliquez avec le bouton droit sur l’environnement que vous souhaitez afficher et sélectionnez **Afficher l’environnement**.

Vous pouvez aussi utiliser la commande `> Azure ML: View Environment` dans la palette de commandes.

## <a name="experiments"></a>Expériences

Pour plus d’informations, consultez [Expériences](concept-azure-machine-learning-architecture.md#experiments).

### <a name="create-job"></a>Créer un travail

Le moyen le plus rapide de créer un travail consiste à cliquer sur l’icône **Créer un travail** dans la barre d’activité de l’extension.

En utilisant les nœuds de ressource dans la vue Azure Machine Learning :

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Cliquez avec le bouton droit sur le nœud **Expériences** dans votre espace de travail et sélectionnez **Créer un travail**.
1. Choisissez le type de votre travail.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Job` dans la palette de commandes.

### <a name="view-job"></a>Afficher le travail

Pour afficher votre travail dans le studio Azure Machine Learning :

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Expériences** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur l’expérience que vous souhaitez afficher et sélectionnez **Afficher l’expérience dans le studio**.
1. Un message s’affiche pour vous inviter à ouvrir l’URL d’expérience dans Azure Machine Learning Studio. Sélectionnez **Ouvrir**.

Vous pouvez également utiliser la commande `> Azure ML: View Experiment in Studio` dans la palette de commandes.

### <a name="track-run-progress"></a>Suivre la progression d’une exécution

Vous souhaiterez peut-être voir la progression de l’exécution de votre travail. Pour suivre la progression d’une exécution dans Azure Machine Learning Studio à partir de l’extension :

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Expériences** à l’intérieur de votre espace de travail.
1. Développez le nœud du travail dont vous souhaitez suivre la progression.
1. Cliquez avec le bouton droit sur l’exécution et sélectionnez **Afficher l’exécution dans le studio**.
1. Un message s’affiche pour vous inviter à ouvrir l’URL d’exécution dans Azure Machine Learning Studio. Sélectionnez **Ouvrir**.

### <a name="download-run-logs--outputs"></a>Télécharger les journaux et les sorties d’exécution

Une fois qu’une exécution est terminée, vous souhaiterez peut-être télécharger les journaux et des ressources telles que le modèle généré dans le cadre d’une exécution.

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Expériences** à l’intérieur de votre espace de travail.
1. Développez le nœud du travail pour lequel vous souhaitez télécharger des journaux et des sorties.
1. Cliquez avec le bouton droit sur l’exécution :
    - Pour télécharger les sorties, sélectionnez **Télécharger les sorties**.
    - Pour télécharger les journaux, sélectionnez **Télécharger les journaux**.

Vous pouvez également utiliser les commandes `> Azure ML: Download Outputs` et `> Azure ML: Download Logs` dans la palette de commandes.

## <a name="compute-instances"></a>Instances de calcul

Pour plus d’informations, consultez [Instances de calcul](concept-compute-instance.md).

### <a name="create-compute-instance"></a>Créer une instance de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud de **calcul**.
1. Cliquez avec le bouton droit sur le nœud **Instances de calcul** dans votre espace de travail et sélectionnez **Créer un calcul**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Compute` dans la palette de commandes.

### <a name="connect-to-compute-instance"></a>Se connecter à une instance de calcul

Pour utiliser une instance de calcul comme un environnement de développement ou serveur Jupyter distant, consultez [Se connecter à une instance de calcul](how-to-set-up-vs-code-remote.md?tabs=extension).

### <a name="stop-or-restart-compute-instance"></a>Arrêter ou redémarrer une instance de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Instances de calcul** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur l’instance de calcul que vous souhaitez arrêter ou redémarrer, puis sélectionnez **Arrêter l’instance de calcul** ou **Redémarrer l’instance de calcul**.

Vous pouvez également utiliser les commandes `> Azure ML: Stop Compute instance` et `Restart Compute instance` dans la palette de commandes.

### <a name="view-compute-instance-configuration"></a>Afficher la configuration d’instances de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Instances de calcul** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur l’instance de calcul que vous souhaitez inspecter, puis sélectionnez **Afficher les propriétés de l’instance de calcul**.

Vous pouvez aussi utiliser la commande `Azure ML: View Compute instance Properties` dans la palette de commandes.

### <a name="delete-compute-instance"></a>Supprimer une instance de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Instances de calcul** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur l’instance de calcul à supprimer et sélectionnez **Supprimer l’instance de calcul**.

Vous pouvez aussi utiliser la commande `Azure ML: Delete Compute instance` dans la palette de commandes.

## <a name="compute-clusters"></a>Clusters de calcul

Pour plus d’informations, consultez [Cibles de calcul d’entraînement](concept-compute-target.md#train).

### <a name="create-compute-cluster"></a>Créer un cluster de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud de **calcul**.
1. Cliquez avec le bouton droit sur le nœud **Clusters de calcul** dans votre espace de travail et sélectionnez **Créer un calcul**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Compute` dans la palette de commandes.

### <a name="view-compute-configuration"></a>Afficher une configuration de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Clusters de calcul** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur la capacité de calcul que vous souhaitez afficher, puis sélectionnez **Afficher les propriétés du calcul**.

Vous pouvez aussi utiliser la commande `> Azure ML: View Compute Properties` dans la palette de commandes.

### <a name="delete-compute-cluster"></a>Supprimer un cluster de calcul

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Clusters de calcul** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur le calcul à supprimer et sélectionnez **Supprimer le calcul**.

Vous pouvez aussi utiliser la commande `> Azure ML: Remove Compute` dans la palette de commandes.

## <a name="inference-clusters"></a>Clusters d’inférence

Pour plus d’informations, consultez [Cibles de calcul pour l’inférence](concept-compute-target.md#deploy).

### <a name="manage-inference-clusters"></a>Gérer les clusters d’inférence

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Clusters d’inférence** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur le calcul de votre choix pour :
    - **Afficher les propriétés du calcul**. Affiche les données de configuration en lecture seule relatives à votre calcul attaché.
    - **Détacher le calcul**. Détache le calcul de votre espace de travail.

Vous pouvez également utiliser les commandes `> Azure ML: View Compute Properties` et `> Azure ML: Detach Compute` dans la palette de commandes.

### <a name="delete-inference-clusters"></a>Supprimer des clusters d’inférence

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Calculs attachés** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur le calcul à supprimer et sélectionnez **Supprimer le calcul**.

Vous pouvez aussi utiliser la commande `> Azure ML: Remove Compute` dans la palette de commandes.

## <a name="attached-compute"></a>Calcul attaché

Pour plus d’informations, consultez [Calcul non managé](concept-compute-target.md#unmanaged-compute).

### <a name="manage-attached-compute"></a>Gérer le calcul attaché

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Développez le nœud **Calculs attachés** à l’intérieur de votre nœud de **calcul**.
1. Cliquez avec le bouton droit sur le calcul de votre choix pour :
    - **Afficher les propriétés du calcul**. Affiche les données de configuration en lecture seule relatives à votre calcul attaché.
    - **Détacher le calcul**. Détache le calcul de votre espace de travail.

Vous pouvez également utiliser les commandes `> Azure ML: View Compute Properties` et `> Azure ML: Detach Compute` dans la palette de commandes.

## <a name="models"></a>Modèles

Pour plus d’informations, consultez [Modèles](concept-azure-machine-learning-architecture.md#models).

### <a name="create-model"></a>Créer un modèle

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Cliquez avec le bouton droit sur le nœud **Modèles** dans votre espace de travail et sélectionnez **Créer un modèle**.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Model` dans la palette de commandes.

### <a name="view-model-properties"></a>Afficher les propriétés d’un modèle

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Modèles** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le modèle dont vous souhaitez afficher les propriétés, puis sélectionnez **Afficher les propriétés du modèle**. Un fichier contenant les propriétés de votre modèle s’ouvre dans l’éditeur.

Vous pouvez aussi utiliser la commande `> Azure ML: View Model Properties` dans la palette de commandes.

### <a name="download-model"></a>Télécharger un modèle

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Modèles** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le modèle que vous souhaitez télécharger, puis sélectionnez **Télécharger le fichier de modèle**.

Vous pouvez aussi utiliser la commande `> Azure ML: Download Model File` dans la palette de commandes.

### <a name="delete-a-model"></a>Supprimer un modèle

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Modèles** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le modèle à supprimer, puis sélectionnez **Supprimer le modèle**.
1. Un message s’affiche pour vous inviter à confirmer la suppression du modèle. Sélectionnez **OK**.

Vous pouvez aussi utiliser la commande `> Azure ML: Remove Model` dans la palette de commandes.

## <a name="endpoints"></a>Points de terminaison

Pour plus d’informations, consultez [Point de terminaison](concept-azure-machine-learning-architecture.md#endpoints).

### <a name="create-endpoint"></a>Création d’un point de terminaison

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud de votre espace de travail.
1. Cliquez avec le bouton droit sur le nœud **Modèles** dans votre espace de travail et sélectionnez **Créer un point de terminaison**.
1. Choisissez votre type de point de terminaison.
1. Un fichier de spécifications apparaît. Configurez le fichier de spécification.
1. Cliquez avec le bouton droit sur le fichier de spécification et sélectionnez **Azure ML : Créer une ressource**.

Vous pouvez aussi utiliser la commande `> Azure ML: Create Endpoint` dans la palette de commandes.

### <a name="delete-endpoint"></a>Supprimer le point de terminaison

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Points de terminaison** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le déploiement que vous souhaitez supprimer et sélectionnez **Supprimer le service**.
1. Un message s’affiche pour vous inviter à confirmer la suppression du service. Sélectionnez **OK**.

Vous pouvez aussi utiliser la commande `> Azure ML: Remove Service` dans la palette de commandes.

### <a name="view-service-properties"></a>Afficher les propriétés du service

Vous pouvez également afficher et modifier les paramètres associés au déploiement.

1. Développez le nœud d’abonnement qui contient votre espace de travail.
1. Développez le nœud **Points de terminaison** à l’intérieur de votre espace de travail.
1. Cliquez avec le bouton droit sur le déploiement que vous souhaitez gérer :
    - Pour afficher les paramètres de configuration du déploiement, sélectionnez **Afficher les propriétés du service**.

Vous pouvez aussi utiliser la commande `> Azure ML: View Service Properties` dans la palette de commandes.

## <a name="next-steps"></a>Étapes suivantes

[Entraînez un modèle de classification d’image](tutorial-train-deploy-image-classification-model-vscode.md) avec l’extension VS Code.