---
title: 'Démarrage rapide : Exécuter un notebook dans le cloud'
titleSuffix: Azure Machine Learning service
description: Ce tutoriel vous montre comment bien démarrer avec Azure Machine Learning service et comment utiliser un serveur de notebooks managé dans le cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: quickstart
author: sdgilley
ms.author: sgilley
ms.date: 05/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1124bb17abb9340b442d8d6075551ffe0dc681f7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68371061"
---
# <a name="quickstart-use-a-cloud-based-notebook-server-to-get-started-with-azure-machine-learning"></a>Démarrage rapide : Utiliser un serveur de notebooks cloud pour démarrer avec Azure Machine Learning

Ce guide de démarrage rapide vous explique comment commencer à utiliser Azure Machine Learning service à l’aide d’un serveur de notebooks managé dans le cloud. Aucune installation n’est requise. Si vous souhaitez installer à la place le kit de développement logiciel (SDK) dans votre propre environnement Python, consultez [Démarrage rapide : Utiliser votre propre serveur de notebooks pour démarrer avec Azure Machine Learning](quickstart-run-local-notebook.md).

Ce guide de démarrage rapide vous montre comment utiliser l’[espace de travail Azure Machine Learning service](concept-azure-machine-learning-architecture.md) pour suivre vos expériences de Machine Learning. Vous devez pour cela créer une [machine virtuelle de notebooks (préversion)](how-to-configure-environment.md#notebookvm). Il s’agit d’une station de travail Azure basée sur le cloud et sécurisée qui fournit un serveur de notebooks Jupyter, JupyterLab et un environnement Machine Learning prêt à l’emploi. Vous exécuterez ensuite un notebook Python sur cette machine virtuelle qui enregistre des valeurs dans l’espace de travail.

Pour cela, effectuez les actions ci-dessous :

* Créez un espace de travail.
* Créez une machine virtuelle Notebook dans votre espace de travail.
* Ouvrez l’interface web Jupyter.
* Ouvrez un notebook qui contient du code pour estimer pi et qui journalise les erreurs à chaque itération.
* Exécutez le notebook.
* Affichez les valeurs d’erreur journalisées dans votre espace de travail. L’exemple suivant montre comment l’espace de travail vous aide à suivre les informations générées dans un script.

Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning service](https://aka.ms/AMLFree).

## <a name="create-a-workspace"></a>Créer un espace de travail

Si vous disposez d’un espace de travail Azure Machine Learning service, passez à la [section suivante](#create-notebook). Dans le cas contraire, créez-en un maintenant.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="create-notebook"></a>Créer une machine virtuelle Notebook

 À partir de votre espace de travail, créez une ressource cloud pour commencer à utiliser des notebooks Jupyter. Cette ressource est une plateforme cloud préconfigurée avec tout ce dont vous avez besoin pour exécuter Azure Machine Learning service.

1. Ouvrez votre espace de travail dans le [portail Azure](https://portal.azure.com/). Si vous ne savez pas comment localiser votre espace de travail sur le portail, découvrez comment [afficher votre espace de travail](how-to-manage-workspace.md#view).

1. Dans la page de votre espace de travail, sélectionnez **Machines virtuelles Notebook** sur la gauche.

1. Sélectionnez **+Nouveau** pour créer un machine virtuelle Notebook.  

     ![Sélectionner Nouvelle machine virtuelle](./media/quickstart-run-cloud-notebook/add-workstation.png)

1. Attribuez un nom à votre machine virtuelle. Sélectionnez ensuite **Créer**.

    > [!NOTE]
    > Le nom de votre machine virtuelle Notebook doit comprendre entre 2 et 16 caractères. Les lettres, les chiffres et les traits d’union sont des caractères valides. Le nom doit être unique dans votre abonnement Azure.

    ![Créer une machine virtuelle](media/quickstart-run-cloud-notebook/create-new-workstation.png)

1. Attendez environ quatre à cinq minutes, le temps que l’état passe à **En cours d’exécution**.


## <a name="open-the-jupyter-web-interface"></a>Ouvrir l’interface web Jupyter

Une fois que votre machine virtuelle s’exécute, ouvrez l’interface web Jupyter à partir de la section **Machines virtuelles Notebook**.

1. Sélectionnez **Jupyter** dans la colonne **URI** correspondant à votre machine virtuelle.  

    ![Démarrer le serveur de notebooks Jupyter](./media/quickstart-run-cloud-notebook/start-server.png)

    Le lien démarre votre serveur de notebooks et ouvre la page web de notebook Jupyter dans un nouvel onglet du navigateur.  Ce lien fonctionne uniquement pour la personne qui crée la machine virtuelle.  Chaque utilisateur de l’espace de travail doit créer sa propre machine virtuelle.

1. Dans la page web du notebook Jupyter, le nom du dossier supérieur est votre nom d’utilisateur. Sélectionnez ce dossier.

    > [!TIP]
    > Ce dossier se trouve sur le [conteneur de stockage](concept-workspace.md#resources) dans votre espace de travail plutôt que sur le notebook de machine virtuelle elle-même.  Vous pouvez supprimer la machine virtuelle du notebook et conserver tous vos travaux.  Lorsque vous créez une nouvelle machine virtuelle notebook, elle chargera ce même dossier.  Si vous partagez votre espace de travail avec d’autres utilisateurs, ils verront votre dossier et vous verrez le leur. 

1. Le nom du dossier samples inclut un numéro de version, (par exemple **samples-1.0.33.1**). Sélectionnez le dossier samples.

1. Sélectionnez le dossier **Quickstart**.

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

Exécutez un notebook qui estime pi et journalise l’erreur dans votre espace de travail.

1. Sélectionnez **01.run-experiment.ipynb** pour ouvrir le notebook.

1. Si vous voyez une alerte « Noyau introuvable », sélectionnez le noyau **Python 3.6 - AzureML** (environ à mi-chemin dans la liste) et définissez le noyau.

1. Sélectionnez la première cellule de code, puis **Exécuter**.

    > [!NOTE]
    > Les cellules de code sont précédées de crochets. Si les crochets sont vides ( __[  ]__ ), cela signifie que le code n’a pas été exécuté. Pendant l’exécution du code, un astérisque ( __[*]__ ) est visible. Le nombre **[1]** s’affiche à la fin de l’exécution du code.  Ce nombre indique l’ordre d’exécution des cellules.
    >
    > Pour exécuter une cellule, utilisez **Maj + Entrée** comme raccourci.

    ![Exécuter la première cellule de code](media/quickstart-run-cloud-notebook/cell1.png)

1. Exécutez la deuxième cellule de code. Si des instructions d’authentification s’affichent, copiez le code et suivez le lien pour vous connecter. Une fois que vous êtes connecté, votre navigateur mémorise cette configuration.  

    ![Authentifier](media/quickstart-run-cloud-notebook/authenticate.png)

1. Quand l’exécution de la cellule de code réussit, le numéro de cellule __[2]__ apparaît. Si vous deviez vous connecter, un message d’état d’authentification réussie s’affiche.   Si vous n’aviez pas besoin de vous connecter, vous ne verrez aucune sortie pour cette cellule. Vous verrez uniquement le numéro, qui s’affiche pour indiquer que la cellule a été exécutée avec succès.

    ![Message de réussite](media/quickstart-run-cloud-notebook/success.png)

1. Exécutez les cellules de code restantes. Le numéro de chaque cellule s’affiche à mesure que son exécution se termine. Seule la dernière cellule peut afficher une autre sortie.  

    Dans la cellule de code la plus grande, `run.log` apparaît à plusieurs endroits. Chaque occurrence de `run.log` ajoute sa valeur à votre espace de travail.

## <a name="view-logged-values"></a>Afficher les valeurs journalisées

1. La sortie de la cellule `run` contient un lien qui ramène au portail Azure, où vous pouvez voir les résultats de l’expérience dans votre espace de travail.

    ![Afficher les expériences](./media/quickstart-run-cloud-notebook/view-exp.png)

1. Sélectionnez **Lien vers le portail Azure** pour voir des informations sur l’exécution dans votre espace de travail. Le lien ouvre votre espace de travail dans le portail Azure.

1. Les tracés des valeurs consignées que vous voyez ont été créés automatiquement dans l’espace de travail. Chaque fois que vous consignez plusieurs valeurs avec le même paramètre de nom, un tracé est généré automatiquement pour vous. Voici un exemple :

   ![Afficher l’historique](./media/quickstart-run-cloud-notebook/web-results.png)

Étant donné que le code pour se rapprocher de pi utilise des valeurs aléatoires, vos tracés pourraient sembler différents.  

## <a name="clean-up-resources"></a>Supprimer des ressources

### <a name="stop-the-notebook-vm"></a>Arrêter la machine virtuelle Notebook

Quand vous n’utilisez pas la machine virtuelle Notebook, arrêtez-la pour réduire les coûts.  

1. Dans votre espace de travail, sélectionnez **Machines virtuelles Notebook**.

   ![Arrêter le serveur de machines virtuelles](./media/quickstart-run-cloud-notebook/stop-server.png)

1. Sélectionnez la machine virtuelle dans la liste.

1. Sélectionnez **Arrêter**.

1. Quand vous êtes prêt à utiliser à nouveau le serveur, sélectionnez **Démarrer**.

### <a name="delete-everything"></a>Tout supprimer

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

Vous pouvez également conserver le groupe de ressources mais supprimer un espace de travail unique. Affichez les propriétés de l’espace de travail, puis sélectionnez **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Une fois ces tâches terminées, accédez à la page Web Jupyter Notebook. Dans le dossier **Quickstart**, ouvrez et exécutez le notebook **02.deploy-web-service.ipynb** pour découvrir comment déployer un service web.

Quand vous souhaitez installer d’autres packages Python dans votre environnement Jupyter, utilisez ce code dans un notebook :

```
!source activate py36 && pip install <packagename>
```

Toujours dans la page web Jupyter Notebook, parcourez les autres notebooks dans le dossier samples pour en savoir plus sur Azure Machine Learning service.

Pour une expérience approfondie du flux de travail, suivez les didacticiels Machine Learning relatifs à l’apprentissage et au déploiement d’un modèle :  

> [!div class="nextstepaction"]
> [Tutoriel : Entraîner un modèle de classification d’images](tutorial-train-models-with-aml.md)
