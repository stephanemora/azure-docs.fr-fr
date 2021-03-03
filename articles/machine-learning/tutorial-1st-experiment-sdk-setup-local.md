---
title: 'Tutoriel : Bien démarrer avec Machine Learning - Python'
titleSuffix: Azure Machine Learning
description: Dans ce tutoriel, vous allez vous familiariser avec le SDK Azure Machine Learning pour Python exécuté dans votre environnement de développement personnel.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 02/11/2021
ms.custom: devx-track-python
adobe-target: true
ms.openlocfilehash: 5d61f330c823aff755a25e637bb58c132aa58100
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100369117"
---
# <a name="tutorial-get-started-with-azure-machine-learning-in-your-development-environment-part-1-of-4"></a>Tutoriel : Bien démarrer avec Azure Machine Learning dans votre environnement de développement (partie 1 sur 4)

Dans cette *série de tutoriels en quatre parties*, vous allez découvrir les notions de base d’Azure Machine Learning et effectuer des tâches de machine learning Python basées sur des travaux, sur la plateforme cloud Azure. 

Dans la partie 1 de cette série de didacticiels, vous allez :

> [!div class="checklist"]
> * Installer le SDK Azure Machine Learning.
> * Configurer la structure de répertoires pour le code.
> * Créez un espace de travail Machine Learning.
> * Configurer votre environnement de développement local.
> * Configurer un cluster de calcul.

> [!NOTE]
> Cette série de tutoriels porte sur les concepts Azure Machine Learning nécessaires pour soumettre des **programmes de traitement par lots** : c’est là que le code est soumis au cloud pour s’exécuter en arrière-plan sans aucune intervention de l’utilisateur. Ces concepts s’avèrent utiles pour les scripts ou le code terminés que vous voulez exécuter à plusieurs reprises, ou bien pour les tâches de Machine Learning gourmandes en calcul. Si vous êtes plus intéressé par un workflow exploratoire, vous pouvez utiliser à la place [Jupyter ou RStudio sur une instance de calcul Azure Machine Learning](tutorial-1st-experiment-sdk-setup.md).

## <a name="prerequisites"></a>Prérequis

- Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez [Azure Machine Learning](https://aka.ms/AMLFree).
- [Anaconda](https://www.anaconda.com/download/) ou [Miniconda](https://www.anaconda.com/download/) pour gérer les environnements virtuels Python et installer les packages.  
- Si vous ne connaissez pas bien l’utilisation de conda, consultez [Bien démarrer avec conda](https://conda.io/projects/conda/en/latest/user-guide/getting-started.html).

## <a name="install-the-azure-machine-learning-sdk"></a>Installer le Kit de développement logiciel (SDK) Azure Machine Learning

Tout au long de ce tutoriel, vous allez utiliser le SDK Azure Machine Learning pour Python. Pour éviter les problèmes de dépendance Python, vous allez créer un environnement isolé. Cette série de tutoriels utilise conda pour créer cet environnement. Si vous préférez utiliser d’autres solutions, comme `venv`, `virtualenv` ou Docker, veillez à utiliser une version de Python > = 3.5 et < 3.9.

Vérifiez si conda est installé sur votre système :
    
```bash
conda --version
```
    
Si cette commande retourne une erreur `conda not found`, [téléchargez et installez Miniconda](https://docs.conda.io/en/latest/miniconda.html). 

Une fois que vous avez installé Conda, utilisez un terminal ou une fenêtre d’invite Anaconda pour créer un environnement :

```bash
conda create -n tutorial python=3.8
```

Ensuite, installez le SDK Azure Machine Learning dans l’environnement Conda que vous avez créé :

```bash
conda activate tutorial
pip install azureml-core
```
    
> [!NOTE]
> L’installation du SDK Azure Machine Learning prend environ 2 minutes.
>
> Si vous recevez une erreur de délai d’expiration, essayez plutôt `pip install --default-timeout=100 azureml-core`.


> [!div class="nextstepaction"]
> [J’ai installé le SDK](?success=install-sdk#dir) [J’ai rencontré un problème](https://www.research.net/r/7C8Z3DN?issue=install-sdk)

## <a name="create-a-directory-structure-for-code"></a><a name="dir"></a>Créer la structure de répertoires pour le code

Nous vous recommandons de configurer la structure de répertoires simple suivante pour ce tutoriel :

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-1.png" alt-text="structure de répertoires : niveau supérieur de tutoriel avec un sous-répertoire .azureml":::


- `tutorial` : Répertoire de plus haut niveau du projet.
- `.azureml` : Sous-répertoire masqué pour le stockage des fichiers de configuration Azure Machine Learning.

Par exemple, pour créer cela dans une fenêtre Bash :

```bash
mkdir tutorial
cd tutorial
mkdir .azureml
```

> [!TIP]
> Pour créer ou afficher la structure dans une fenêtre graphique, vous devez d’abord activer la capacité à afficher et à créer des fichiers et des dossiers masqués :
>
> * Dans une fenêtre Finder Mac, utilisez **Commande+Maj+.** pour activer/désactiver l’affichage des fichiers/dossiers masqués.  
> * Dans un Explorateur de fichiers Windows 10, consultez le [guide pratique pour afficher les fichiers et dossiers cachés](https://support.microsoft.com/en-us/windows/view-hidden-files-and-folders-in-windows-10-97fbc472-c603-9d90-91d0-1166d1d9f4b5). 
> * Dans l’interface graphique Linux, utilisez **Ctrl+H** ou le menu **Afficher**, puis cochez la case **Afficher les fichiers masqués**.




> [!div class="nextstepaction"]
> [J’ai créé un répertoire](?success=create-dir#workspace) [J’ai rencontré un problème](https://www.research.net/r/7C8Z3DN?issue=create-dir)

## <a name="create-an-azure-machine-learning-workspace"></a><a name="workspace"></a>Création d’un espace de travail Microsoft Azure Machine Learning

Un espace de travail est la ressource de niveau supérieur pour Azure Machine Learning et est un endroit centralisé où vous pouvez :

- Gérer des ressources comme le calcul.
- Stocker des ressources comme des notebooks, des environnements, des jeux de données, des pipelines, des modèles et des points de terminaison.
- Collaborer avec d’autres membres de l’équipe.

Dans le répertoire de plus haut niveau, `tutorial`, ajoutez un nouveau fichier Python appelé `01-create-workspace.py` en utilisant le code suivant. Adaptez les paramètres (nom, ID d’abonnement, groupe de ressources et [emplacement](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)) selon vos préférences.

Vous pouvez exécuter le code dans une session interactive ou sous la forme d’un fichier Python.

>[!NOTE]
> Quand vous utilisez un environnement de développement local (par exemple votre ordinateur), vous êtes invité à vous authentifier auprès de votre espace de travail en utilisant un *code d’appareil* la première fois que vous exécutez le code suivant. Suivez les instructions à l’écran.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Dans la fenêtre qui contient l’environnement Conda *tutorial1* activé, exécutez ce code à partir du répertoire `tutorial`.

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

> [!TIP]
> Si l’exécution de ce code génère une erreur indiquant que vous n’avez pas accès à l’abonnement, consultez [Créer un espace de travail](how-to-manage-workspace.md?tab=python#create-multi-tenant) pour obtenir plus d’informations sur les options d’authentification.


Une fois que vous avez exécuté *01-create-workspace.py* avec succès, votre structure de dossiers se présente comme suit :

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-2.png" alt-text="Le fichier config.json apparaît dans le sous-répertoire .azureml après l’exécution de 01-create-workspace.py":::

Le fichier `.azureml/config.json` contient les métadonnées nécessaires pour se connecter à votre espace de travail Azure Machine Learning. Il contient en fait votre ID d’abonnement, le groupe de ressources et le nom de l’espace de travail. 

> [!NOTE]
> Le contenu de `config.json` n’est pas constitué de secrets. Cela ne pose pas de problèmes de partager ces informations.
>
> L’authentification est toujours requise pour interagir avec votre espace de travail Azure Machine Learning.

> [!div class="nextstepaction"]
> [J’ai créé un espace de travail](?success=create-workspace#cluster) [J’ai rencontré un problème](https://www.research.net/r/7C8Z3DN?issue=create-workspace)

## <a name="create-an-azure-machine-learning-compute-cluster"></a><a name="cluster"></a> Créer un cluster de calcul Azure Machine Learning

Créez un script Python dans le répertoire de plus haut niveau `tutorial` appelé `02-create-compute.py`. Placez-y le code suivant pour créer un cluster de calcul Azure Machine Learning qui sera mis à l’échelle automatiquement entre zéro et quatre nœuds :

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # This automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that the cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                           idle_seconds_before_scaledown=2400,
                                                           min_nodes=0,
                                                           max_nodes=4)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Dans la fenêtre qui contient l’environnement Conda *tutorial1* activé, exécutez le fichier Python :

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Quand le cluster est créé, aucun nœud n’y est provisionné. Le cluster n’implique *aucun* coût tant que vous n’avez pas soumis un travail. Ce cluster fera l’objet d’un scale-down après 2 400 secondes (40 minutes) d’inactivité.

Votre structure de dossiers se présente désormais comme suit :

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/directory-structure-3.png" alt-text="Ajouter 02-create-compute.py au répertoire du tutoriel":::

> [!div class="nextstepaction"]
> [J’ai créé un cluster de calcul](?success=create-compute-cluster#next-steps) [J’ai rencontré un problème](https://www.research.net/r/7C8Z3DN?issue=create-compute-cluster)

## <a name="view-in-the-studio"></a>Afficher dans le studio

Connectez-vous au [studio Azure Machine Learning](https://ml.azure.com) pour afficher l’espace de travail et l’instance de calcul que vous avez créés.

1. Sélectionnez l’**abonnement** que vous avez utilisé pour créer l’espace de travail.
1. Sélectionnez l’**espace de travail Machine Learning** que vous avez créé, *tutorial-ws*.
1. Une fois que l’espace de travail est chargé, sur le côté gauche, sélectionnez **Calcul**.
1. En haut, sélectionnez l’onglet **Clusters de calcul**.

:::image type="content" source="media/tutorial-1st-experiment-sdk-local/compute-instance-in-studio.png" alt-text="Capture d’écran : Afficher l’instance de calcul dans votre espace de travail.":::

Cette vue montre le cluster de calcul provisionné, ainsi que le nombre de nœuds inactifs, de nœuds occupés et de nœuds non provisionnés.  Étant donné que vous n’avez pas encore utilisé le cluster, tous les nœuds ne sont pas provisionnés pour le moment.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel de configuration, vous avez :

- Créé un espace de travail Azure Machine Learning
- Configuré votre environnement de développement local.
- Créé un cluster de calcul Azure Machine Learning.

Dans les autres sections de ce tutoriel, vous allez apprendre à :

* Partie 2. Exécuter du code dans le cloud en utilisant le SDK Azure Machine Learning pour Python.
* Partie 3. Gérer l’environnement Python que vous utilisez pour entraîner le modèle.
* Partie 4. Charger des données dans Azure et les utiliser dans la formation.

Continuez avec le tutoriel suivant pour découvrir comment envoyer un script au cluster de calcul Azure Machine Learning.

> [!div class="nextstepaction"]
> [Tutoriel : Exécuter un script Python « Hello world! » sur Azure](tutorial-1st-experiment-hello-world.md)
