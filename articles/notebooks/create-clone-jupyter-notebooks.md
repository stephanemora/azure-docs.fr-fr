---
title: Créer et cloner des notebooks Jupyter - Azure Notebooks en préversion
description: Les projets Azure Notebooks (préversion) gèrent une collection de notebooks et de fichiers associés, que vous pouvez créer ou cloner à partir d’une autre source.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: 4a51f9b12ca24d16858b41357627ff26d233357f
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86181492"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Créer et cloner des projets dans Azure Notebooks en préversion

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Azure Notebooks organise vos notebooks Jupyter et fichiers associés en groupes logiques appelés *projets*. Vous créez un projet d’abord en tant que conteneur, puis créez ou clonez un ou plusieurs notebooks au sein d’un dossier en même temps que d’autres fichiers projet. (Ce processus est illustré dans le [tutoriel](tutorial-create-run-jupyter-notebook.md).)

Un projet gère également des métadonnées et d’autres paramètres de configuration qui affectent le serveur sur lequel les notebooks sont exécutés, notamment les étapes de configuration personnalisées et l’installation des packages. Pour plus d’informations, consultez [Gérer et configurer des projets](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Utiliser le tableau de bord Mes projets

Votre tableau de bord **Mes projets** sur `https://notebooks.azure.com/<userID>/projects` est l’emplacement où vous affichez, gérez et créez des projets :

[![Tableau de bord Mes projets dans Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

Les actions possibles dans le tableau de bord varient selon si vous êtes connecté avec le compte qui a l’ID utilisateur :

| Commande | Disponible pour | Description |
| --- | --- | --- |
| **Exécuter** | Propriétaire | Démarre le serveur de projet et ouvre le dossier de projet dans Jupyter. (En général, vous accédez tout d’abord à un dossier de projet, puis démarrez un notebook à partir de là.) |
| **Télécharger** | Tout le monde | Télécharge une copie du projet sélectionné sous la forme d’un fichier ZIP. |
| **Partager** | Tout le monde | Affiche la fenêtre contextuelle de partage par le biais de laquelle vous pouvez obtenir une URL vers un projet sélectionné, partager sur les réseaux sociaux, envoyer un e-mail avec l’URL et obtenir le code HTML ou Markdown associé avec un badge « lancement de notebook » (consultez [Obtenir un badge de lancement](#obtain-a-launch-badge)) avec l’URL. |
| **Supprimer** | Propriétaire | Supprime le projet sélectionné. Cette opération ne peut pas être annulée. |
| **Terminal** | Propriétaire | Démarre le serveur de projet, puis ouvre une nouvelle fenêtre de navigateur avec le terminal bash pour ce serveur. |
| **+ Nouveau projet** | Propriétaire | Crée un projet. Consultez [Créer un projet](#create-a-new-project). |
| **Charger un dépôt GitHub** | Propriétaire | Importe un projet à partir de GitHub. [Importer un projet à partir de GitHub](#import-a-project-from-github). |
| **Cloner** | Tout le monde | Copie un projet sélectionné dans votre propre compte. Vous invite à vous connecter si ce n’est déjà fait. Consultez [Cloner un projet](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Obtenir un badge de lancement

Quand vous utilisez la commande **Partager** et sélectionnez l’onglet **Incorporer**, vous pouvez copier le code HTML ou Markdown qui crée un badge « lancement de notebook » :

![Badge de lancement de notebook](https://notebooks.azure.com/launch.png)

Si vous n’avez pas de projet Azure Notebooks, vous pouvez créer un lien qui clone à partir de GitHub directement à l’aide des modèles suivants, en remplaçant le nom d’utilisateur et les noms de dépôt appropriés :

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Création d'un projet

Quand vous utilisez la commande **+ Nouveau projet**, Azure Notebooks affiche une fenêtre contextuelle **Créer un projet**. Dans cette fenêtre contextuelle, entrez les informations suivantes, puis sélectionnez **Créer** :

| Champ | Description |
| --- | --- |
| Nom du projet | Nom convivial de votre projet utilisé par Azure Notebooks pour l’affichage. Par exemple « Mon projet Notebook ». |
| ID de projet | Identificateur personnalisé qui devient partie intégrante de l’URL que vous utilisez pour partager un projet (sous la forme `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Cet ID peut utiliser uniquement des lettres, des chiffres et des traits d’union, et est limité à 30 caractères. Il ne peut pas s’agir de l’[ID d’un projet réservé](#reserved-project-ids). Si vous ne savez pas quoi utiliser, il est d’usage d’employer une version en minuscules du nom de votre projet où les espaces sont transformés en traits d’union, par exemple « mon-projet-notebook » (nom tronqué si nécessaire pour l’adapter à la limite de longueur). |
| Public | Si ce paramètre est défini, permet à toute personne disposant du lien d’accéder au projet. Quand vous créez un projet privé, désactivez cette option. |
| Initialiser ce projet avec un fichier README | Si ce champ est défini, crée un fichier *README.md* par défaut dans le projet. Un fichier *README.md* est l’emplacement où vous fournissez la documentation de votre projet, si vous le souhaitez. |

### <a name="reserved-project-ids"></a>ID de projet réservés

Les mots réservés suivants ne peut pas être utilisés en tant qu’ID de projet. Ces mots réservés peuvent, toutefois, être utilisés dans le cadre d’ID de projet plus longs.

- à propos de
- account
- administration
- api
- blog
- classe
- content
- dashboard
- explorer
- faq
- help
- html
- home
- importer
- bibliothèque
- gestion
- new
- notebook
- blocs-notes
- pdf
- preview
- Prix
- profile
- recherche
- status
- support
- test

Si vous essayez d’utiliser un de ces mots dans un ID de projet, les fenêtres contextuelles **Créer un projet** et **Paramètres du projet** indiquent : « L’ID de cette bibliothèque est un identificateur réservé ».

Comme un ID de projet est également dans les URL d’un projet, le logiciel Ad Blocker risque de bloquer l’utilisation de certains mots clés, tels que « annonce ». Dans ce cas, utilisez un autre mot dans l’ID du projet.

## <a name="import-a-project-from-github"></a>Importer un projet à partir de GitHub

Vous pouvez facilement importer un dépôt GitHub public entier en tant que projet, avec l’ensemble des données et fichiers *README.md*. Utilisez la commande **Charger un dépôt GitHub**, indiquez les informations suivantes dans la fenêtre contextuelle, puis sélectionnez **Importer** :

| Champ | Description |
| --- | --- |
| Dépôt GitHub | Nom du dépôt source sur github.com. Par exemple, pour cloner les notebooks Jupyter pour Azure Cognitive Services sur [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), entrez « Microsoft/cognitive-services-notebooks ».  |
| Cloner de manière récursive | Les dépôts GitHub peuvent contenir plusieurs dépôts enfants. Définissez cette option si vous souhaitez cloner le dépôt parent et tous ses enfants. Comme il est possible pour un dépôt d’avoir plusieurs enfants, laissez cette option désactivée sauf si vous savez que vous en avez besoin. |
| Nom du projet | Nom convivial de votre projet utilisé par Azure Notebooks pour l’affichage. |
| ID de projet | Identificateur personnalisé qui devient partie intégrante de l’URL que vous utilisez pour partager un projet (sous la forme `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Cet ID peut utiliser uniquement des lettres, des chiffres et des traits d’union, et est limité à 30 caractères. Il ne peut pas s’agir de l’[ID d’un projet réservé](#reserved-project-ids). Si vous ne savez pas quoi utiliser, il est d’usage d’employer une version en minuscules du nom de votre projet où les espaces sont transformés en traits d’union, par exemple « mon-projet-notebook » (nom tronqué si nécessaire pour l’adapter à la limite de longueur). |
| Public | Si ce paramètre est défini, permet à toute personne disposant du lien d’accéder au projet. Quand vous créez un projet privé, désactivez cette option. |

L’importation d’un dépôt à partir de GitHub importe également son historique. Vous pouvez utiliser les commandes Git standard à partir du terminal pour valider les nouvelles modifications, tirer (pull) des modifications de GitHub et ainsi de suite.

## <a name="clone-a-project"></a>Cloner un projet

Le clonage crée une copie d’un projet existant dans votre propre compte, où vous pouvez ensuite exécuter et modifier n’importe quel notebook ou autre fichier dans le projet. Vous pouvez également utiliser le clonage pour effectuer des copies de vos propres projets dans lesquels vous procédez à des tests ou d’autres tâches sans perturber le projet d’origine.

Pour cloner un projet :

1. Dans le tableau de bord **Mes projets**, cliquez avec le bouton droit sur le projet voulu et sélectionnez **Cloner** (raccourci clavier : c).

    ![Commande Cloner dans le menu contextuel du projet](media/clone-command.png)

1. Dans la fenêtre contextuelle **Cloner le projet**, entrez un nom et un ID pour le clone, puis spécifiez si le clone est public. Ces paramètres sont les mêmes que pour un [nouveau projet](#create-a-new-project).

    ![Fenêtre contextuelle Cloner le projet](media/clone-project.png)

1. Après avoir sélectionné le bouton **Cloner**, Azure Notebooks vous permet d’accéder directement à la copie.

## <a name="next-steps"></a>Étapes suivantes

- [Explorer des exemples de notebooks](azure-notebooks-samples.md)
- [Procédure : configurer et gérer des projets](configure-manage-azure-notebooks-projects.md)
- [Procédure : installer des packages à partir d’un notebook](install-packages-jupyter-notebook.md)
- [Procédure : présenter un diaporama](present-jupyter-notebooks-slideshow.md)
- [Procédure : utiliser des fichiers de données](work-with-project-data-files.md)
- [Procédure : accéder aux ressources de données](access-data-resources-jupyter-notebooks.md)
- [Procédure : Utiliser Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
