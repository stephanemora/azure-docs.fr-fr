---
title: Exécuter des notebooks Jupyter dans votre espace de travail
titleSuffix: Azure Machine Learning
description: Découvrez comment exécuter un notebook Jupyter sans quitter votre espace de travail dans Azure Machine Learning studio.
services: machine-learning
author: abeomor
ms.author: osomorog
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 01/19/2021
ms.openlocfilehash: 18ccadcf43d41c677a665ed068d093f51389b576
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657330"
---
# <a name="run-jupyter-notebooks-in-your-workspace"></a>Exécuter des notebooks Jupyter dans votre espace de travail

Découvrez comment exécuter vos Jupyter notebooks Jupyter directement dans votre espace de travail dans Azure Machine Learning studio. En plus de la possibilité de lancer [Jupyter](https://jupyter.org/) ou [JupyterLab](https://jupyterlab.readthedocs.io), vous pouvez modifier et exécuter vos blocs-notes sans quitter l’espace de travail.

Pour plus d’informations sur la création et la gestion de fichiers, y compris de notebooks, consultez [Créer et gérer des fichiers dans votre espace de travail](how-to-manage-files.md).

## <a name="prerequisites"></a>Prérequis

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://aka.ms/AMLFree) avant de commencer.
* Un espace de travail Machine Learning. Consultez [Créer un espace de travail Microsoft Azure Machine Learning](how-to-manage-workspace.md).

## <a name="edit-a-notebook"></a>Exécuter un bloc-notes

Pour modifier un bloc-notes, ouvrez n’importe quel bloc-notes situé dans la section **Fichiers utilisateur** de votre espace de travail. Cliquez sur la cellule à modifier.  Si vous n’avez pas de notebooks dans cette section, consultez [Créer et gérer des fichiers dans votre espace de travail](how-to-manage-files.md).

Vous pouvez modifier le bloc-notes sans vous connecter à une instance de calcul.  Lorsque vous souhaitez exécuter les cellules du bloc-notes, sélectionnez ou créez une instance de calcul.  Si vous sélectionnez une instance de calcul arrêtée, elle démarre automatiquement lorsque vous exécutez la première cellule.

Quand une instance de calcul est en cours d’exécution, vous pouvez également utiliser la saisie semi-automatique du code, optimisée par [IntelliSense](https://code.visualstudio.com/docs/editor/intellisense), dans n’importe quel notebook Python.

Vous pouvez également lancer Jupyter ou JupyterLab à partir de la barre d’outils du notebook.  Azure Machine Learning ne fournit pas de mises à jour et ne corrige pas les bogues de Jupyter ou JupyterLab, car il s’agit de produits open source qui sortent des limites du Support Microsoft.

## <a name="focus-mode"></a>Mode focus

Utilisez le mode focus pour développer votre affichage actuel afin de pouvoir vous concentrer sur vos onglets actifs. Le mode focus masque l’explorateur de fichiers Notebooks.

1. Dans la barre d’outils de la fenêtre du terminal, sélectionnez **Mode focus** pour activer le mode focus. En fonction de la largeur de la fenêtre, il peut se trouver sous l’élément de menu **…** dans la barre d’outils.
1. En mode focus, revenez à l’affichage standard en sélectionnant **Affichage standard**.

    :::image type="content" source="media/how-to-run-jupyter-notebooks/focusmode.gif" alt-text="Activer/désactiver le mode focus et l’affichage standard":::

## <a name="use-intellisense"></a>Utiliser IntelliSense

[IntelliSense](https://code.visualstudio.com/docs/editor/intellisense) est une aide à la saisie semi-automatique de code qui comprend un certain nombre de fonctionnalités : Liste des membres, Informations sur les paramètres, Info Express et Compléter le mot. Ces fonctionnalités vous aident à en savoir plus sur le code que vous utilisez, à suivre les paramètres que vous entrez et à ajouter des appels aux propriétés et aux méthodes avec seulement quelques séquences de touches.  

Lorsque vous entrez du code, utilisez Ctrl + Espace pour déclencher IntelliSense.

## <a name="clean-your-notebook-preview"></a>Nettoyer votre notebook (préversion)

> [!IMPORTANT]
> La fonctionnalité Gather (Assembler) est actuellement en préversion publique.
> La préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail en production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Au cours de la création d’un notebook, vous récupérez généralement les cellules que vous avez utilisées pour l’exploration des données ou le débogage. La fonctionnalité *Gather (Assembler)* vous permet de créer un notebook propre sans ces cellules superflues.

1. Exécutez toutes les cellules de votre notebook.
1. Sélectionnez la cellule qui contient le code que vous souhaitez que le nouveau notebook exécute. Par exemple, le code qui soumet une expérience ou le code qui inscrit un modèle.
1. Sélectionnez l’icône **Gather (Assembler)** qui apparaît dans la barre d’outils de la cellule.
    :::image type="content" source="media/how-to-run-jupyter-notebooks/gather.png" alt-text="Capture d’écran : sélectionner l’icône Gather (Assembler)":::
1. Entrez le nom de votre nouveau notebook « assemblé ».  

Le nouveau notebook contient uniquement des cellules de code, ainsi que toutes les cellules requises pour produire les mêmes résultats que ceux obtenus dans la cellule que vous avez sélectionnée pour l’assemblage.

## <a name="save-and-checkpoint-a-notebook"></a>Enregistrement et point de contrôle d’un bloc-notes

Azure Machine Learning crée un fichier de point de contrôle lorsque vous créez un fichier *ipynb*.

Dans la barre d’outils du bloc-notes, sélectionnez le menu, puis **Fichier&gt;Enregistrer et effectuer un point de contrôle** pour enregistrer manuellement le bloc-notes et ajouter un fichier de point de contrôle associé au bloc-notes.

:::image type="content" source="media/how-to-run-jupyter-notebooks/file-save.png" alt-text="Capture d’écran de l’outil d’enregistrement dans la barre d’outils du bloc-notes":::

Chaque bloc-notes est enregistré de façon automatique toutes les 30 secondes. L’enregistrement automatique met à jour uniquement le fichier *ipynb* initial, et non le fichier de point de contrôle.
 
Sélectionnez **Points de contrôle** dans le menu du bloc-notes pour créer un point de contrôle nommé et restaurer le bloc-notes à un point de contrôle enregistré.

## <a name="export-a-notebook"></a>Exporter un notebook

Dans la barre d’outils du notebook, sélectionnez le menu, puis **Exporter en tant que** pour exporter le notebook en tant qu’un des types pris en charge :

* Notebook
* Python
* HTML
* LaTeX

:::image type="content" source="media/how-to-run-jupyter-notebooks/export-notebook.png" alt-text="Exporter un notebook sur votre ordinateur":::

Le fichier exporté est enregistré sur votre ordinateur.

## <a name="run-a-notebook-or-python-script"></a>Exécuter un notebook ou un script Python

Pour exécuter un notebook ou un script Python, vous devez d’abord vous connecter à une [instance de calcul](concept-compute-instance.md) en cours d’exécution.

* Si vous n’avez pas d’instance de calcul, procédez comme suit pour en créer une :

    1. Dans la barre d’outils du notebook ou du script, à droite de la liste déroulante Calcul, sélectionnez **+ Nouveau calcul**. Selon la taille de votre écran, cette option peut se trouver dans un menu **...** .
        :::image type="content" source="media/how-to-run-jupyter-notebooks/new-compute.png" alt-text="Créer un calcul":::
    1. Nommez l’instance de calcul, puis choisissez une **taille de machine virtuelle**. 
    1. Sélectionnez **Create** (Créer).
    1. L’instance de calcul est connectée automatiquement au fichier.  Vous pouvez maintenant exécuter les cellules du notebook ou du script Python à l’aide de l’outil situé à gauche de l’instance de calcul.

* Si vous avez une instance de calcul arrêtée, sélectionnez **Démarrer le calcul** à droite de la liste déroulante Calcul. Selon la taille de votre écran, cette option peut se trouver dans un menu **...** .

    :::image type="content" source="media/how-to-run-jupyter-notebooks/start-compute.png" alt-text="Démarrer l’instance de calcul":::

Vous seul pouvez voir et utiliser les instances de calcul que vous créez.  Vos **Fichiers utilisateur** sont stockés séparément de la machine virtuelle, et partagés entre toutes les instances de calcul dans l’espace de travail.

### <a name="view-logs-and-output"></a>Afficher les journaux et la sortie

Utilisez des [widgets de notebook](/python/api/azureml-widgets/azureml.widgets?preserve-view=true&view=azure-ml-py) pour afficher la progression de l’exécution et les journaux. Un widget est asynchrone et fournit des mises à jour jusqu’à ce que l’apprentissage se termine. Les widgets d’Azure Machine Learning sont également pris en charge dans Jupyter et JupterLab.

:::image type="content" source="media/how-to-run-jupyter-notebooks/jupyter-widget.png" alt-text="Capture d’écran : Widget de notebook Jupyter ":::

## <a name="explore-variables-in-the-notebook"></a>Explorer les variables dans le notebook

Dans la barre d’outils du notebook, utilisez l’outil **Explorateur de variables** pour afficher le nom, le type, la longueur et les exemples de valeurs pour toutes les variables qui ont été créées dans votre notebook.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer.png" alt-text="Capture d’écran : Outil Explorateur de variables":::

Sélectionnez l’outil pour afficher la fenêtre de l’Explorateur de variables.

:::image type="content" source="media/how-to-run-jupyter-notebooks/variable-explorer-window.png" alt-text="Capture d’écran : Fenêtre de l’Explorateur de variables":::

## <a name="navigate-with-a-toc"></a>Naviguer avec une table des matières

Dans la barre d’outils du notebook, utilisez l’outil **Table des matières** pour afficher ou masquer la table des matières.  Démarrez une cellule Markdown avec un titre pour l’ajouter à la table des matières. Cliquez sur une entrée de la table pour atteindre cette cellule dans le notebook.  

:::image type="content" source="media/how-to-run-jupyter-notebooks/table-of-contents.png" alt-text="Capture d’écran : Table des matières dans le notebook":::

## <a name="change-the-notebook-environment"></a>Modifier l’environnement du bloc-notes

La barre d’outils du notebook vous permet de modifier l’environnement d’exécution de votre notebook.  

Ces actions ne modifient pas l’état du bloc-notes ou les valeurs des variables dans celui-ci :

|Action  |Résultats  |
|---------|---------| --------|
|Arrêter le noyau     |  Arrête toute cellule en cours d’exécution. L’exécution d’une cellule entraîne automatiquement le redémarrage du noyau. |
|Naviguer vers une autre section de l’espace de travail     |     Les cellules en cours d’exécution sont arrêtées. |

Les actions ci-après ont pour effet de réinitialiser l’état du bloc-notes et toutes les variables dans celui-ci.

|Action  |Résultats  |
|---------|---------| --------|
| Modifier le noyau | Le bloc-notes utilise le nouveau noyau. |
| Changer d’instance de calcul    |     Le bloc-notes utilise automatiquement la nouvelle instance de calcul. |
| Réinitialiser l’instance de calcul | Redémarre quand vous tentez d’exécuter une cellule. |
| Arrêter l’instance de calcul     |    Aucune cellule ne s’exécute.  |
| Ouvrir le bloc-notes dans Jupyter ou JupyterLab     |    Le bloc-notes s’ouvre dans un nouvel onglet.  |

## <a name="add-new-kernels"></a>Ajouter de nouveaux noyaux

[Utilisez le terminal ](how-to-access-terminal.md#add-new-kernels) pour créer des noyaux et les ajouter à votre instance de calcul. Le notebook trouve automatiquement tous les noyaux Jupyter installés sur l’instance de calcul connectée.

Utilisez la liste déroulante des noyaux à droite pour passer à l’un des noyaux installés.  


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

## <a name="troubleshooting"></a>Dépannage

* Si vous ne pouvez pas vous connecter à un notebook, vérifiez que la communication avec le socket web n’est **pas** désactivée. Pour que la fonctionnalité d’instance de calcul Jupyter fonctionne, la communication avec le socket web doit être activée. Vérifiez que votre réseau autorise les connexions WebSocket à *.instances.azureml.net et *.instances.azureml.ms. 

* Lorsque l’instance de calcul est déployée dans un espace de travail privé, elle n’est accessible qu’à partir d’un réseau virtuel. Si vous utilisez un DNS ou un fichier d’hôtes personnalisé, ajoutez une entrée pour <nom-instance>.<region>.instances.azureml.ms avec l’adresse IP privée du point de terminaison privé de l’espace de travail. Pour plus d’informations, consultez l’article [DNS personnalisé](./how-to-custom-dns.md?tabs=azure-cli).
    
## <a name="next-steps"></a>Étapes suivantes

* [Exécuter votre première expérience](tutorial-1st-experiment-sdk-train.md)
* [Sauvegarder votre stockage de fichiers avec des captures instantanées](../storage/files/storage-snapshots-files.md)
* [Utilisation des environnements sécurisés](./how-to-secure-training-vnet.md#compute-instance)