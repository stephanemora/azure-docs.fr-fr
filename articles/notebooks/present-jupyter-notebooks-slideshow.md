---
title: Présenter un notebook Jupyter sous forme de diaporama sur Azure Notebooks en préversion
description: Apprenez à configurer des cellules pour le mode Diaporama dans un notebook Jupyter, puis présenter le diaporama à l’aide de l’extension RISE.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 05dd3d9c5580e208ecf6f9e6d762476b0b493a6c
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75647116"
---
# <a name="run-a-notebook-slideshow-in-azure-notebooks-preview"></a>Exécuter un diaporama de notebook dans Azure Notebooks en préversion

Azure Notebooks est préconfiguré avec l’extension de diaporama Jupyter/IPython (RISE) qui vous permet de présenter un notebook directement sous forme de diaporama. Dans un diaporama, les cellules sont généralement affichées une par une avec une taille de police adaptée à une présentation sur grand écran, tout en vous permettant quand même d’exécuter le code sans avoir à basculer vers un autre ordinateur de démonstration.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

L’image suivante montre la vue de notebook standard, dans lequel vous pouvez voir l’ensemble des cellules Markdown et de code :

![Notebook dans la vue standard](media/slideshow/slideshow-notebook-view.png)

Quand vous démarrez un diaporama, la première cellule est agrandie pour remplir le navigateur, où le **X** en haut à gauche permet de quitter le diaporama, **?** en bas à gauche d’afficher des raccourcis clavier, et les flèches en bas à droite de naviguer entre les diapositives :

![Notebook en mode Diaporama](media/slideshow/slideshow-slide-view.png)

La préparation d’un notebook pour un diaporama implique deux activités principales :

1. Comme les cellules Markdown sont affichées avec des grandes polices, une partie du contenu risque de ne pas être visible dans le diaporama. Vous limitez généralement ainsi la quantité de texte dans une cellule donnée ; un en-tête de quatre à six lignes est souvent la solution qui convient le mieux. Si vous avez plus de texte, répartissez ces informations sur plusieurs cellules.

2. Configurez le comportement de chaque cellule dans le diaporama à l’aide de la barre d’outils des cellules de diaporama. Les types de cellules déterminent le comportement des boutons de navigation.

## <a name="the-anatomy-of-a-slideshow"></a>Anatomie d’un diaporama

Si vous prenez un notebook aléatoire et l’utilisez pour un diaporama, vous trouvez généralement que toutes les cellules sont mélangées et la majorité du contenu est masquée au niveau de la partie inférieure de la fenêtre du navigateur. Pour obtenir une présentation efficace, vous devez donc affecter un type de diaporama à chaque cellule à l’aide de la barre d’outils des cellules de diaporama :

1. Dans le menu **Affichage**, sélectionnez **Barre d’outils des cellules** > **Diaporama** :

    ![Activation de la barre d’outils des cellules de diaporama](media/slideshow/slideshow-view-cell-toolbar.png)

1. Une liste déroulante **Type de diapositive** s’affiche en haut à droite de chaque cellule du notebook :

    ![Barre d’outils des cellules de diaporama](media/slideshow/slideshow-cell-toolbar.png)

1. Pour chaque cellule, sélectionnez l’un des cinq types :

    ![Types des cellules de diaporama](media/slideshow/slideshow-cell-slide-types.png)

    | Type de diapositive | Comportement |
    | --- | --- |
    | - (non défini) | La cellule est affichée avec la cellule précédente, ce qui n’est pas souvent un effet souhaité dans un diaporama. |
    | Diapositive | La cellule est une diapositive principale, accessible en utilisant les flèches gauche et droite du contrôle de navigation. |
    | Sous-diapositive | La cellule est « sous » une diapositive principale, accessible en utilisant la flèche bas de la commande de navigation. La flèche haut permet de revenir à la diapositive principale. Les sous-diapositives sont utilisées pour des documents secondaires que vous pourriez ignorer dans le chemin principal d’une présentation, mais qui sont immédiatement disponibles si nécessaire. |
    | Fragment | Le contenu de la cellule apparaît dans le contexte de la diapositive précédente ou sous-diapositive lors de l’utilisation de la flèche de navigation bas (un fragment est supprimé quand vous utilisez la flèche haut). Vous pouvez utiliser un fragment avec une cellule de code pour que ce code s’affiche dans une diapositive, ou vous pouvez utiliser plusieurs fragments pour que les puces de texte s’affichent une par une (voir l’exemple dans la section suivante). Comme les fragments reposent sur la diapositive active, les fragments en trop ne seront pas visibles au niveau de la partie inférieure de la fenêtre du navigateur. |
    | Ignorer | La cellule n’est pas affichée dans le diaporama. |
    | Notes | La cellule contient des remarques de l’intervenant, qui ne sont pas affichées dans le diaporama. |

