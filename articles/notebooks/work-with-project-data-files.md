---
title: Importer et exporter des données de projet avec Azure Notebooks (préversion)
description: Découvrez comment importer des données dans un projet Azure Notebooks (préversion) à partir de sources externes et comment exporter les données à partir d’un projet.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: b3669128582d3bdd6a3c4506a040856ab7b07e9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85834112"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Utiliser des fichiers de données dans des projets Azure Notebooks (préversion)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Les données sont l'élément moteur des notebooks Jupyter, notamment des notebooks utilisés pour la science des données. Avec Azure Notebooks, vous pouvez facilement importer des données issues de différentes sources dans un projet, puis utiliser ces données à partir des notebooks. Vous pouvez aussi veiller à ce que les notebooks génèrent les données stockés dans le projet, puis les télécharger pour les utiliser ailleurs.

Le menu **Données** d'un notebook en cours d'exécution propose également les commandes **Charger** et **Télécharger**, qui fonctionnent avec les fichiers du projet ainsi que les fichiers temporaires pour la session de notebook en cours.

Vous pouvez également utiliser du code dans un notebook pour accéder directement à diverses sources de données, parmi lesquelles les fichiers d'un projet. Vous pouvez aussi accéder à des données arbitraires en utilisant des commandes dans une cellule de code. Ces données étant stockées dans des variables de la session de notebook, elles ne sont pas enregistrées dans le projet, sauf si vous utilisez du code pour générer des fichiers de projet spécifiques.

Utiliser du code dans les données se révèle plus efficace dans un notebook en cours d'exécution : pour ce faire, consultez [Accéder à vos données dans un exemple de notebook Azure Notebooks](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

La suite de cet article fournit des détails relatifs aux opérations sur les fichiers au niveau du projet.

## <a name="import-data"></a>Importer des données

Vous pouvez déplacer des fichiers dans un projet depuis le tableau de bord du projet ou dans un notebook en cours d’exécution à l’aide du menu **Données** ou d'une commande telle que `curl`.

### <a name="import-files-from-the-project-dashboard"></a>Importer des fichiers à partir du tableau de bord du projet

1. Dans le projet, accédez au dossier où vous souhaitez importer les fichiers.

1. Sélectionnez la commande **Charger**, puis **À partir d'une URL** ou **À partir de l'ordinateur** et les détails correspondant aux données du projet que vous souhaitez importer :

   - **À partir d'une URL** : Entrez l’adresse source dans le champ **URL du fichier** ainsi que le nom de fichier à des fins d'affectation au notebook de votre projet dans le champ **Nom de fichier**. Sélectionnez ensuite **+ Ajouter un fichier** pour ajouter l’URL à la liste de téléchargement. Répétez la procédure pour les URL supplémentaires, puis sélectionnez **Terminé**.

     ![Charger à partir de la fenêtre contextuelle de l’URL](media/quickstarts/upload-from-url-popup.png)

   - **À partir de l'ordinateur** : Faites glisser les fichiers dans la fenêtre contextuelle ou sélectionnez **Choisir des fichiers**, puis recherchez et sélectionnez les fichiers de données que vous souhaitez importer. Vous pouvez supprimer ou choisir le nombre de fichiers de votre choix, de même que leur type et leur format puisqu'il incombe au code du notebook d'ouvrir ces fichiers et d'analyser les données qu'ils contiennent.

     ![Charger à partir de la fenêtre contextuelle de l’ordinateur](media/quickstarts/upload-from-computer-popup.png)

1. Une fois importés, les fichiers apparaissent dans le tableau de bord du projet et sont accessibles dans le code du notebook à l’aide de chemins d’accès menant au dossier correspondant.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Importer des fichiers dans un notebook à partir du menu Fichiers

1. Dans un notebook en cours d’exécution, sélectionnez la commande **Chargement** > **de fichiers** :

    ![Commande de menu Chargement de fichiers dans un notebook](media/file-menu-upload.png)

1. Dans la boîte de dialogue qui s’ouvre, accédez aux fichiers et sélectionnez ceux que vous voulez charger. Vous pouvez sélectionner le nombre et le type de fichiers de votre choix. Lorsque vous avez terminé, sélectionnez **Ouvrir**.

