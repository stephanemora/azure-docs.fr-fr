---
title: Créer et partager un notebook Jupyter sur Azure
description: Créez et exécutez rapidement un notebook Jupyter sur Azure Notebooks, puis partagez-le avec d’autres utilisateurs.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 71220fa5aa0367d1cb1694582b4f96459a3016e7
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277502"
---
# <a name="quickstart-create-and-share-a-notebook"></a>Démarrage rapide : Créer et partager un notebook

1. Accédez à [Azure Notebooks](https://notebooks.azure.com) et connectez-vous. (Pour plus d’informations, consultez la rubrique [Démarrage rapide sur la connexion à Azure Notebooks](quickstart-sign-in-azure-notebooks.md).)

1. En haut de votre page de profil public, sélectionnez **Mes projets** :

    ![Lien Mes projets en haut de la fenêtre du navigateur](media/quickstarts/my-projects-link.png)

1. Dans la page **Mes projets**, sélectionnez **+ Nouveau projet** (raccourci clavier : n). Le bouton peut apparaître uniquement sous la forme **+** si la fenêtre du navigateur est étroite :

    ![Commande Nouveau projet dans la page Mes projets](media/quickstarts/new-project-command.png)

1. Dans la fenêtre contextuelle **Créer un projet** qui s’affiche, entrez ou définissez les détails suivants, puis sélectionnez **Créer** :

   - **Nom du projet** : Hello World dans Python
   - **ID du projet** : hello-world-python
   - **Projet public** : (désactivé)
   - **Créer un fichier README.md** : (désactivé)

     ![Fenêtre contextuelle Nouveau projet avec les détails remplis](media/quickstarts/new-project-popup.png)

1. Après quelques instants, Azure Notebooks accède au nouveau projet. Pour ajouter un notebook au projet, sélectionnez la liste déroulante **+ Nouveau** (qui peut apparaître seulement sous la forme **+** ), puis **Notebook** :

    [![](media/quickstarts/empty-project-new-notebook-button.png "A new, empty project and add notebook command")](media/quickstarts/empty-project-new-notebook-button.png#lightbox)

1. Dans la fenêtre contextuelle **Créer un notebook** qui s’affiche, entrez un nom de fichier pour votre notebook, tel que *HelloWorldInPython.ipynb* ( *.ipynb* désigne un notebook IronPython (Jupyter)), puis sélectionnez **Python 3.6** pour le langage (également appelé *noyau*) :

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

    ![Icône Enregistrer dans la barre d’outils du notebook Jupyter](media/quickstarts/hello-results-save-icon.png)

1. Sélectionnez la commande de menu **Fichier** > **Fermer et arrêter** pour arrêter le serveur et fermer la fenêtre du navigateur.

## <a name="share-the-notebook"></a>Partager le notebook

Pour partager votre notebook, revenez à la page des projets si nécessaire, cliquez sur le fichier de notebook, sélectionnez **copier le lien** (raccourci clavier : y) et collez ce lien dans un message approprié (e-mail, messagerie instantanée, etc.).

Sur la page des projets, vous pouvez également utiliser le menu **Partager** pour obtenir un lien, créer un message électronique avec le lien ou obtenir le code HTML et Markdown incorporé :

![Commande de partage de projet](media/quickstarts/share-project-command.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Créer et exécuter un notebook Jupyter pour effectuer une régression linéaire](tutorial-create-run-jupyter-notebook.md)
