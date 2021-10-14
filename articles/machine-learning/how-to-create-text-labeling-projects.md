---
title: Configurer un projet d’étiquetage de texte
titleSuffix: Azure Machine Learning
description: Créez un projet pour étiqueter le texte à l’aide de l’outil d’étiquetage des données. Spécifiez une seule étiquette ou plusieurs étiquettes à appliquer à chaque texte.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e4d227cf215ab5d37af8a25e6e44e811f739f25b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423706"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>Créer un projet d’étiquetage de texte et exporter des étiquettes (préversion)

Découvrez comment créer et exécuter des projets d'étiquetage de données pour étiqueter des données textuelles dans Azure Machine Learning.  Spécifiez une seule étiquette ou plusieurs étiquettes à appliquer à chaque texte.

Vous pouvez également utiliser l’outil d’étiquetage des données pour [créer un projet d’étiquetage d’image](how-to-create-image-labeling-projects.md).

> [!IMPORTANT]
> L’étiquetage de texte est actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="text-labeling-capabilities"></a>Fonctionnalités d’étiquetage textuel

L’étiquetage des données Azure Machine Learning est un emplacement central pour créer, gérer et superviser les projets d’étiquetage des données :

- Coordonnez les données, les étiquettes et les membres de l’équipe pour gérer efficacement les tâches d’étiquetage. 
- Suivez la progression et gérez la file d’attente des tâches d’étiquetage incomplètes.
- Démarrez et arrêtez le projet pour contrôler la progression de l’étiquetage.
- Passez en revue les données étiquetées et exportez-les en tant que jeu de données Azure Machine Learning.

> [!Important]
> Les données textuelles doivent être disponibles dans un magasin de données d’objets blob Azure. (Si vous ne disposez pas d’un magasin de données, vous pouvez charger des fichiers pendant la création du projet.)

Les données de texte peuvent être des fichiers .txt ou .csv.

* Pour les fichiers .txt, chaque fichier représente un seul élément à étiqueter.
* Pour les fichiers « .csv », chaque ligne représente un élément présenté à l’étiqueteur.  Vous pouvez afficher une ou plusieurs colonnes à utiliser lors de l’étiquetage de cette ligne.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>Pour un projet d’étiquetage textuel

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Pour créer un projet, sélectionnez **Ajouter un projet**. Donnez au projet un nom approprié. Le nom du projet ne peut pas être réutilisé, même si le projet est supprimé ultérieurement.

1. Sélectionnez **Texte** pour créer un projet d’étiquetage de texte.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="Création d’un projet d’étiquetage de texte":::

    * Choisissez **Classification de texte multiclasse (préversion)** pour les projets quand vous voulez appliquer *une seule étiquette* issue d’un ensemble d’étiquettes à chaque morceau de texte.
    * Choisissez **Classification de texte multiétiquette (préversion)** pour les projets quand vous voulez appliquer *une ou plusieurs* étiquettes issue d’un ensemble d’étiquettes à chaque morceau de texte.

1. Quand vous êtes prêt à continuer, sélectionnez **Suivant**.

## <a name="add-workforce-optional"></a>Ajouter du personnel (facultatif)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>Spécifier les données à étiqueter

Si vous avez déjà créé un jeu de données qui contient vos données, sélectionnez-le dans la liste déroulante **Sélectionner un jeu de données existant**. Sinon, sélectionnez **Créer un jeu de données** pour utiliser un magasin de données Azure existant ou pour charger des fichiers locaux.

> [!NOTE]
> Un projet ne peut pas contenir plus de 500 000 fichiers.  Si votre jeu de données en contient davantage, seuls les 500 000 premiers fichiers sont chargés.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Créer un jeu de données à partir d’un magasin de données Azure

Dans de nombreux cas, il suffit de charger des fichiers locaux. Toutefois, l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) constitue un moyen plus rapide et plus robuste de transférer une grande quantité de données. Nous recommandons l’Explorateur Stockage en tant que méthode par défaut pour déplacer des fichiers.

Pour créer un jeu de données à partir de données que vous avez déjà stockées dans le Stockage Blob Azure :

1. Sélectionnez **Créer un jeu de données** > **À partir du magasin de données**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez le **type de jeu de données** :
    * Sélectionnez **Tabulaire** si vous utilisez un fichier .csv, où chaque ligne contient une réponse.
    * Sélectionnez **Fichier** si vous utilisez des fichiers .txt distincts pour chaque réponse.