1. Dans la fenêtre **État du chargement** qui s'affiche, sélectionnez un **dossier de destination** à partir de la liste déroulante :

    - Dossier de session ( *~/* ) : Télécharge les fichiers dans la session de notebook en cours, mais ne crée pas de fichiers dans le projet. Le dossier de session est un homologue du dossier de projet, mais ne persiste pas au-delà de la session. Pour accéder aux fichiers de session dans le code, faites précéder les noms de fichier du chemin d'accès relatif *../* .

        Le dossier de session est utile à des fins d'expérimentation et permet d'éviter de surcharger le projet avec des fichiers dont vous n'aurez peut-être pas besoin sur le long terme. Vous pouvez également charger dans le dossier de session des fichiers portant des noms identiques aux fichiers du projet, sans créer de conflits ou devoir les renommer. Par exemple, supposons que le projet contienne déjà une version du fichier *data.csv*, mais que vous souhaitiez expérimenter une version différente de ce fichier *data.csv*. En chargeant le fichier dans le dossier de session, vous pouvez exécuter le notebook à l’aide des données contenues dans le fichier chargé (en y faisant référence dans le code à l'aide de *../data.csv*) plutôt que des données contenues dans le fichier du projet.

    - Dossier du projet ( */project*) : charge les fichiers dans le projet où ils sont accessibles à l’aide des chemins d’accès relatifs dans le code. Le chargement d'un fichier vers ce dossier est identique au chargement d'un fichier dans le tableau de bord du projet. Le fichier est enregistré avec le projet et disponible dans les sessions ultérieures.

        Le chargement échoue si vous tentez de charger un fichier portant le même nom qu'un autre fichier déjà présent dans le projet. Pour remplacer un fichier, chargez le nouveau fichier à partir du tableau de bord du projet afin d'accéder à cette possibilité.

1. Sélectionnez **Démarrer le chargement** pour terminer le processus.

### <a name="create-or-import-files-using-commands"></a>Créer ou importer des fichiers à l’aide de commandes

Vous pouvez utiliser des commandes dans un terminal ou dans une cellule de code Python pour créer des fichiers dans des dossiers de session et de projet. Par exemple, les commandes `curl` et `wget` permettent de charger directement des fichiers depuis Internet.

Pour télécharger des fichiers dans le terminal, sélectionnez la commande **Terminal** dans le tableau de bord du projet, puis entrez les commandes appropriées :

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

Lorsque vous utilisez une cellule de code Python dans un notebook, faites précéder les commandes de `!`.

Le dossier du projet correspond au dossier par défaut. Ainsi, lorsque vous spécifiez un nom de fichier de destination tel que *oil_price.csv*, le fichier est créé dans le projet. Pour créer un fichier de session, faites précéder le nom de *../* comme dans *../oil_price.csv*.

### <a name="create-files-in-code"></a>Créer des fichiers dans du code

Lorsque vous utilisez du code pour créer un fichier, comme la fonction `write_csv` Pandas, les noms de fichier correspondent toujours au dossier du projet. Utilisez *../* pour créer un fichier de session qui est supprimé lorsque le notebook est arrêté et fermé.

## <a name="export-files"></a>Exporter des fichiers

Vous pouvez exporter des données depuis le tableau de bord du projet ou un notebook.

## <a name="export-files-from-the-project-dashboard"></a>Exporter des fichiers à partir du tableau de bord du projet

Sur le tableau de bord du projet, cliquez avec le bouton droit sur un fichier, puis sélectionnez **Télécharger** :

![Commande de téléchargement dans la fenêtre contextuelle des éléments de projet](media/download-command.png)

Vous pouvez également sélectionner un fichier et utiliser la commande **Télécharger** (raccourci clavier : d) sur le tableau de bord :

![Télécharger la commande de barre d’outils sur le tableau de bord du projet](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Exporter des fichiers dans un notebook à partir du menu Données

1. Sélectionnez le menu **Télécharger** > **des fichiers** :

    ![Commande de menu Télécharger des données d'un notebook](media/file-menu-download.png)

1. Une fenêtre contextuelle apparaît pour afficher les dossiers de la session. Le dossier du *projet* contient les fichiers de projet :

    ![Fenêtre contextuelle de la commande Télécharger des données dans laquelle vous sélectionnez des fichiers et dossiers](media/file-menu-download-popup.png)

1. Activez les cases à cocher situées à gauche des fichiers et dossiers que vous souhaitez télécharger, puis sélectionnez **Télécharger la sélection**.

1. Le notebook prépare un seul fichier *.zip* contenant les fichiers sélectionnés et vous pouvez l'enregistrer de la même manière que vous procédez habituellement depuis votre navigateur. Le notebook crée un fichier *.zip* même en cas de téléchargement d'un seul fichier.

## <a name="next-steps"></a>Étapes suivantes

- [Accéder aux données de cloud dans un notebook](access-data-resources-jupyter-notebooks.md)
