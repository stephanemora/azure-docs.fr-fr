---
title: Configurer et gérer Azure Notebooks (préversion)
description: Découvrez comment gérer les métadonnées de projet, les fichiers projet, l’environnement et les étapes de configuration du projet via l’interface utilisateur Azure Notebooks et un accès direct au terminal.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: tracking-python
ms.openlocfilehash: 53acf7c916b402e291b9779948579761e79179ef
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85834078"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Gérer et configurer des projets dans Azure Notebooks (préversion)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Un projet dans Azure Notebooks (préversion) est essentiellement une configuration de la machine virtuelle Linux sous-jacente dans laquelle des notebooks Jupyter sont exécutés, avec un dossier de fichiers et des métadonnées descriptives. 

Le tableau de bord du projet dans Azure Notebooks vous permet de gérer les fichiers et de configurer autrement les caractéristiques du projet :

- le niveau de calcul du projet, qui peut être le niveau Gratuit ou une machine virtuelle Azure ;
- les métadonnées du projet, à savoir un nom, une description, un identificateur utilisé lors du partage du projet et le type (public ou privé) ;
- le notebook, les données et d’autres fichiers du projet, gérés comme n’importe quel autre système de fichiers ;
- l’environnement du projet, géré avec des scripts de démarrage ou directement dans le terminal ;
- les journaux d’activité, accessibles dans le terminal.

> [!Note]
> Les fonctionnalités de gestion et de configuration décrites ici ne sont accessibles qu’au propriétaire du projet, qui l’a initialement créé. Vous pouvez cependant cloner le projet dans votre propre compte pour en devenir propriétaire et le configurer comme vous le souhaitez.

Azure Notebooks démarre la machine virtuelle sous-jacente dès que vous exécutez un notebook ou un autre fichier. Le serveur enregistre automatiquement les fichiers et s’arrête après 60 minutes d’inactivité. Vous pouvez également arrêter le serveur à tout moment avec la commande d’**arrêt** (raccourci clavier : h).

## <a name="compute-tier"></a>Niveau de calcul

Par défaut, les projets se trouvent au niveau de **calcul Gratuit**, limité à 4 Go de mémoire et à 1 Go de données pour éviter les abus. Vous pouvez contourner ces limitations et augmenter la puissance de calcul en utilisant une autre machine virtuelle configurée dans un abonnement Azure. Pour plus d’informations, consultez la page [How to use Data Science Virtual Machines](use-data-science-virtual-machine.md) (Comment utiliser les machines virtuelles Science des données).

## <a name="edit-project-metadata"></a>Modifier les métadonnées de projet

Dans le tableau de bord du projet, sélectionnez **Paramètres du projet**, puis l’onglet **Informations** qui contient les métadonnées du projet, comme décrit dans le tableau suivant. Vous pouvez modifier les métadonnées de projet à tout moment.

