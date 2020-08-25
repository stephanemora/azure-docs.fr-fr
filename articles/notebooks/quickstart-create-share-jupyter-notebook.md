---
title: Créer et partager un Jupyter Notebook sur la préversion d’Azure Notebooks
description: Créez et exécutez rapidement un Jupyter Notebook sur Azure Notebooks (préversion), puis partagez-le avec d’autres utilisateurs.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: 77575b5b9e006ac9881d03dc95509d55c53c26d7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589331"
---
# <a name="quickstart-create-and-share-a-notebook-in-azure-notebooks-preview"></a>Démarrage rapide : Créer et partager un notebook dans Azure Notebooks (préversion)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Dans ce guide de démarrage rapide, vous créez et exécutez un Jupyter Notebook sur Azure Notebooks, puis vous le partagez avec d’autres utilisateurs. Jupyter vous permet de combiner facilement du texte Markdown, du code exécutable, des données persistantes, des graphiques et des visualisations sur un canevas partageable, le notebook. Azure Notebooks est un service hébergé gratuit à l’aide duquel vous pouvez développer et exécuter des Jupyter Notebooks dans le cloud sans aucune installation.

## <a name="prerequisites"></a>Prérequis
Aucun.

## <a name="create-a-new-project-and-notebook"></a>Créer un projet et un notebook

1. Accédez au [site d’Azure Notebooks (https://notebooks.azure.com)](https://notebooks.azure.com) et connectez-vous. Pour plus d’informations, consultez [Démarrage rapide sur la connexion à Azure Notebooks](quickstart-sign-in-azure-notebooks.md).

1. En haut de votre page de profil public, sélectionnez **Mes projets** :

    ![Lien Mes projets en haut de la fenêtre du navigateur](media/quickstarts/my-projects-link.png)

1. Dans la page **Mes projets**, sélectionnez **+ Nouveau projet** (raccourci clavier : n). Le bouton peut apparaître uniquement sous forme de **+** si la fenêtre du navigateur est étroite :

    ![Commande Nouveau projet dans la page Mes projets](media/quickstarts/new-project-command.png)

1. Dans la fenêtre contextuelle **Créer un projet** qui s’affiche, entrez ou définissez les détails suivants, puis sélectionnez **Créer** :

   - **Nom du projet** : Hello World dans Python
   - **ID du projet** : hello-world-python
   - **Projet public** : (désactivé)
   - **Créer un fichier README.md** : (désactivé)

     ![Fenêtre contextuelle Nouveau projet avec les détails remplis](media/quickstarts/new-project-popup.png)

1. Après quelques instants, Azure Notebooks vous dirige vers le nouveau projet. Pour ajouter un notebook au projet, sélectionnez la liste déroulante **+ Nouveau** (qui peut apparaître seulement sous la forme **+** ), puis **Notebook** :

    :::image type="content" source="media/quickstarts/empty-project-new-notebook-button.png" alt-text="Nouveau projet vide et commande d’ajout d’un notebook." lightbox="media/quickstarts/empty-project-new-notebook-button.png":::

1. Dans la fenêtre contextuelle **Créer un notebook** qui s’affiche, entrez un nom de fichier pour votre notebook, tel que *HelloWorldInPython.ipynb* (*.ipynb* désigne un notebook IronPython (Jupyter)), puis sélectionnez **Python 3.6** pour le langage (également appelé *noyau*) :

    ![Fenêtre contextuelle Créer un notebook](media/quickstarts/new-notebook-popup.png)

1. Sélectionnez **Nouveau** pour terminer la création du notebook, qui apparaît dans la liste des fichiers de votre projet :

    ![Nouveau notebook figurant dans la liste des fichiers du projet](media/quickstarts/new-notebook-created.png)

## <a name="run-the-notebook"></a>Exécuter le bloc-notes

1. Sélectionnez le nouveau notebook pour l’exécuter dans l’éditeur. Le noyau que vous avez sélectionné (Python 3.6 dans cet exemple) est automatiquement activé pour ce notebook :

    ![Vue d’un notebook dans Azure Notebooks](media/quickstarts/create-notebook-first-open.png)

1. Par défaut, le notebook a une cellule de code vide. Pour remplacer le type de cellule par **Markdown**, utilisez la liste déroulante de type de cellule pour sélectionner **Markdown** :

    ![Modification du type de cellule dans un nouveau notebook](media/quickstarts/create-notebook-cell-type.png)

1. Entrez ou collez le texte d’en-tête suivant dans la cellule :

    ```markdown
    # Hello World in Python
    ```

1. Étant donné que vous éditez la syntaxe Markdown, le texte apparaît sous la forme d’un en-tête avec le signe « # ». Pour afficher la syntaxe Markdown en HTML, cliquez sur le bouton **Exécuter**. Azure Notebooks crée alors automatiquement une nouvelle cellule de code :

    ![Bouton Exécuter d’une cellule et rendu Markdown](media/quickstarts/run-cell-markdown-render.png)

1. Dans la cellule de code, entrez le code Python suivant :

    ```python
    from datetime import datetime

    now = datetime.now()
    msg = "Hello, Azure Notebooks! Today is %s"  % now.strftime("%A, %d %B, %Y")
    print(msg)
    ```

1. Sélectionnez **Exécuter** (raccourci clavier : Maj+Entrée) pour exécuter le code. Sous la cellule, une sortie semblable à ce qui suit doit s’afficher :

    ```output
    Hello, Azure Notebooks! Today is Thursday, 15 November, 2018
    ```

1. Cliquez sur l’icône Enregistrer pour enregistrer votre travail :

    ![Icône Enregistrer dans la barre d’outils du Jupyter Notebook](media/quickstarts/hello-results-save-icon.png)

1. Sélectionnez la commande de menu **Fichier** > **Fermer et arrêter** pour arrêter le serveur et fermer la fenêtre du navigateur.

## <a name="share-the-notebook"></a>Partager le notebook

Pour partager votre notebook, revenez à la page des projets si nécessaire, cliquez sur le fichier de notebook, sélectionnez **copier le lien** (raccourci clavier : y) et collez ce lien dans un message approprié (e-mail, messagerie instantanée, etc.).

Sur la page des projets, vous pouvez également utiliser le menu **Partager** pour obtenir un lien, créer un message électronique avec le lien ou obtenir le code HTML et Markdown incorporé :

![Commande de partage de projet](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Créer et exécuter un Jupyter Notebook pour effectuer une régression linéaire](tutorial-create-run-jupyter-notebook.md)
