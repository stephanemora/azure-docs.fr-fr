---
title: 'ML Studio (classique) : Migrer vers Azure Machine Learning – Reconstruire un jeu de données'
description: Reconstruisez des jeux de données Studio (classique) dans le concepteur d’Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 9604fc9d862d94ba5e566753d2186d7d28aa37ee
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308844"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Migrer un jeu de données Studio (classique) vers Azure Machine Learning

Dans cet article, vous allez apprendre à migrer un jeu de données Studio (classique) vers Azure Machine Learning. Pour plus d’informations sur la migration à partir de Studio (classique), consultez [l’article de présentation sur la migration](migrate-overview.md).

Vous avez le choix entre trois options pour migrer un jeu de données vers Azure Machine Learning. Lisez chaque section pour déterminer l’option qui convient le mieux à votre scénario.


|Où sont les données ? | Option de migration  |
|---------|---------|
|Dans Studio (classique)     |  Option 1 : [Téléchargez le jeu de données à partir de Studio (classique) et chargez-le dans Azure Machine Learning](#download-the-dataset-from-studio-classic).      |
|Stockage cloud     | Option 2 : [Inscrivez un jeu de données à partir d’une source cloud](#import-data-from-cloud-sources). <br><br>  Option 3 : [Utilisez le module Importer des données pour obtenir des données à partir d’une source cloud](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning prend également en charge les [workflows de type « Code First »](../how-to-create-register-datasets.md) pour la création et la gestion des jeux de données. 

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Un espace de travail Azure Machine Learning. [Créez un espace de travail Azure Machine Learning](../how-to-manage-workspace.md#create-a-workspace).
- Un jeu de données Studio (classique) à migrer.


## <a name="download-the-dataset-from-studio-classic"></a>Télécharger le jeu de données à partir de Studio (classique)

La façon la plus simple de migrer un jeu de données Studio (classique) vers Azure Machine Learning consiste à télécharger votre jeu de données et à l’inscrire dans Azure Machine Learning. Cela crée une nouvelle copie de votre jeu de données et le charge dans un magasin de données d’Azure Machine Learning.

Vous pouvez télécharger directement les types de jeux de données Studio (classique) suivants.

* Texte brut (.txt)
* Comma-separated values (CSV) avec un en-tête (.csv) ou sans (. nh.csv)
* Tab-separated values (TSV) avec un en-tête (.tsv) ou sans (. nh.tsv)
* Fichier Excel
* Fichier zip (.zip)

Pour télécharger des jeux de données directement :
1. Accédez à votre espace de travail Studio (classique) [[https://studio.azureml.net](https://studio.azureml.net)].
1. Dans la barre de navigation de gauche, sélectionnez l’onglet **Jeux de données**.
1. Sélectionnez le ou les jeux de données que vous souhaitez télécharger.
1. Dans la barre d’action inférieure, sélectionnez **Télécharger**.

    ![Capture d’écran montrant comment télécharger un jeu de données dans Studio (classique)](./media/migrate-register-dataset/download-dataset.png)

Pour les types de données suivants, vous devez utiliser le module **Convertir au format CSV** pour télécharger des jeux de données.

* Données SVMLight (.svmlight) 
* Données Attribute Relation File Format [ARFF] (.arff) 
* Fichier d’espace de travail ou d’objet R (.RData)
* Type de jeu de données (.data). Le type de jeu de données est le type de données internes Studio (classique) pour la sortie du module.

Pour convertir votre jeu de données en CSV et télécharger les résultats :

1. Accédez à votre espace de travail Studio (classique) [[https://studio.azureml.net](https://studio.azureml.net)].
1. Création d'une nouvelle expérience.
1. Faites glisser et déposez le jeu de données que vous souhaitez télécharger sur le canevas.
1. Ajoutez un module **Convertir au format CSV**.
1. Connectez le port d’entrée du module **Convertir au format CSV** au port de sortie de votre jeu de données.
1. Exécutez l’expérience.
1. Cliquez avec le bouton droit sur le module **Convertir au format CSV**.
1. Sélectionnez **Jeu de données des résultats** > **Télécharger**.

    ![Capture d’écran montrant comment configurer un pipeline de conversion en CSV](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Charger votre jeu de données sur Azure Machine Learning

Après avoir téléchargé le fichier de données, vous pouvez inscrire le jeu de données dans Azure Machine Learning :

1. Accédez à Azure Machine Learning studio ([ml.azure.com](https://ml.azure.com)).
1. Dans le volet de navigation de gauche, sélectionnez l’onglet **Jeux de données**.
1. Sélectionnez **Créer un jeu de données** > **À partir de fichiers locaux**.
    ![Capture d’écran montrant l’onglet Jeux de données et le bouton de création d’un fichier local](./media/migrate-register-dataset/register-dataset.png)
1. Entrez un nom et une description.
1. Pour **Type de jeu de données**, sélectionnez **Tabulaire**.

    > [!NOTE]
    > Vous pouvez également charger des fichiers ZIP en tant que jeux de données. Pour charger un fichier ZIP, sélectionnez **Fichier** pour **Type de jeu de données**.

1. Pour **Sélection du magasin de données et des fichiers**, sélectionnez le magasin de données dans lequel vous souhaitez charger votre fichier de jeu de données.

    Par défaut, Azure Machine Learning stocke le jeu de données dans l’espace de travail par défaut « blobstore ». Pour plus d’informations sur les magasins de données, consultez [Se connecter aux services de stockage](../how-to-access-data.md).

1. Définissez les paramètres et le schéma d’analyse des données pour votre jeu de données. Ensuite, confirmez vos paramètres.

## <a name="import-data-from-cloud-sources"></a>Importer des données à partir de sources cloud

Si vos données se trouvent déjà dans un service de stockage cloud et que vous souhaitez conserver vos données à leur emplacement d’origine, vous pouvez utiliser l’une des options suivantes :

|Méthode d’ingestion|Description|
|---| --- |
|Inscrire un jeu de données Azure Machine Learning|Ingérer des données à partir de sources de données locales et en ligne (Blob, ADLS Gen1, ADLS Gen2, Partage de fichiers, Base de données SQL). <br><br>Crée une référence à la source de données, qui est évaluée tardivement au moment de l’exécution. Utilisez cette option si vous accédez à ce jeu de données à plusieurs reprises et que vous souhaitez activer des fonctionnalités avancées de données, telles que le contrôle de version et la surveillance des données.
|Module Importer des données|Ingérer des données à partir de sources de données en ligne (Blob, ADLS Gen1, ADLS Gen2, Partage de fichiers, Base de données SQL). <br><br> Le jeu de données est importé uniquement dans l’exécution du pipeline du concepteur en cours.


>[!Note]
> Les utilisateurs de Studio (classique) doivent noter que les sources cloud suivantes ne sont pas prises en charge en mode natif dans Azure Machine Learning :
> - Requête Hive
> - table Azure
> - Azure Cosmos DB
> - Base de données SQL locale
>
> Nous recommandons aux utilisateurs de migrer leurs données vers des services de stockage pris en charge à l’aide d’Azure Data Factory.  

### <a name="register-an-azure-machine-learning-dataset"></a>Inscrire un jeu de données Azure Machine Learning

Procédez comme suit pour inscrire un jeu de données à Azure Machine Learning à partir d’un service cloud : 

1. [Créez un magasin de données](../how-to-connect-data-ui.md#create-datastores) qui relie le service de stockage cloud à votre espace de travail Azure Machine Learning. 

1. [Inscrivez un jeu de données](../how-to-connect-data-ui.md#create-datasets). Si vous migrez un jeu de données Studio (classique), sélectionnez le paramètre de jeu de données **Tabulaire**.

Une fois que vous avez inscrit un jeu de données dans Azure Machine Learning, vous pouvez l’utiliser dans le concepteur :
 
1. Créez un brouillon de pipeline de concepteur.
1. Dans la palette du module à gauche, développez la section **Jeux de données**.
1. Faites glisser votre jeu de données inscrit sur le canevas. 

### <a name="use-the-import-data-module"></a>Utiliser le module Importer des données

Pour importer des données directement dans votre pipeline du concepteur, procédez comme suit :

1. [Créez un magasin de données](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores) qui relie le service de stockage cloud à votre espace de travail Azure Machine Learning. 

Une fois que vous avez créé le magasin de données, vous pouvez utiliser le module [**Importer des données**](../algorithm-module-reference/import-data.md) dans le concepteur pour ingérer des données à partir de celui-ci :

1. Créez un brouillon de pipeline de concepteur.
1. Dans la palette du module à gauche, recherchez le module **Importer des données** et faites-le glisser dans le canevas.
1. Sélectionnez le module **Importer des données** et utilisez les paramètres dans le volet droit pour configurer votre source de données.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à migrer un jeu de données Studio (classique) vers Azure Machine Learning. L’étape suivante consiste à [reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).


Consultez les autres articles de la série sur la migration Studio (classique) :

1. [Vue d’ensemble de la migration](migrate-overview.md).
1. **Migrer des jeux de données**.
1. [Reconstruire un pipeline de formation Studio (classique)](migrate-rebuild-experiment.md).
1. [Reconstruire un service web Studio (classique)](migrate-rebuild-web-service.md).
1. [Intégrer un service web Azure Machine Learning à des applications clientes](migrate-rebuild-integrate-with-client-app.md).
1. [Migrer Exécuter un script R](migrate-execute-r-script.md).