1. (Facultatif) Fournissez une description pour votre jeu de données.
1. Sélectionnez **Suivant**.
1. Sélectionnez le magasin de données.
1. Si vos données se trouvent dans un sous-dossier de votre Stockage Blob, choisissez **Parcourir** pour sélectionner le chemin.
    * Ajoutez « /** » au chemin pour inclure tous les fichiers des sous-dossiers du chemin sélectionné.
    * Ajoutez « * */* .* » pour inclure toutes les données figurant dans le conteneur actuel et ses sous-dossiers.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

### <a name="create-a-dataset-from-uploaded-data"></a>Créer un jeu de données à partir des données chargées

Pour charger directement vos données :

1. Sélectionnez **Créer un jeu de données** > **À partir de fichiers locaux**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez le **type de jeu de données**.
    * Sélectionnez **Tabulaire** si vous utilisez un fichier .csv, où chaque ligne est une réponse.
    * Sélectionnez **Fichier** si vous utilisez des fichiers .txt distincts pour chaque réponse.
1. (Facultatif) Fournissez une description de votre jeu de données.
1. Sélectionnez **Suivant**.
1. (Facultatif) Sélectionnez ou créez un magasin de données. Ou conservez la sélection par défaut pour charger dans le magasin d’objets blob par défaut (« workspaceblobstore ») de votre espace de travail Machine Learning.
1. Sélectionnez **Charger** pour choisir les fichiers ou dossiers locaux à charger.
1. Sélectionnez **Suivant**.
1. Si le chargement de fichiers.csv :
    * Confirmez les paramètres et l’aperçu, puis sélectionnez **Suivant**.
    * Incluez toutes les colonnes de texte que vous souhaitez que l’étiqueteur affiche lors de la classification de cette ligne.
    * Sélectionnez **Suivant**.
1.  Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a> Configurer l’actualisation incrémentielle

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Spécifier des classes d’étiquettes

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>Décrivez la tâche d’étiquetage des données

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> Notez bien que les étiqueteurs peuvent sélectionner les 9 premières étiquettes à l’aide des touches numériques allant de 1 à 9.


## <a name="initialize-the-text-labeling-project"></a>Initialisez le projet d’étiquetage des données

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Exécuter et surveiller le projet

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>tableau de bord


L’onglet **Tableau de bord** affiche la progression de la tâche d’étiquetage.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="Tableau de bord d’étiquetage des données textuelles":::


Le graphique de progression indique le nombre d’éléments étiquetés, ignorés ou non encore terminés.  Pointez sur le graphique pour afficher le nombre d’éléments dans chaque section.

La section centrale affiche la file d’attente des tâches à affecter. 


Dans la partie droite se trouve une distribution des étiquettes pour les tâches qui sont terminées.  Notez que, dans certains types de projets, un élément peut avoir plusieurs étiquettes, auquel cas le nombre total d’étiquettes peut être supérieur au nombre total d’éléments.

### <a name="data-tab"></a>Onglet Données

Sous l’onglet **Données**, vous pouvez voir votre jeu de données et passer en revue les données étiquetées. Faites défiler les données étiquetées pour afficher les étiquettes. Si vous voyez des données étiquetées de manière incorrecte, sélectionnez-les, puis choisissez **Rejeter**. Les étiquettes sont alors supprimées et les données replacées en file d’attente des données sans étiquette.

### <a name="details-tab"></a>Onglet Détails

Affichez les détails de votre projet.  Dans cet onglet, vous pouvez :

* Afficher les détails du projet et les jeux de données d’entrée
* Activer l’actualisation incrémentielle
* Afficher les détails du conteneur de stockage utilisé pour stocker les sorties étiquetées dans votre projet
* Ajouter des étiquettes à votre projet
* Modifier les instructions que vous donnez à vos étiquettes

### <a name="access-for-labelers"></a>Accès pour les étiqueteurs

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Ajouter une nouvelle classe d’étiquette à un projet

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exporter les étiquettes
 
Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage. Vous pouvez exporter les données d’étiquette pour Machine Learning - Expérimentation à tout moment.

Il est possible d’exporter différents éléments :

* Fichier CSV. Le fichier CSV est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé dans *Labeling/export/csv*. 
* [Jeu de données Azure Machine Learning comportant des étiquettes](how-to-use-labeled-dataset.md). 

Accédez aux jeux de données Azure Machine Learning exportés dans la section **Jeux de données** de Machine Learning. La page de détails du jeu de données fournit également un exemple de code pour accéder aux étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Dépannage

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>Étapes suivantes

* [Comment étiqueter du texte](how-to-label-data.md#label-text)

