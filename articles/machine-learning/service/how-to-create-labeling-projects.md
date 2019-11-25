---
title: Obtenir des étiquettes pour des données
titleSuffix: Azure Machine Learning
description: Cet article explique comment créer et exécuter des projets d’étiquetage afin de baliser des données pour le Machine Learning.
author: lobrien
ms.author: laobri
ms.service: machine-learning
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: ca3486610d6cf71ba315e407b58a2a2551ad6ee1
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837483"
---
# <a name="get-labels-for-data"></a>Obtenir des étiquettes pour des données

L’étiquetage des grandes quantités de données a souvent été un casse-tête dans les projets de Machine Learning. Les projets de Machine Learning avec un composant de vision par ordinateur, tels que la classification d’images ou la détection d’objets, requièrent généralement des milliers d’images et d’étiquettes correspondantes. 
 
Azure Machine Learning vous fournit un emplacement central pour créer, gérer et superviser les projets d’étiquetage. Les projets d’étiquetage aident à coordonner les données, les étiquettes et les membres de l’équipe, ce qui vous permet de gérer plus efficacement les tâches d’étiquetage. Les tâches actuellement prises en charge sont la classification d’images, à plusieurs étiquettes ou multiclasse, et l’identification des objets à l’aide de zones délimitées.

Azure suit la progression et gère la file d’attente des tâches des tâches d’étiquetage incomplètes. Les « étiqueteurs » n’ont pas besoin d’un compte Azure pour participer. Une fois authentifiés à l’aide de leur compte Microsoft (MSA) ou [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-whatis), ils peuvent faire autant d’étiquetage que le temps le leur permet. Ils peuvent attribuer et modifier des étiquettes à l’aide de raccourcis clavier. 

Vous pouvez démarrer et arrêter le projet, ajouter et supprimer des personnes et des équipes, et surveiller la progression. Vous pouvez exporter les données étiquetées au format COCO ou en tant que jeu de données Azure Machine Learning. 

Cet article porte sur les points suivants :

> [!div class="checklist"]
> * Création d’un projet
> * Spécifier les données et la structure du projet
> * Gérer les équipes et les personnes travaillant sur le projet
> * Exécuter et surveiller le projet
> * Exporter les étiquettes 

## <a name="prerequisites"></a>Prérequis

* Les données que vous souhaitez étiqueter, soit dans des fichiers locaux, soit dans un stockage Azure
* L’ensemble d’étiquettes que vous souhaitez appliquer
* Des instructions pour l’étiquetage
* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree) dès aujourd’hui
* Un espace de travail Azure Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-a-labeling-project"></a>Créer un projet d’étiquetage

