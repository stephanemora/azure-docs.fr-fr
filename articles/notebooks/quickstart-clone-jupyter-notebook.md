---
title: Cloner un notebook Jupyter à partir de GitHub avec Azure Notebooks (préversion)
description: Clonez rapidement un notebook Jupyter à partir d’un référentiel GitHub et comment l’exécuter dans votre compte Azure Notebooks.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 8aa88008ece170a5eed7ab491e3318aed5168923
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647099"
---
# <a name="quickstart-clone-a-notebook-in-azure-notebooks-preview"></a>Démarrage rapide : Cloner un notebook dans Azure Notebooks (préversion)

De nombreux scientifiques et développeurs de données stockent leurs notebooks dans des [référenties GitHub](https://github.com), un service gratuit qui permet le stockage et le contrôle de versions pour de nombreux types de projets différents. GitHub est souvent utilisé comme un moyen de collaborer à des notebooks Jupyter qui sont exécutés localement. Dans ce cas, chaque collaborateur conserve une copie locale du référentiel et exécute les notebooks à partir de cette copie.

Le clonage crée une copie d’un notebook GitHub dans votre compte Azure Notebooks à la place. Ce clone est indépendant de son référentiel d’origine ; les modifications sont stockées dans votre compte Azure Notebooks uniquement et n’affectent pas l’original. Comme votre clone réside dans le cloud, vous pouvez partager le projet avec d’autres collaborateurs qui n’ont pas besoin de faire des copies locales ou même d’installer Jupyter sur leurs propres ordinateurs. Vous pouvez également cloner un notebook simplement comme point de départ pour un projet personnel ou pour obtenir des fichiers de données.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="clone-azure-cognitive-services-notebooks"></a>Cloner des notebooks Azure Cognitive Services

1. Accédez à [Azure Notebooks](https://notebooks.azure.com) et connectez-vous. (Pour plus d’informations, consultez la rubrique [Démarrage rapide sur la connexion à Azure Notebooks](quickstart-sign-in-azure-notebooks.md).)

1. En haut de votre page de profil public, sélectionnez **Mes projets** :

    ![Lien Mes projets en haut de la fenêtre du navigateur](media/quickstarts/my-projects-link.png)

1. Sur la page **Mes projets**, sélectionnez le bouton flèche haut (raccourci clavier : U ; le bouton apparaît avec le libellé **Charger le référentiel GitHub** lorsque la fenêtre du navigateur est suffisamment large) :

    ![Commande Charger un référentiel GitHub sur la page Mes projets](media/quickstarts/upload-github-repo-command.png)

1. Dans la fenêtre contextuelle **Charger un référentiel GitHub** qui s’affiche, entrez ou définissez les détails suivants, puis sélectionnez **Importer** :

   - **Dépôt GitHub** : Microsoft/cognitive-services-notebooks (ce nom clone les notebooks Jupyter pour Azure Cognitive Services à l’adresse [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Cloner de manière récursive** : (désactivé)
   - **Nom du projet** : Clone Cognitive Services
   - **ID du projet** : cognitive-services-clone
   - **Public** : (effacé)

     ![Menu contextuel Charger le référentiel GitHub pour collecter des informations de référentiel](media/quickstarts/upload-github-repo-popup.png)

1. Soyez patient pendant que le processus se termine ; le clonage d’un référentiel peut prendre quelques minutes.

1. Une fois le clonage terminé, Azure Notebooks ouvre le nouveau projet où vous pourrez voir les copies de tous les fichiers.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Partager un notebook

1. Pour partager votre copie du projet cloné, utilisez le contrôle **Partager** ou obtenez un lien, obtenez un code HTML ou Markdown qui contient le lien ou créez un message électronique avec le lien :

    ![Commande de partage de projet](media/quickstarts/share-project-command.png)

1. Parce que vous avez désactivé l’option **Public** lors du clonage du projet, le clone est privé. Pour rendre votre copie publique, sélectionnez **Paramètres du projet**, définissez l’option **Projet public** dans le menu contextuel, puis sélectionnez **Enregistrer**.

1. Sélectionnez un notebook dans le projet pour l’exécuter. Chaque notebook du référentiel Azure Cognitive Services, par exemple, est son propre Démarrage rapide autonome. L’image ci-dessous montre le résultat de l’utilisation du notebook BingImageSearchAPI, après l’ajout d’une clé d’abonnement de l’API Cognitive Services, dans laquelle le terme de recherche « puppies » a été remplacé par « bunnies» :

    ![Exécution d’un notebook Jupyter cloné depuis GitHub](media/quickstarts/clone-notebook-result.png)

1. Lorsque vous avez terminé d’exécuter le notebook, sélectionnez **Fichier** > **Fermer et interrompre** pour fermer le notebook et sa fenêtre de navigation.

1. Pour partager un notebook dans le projet, cliquez avec le bouton droit sur le notebook et sélectionnez **Copier le lien** (raccourci clavier : y) :

    ![Commande de menu contextuel pour copier un lien vers un notebook individuel](media/quickstarts/copy-link-to-individual-notebook.png)

1. Pour modifier des fichiers autres que des notebooks, cliquez avec le bouton droit sur le fichier dans le projet et sélectionnez **Modifier le fichier** (raccourci clavier : i). L’action par défaut, **Exécuter** (raccourci clavier : r), affiche uniquement le contenu du fichier et ne permet pas de le modifier.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Tutoriel : Créer et exécuter un notebook Jupyter pour effectuer une régression linéaire](tutorial-create-run-jupyter-notebook.md)
