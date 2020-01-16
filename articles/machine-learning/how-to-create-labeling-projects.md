---
title: Créer un projet d’étiquetage des données
titleSuffix: Azure Machine Learning
description: Découvrez comment créer et exécuter des projets d’étiquetage afin de marquer des données pour le machine learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 864cccc4629140754a326823cbaebd7ad8933d3d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75765067"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Créer un projet d’étiquetage des données et exporter des étiquettes 

L’étiquetage de grandes quantités de données est souvent un casse-tête dans les projets de Machine Learning. Les projets qui ont un composant de vision par ordinateur, par exemple la classification d’images ou la détection d’objet, nécessitent généralement des étiquettes pour des milliers d’images.
 
[Azure Machine Learning](https://ml.azure.com/) vous fournit un emplacement central pour créer, gérer et superviser les projets d’étiquetage. Il vous permet de coordonner les données, les étiquettes et les membres de l’équipe pour gérer efficacement les tâches d’étiquetage. Machine Learning prend en charge la classification d’images (multiétiquettes ou multiclasses) ainsi que l’identification d’objets avec des cadres englobants.

Machine Learning effectue un suivi de la progression et gère la file d’attente des tâches d’étiquetage incomplètes. Les étiqueteurs n’ont pas besoin d’un compte Azure pour participer. Une fois qu’ils se sont authentifiés à l’aide de votre compte Microsoft ou d’[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), ils peuvent effectuer autant d’étiquetage que le temps le leur permet.

Dans Machine Learning, vous pouvez démarrer et arrêter le projet, ajouter et supprimer des personnes ou des équipes, et superviser la progression. Vous pouvez exporter des données étiquetées au format COCO ou en tant que jeu de données Azure Machine Learning.

> [!Important]
> Seuls les projets d’étiquetage de classification des images et d’identification des objets sont actuellement pris en charge. De plus, les images de données doivent être disponibles dans un magasin de données blob Azure. (Si vous ne disposez pas d’un magasin de données, vous pouvez charger des images pendant la création du projet.) 

Dans cet article, vous allez apprendre à :

> [!div class="checklist"]
> * Création d’un projet
> * Spécifier les données et la structure du projet
> * Gérer les équipes et les personnes qui travaillent sur le projet
> * Exécuter et surveiller le projet
> * Exporter les étiquettes


## <a name="prerequisites"></a>Conditions préalables requises

* Les données à étiqueter, dans des fichiers locaux ou un stockage Azure.
* L’ensemble d’étiquettes à appliquer.
* Des instructions pour l’étiquetage.
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Créer un projet d’étiquetage

Les projets d’étiquetage sont administrés à partir d’Azure Machine Learning. La page **Projets d’étiquetage** vous permet de gérer les projets et les personnes. Une ou plusieurs équipes sont attribuées à un projet, et chaque équipe comprend une ou plusieurs personnes attribuées à celui-ci.

Si vos données se trouvent déjà dans le Stockage Blob Azure, vous devez les rendre disponibles sous la forme d’un magasin de données avant de créer le projet d’étiquetage. Pour plus d’informations, consultez [Créer et inscrire des magasins de données](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores).

Pour créer un projet, sélectionnez **Ajouter un projet**. Donnez-lui un nom approprié, puis sélectionnez **Type de tâche d’étiquetage**.

![Assistant Création de projet d’étiquetage](./media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Choisissez **Classification d’images multiétiquettes** pour les projets où vous souhaitez appliquer *une ou plusieurs* étiquettes d’un ensemble de classes à une image. Par exemple, vous pouvez étiqueter la photo d’un chien avec *dog* (chien) et *daytime* (jour).
* Choisissez **Classification d’images multiclasses** pour les projets où vous souhaitez appliquer uniquement une *seule classe* d’un ensemble de classes à une image.
* Choisissez **Identification d’objet (cadre englobant)** pour les projets où vous souhaitez affecter une classe et un cadre englobant à chaque objet d’une image.

Quand vous êtes prêt à continuer, sélectionnez **Suivant**.

## <a name="specify-the-data-to-label"></a>Spécifier les données à étiqueter

Si vous avez déjà créé un jeu de données qui contient vos données, sélectionnez-le dans la liste déroulante **Sélectionner un jeu de données existant**. Sinon, sélectionnez **Créer un jeu de données** pour utiliser un magasin de données Azure existant ou pour charger des fichiers locaux.

### <a name="create-a-dataset-from-an-azure-datastore"></a>Créer un jeu de données à partir d’un magasin de données Azure

Dans de nombreux cas, il suffit de charger des fichiers locaux. Toutefois, l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) constitue un moyen plus rapide et plus robuste de transférer une grande quantité de données. Nous recommandons l’Explorateur Stockage en tant que méthode par défaut pour déplacer des fichiers.

Pour créer un jeu de données à partir de données que vous avez déjà stockées dans le Stockage Blob Azure :

1. Sélectionnez **Créer un jeu de données** > **À partir du magasin de données**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez **Fichier** en tant que **Type de jeu de données**.  
1. Sélectionnez le magasin de données.
1. Si vos données se trouvent dans un sous-dossier de votre Stockage Blob, choisissez **Parcourir** pour sélectionner le chemin.
    * Ajoutez « /** » au chemin pour inclure tous les fichiers des sous-dossiers du chemin sélectionné.
    * Ajoutez « * */* .* » pour inclure toutes les données figurant dans le conteneur actuel et ses sous-dossiers.
1. Fournissez une description pour votre jeu de données.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

### <a name="create-a-dataset-from-uploaded-data"></a>Créer un jeu de données à partir des données chargées

Pour charger directement vos données :

1. Sélectionnez **Créer un jeu de données** > **À partir de fichiers locaux**.
1. Affectez un **Nom** à votre jeu de données.
1. Choisissez « Fichier » en tant que **Type de jeu de données**.
1. *Facultatif :* Sélectionnez **Paramètres avancés** pour personnaliser le magasin de données, le conteneur et le chemin de vos données.
1. Sélectionnez **Parcourir** pour choisir les fichiers locaux à charger.
1. Fournissez une description de votre jeu de données.
1. Sélectionnez **Suivant**.
1. Vérifiez les détails. Sélectionnez **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données.

Les données sont chargées dans le magasin d’objets blob par défaut (« workspaceblobstore ») de votre espace de travail Machine Learning.

## <a name="specify-label-classes"></a>Spécifier des classes d’étiquettes

Dans la page **Classes d’étiquettes**, spécifiez l’ensemble des classes permettant de catégoriser vos données. Faites-le avec précaution, car la justesse et la rapidité de vos étiqueteurs sont affectées par leur capacité à opérer des choix corrects parmi les classes. Par exemple, au lieu d’indiquer le genre et l’espèce complets de plantes ou d’animaux, utilisez un code de champ ou abrégez le genre.

Entrez une étiquette par ligne. Utilisez le bouton **+** pour ajouter une nouvelle ligne. Si vous avez plus de 3 ou 4 étiquettes mais moins de 10, vous pouvez ajouter des chiffres devant les noms (« 1 : », « 2 : »), ce qui permet aux étiqueteurs d’utiliser les touches numériques pour accélérer le travail.

## <a name="describe-the-labeling-task"></a>Décrire la tâche d’étiquetage

Il est important d’expliquer clairement la tâche d’étiquetage. Dans la page **Instructions d’étiquetage**, vous pouvez ajouter un lien vers un site externe où trouver des instructions relatives à l’étiquetage. Veillez à ce que les instructions soient axées sur la tâche et appropriées pour le public. Prenez en compte les questions suivantes :

* Quelles sont les étiquettes qu’ils vont voir, et comment vont-ils les choisir ? Existe-t-il un texte de référence à consulter ?
* Que doivent-ils faire si aucune étiquette ne semble appropriée ?
* Que doivent-ils faire si plusieurs étiquettes semblent appropriées ?
* Quel seuil de confiance doivent-ils appliquer à une étiquette ? Voulez-vous qu’ils exercent leur « meilleur jugement » s’ils hésitent ?
* Que doivent-ils faire avec des objets partiellement masqués ou qui se chevauchent ?
* Que doivent-ils faire si un objet est coupé par le bord de l’image ?
* Que doivent-ils faire après avoir soumis une étiquette, s’ils pensent avoir commis une erreur ?

Pour les cadres englobants, les questions importantes sont les suivantes :

* Comment le rectangle englobant est-il défini pour cette tâche ? Doivent-ils être entièrement à l’intérieur des objets, ou doivent-il être à l’extérieur ? Doivent-ils être rognés le plus fidèlement possible, ou une certaine approximation est-elle acceptable ?
* Quel niveau de soin et de cohérence attendez-vous des étiqueteurs dans la définition des cadres englobants ?

>[!NOTE]
> Notez bien que les étiqueteurs peuvent sélectionner les 9 premières étiquettes à l’aide des touches numériques allant de 1 à 9.

## <a name="initialize-the-labeling-project"></a>Initialiser le projet d’étiquetage

Une fois le projet d’étiquetage initialisé, certains de ses aspects sont non modifiables. Vous ne pouvez pas changer le type de tâche ou le jeu de données. Vous *pouvez* modifier les étiquettes et l’URL de la description de la tâche. Passez en revue attentivement les paramètres avant de créer le projet. Une fois le projet soumis, vous retournez à la page d’accueil **Étiquetage**, qui montre que l’état du projet indique **Initialisation**. Cette page ne s’actualise pas automatiquement. Après une pause, actualisez donc manuellement la page pour que l’état du projet indique **Créé**.

## <a name="manage-teams-and-people"></a>Gérer les équipes et les personnes

Par défaut, chaque projet d’étiquetage que vous créez se voit affecter une nouvelle équipe dont vous êtes membre. Toutefois, les équipes peuvent également être partagées entre les projets. Et les projets peuvent avoir plus d’une équipe. Pour créer une équipe, sélectionnez **Ajouter une équipe** dans la page **Équipes**.

Vous gérez les personnes concernées dans la page **Personnes**. Ajoutez et supprimez des personnes à l’aide de leur adresse e-mail. Chaque étiqueteur doit s’authentifier via votre compte Microsoft ou Azure Active Directory, si vous l’utilisez.  

Après avoir ajouté une personne, vous pouvez l’affecter à une ou plusieurs équipes : Accédez à la page **Équipes**, sélectionnez l’équipe, puis sélectionnez **Affecter des personnes** ou **Supprimer des personnes**.

Pour envoyer un e-mail à l’équipe, sélectionnez-la afin d’afficher la page **Détails de l’équipe**. Dans cette page, sélectionnez **Envoyer un e-mail à l’équipe** pour ouvrir un brouillon d’e-mail contenant les adresses de tous les membres de l’équipe.

## <a name="run-and-monitor-the-project"></a>Exécuter et surveiller le projet

Une fois le projet initialisé, Azure commence à l’exécuter. Sélectionnez le projet dans la page **Étiquetage** principale pour accéder à **Détails du projet**. L’onglet **Tableau de bord** affiche la progression de la tâche d’étiquetage.

Sous l’onglet **Données**, vous pouvez voir votre jeu de données et passer en revue les données étiquetées. Si vous voyez des données étiquetées de manière incorrecte, sélectionnez-les, puis choisissez **Rejeter**. Les étiquettes sont alors supprimées et les données replacées en file d’attente des données sans étiquette.

Utilisez l’onglet **Équipe** pour affecter ou annuler l’affectation des équipes au projet.

Pour suspendre ou redémarrer le projet, sélectionnez le bouton **Suspendre**/**Démarrer** . Vous pouvez uniquement étiqueter des données quand le projet est en cours d’exécution.

Vous pouvez étiqueter des données directement à partir de la page **Détails du projet** en sélectionnant **Étiqueter les données**.

## <a name="export-the-labels"></a>Exporter les étiquettes

Vous pouvez exporter les données d’étiquette pour Machine Learning - Expérimentation à tout moment. Vous pouvez exporter les étiquettes d’image au [format COCO](http://cocodataset.org/#format-data) ou en tant que jeu de données Azure Machine Learning. Utilisez le bouton **Exporter** de la page **Détails du projet** de votre projet d’étiquetage.

Le fichier COCO est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier situé au sein de *export/coco*. Vous pouvez accéder au jeu de données Azure Machine Learning exporté dans la section **Jeux de données** de Machine Learning. La page de détails du jeu de données fournit également un exemple de code pour accéder aux étiquettes à partir de Python.

![Jeu de données exporté](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Étapes suivantes

* Étiqueter des images pour [la classification d’images ou la détection d’objets](how-to-label-images.md)
* En savoir plus sur [Azure Machine Learning et Machine Learning Studio (classique)](compare-azure-ml-to-studio-classic.md)