Les projets d’étiquetage sont administrés à partir d’[Azure Machine Learning](https://ml.azure.com/). La page **Projets d’étiquetage** vous permet de gérer vos projets, équipes et personnes. Une ou plusieurs équipes sont attribuées à un projet, et chaque équipe comprend une ou plusieurs personnes attribuées à celui-ci. 

Si vos données sont déjà stockées dans un stockage d’objets blob Azure, vous devez les mettre à disposition en tant que magasin de données avant de créer votre projet d’étiquetage. Pour plus d’informations, voir [Créer et inscrire des magasins de données](https://docs.microsoft.com/azure/machine-learning/service/how-to-access-data#create-and-register-datastores). 

Pour créer un projet, choisissez **Ajouter un projet**. Donnez-lui un nom approprié, puis sélectionnez **Type de tâche d’étiquetage**. 

![Assistant Création de projet d’étiquetage](media/how-to-create-labeling-projects/labeling-creation-wizard.png)

* Choisissez **Classification d’images à plusieurs étiquettes** pour des projets dans lesquels **une _ou plusieurs_** étiquettes d’un ensemble de classes peuvent être appliquées à une image. Par exemple, une photo de chien peut être étiquetée avec *dog* et *Daytime*
* Choisissez **Classification d’images multiclasse** pour des projets dans lesquels une **classe unique** d’un ensemble de classes peut être appliquée à une image
* Choisissez **Identification d’objet (rectangle englobant)** pour des projets dans lesquels la tâche consiste à la fois à attribuer une classe à un objet figurant dans une image, et à spécifier un cadre rectangle englobant entourant l’objet

Quand vous êtes prêt à continuer, choisissez **Suivant**.

## <a name="specify-data-to-be-labeled"></a>Spécifier les données à étiqueter

Si vous avez déjà créé un jeu de données contenant vos données, vous pouvez le sélectionner dans la liste déroulante **Sélectionner un jeu de données existant**. Vous pouvez aussi choisir **Créer un jeu de données** pour sélectionner un magasin de données Azure existant ou pour charger des fichiers locaux. 

### <a name="create-a-dataset-from-an-azure-datastore"></a>Créer un jeu de données à partir d’un magasin de données Azure

Bien que le choix du chargement direct de fichiers locaux soit parfait pour de nombreux cas d’utilisation, l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) est plus robuste et plus rapide pour transférer de grandes quantités de données. Nous vous recommandons l’Explorateur Stockage Azure comme méthode par défaut pour déplacer des fichiers.

Pour créer un jeu de données à partir de données que vous avez déjà stockées dans un stockage d’objets blob Azure :

1. Choisissez **Créer un jeu de données** et **À partir du magasin de données**
1. Attribuez un **Nom** à votre jeu de données
1. Vous devez choisir « Fichier » comme **Type de jeu de données**  
1. Sélectionner le magasin de données 
1. Si vos données se trouvent dans un sous-dossier à l’intérieur du stockage d’objets blob, choisissez **Parcourir** pour sélectionner le chemin d’accès. 
    * Vous pouvez également ajouter `/**` après le chemin d’accès pour inclure tous les fichiers contenus dans les sous-dossiers du chemin d’accès sélectionné
    * Utilisez `**/*.*` pour inclure toutes les données figurant dans le conteneur et les sous-dossiers actuels
1. Fournissez une description de votre jeu de données
1. Sélectionnez **Suivant** 
1. Vérifiez les détails. Vous pouvez choisir **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données

### <a name="create-a-dataset-by-uploading-data"></a>Créer un jeu de données en chargeant des données

Si vous souhaitez charger directement vos données :

1. Choisissez **Créer un jeu de données** et **À partir de fichiers locaux**
1. Attribuez un **Nom** à votre jeu de données
1. Vous devez choisir « Fichier » comme **Type de jeu de données**
1. Si vous choisissez **Paramètres avancés**, vous pouvez personnaliser le magasin de données, le conteneur et le chemin d’accès de vos données
1. Choisissez **Parcourir** pour sélectionner les fichiers locaux à charger
1. Fournissez une description de votre jeu de données
1. Sélectionnez **Suivant** 
1. Vérifiez les détails. Vous pouvez choisir **Précédent** pour modifier les paramètres, ou **Créer** pour créer le jeu de données

Les données sont chargées dans le magasin d’objets blob par défaut (`workspaceblobstore`) de votre espace de travail Azure Machine Learning.

## <a name="specify-label-classes"></a>Spécifier des classes d’étiquettes

Dans la page **Classes d’étiquettes**, vous spécifiez l’ensemble des classes utilisées pour classer vos données. Réfléchissez bien à ces classes, car la précision et la vitesse de vos étiqueteurs dépendront de la facilité avec laquelle ils pourront opérer un choix correct parmi celles-ci. Par exemple, au lieu d’épeler le genre et l’espèce complets de plantes ou d’animaux, il peut être préférable d’utiliser des codes de champ ou d’abréger le genre. 

Entrez une étiquette par ligne en utilisant le bouton **+** pour ajouter une nouvelle ligne. Si vous avez plus de 3 ou 4 étiquettes, mais moins de 10, envisagez de les préfixer avec « 1 : », « 2 : », etc. pour inciter les instructeurs à utiliser les touches numériques pour accélérer leur travail. 

## <a name="describe-the-labeling-task"></a>Décrire la tâche d’étiquetage

Il est important de décrire clairement la tâche d’étiquetage. La page **Instructions d’étiquetage** vous permet de créer un lien vers un site externe où trouver des instructions à l’adresse des étiqueteurs. Veillez à ce que les instructions soient axées sur la tâche et appropriées pour le public. 

* Quelles étiquettes verront-ils et comment les choisiront-ils ? Existe-t-il un texte de référence à consulter ?
* Que doivent-ils faire si aucune étiquette ne semble appropriée ? 
* Que doivent-ils faire si plusieurs étiquettes semblent appropriées ?
* Quel seuil de confiance doivent-ils appliquer à une étiquette ? Voulez-vous qu’ils exercent leur « meilleur jugement » s’ils hésitent ?
* Que doivent-ils faire avec des objets partiellement masqués ou qui se chevauchent ?
* Que doivent-ils faire si un objet est coupé par le bord de l’image ?
* Que doivent-ils faire s’ils pensent avoir commis une erreur avec une image déjà envoyée ? 

Les rectangles englobants soulèvent d’autres questions importantes :

* Comment le rectangle englobant est-il défini pour cette tâche ? Doit-il être à l’intérieur de l’objet, rogné au plus près possible de celui-ci, ou un certain écartement est-il acceptable ? 
* Quel niveau de soin et de cohérence attendez-vous que les étiqueteurs appliquent à la définition des rectangles englobants ?

>[!Note]
> Veillez à préciser que l’étiqueteur pourra choisir parmi les 9 premières étiquettes à l’aide des touches numériques de 1 à 9. 

## <a name="initialize-the-labeling-project"></a>Initialiser le projet d’étiquetage

Une fois le projet d’étiquetage initialisé, certains de ses aspects sont immuables : vous ne pouvez plus modifier le type de tâche ou le jeu de données. Vous pouvez modifier les étiquettes et l’URL de la description de la tâche. Examinez attentivement les paramètres avant de créer le projet. Une fois le projet envoyé, vous êtes redirigé vers la page d’accueil **Étiquetage**, qui indique que le statut du projet est **Initialisation en cours**. Cette page ne s’actualise pas automatiquement. Par conséquent, après un certain temps, un actualisation manuelle indique que le statut du projet est **Créé**. 

## <a name="manage-teams-and-people"></a>Gérer les équipes et les personnes

Un projet d’étiquetage obtient une équipe par défaut et vous ajoute en tant que membre par défaut. Chaque projet d’étiquetage reçoit une nouvelle équipe par défaut, mais les équipes peuvent être partagées entre plusieurs projets. Des projets peuvent avoir plusieurs équipes. Pour créer une équipe, choisissez **Ajouter une équipe** dans la page **Teams**. 

Les personnes sont gérées via la page **personnes**. Vous pouvez ajouter et supprimer des personnes à l’aide de leur adresse de courrier. Chaque étiqueteur doit s’authentifier à l’aide de son compte Microsoft, ou Azure Active Directory si vous l’utilisez.  

Après avoir ajouté une personne, vous pouvez l’attribuer à une ou plusieurs équipes. Accédez à la page **Équipes**, sélectionnez l’équipe qui vous intéresse, puis utilisez les options **Affecter des personnes** ou **Supprimer des personnes** comme vous le souhaitez.

Si vous souhaitez envoyer un e-mail à tous les membres de l’équipe, vous le pouvez en choisissant l’équipe pour afficher la page **Détails de l’équipe**. Dans cette page, le bouton **Envoyer un e-mail à l’équipe** ouvre l’éditeur d’e-mails avec les adresses de tous les membres de l’équipe.

## <a name="run-and-monitor-the-project"></a>Exécuter et surveiller le projet

Une fois le projet initialisé, Azure commence à l’exécuter. Si, dans la page **Étiquetage** principale, vous cliquez sur le projet, vous êtes dirigé vers les **Détails du projet**. L’onglet **Tableau de bord** affiche la progression de la tâche d’étiquetage. 

Sous l’onglet **Données**, vous pouvez examiner votre jeu de données et réviser les données étiquetées. Si vous voyez des données étiquetées incorrectement, vous pouvez les **Sélectionner**, puis choisir **Rejeter**, ce qui a pour effet de supprimer les étiquettes et de replacer les données dans la file d’attente des données sans étiquette. 

Sous l’onglet **Équipe**, vous pouvez attribuer ou annuler l’attribution d’équipes à ce projet. 

Si vous souhaitez mettre le projet hors connexion ou en ligne, choisissez le bouton **Suspendre**/**Démarrer**, qui bascule l’état d’exécution du projet.

Vous pouvez étiqueter des données directement à partir de la page **Détails du projet** en sélectionnant **Étiqueter les données**. Vous ne pouvez étiqueter des données que quand le projet est en cours d’exécution. 

## <a name="export-the-labels"></a>Exporter les étiquettes

Vous pouvez exporter à tout moment les données d’étiquette pour expérimentation de Machine Learning. Vous pouvez exporter des étiquettes d’image au [format COCO](http://cocodataset.org/#format-data) ou en tant que jeu de données Azure Machine Learning. Le bouton **Exporter** se trouve sur la page **Détails du projet** de votre projet d’étiquetage.

Le fichier COCO est créé dans le magasin d’objets blob par défaut de l’espace de travail Azure Machine Learning, dans un dossier sous **export/coco**. Vous pouvez accéder au jeu de données Azure ML exporté dans la section **Jeux de données** d’Azure Machine Learning. La page Détails du jeu de données fournit également un exemple de code pour accéder à vos étiquettes à partir de Python.

![Jeu de données exporté](media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="next-steps"></a>Étapes suivantes

* Étiqueter des images pour [la classification d’images ou la détection d’objets](how-to-label-images.md)
* En savoir plus sur [Azure Machine Learning et Studio](../compare-azure-ml-to-studio-classic.md)