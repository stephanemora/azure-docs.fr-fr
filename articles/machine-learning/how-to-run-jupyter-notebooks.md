---
title: Comment exécuter des blocs-notes Jupyter dans votre espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter un bloc-notes Jupyter sans quitter votre espace de travail dans Azure Machine Learning Studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 96e37afd8bf7d59eef4a4c0c831f535faa36d34d
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83681447"
---
# <a name="how-to-run-jupyter-notebooks-in-your-workspace"></a>Comment exécuter des blocs-notes Jupyter dans votre espace de travail
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Découvrez comment exécuter vos blocs-notes Jupyter directement dans votre espace de travail dans Azure Machine Learning Studio. En plus de la possibilité de lancer [Jupyter](https://jupyter.org/) ou [JupyterLab](https://jupyterlab.readthedocs.io), vous pouvez modifier et exécuter vos blocs-notes sans quitter l’espace de travail.

Découvrez comment effectuer les opérations suivantes :

* Créer des blocs-notes Jupyter dans votre espace de travail
* Exécuter une expérience à partir d’un bloc-notes
* Modifier l’environnement du bloc-notes
* Trouver des détails sur les instances de calcul utilisées pour exécuter vos blocs-notes

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="create-notebooks"></a><a name="create"></a> Créer des blocs-notes

Dans votre espace de travail Azure Machine Learning, créez un bloc-notes Jupyter, puis commencez à travailler. Le bloc-notes nouvellement créé est stocké dans le stockage de l’espace de travail par défaut. Vous pouvez partager ce bloc-notes avec quiconque ayant accès à l’espace de travail. 

Pour créer un bloc-notes : 

1. Dans [Azure Machine Learning Studio](https://ml.azure.com), ouvrez votre espace de travail.
1. Sur le côté gauche, sélectionnez **Bloc-notes**. 
1. Sélectionnez l’icône **Créer un fichier** dans la section **Mes fichiers** au-dessus de la liste **Fichiers utilisateur**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/create-new-file.png" alt-text="Créer un fichier":::

1. Nommez le fichier. 
1. Pour Fichiers Jupyter Notebook, sélectionnez le type de fichier **Bloc-notes Python**.
1. Sélectionnez un répertoire de fichiers.
1. Sélectionnez **Créer**.

> [!TIP]
> Vous pouvez également créer des fichiers texte.  Sélectionnez le type de fichier **Texte**, puis ajoutez l’extension au nom (par exemple, monfichier.py ou monfichier.txt).  

Vous pouvez également charger des dossiers et fichiers, dont des blocs-notes, avec les outils disponibles en haut de la page Blocs-notes.  Les blocs-notes et la plupart des types de fichiers texte figurent dans la section d’aperçu.  Aucun aperçu n’est disponible pour la plupart des autres types de fichiers.

### <a name="clone-samples"></a>Cloner des exemples

Votre espace de travail comprend un dossier **Exemples** contenant des blocs-notes conçus pour vous aider à explorer le Kit de développement logiciel (SDK) et servir d’exemples pour vos propres projets de Machine Learning.  Vous pouvez cloner ces blocs-notes vers votre propre dossier dans le conteneur de stockage de votre espace de travail.  

Pour obtenir un exemple, consultez [Tutoriel : Créer votre première expérience ML](tutorial-1st-experiment-sdk-setup.md#azure).

### <a name="use-files-from-git-and-version-my-files"></a><a name="terminal"></a> Utiliser des fichiers de Git et contrôler la version de mes fichiers

Vous pouvez accéder à toutes les opérations Git à l’aide d’une fenêtre de terminal. Tous les fichiers et dossiers Git sont stockés dans le système de fichiers de votre espace de travail.

> [!NOTE]
> Ajoutez vos fichiers et dossiers n’importe où dans le dossier **~/cloudfiles/code/Users** afin qu’ils soient visibles dans tous vos environnements Jupyter.

Pour accéder au terminal :

1. Dans [Azure Machine Learning Studio](https://ml.azure.com), ouvrez votre espace de travail.
1. Sur le côté gauche, sélectionnez **Bloc-notes**.
1. Sélectionnez un bloc-notes dans la section **Fichiers utilisateur** sur le côté gauche.  Si aucun bloc-notes ne s’y trouve, commencez par [créer un bloc-notes](#create).
1. Sélectionnez ou créez une cible de **calcul**, puis attendez qu’elle s’exécute.
1. Sélectionnez l’icône **Ouvrir le terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/open-terminal.png" alt-text="Ouvrir le terminal":::

1. Si vous ne voyez pas l’icône, sélectionnez les points de suspension ( **...** ) à droite de la cible de calcul, puis choisissez **Ouvrir le terminal**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/alt-open-terminal.png" alt-text="Ouvrir le terminal à partir de...":::


Apprenez-en davantage sur le [clonage des dépôts Git dans le système de fichiers de votre espace de travail](concept-train-model-git-integration.md#clone-git-repositories-into-your-workspace-file-system).


### <a name="share-notebooks-and-other-files"></a>Partager des blocs-notes et d’autres fichiers

Copiez et collez l’URL pour partager un bloc-notes ou un fichier.  Seuls les autres utilisateurs de l’espace de travail pourront accéder à cette URL.  Apprenez-en davantage sur l’[octroi d’accès à votre espace de travail](how-to-assign-roles.md).

## <a name="edit-a-notebook"></a>Exécuter un bloc-notes

Pour modifier un bloc-notes, ouvrez n’importe quel bloc-notes situé dans la section **Fichiers utilisateur** de votre espace de travail. Cliquez sur la cellule à modifier. 

Quand une instance de calcul active est en cours d’exécution, vous pouvez également utiliser la saisie semi-automatique du code, optimisée par [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), dans n’importe quel bloc-notes Python.

Vous pouvez également lancer Jupyter ou JupyterLab à partir de la barre d’outils du bloc-notes.  Azure Machine Learning ne fournit pas de mises à jour et ne corrige pas les bogues de Jupyter ou JupyterLab, car il s’agit de produits open source qui sortent des limites du Support Microsoft.

### <a name="useful-keyboard-shortcuts"></a>Raccourcis clavier utiles

|Clavier  |Action  |
|---------|---------|
|Maj + Entrée     |  Exécuter une cellule       |
|Ctrl + M (Windows)     |  Activer/désactiver le recouvrement par tabulation dans le bloc-notes.       |
|Ctrl + Maj + M (Mac & Linux)     |    Activer/désactiver le recouvrement par tabulation dans le bloc-notes.     |
|Tab (quand le recouvrement par tabulation est activé) | Ajouter un caractère « \t » (retrait)
|Tab (quand le recouvrement par tabulation est désactivé) | Déplacer le focus vers le prochain élément sur lequel il est possible de le positionner (bouton de suppression de cellule, bouton d’exécution, etc.)

## <a name="delete-a-notebook"></a>Supprimer un bloc-notes

Vous *ne pouvez pas* supprimer les **exemples** de blocs-notes.  Ces blocs-notes font partie du studio et sont mis à jour à chaque publication d’un nouveau Kit de développement logiciel (SDK).  

Vous *pouvez* supprimer des blocs-notes **Fichiers utilisateur** de l’une des manières suivantes :

* Dans le studio, sélectionnez les points de suspension ( **...** ) à la fin d’un nom de dossier ou de fichier.  Veillez à utiliser un navigateur pris en charge (Microsoft Edge, chrome ou Firefox).
* Dans n’importe quelle barre d’outils de bloc-notes, sélectionnez [**Ouvrir le terminal**](#terminal) pour accéder à la fenêtre de terminal de l’instance de calcul.
* Dans Jupyter ou JupyterLab, avec leurs outils.

## <a name="run-an-experiment"></a>Exécuter une expérience

Pour exécuter une expérience à partir d’un bloc-notes, vous devez d’abord vous connecter à une [instance de calcul](concept-compute-instance.md) en cours d’exécution. Si vous n’avez pas d’instance de calcul, procédez comme suit pour en créer une : 

1. Sélectionnez **+** dans la barre d’outils du bloc-notes. 
2. Nommez l’instance de calcul, puis choisissez une **taille de machine virtuelle**. 
3. Sélectionnez **Create** (Créer).
4. L’instance de calcul est automatiquement connectée au bloc-notes et vous pouvez maintenant exécuter vos cellules.

Vous seul pouvez voir et utiliser les instances de calcul que vous créez.  Vos **Fichiers utilisateur** sont stockés séparément de la machine virtuelle, et partagés entre toutes les instances de calcul dans l’espace de travail.

### <a name="view-logs-and-output"></a>Afficher les journaux et la sortie

Utilisez des [widgets de bloc-notes](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) pour afficher la progression de l’exécution et les journaux. Un widget est asynchrone et fournit des mises à jour jusqu’à ce que l’apprentissage se termine. Les widgets d’Azure Machine Learning sont également pris en charge dans Jupyter et JupterLab.

## <a name="change-the-notebook-environment"></a>Modifier l’environnement du bloc-notes

La barre d’outils du bloc-notes vous permet de modifier l’environnement d’exécution de votre bloc-notes.  

Ces actions ne modifient pas l’état du bloc-notes ou les valeurs des variables dans celui-ci :

|Action  |Résultats  |
|---------|---------| --------|
|Arrêter le noyau     |  Arrête toute cellule en cours d’exécution. L’exécution d’une cellule entraîne automatiquement le redémarrage du noyau. |
|Naviguer vers une autre section de l’espace de travail     |     Les cellules en cours d’exécution sont arrêtées. |

Les actions ci-après ont pour effet de réinitialiser l’état du bloc-notes et toutes les variables dans celui-ci.

|Action  |Résultat  |
|---------|---------| --------|
| Modifier le noyau | Le bloc-notes utilise le nouveau noyau. |
| Changer d’instance de calcul    |     Le bloc-notes utilise automatiquement la nouvelle instance de calcul. |
| Réinitialiser l’instance de calcul | Redémarre quand vous tentez d’exécuter une cellule. |
| Arrêter l’instance de calcul     |    Aucune cellule ne s’exécute.  |
| Ouvrir le bloc-notes dans Jupyter ou JupyterLab     |    Le bloc-notes s’ouvre dans un nouvel onglet.  |

### <a name="add-new-kernels"></a>Ajouter de nouveaux noyaux

Le bloc-notes trouve automatiquement tous les noyaux Jupyter installés sur l’instance de calcul connectée.  Pour ajouter un noyau à l’instance de calcul :

1. Dans la barre d’outils du bloc-notes, s²électionnez [**Ouvrir le terminal**](#terminal).
1. Utilisez la fenêtre de terminal pour créer un environnement.
1. Active l’environnement.  Par exemple, après la création de `newenv` :

    ```shell
    source activate newenv
    python -m ipykernel install --user --name newenv --display-name "Python (newenv)"
    ```

Vous pouvez installer n’importe lequel des [noyaux Jupyter disponibles](https://github.com/jupyter/jupyter/wiki/Jupyter-kernels).

### <a name="status-indicators"></a>Indicateurs d’état

Un indicateur en regard de la liste déroulante **Calcul** affiche son état.  L’État est également indiqué dans la liste déroulante elle-même.  

|Couleur |État du calcul |
|---------|---------| 
| Vert | Calcul en cours d’exécution |
| Rouge |Échec du calcul | 
| Noir | Calcul arrêté |
|  Bleu clair |Calcul en cours de création, de démarrage, de redémarrage ou de configuration |
|  Gris |Calcul en cours d’arrêt ou de suppression |

Un indicateur en regard de la liste déroulante **Noyau** affiche son état.

|Couleur |État du noyau |
|---------|---------|
|  Vert |Noyau connecté, inactif, occupé|
|  Gris |Tunnel non connecté. |

## <a name="find-compute-details"></a>Rechercher les détails d’un calcul 

Pour plus de détails sur vos instances de calcul, consultez la page **Calcul** dans de [Studio](https://ml.azure.com).

## <a name="next-steps"></a>Étapes suivantes

* [Exécuter votre première expérience](tutorial-1st-experiment-sdk-train.md)
* [Sauvegarder votre stockage de fichiers avec des captures instantanées](../storage/files/storage-snapshots-files.md)
