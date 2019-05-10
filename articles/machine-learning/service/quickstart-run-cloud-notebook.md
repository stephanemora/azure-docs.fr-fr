---
title: 'Démarrage rapide : Exécuter un notebook dans le cloud'
titleSuffix: Azure Machine Learning service
description: Prise en main d’Azure Machine Learning service. Utilisez un serveur de notebooks managé dans le cloud pour essayer votre espace de travail.  Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, entraîner et déployer des modèles Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 1a48f8620fb99f1cf8787dabc738d328a796d093
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65510612"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utiliser un serveur de notebooks cloud pour démarrer avec Azure Machine Learning

Créez un serveur de notebooks basé sur le cloud, puis utilisez-le.  Dans ce guide de démarrage rapide, vous allez exécuter du code Python qui journalise les valeurs dans l’[espace de travail Azure Machine Learning service](concept-azure-machine-learning-architecture.md). Dans le cloud, l’espace de travail est le socle que vous utilisez pour expérimenter, effectuer l’apprentissage et déployer des modèles Machine Learning avec Machine Learning. 

Ce guide de démarrage rapide montre comment créer une ressource cloud dans votre espace de travail Azure Machine Learning, configuré avec l’environnement Python nécessaire à l’exécution d’Azure Machine Learning. Pour utiliser à la place votre propre environnement, consultez [Démarrage rapide : Utiliser votre propre serveur de notebooks pour démarrer avec Azure Machine Learning](quickstart-run-local-notebook.md).  

Ce démarrage rapide décrit les actions suivantes :

* Création d’un serveur de notebook basé sur le cloud dans votre espace de travail
* Lancement de l’interface web Jupyter
* Ouverture d’un notebook qui contient du code pour estimer pi et qui journalise les erreurs à chaque itération
* Exécution du notebook
* Affichez les valeurs d’erreur journalisées dans votre espace de travail. Cet exemple montre comment l’espace de travail peut vous aider à suivre les informations générées dans un script.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree) dès aujourd’hui.

## <a name="create-a-workspace"></a>Créer un espace de travail