1. Initialement, il est utile de choisir **Diapositive** pour chaque cellule. Vous pouvez ensuite exécuter le diaporama et apporter les modifications nécessaires.

### <a name="example-fragment-cells-for-bullet-items"></a>Exemple : cellules de type fragment pour les éléments à puce

Pour que les puces d’une diapositive s’affichent une par une, placez l’en-tête de la diapositive dans une cellule Markdown avec le type **Diapositive**, puis placez chaque puce dans une cellule Markdown distincte avec le type **Fragment** :

![Exemple de création de plusieurs cellules Markdown pour les éléments à puce](media/slideshow/slideshow-fragments.png)

Comme le diaporama restitue les fragments avec un espacement plus vertical que quand toutes les puces se trouvent dans la même cellule, vous risquez de ne pas pouvoir utiliser autant d’éléments à puce.

## <a name="run-the-slideshow"></a>Exécuter le diaporama

1. Si vous avez modifié des cellules Markdown, veillez à les exécuter pour effectuer le rendu HTML correspondant, sinon elles apparaissent *comme* Markdown dans le diaporama.

1. Une fois que vous avez configuré le **type de diapositive** pour chaque cellule, sélectionnez la cellule avec laquelle démarrer le diaporama, puis le bouton **Entrer dans/Quitter le diaporama RISE** dans la barre d’outils principale :

    ![Bouton Entrer dans/Quitter le diaporama RISE dans la barre d’outils principale](media/slideshow/slideshow-start.png)

1. Pour naviguer entre les diapositives et les fragments, utilisez les flèches gauche et droite dans la commande de navigation. Le texte de la commande indique un nombre représentant *diapositive.sous-diapositive*.

    ![Commande de navigation de diaporama](media/slideshow/slideshow-navigation-control.png)

1. Pour naviguer entre les diapositives, les sous-diapositives et les fragments, utilisez les flèches haut et bas, si elles sont activées :

    ![Commandes de navigation pour les sous-diapositives de diaporama](media/slideshow/slideshow-navigation-control-subslide.png)

1. Dans une cellule de code, utilisez le bouton Lecture pour exécuter le code ; la sortie s’affiche sur la diapositive :

    ![Bouton Lecture pour exécuter une cellule de code](media/slideshow/slideshow-run-code-cell.png)

    ![La sortie de cellule de code s’affiche dans le diaporama](media/slideshow/slideshow-run-code-cell-output.png)

    > [!Tip]
    > La sortie de la cellule est considérée comme faisant partie de la cellule d’un diaporama. Si vous exécutez une cellule dans la vue de notebook ou de diaporama, la sortie apparaît aussi dans l’autre vue. Pour effacer la sortie, utilisez la commande **Cellule** > **Sorties actuelles** > **Effacer** (pour la cellule active) ou **Cellule** > **Toutes les sorties** > **Effacer** (pour toutes les cellules).

1. Quand vous en avez fini avec le diaporama, utilisez le **X** pour revenir à la vue de notebook.

## <a name="next-steps"></a>Étapes suivantes

- [Procédure : configurer et gérer des projets](configure-manage-azure-notebooks-projects.md)
- [Procédure : installer des packages à partir d’un notebook](install-packages-jupyter-notebook.md)
- [Procédure : utiliser des fichiers de données](work-with-project-data-files.md)
- [Procédure : accéder aux ressources de données](access-data-resources-jupyter-notebooks.md)