| Paramètre | Description |
| --- | --- |
| Nom du projet | Nom convivial de votre projet utilisé par Azure Notebooks pour l’affichage. par exemple « Hello World dans Python ». |
| ID de projet | Identificateur personnalisé qui devient partie intégrante de l’URL que vous utilisez pour partager un projet. Cet ID peut utiliser uniquement des lettres, des chiffres et des traits d’union, et est limité à 30 caractères. Il ne peut pas s’agir de l’[ID d’un projet réservé](create-clone-jupyter-notebooks.md#reserved-project-ids). Si vous ne savez pas quoi utiliser, il est d’usage d’employer une version en minuscules du nom de votre projet où les espaces sont transformés en traits d’union, par exemple « mon-projet-notebook » (nom tronqué si nécessaire pour l’adapter à la limite de longueur). |
| Projet public | Si ce paramètre est défini, permet à toute personne disposant du lien d’accéder au projet. Quand vous créez un projet privé, désactivez cette option. |
| Hide clones (Masquer les clones) | Si ce paramètre est défini, les autres utilisateurs ne peuvent pas afficher la liste des clones qui ont été créés pour ce projet. Il est utile de masquer les clones pour les projets qui sont partagés avec beaucoup de personnes qui ne font pas partie de la même organisation, comme quand vous utilisez un notebook pour enseigner à une classe. |

> [!Important]
>
> La modification de l’ID de projet invalide tous les liens vers le projet que vous pouvez avoir précédemment partagé.

## <a name="manage-project-files"></a>Gérer les fichiers projet

Le tableau de bord du projet affiche le contenu du système de dossiers du projet. Vous pouvez utiliser plusieurs commandes pour gérer ces fichiers.

### <a name="create-new-files-and-folders"></a>Créer des fichiers et dossiers

La commande **+ Nouveau** (raccourci clavier : n) crée des fichiers ou dossiers. Quand vous utilisez la commande, sélectionnez tout d’abord le type d’élément à créer :

| Type d’élément | Description | Comportement de la commande |
| --- | --- | --- |
| **Notebook** | Notebook Jupyter | Affiche une fenêtre contextuelle dans laquelle vous spécifiez le nom de fichier et le langage du notebook. |
| **Folder** | Sous-dossier | Crée un champ d’édition dans la liste des fichiers du projet dans lequel vous entrez le nom du dossier. |
| **Fichier vide** | Fichier dans lequel vous pouvez stocker n’importe quel contenu comme du texte, des données, etc. | Crée un champ d’édition dans la liste des fichiers du projet dans lequel vous entrez le nom du fichier. |
| **Markdown** | Fichier Markdown. | Crée un champ d’édition dans la liste des fichiers du projet dans lequel vous entrez le nom du fichier. |

### <a name="upload-files"></a>Charger des fichiers

La commande **Charger** fournit deux options pour importer des données à partir d’autres emplacements : **À partir de l’URL** et **À partir de l’ordinateur**. Pour plus d’informations, consultez [Utiliser des fichiers de données dans les projets Azure Notebooks](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Sélectionner des commandes spécifiques au fichier

Chaque élément dans la liste des fichiers du projet fournit des commandes via un menu contextuel :

![Commandes dans un menu contextuel de fichier](media/project-file-commands.png)

| Commande | Raccourci clavier | Action |
| --- | --- | --- |
| Exécuter | r (ou clic) | Exécute un fichier notebook. D’autres types de fichiers sont ouverts pour être affichés.  |
| Copier le lien | y | Copie un lien vers le fichier dans le Presse-papiers. |
| Exécuter dans JupyterLab | j | Exécute un notebook dans JupyterLab, qui est une interface plus orientée développeur que celle fournie normalement par Jupyter. |
| PRÉVERSION | p | Ouvre un aperçu HTML du fichier ; pour les notebooks, l’aperçu est un rendu en lecture seule du notebook. Pour plus d’informations, consultez la section [Aperçu](#preview). |
| Modifier le fichier | i | Ouvre le fichier pour modification. |
| Téléchargement | d | Télécharge un fichier zip qui contient le fichier ou le contenu d’un dossier. |
| Renommer | a | Demande un nouveau nom pour le fichier ou dossier. |
| DELETE | x | Demande une confirmation, puis supprime définitivement le fichier du projet. Les suppressions ne peuvent pas être annulées. |
| Déplacer | m | Déplace un fichier vers un autre dossier dans le même projet. |

#### <a name="preview"></a>PRÉVERSION

Un aperçu d’un fichier ou d’un notebook est une vue en lecture seule du contenu ; l’exécution des cellules de notebook est désactivée. Un aperçu est affiché pour toute personne qui a un lien vers le fichier ou le notebook, mais qui ne s’est pas connectée à Azure Notebooks. Une fois connecté, un utilisateur peut cloner le notebook sur son propre compte ou le télécharger sur son ordinateur local.

La page d’aperçu prend en charge plusieurs commandes de barre d’outils avec des raccourcis clavier :

| Commande | Raccourci clavier | Action |
| --- | --- | --- |
| Partager | s | Affiche la fenêtre contextuelle de partage à partir de laquelle vous pouvez obtenir un lien, partager sur les réseaux sociaux, obtenir le code HTML pour l’incorporation et envoyer un e-mail. |
| Clone | c  | Clone le notebook sur votre compte. |
| Exécuter | r | Exécute le notebook, si vous êtes autorisé à le faire. |
| Téléchargement | d | Télécharge une copie du notebook. |

## <a name="configure-the-project-environment"></a>Configurer l’environnement de projet

Il existe trois façons de configurer l’environnement de la machine virtuelle sous-jacente où vos notebooks sont exécutés :

- Inclure un script d’initialisation à usage unique
- Utiliser les paramètres d’environnement du projet pour spécifier les étapes de configuration
- Accéder à la machine virtuelle via un terminal.

Toutes les formes de la configuration de projet sont appliquées chaque fois que la machine virtuelle est démarrée et cela affecte donc tous les notebooks au sein du projet.

### <a name="one-time-initialization-script"></a>Script d’initialisation à usage unique

La première fois qu’Azure Notebooks crée un serveur pour le projet, il recherche un fichier dans le projet appelé *aznbsetup.sh*. Si ce fichier est présent, Azure Notebooks l’exécute. La sortie du script est stockée dans votre dossier de projet comme *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Étapes de configuration de l’environnement

Vous pouvez utiliser les paramètres d’environnement du projet pour créer des étapes individuelles qui configurent l’environnement.

Dans le tableau de bord du projet, sélectionnez **Paramètres du projet**, puis l’onglet **Environnement** dans lequel vous ajoutez, supprimez et modifiez des étapes de configuration pour le projet :

![Fenêtre contextuelle Paramètres du projet avec l’onglet Environnement sélectionné](media/project-settings-environment-steps.png)

Pour ajouter une étape, commencez par sélectionner **+ Ajouter**, puis sélectionnez un type d’étape dans la liste déroulante **Opération** :

![Sélecteur d’opérations pour une nouvelle étape de configuration d’environnement](media/project-settings-environment-details.png)

Les informations relatives au projet varient selon le type d’opération que vous avez choisi :

- **Requirements.txt** : Dans la deuxième liste déroulante, sélectionnez un fichier *requirements.txt* qui existe déjà dans le projet. Sélectionnez ensuite une version de Python à partir de la troisième liste déroulante qui s’affiche. À l’aide d’un fichier *requirements.txt*, Azure Notebooks exécute `pip install -r` avec le fichier *requirements.txt* lors du démarrage d’un serveur notebook. Vous n’avez pas besoin d’installer explicitement les packages à partir du notebook lui-même.

- **Script shell** : Dans la deuxième liste déroulante, sélectionnez un script shell bash dans le projet (généralement un fichier avec l’extension *.sh*) qui contient les commandes que vous souhaitez exécuter pour initialiser l’environnement.

- **Environment.yml** : Dans la deuxième liste déroulante, sélectionnez un fichier *environments.yml* pour les projets Python avec un environnement conda.

   > [!WARNING]
   > Comme il s’agit d’un service en préversion en cours de développement, il existe un problème connu où le paramètre `Environment.yml` n’est pas appliqué à votre projet comme prévu. À l’heure actuelle, le projet et les notebooks Jupyter qu’il contient ne chargent pas le fichier d’environnement spécifié.

Quand vous avez terminé l’ajout des étapes, sélectionnez **Enregistrer**.

### <a name="use-the-terminal"></a>Utiliser le terminal

Dans le tableau de bord du projet, la commande **Terminal** ouvre un terminal Linux qui vous permet d’accéder directement au serveur. Dans le terminal, vous pouvez télécharger des données, modifier ou gérer des fichiers, examiner des processus et même utiliser des outils tels que vi et nano.

> [!Note]
> Si vous avez des scripts de démarrage dans l’environnement de votre projet, l’ouverture du terminal peut afficher un message indiquant que la configuration est toujours en cours.

Vous pouvez émettre des commandes Linux standard dans le terminal. Vous pouvez également utiliser `ls` dans le dossier de base pour voir les différents environnements qui existent sur la machine virtuelle, comme *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp* et *R*, avec un dossier de *projet* qui contient le projet :

![Terminal de projet dans Azure Notebooks](media/project-terminal.png)

Pour agir sur un environnement spécifique, remplacez tout d’abord les répertoires dans le dossier de cet environnement.

Pour les environnements Python, vous pouvez rechercher `pip` et `conda` dans le dossier *bin* de chaque environnement. Vous pouvez également utiliser des alias intégrés pour les environnements :

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

Les modifications apportées au serveur s’appliquent uniquement à la session active, à l’exception des fichiers et dossiers que vous créez dans le dossier de *projet* lui-même. Par exemple, la modification d’un fichier dans le dossier de projet est conservée entre les sessions, contrairement aux packages avec `pip install`.

> [!Note]
> Si vous utilisez `python` ou `python3`, vous appelez les versions de Python installées par le système, qui ne sont pas utilisées pour les notebooks. Comme vous ne disposez pas non plus des autorisations nécessaires pour les opérations telles que `pip install`, veillez à utiliser les alias spécifiques à la version.

## <a name="access-notebook-logs"></a>Accéder aux journaux d’activité du notebook

Si vous rencontrez des problèmes lors de l’exécution d’un notebook, la sortie de Jupyter est stockée dans un dossier nommé *.nb.log*. Vous pouvez accéder à ces journaux d’activité via la commande **Terminal** ou le tableau de bord du projet.

En général, quand vous exécutez Jupyter en local, vous pouvez l’avoir démarré à partir d’une fenêtre de terminal. La fenêtre de terminal affiche une sortie telle que l’état du noyau.

Pour afficher les journaux d’activité, utilisez la commande suivante dans le terminal :

```bash
cat .nb.log
```

Vous pouvez également utiliser la commande à partir d’une cellule de code dans un notebook Python :

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Étapes suivantes

- [Procédure : utiliser des fichiers de données de projets](work-with-project-data-files.md)
- [Accéder aux données de cloud dans un notebook](access-data-resources-jupyter-notebooks.md)