Si vous disposez d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#create-a-cloud-based-notebook-server). Dans le cas contraire, créez-en un maintenant.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-a-cloud-based-notebook-server"></a>Créer un serveur de notebooks basé sur le cloud

 À partir de votre espace de travail, créez une ressource cloud pour commencer à utiliser des notebooks Jupyter. Cette ressource met à votre disposition une plateforme cloud préconfigurée avec tout ce dont vous avez besoin pour exécuter Azure Machine Learning service.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/).  Si vous ne savez pas comment localiser votre espace de travail sur le portail, découvrez comment [retrouver votre espace de travail](how-to-manage-workspace.md#view).

1. Dans la page de votre espace de travail sur le portail Azure, sélectionnez **Machines virtuelles Notebook** à gauche.

1. Sélectionnez **+Nouveau** pour créer un machine virtuelle Notebook.

     ![Sélectionner Nouvelle machine virtuelle](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Attribuez un nom à votre machine virtuelle. Sélectionnez ensuite **Créer**.

    > [!NOTE]
    > Le nom de votre machine virtuelle Notebook doit comprendre entre 2 et 16 caractères. Les caractères valides sont les lettres, les chiffres et le caractère « - ».  Le nom doit aussi être unique dans votre abonnement Azure.

    ![Créer une machine virtuelle](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Attendez environ 4 à 5 minutes, le temps que l’état passe à **En cours d’exécution**.

## <a name="launch-jupyter-web-interface"></a>Lancer l’interface web Jupyter

Dès lors que votre machine virtuelle s’exécute, lancez l’interface web Jupyter à partir de la section **Machines virtuelles Notebook**.

1. Sélectionnez **Jupyter** dans la colonne **URI** correspondant à votre machine virtuelle.  

    ![Démarrer le serveur de notebooks Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Le lien démarre votre serveur de notebooks et ouvre la page web de notebook Jupyter dans un nouvel onglet du navigateur.  Ce lien fonctionne uniquement pour la personne qui crée la machine virtuelle.

1. Dans la page web du notebook Jupyter, le nom de dossier en haut est votre nom d’utilisateur.  Sélectionnez ce dossier.

1. Le nom de dossier samples inclut un numéro de version, par exemple **samples-1.0.33.1**.  Sélectionnez le dossier samples.

1. Sélectionnez le notebook **quickstart**.

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

Exécutez un notebook qui estime pi et journalise l’erreur dans votre espace de travail.

1. Sélectionnez **01.run-experiment.ipynb** pour ouvrir le notebook.

1. Cliquez dans la première cellule de code, puis sélectionnez **Run** (Exécuter).

    > [!NOTE]
    > Les cellules de code sont précédées de crochets. Si les crochets sont vides (__[  ]__), cela signifie que le code n’a pas été exécuté. Pendant l’exécution du code, un astérisque (__[*]__) est visible. Un nombre **[1]** s’affiche à la fin de l’exécution du code.  Ce nombre indique l’ordre d’exécution des cellules.
    >
    > Pour exécuter une cellule, utilisez **Maj-Entrée** comme raccourci.

    ![Exécuter la première cellule de code](media/quickstart-run-cloud-notebook/cell1.png)

1. Exécutez la deuxième cellule de code. Si des instructions d’authentification s’affichent, copiez le code et suivez le lien pour vous connecter. Une fois que vous êtes connecté, votre navigateur mémorise cette configuration.  

    ![Authentifier](media/quickstart-run-cloud-notebook/authenticate.png)

1. Quand vous avez terminé, le numéro de cellule __[2]__ s’affiche.  Si vous deviez vous connecter, un message d’état d’authentification réussie s’affiche.   Si vous n’aviez pas à vous connecter, aucune sortie n’est émise pour cette cellule ; seul le numéro s’affiche pour indiquer que la cellule s’est exécutée correctement.

    ![Message de réussite](media/quickstart-run-cloud-notebook/success.png)

1. Exécutez les cellules de code restantes.  Le numéro de chaque cellule s’affiche à mesure que son exécution se termine. Seule la dernière cellule peut afficher une autre sortie.  

    Dans la cellule de code la plus grande, vous pouvez constater que `run.log` apparaît à plusieurs endroits. Chaque occurrence de `run.log` ajoute sa valeur à votre espace de travail.

## <a name="view-logged-values"></a>Afficher les valeurs journalisées

1. La sortie de la cellule `run` contient un lien qui ramène au portail Azure pour voir les résultats de l’expérience dans votre espace de travail.

    ![Afficher les expériences](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Cliquez sur le **lien vers le portail Azure** pour voir des informations sur l’exécution dans votre espace de travail.  Le lien ouvre votre espace de travail dans le portail Azure.

1. Les tracés des valeurs consignées que vous voyez ont été créés automatiquement dans l’espace de travail. Chaque fois que vous consignez plusieurs valeurs avec le même paramètre de nom, un tracé est généré automatiquement pour vous.

   ![Afficher l’historique](./media/quickstart-run-cloud-notebook/web-results.png)

Étant donné que le code pour se rapprocher de pi utilise des valeurs aléatoires, vos tracés affichent des valeurs différentes.  

## <a name="clean-up-resources"></a>Supprimer des ressources

### <a name="stop-the-notebook-vm"></a>Arrêter la machine virtuelle Notebook

Quand vous n’utilisez pas la machine virtuelle Notebook, arrêtez-la pour réduire les coûts.  

1. Dans votre espace de travail, sélectionnez **Machines virtuelles Notebook**.

   ![Arrêter le serveur de machines virtuelles](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Sélectionnez la machine virtuelle dans la liste.

1. Sélectionnez **Arrêter**.

1. Quand vous êtes disposé à réutiliser le serveur, sélectionnez **Démarrer**.

### <a name="delete-everything"></a>Tout supprimer

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide de démarrage rapide, vous avez effectué les tâches suivantes :

* Création d’une machine virtuelle Notebook
* Lancement de l’interface web Jupyter
* Ouverture d’un notebook qui contient du code pour estimer pi et qui journalise les erreurs à chaque itération
* Exécution du notebook
* Affichez les valeurs d’erreur journalisées dans votre espace de travail.  Cet exemple montre comment l’espace de travail peut vous aider à suivre les informations générées dans un script. 

Dans la page web Jupyter Notebook, parcourez les autres notebooks dans le dossier samples pour en savoir plus sur le service Azure Machine Learning.

Pour une expérience approfondie du flux de travail, suivez les didacticiels Machine Learning relatifs à l’apprentissage et au déploiement d’un modèle :  

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)
