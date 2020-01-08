---
title: Azure Machine Learning dans Visual Studio Code
titleSuffix: Azure Machine Learning
description: Découvrez comment installer Azure Machine Learning pour Visual Studio Code et créer une expérience dans Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jimgries
author: greazer
ms.date: 09/20/2019
ms.custom: seodec18
ms.openlocfilehash: d5843937233309e1b37f0127d364066a8a747e21
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75534617"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Premiers pas avec Azure Machine Learning pour Visual Studio Code

Cet article explique comment entraîner et déployer des modèles Machine Learning à l’aide de l’extension **Azure Machine Learning pour Visual Studio Code**.

[Azure Machine Learning](overview-what-is-azure-ml.md) simplifie la création, l’entraînement et le déploiement de modèles Machine Learning.
+ Dans le cadre de l’entraînement, il permet d’exécuter des expériences en local ou à distance. Pour chaque expérience, vous pouvez journaliser des métriques personnalisées de plusieurs exécutions en vue d’optimiser les hyperparamètres.
+ Vous pouvez également utiliser Azure Machine Learning pour déployer facilement des modèles Machine Learning dans vos environnements de test et de production.

## <a name="prerequisites"></a>Conditions préalables requises

+ Si vous n’avez pas d’abonnement Azure, créez un compte gratuit avant de commencer. Essayez la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

+ Installez [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview), un éditeur de code léger qui s’exécute sur Windows, Mac et Linux.

+ [Installez Python 3.5 ou version ultérieure](https://www.anaconda.com/download/).


## <a name="install-the-extension"></a>Installer l’extension

Lorsque vous installez l’extension Azure Machine Learning, deux autres extensions sont automatiquement installées. Il s’agit de l’extension [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) et de l’extension [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-Python.Python). Pour plus d'informations sur l’utilisation de l’extension Python pour modifier, exécuter et déboguer du code Python, consultez le [tutoriel Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).

Installer l’extension Azure Machine Learning :

1. Ouvrez Visual Studio Code.

1. Basculez vers l’onglet Extensions et recherchez « Azure Machine Learning ».

1. Dans l’onglet de l’extension, cliquez sur **Installer**.

1. Un onglet de bienvenue dans l’extension s’ouvre dans Visual Studio Code, et le symbole Azure (en rouge dans la capture d’écran suivante) est ajouté à la barre d’activités.

   ![Icône Azure dans la barre d’activités Visual Studio Code](./media/how-to-vscode-tools/azure-activity-bar.png)

1. Dans la boîte de dialogue, sélectionnez **Se connecter** et suivez les instructions pour vous authentifier auprès Azure.

   L’extension Azure Account, qui a été installée avec l’extension Azure Machine Learning pour Visual Studio Code, vous permet de vous authentifier avec votre compte d’Azure. Pour obtenir une liste des commandes, consultez la page de l’[extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!TIP]
> Vous pouvez également télécharger le programme d’installation de l’extension directement de la page [Extension Azure Machine Learning pour Visual Studio Code (préversion)](https://aka.ms/vscodetoolsforai).

## <a name="quickstart-with-azure-machine-learning"></a>Démarrage rapide avec Azure Machine Learning
Il existe plusieurs façons d’exécuter vos scripts d’entraînement à l’aide d’Azure Machine Learning. Si vous débutez, commencez par la procédure qui explique comment envoyer rapidement un script d’entraînement à exécuter dans Azure.

Si vous êtes déjà familiarisé avec les concepts Azure Machine Learning et que vous souhaitez en savoir plus sur la façon de les utiliser et les gérer avec l’extension, consultez [Approfondissement de l’utilisation des concepts Azure Machine Learning avec VS Code](how-to-vscode-tools.md#azure-machine-learning-in-depth-with-vs-code), plus loin dans cet article.

## <a name="run-an-existing-python-training-script-in-azure"></a>Exécuter un script d’entraînement Python existant dans Azure
Si vous avez déjà un script d’entraînement, l’extension Azure Machine Learning pour VS Code offre non seulement une excellente expérience pour la modification, le débogage et la gestion des sources, mais elle facilite également l’exécution et le stockage des métriques de votre script dans Azure.

Nous pouvons commencer. Utilisez votre propre script d’entraînement si vous l’avez, ou clonez l’exemple [vscode-tools-for-ai repo](https://github.com/microsoft/vscode-tools-for-ai). Il s’agit du dépôt public où sont consignés les problèmes liés à cette extension. Il contient également un petit exemple de dossier **mnist** que nous allons utiliser ici.

1. Ouvrez le dossier **mnist** dans VS Code.

1. Créez un environnement Python à l’aide de votre package d’environnement virtuel habituel ou avec Anaconda, puis installez les packages TensorFlow et numpy.

1. Sélectionnez votre nouvel environnement en tant qu’interpréteur Python en bas à gauche de la barre d’état VS Code.

1. Ouvrez le fichier **train.py** et exécutez-le en ouvrant le débogueur et en appuyant sur le bouton Exécuter (ou en appuyant simplement sur F5).

   [![Exécuter le script d’entraînement MNIST](./media/how-to-vscode-tools/run-mnist.gif)](./media/how-to-vscode-tools/run-mnist.gif#lightbox)

Si tout a été correctement installé, le script s’exécute et crée un modèle TensorFlow dans le dossier outputs.

[![Afficher le modèle TensorFlow](./media/how-to-vscode-tools/show-tensorflow-model.gif)](./media/how-to-vscode-tools/show-tensorflow-model.gif#lightbox)

Maintenant que vous savez que votre script s’exécute correctement, exécutez-le dans Azure.

Cette opération peut s’effectuer facilement sans aucune autre modification de **train.py**. Toutefois, en apportant quelques légères modifications, vous pouvez utiliser Azure Machine Learning pour suivre automatiquement les métriques clés de votre choix à chaque exécution de l’entraînement.

### <a name="make-azure-aware-of-your-run-metrics"></a>Communiquer vos métriques d’exécution à Azure
Pour modifier votre projet afin de communiquer à Azure les informations importantes de vos exécutions :

1. Créez un fichier appelé **amlrun.py** dans le même dossier que **train.py**

    ```Python
    import azureml
    from azureml.core import Run

    # Access the Azure ML run
    # Init run param to check if running within AML
    def get_AMLRun():
        try:
            run = Run.get_submitted_run()
            return run
        except Exception as e:
            print("Caught = {}".format(e.message))
            return None
    ```

2. Importez le fichier amlrun dans **train.py**

    ```Python
    ...
    from utils import prepare_data
    from amlrun import get_AMLRun
    ...
    ```
3. Initialisez l’objet d’exécution dans **train.py**

    ```Python
    ...
    init = tf.global_variables_initializer()
    saver = tf.train.Saver()
    run = get_AMLRun()
    ...
    ```
4. Journalisez les métriques dans Azure avec la fonction run.log() :

    ```Python
    ...
            acc_val = acc_op.eval(feed_dict = {X: X_test, y: y_test})

            # Log accuracies to AML logger if using AML
            if run != None:
                run.log('Validation Accuracy', np.float(acc_val))
                run.log('Training Accuracy', np.float(acc_train))

            print(epoch, '-- Training accuracy:', acc_train, '\b Validation
    ...
    ```
### <a name="run-the-script-in-azure"></a>Exécutez le script dans Azure
Et voilà ! À présent, utilisez simplement l’extension pour exécuter votre script dans le cloud. Notez que, dans l’animation ci-dessous, la procédure pas à pas présentée est plus courte que le temps réellement nécessaire pour créer un espace de travail et un calcul Azure Machine Learning, et pour exécuter le script d’entraînement.

   [![Démarrer une expérience Azure ML](./media/how-to-vscode-tools/start-golden-path.gif)](./media/how-to-vscode-tools/start-golden-path.gif#lightbox)

Après avoir cliqué sur le bouton Run Experiment (Exécuter l’expérience), répondez aux invites comme suit :

1. Choisissez votre abonnement Azure.
1. Choisissez de créer un *nouvel* espace de travail Azure Machine Learning.
1. Sélectionnez un modèle parmi les modèles préconfigurés afin d’initialiser l’environnement Python pour les exécutions. Les modèles fournissent un point de départ et incluent ces paramètres :
    1. **PyTorch**, **TensorFlow** ou **Scikit-learn**
    1. Entraînement de calcul **Single** (Unique) ou **Distributed** (Distribué)
    1. **General** (Général) pour les environnements personnalisés
1. Assurez-vous que la liste des packages pip et conda est complète pour votre script en y ajoutant tous les packages qui ne sont pas inclus dans le modèle.
1. Vérifiez les noms et spécifications par défaut utilisés pour l’exécution de l’expérience, puis cliquez sur le lien **Submit Experiment** (Envoyer l’expérience) dans le fichier JSON. Le fichier JSON ne sera pas enregistré, car il est fourni uniquement pour vous permettre de vérifier ou modifier les paramètres de l’expérience avant l’envoi.
1. Asseyez-vous et détendez-vous pendant que l’extension prépare tout et exécute votre script !

    [![Effectuer l’entraînement dans le cloud](./media/how-to-vscode-tools/run-golden-path.gif)](./media/how-to-vscode-tools/run-golden-path.gif#lightbox)

Quelques secondes plus tard, vous êtes averti que l’expérience a été envoyée à Azure. Vous pouvez dès lors surveiller la progression de son exécution dans Azure Machine Learning Studio en cliquant sur le lien **View Experiment Run** (Voir l’exécution de l’expérience) dans la notification de VS Code, ou directement à partir de VS Code en appuyant sur le bouton d’actualisation dans l’onglet Azure.

Pour le moment, la consultation des métriques d’exécution est possible uniquement dans Studio. Le lien **View Experiment Run** mentionné ci-dessus vous renvoie à l’exécution où vous pouvez voir les métriques que vous avez journalisées.
[![Exécution de l’expérience dans le portail](./media/how-to-vscode-tools/experiment-run-on-portal.PNG)](./media/how-to-vscode-tools/experiment-run-on-portal.PNG#lightbox)

## <a name="azure-machine-learning-in-depth-with-vs-code"></a>Approfondissement de l’utilisation des concepts Azure Machine Learning avec VS Code

Dans la procédure pas à pas que nous venons de voir, nous avons envoyé une expérience en suivant le chemin le plus simple. Comme vous l’avez peut-être remarqué, l’extension réduit les étapes que vous avez à effectuer pour exécuter une expérience. Dans cette section, nous allons voir de quelle façon vous pouvez gérer tous les concepts Azure Machine Learning individuellement, pour un contrôle maximal.

Avant de commencer à entraîner et à déployer les modèles Machine Learning à l’aide de Visual Studio Code, vous devez créer un [espace de travail Azure Machine Learning](concept-workspace.md) dans le cloud. Cet espace de travail contiendra vos modèles et ressources.

### <a name="create-a-workspace"></a>Créer un espace de travail

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

    [![Créer un espace de travail](./media/how-to-vscode-tools/create-workspace.gif)](./media/how-to-vscode-tools/create-workspace.gif#lightbox)


1. Cliquez avec le bouton droit sur votre abonnement Azure et sélectionnez **Créer un espace de travail**. Par défaut, un nom est généré, indiquant la date et l’heure de création. Remplacez le nom par **TeamWorkspace** et appuyez sur Entrée.

1. Sélectionnez un groupe de ressources dans la liste si vous savez lequel choisir, ou créez-en un autre. Si vous créez un groupe de ressources, choisissez un emplacement le plus proche possible de l’emplacement où vous comptez déployer votre modèle. Dans cet exemple, nous avons choisi **West US 2**.

1. Une fois que vous avez appuyé sur Entrée, Azure Machine Learning reçoit la requête de création de votre espace de travail. Vous êtes averti du processus dans la zone de notification de Visual Studio Code.

1. Développez le nœud Subscription (Abonnement) pour trouver l’espace de travail que vous venez de créer.

### <a name="create-an-experiment"></a>Créer une expérience
Vous pouvez créer une ou plusieurs expériences dans votre espace de travail pour suivre et analyser individuellement les exécutions d’entraînement du modèle. Les exécutions peuvent être effectuées dans le cloud Azure ou sur votre machine locale.

1. Développez l’espace de travail **TeamWorkspace**. Cliquez avec le bouton droit sur le nœud **Experiments** (Expériences), et choisissez **Create Experiment** (Créer une expérience) dans le menu contextuel.

1. À l’invite, entrez un nom pour votre expérience. Dans les exemples de captures d’écran, l’expérience s’appelle **MNIST**.

1. Sélectionnez Entrée pour créer la nouvelle l’expérience. La nouvelle expérience s’affiche dans l’arborescence en tant qu’enfant du nœud **Experiments** (Expériences).

1. Dans un espace de travail, vous pouvez cliquer avec le bouton droit sur une expérience pour la définir comme **Active**. L’expérience **Active** lie cette expérience dans le cloud au dossier que vous avez précédemment ouvert dans Visual Studio Code. Ce dossier doit contenir vos scripts Python locaux. En définissant une expérience active, les métriques clés de toutes les exécutions d’entraînement seront stockées dans l’expérience, quel que soit l’emplacement de leur exécution.

    [![Créer une expérience](./media/how-to-vscode-tools/create-experiment.gif)](./media/how-to-vscode-tools/create-experiment.gif#lightbox)


### <a name="create-and-manage-compute-targets"></a>Créer et gérer des cibles de calcul

Avec Azure Machine Learning pour Visual Studio Code, vous pouvez préparer vos données, effectuer l’apprentissage des modèles et les déployer à la fois en local et sur des cibles de calcul à distance.

L’extension prend en charge plusieurs cibles de calcul à distance pour Azure Machine Learning. Pour plus d’informations, consultez la liste complète des [cibles de calcul prises en charge pour Azure Machine Learning](how-to-set-up-training-targets.md).

### <a name="create-compute-targets-for-azure-machine-learning-in-visual-studio-code"></a>Créer des cibles de calcul pour Azure Machine Learning dans Visual Studio Code

Pour créer une cible de calcul :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail Azure Machine Learning.

1. Sous le nœud de l’espace de travail, cliquez avec le bouton droit sur le nœud **Calcul**, puis choisissez **Create Compute** (Créer un calcul).

1. Choisissez le type de cible de calcul dans la liste.

1. À l’invite de la palette de commandes, sélectionnez une taille de machine virtuelle. Vous pouvez filtrer les calculs sur du texte, par exemple, sur « gpu ».

1. À l’invite de la palette de commandes, entrez un nom pour la cible de calcul.

1. Une fois le nom entré, le calcul est créé avec les paramètres par défaut. Pour changer les paramètres, cliquez avec le bouton droit sur le nouveau calcul et choisissez **Edit Compute** (Modifier le calcul).

1. Dans le fichier JSON qui s’affiche, effectuez les modifications souhaitées, puis cliquez sur l’option CodeLens « Enregistrer et continuer » (avec le clavier, appuyez sur **Ctrl+Maj+P** pour appeler la palette de commandes et exécuter la commande **Azure ML : Enregistrer et continuer**).

Voici un exemple de création et de modification d’un calcul Azure Machine Learning (AMLCompute) :

[![Créer un calcul AML dans Visual Studio Code](./media/how-to-vscode-tools/create-remote-compute.gif)](./media/how-to-vscode-tools/create-remote-compute.gif#lightbox).

#### <a name="the-run-configuration-file"></a>Fichier de configuration de série de tests

Pour pouvoir exécuter une expérience Azure Machine Learning sur un calcul, vous devez avoir configuré ce calcul de manière appropriée. L’environnement à utiliser est spécifié dans un fichier de configuration d’exécution.

Cet exemple montre comment créer une configuration d’exécution pour le calcul AmlCompute créé plus haut.

[![Créer une configuration d’exécution pour un calcul](./media/how-to-vscode-tools/create-runconfig.gif)](./media/how-to-vscode-tools/create-runconfig.gif#lightbox)

Pour exécuter des expériences Azure ML sur votre machine locale, vous avez également besoin d’un fichier de configuration d’exécution. Lors de la création d’une configuration d’exécution locale, l’environnement Python utilisé choisit par défaut le chemin de l’interpréteur que vous avez défini dans VS Code.

### <a name="train-and-tune-models"></a>Effectuer l’apprentissage des modèles et les ajuster

Avec l’extension Azure ML pour VS Code, il existe plusieurs façons d’exécuter un script d’entraînement dans une expérience.

1. Cliquez avec le bouton droit sur le script d’entraînement et choisissez **Azure ML: Run as Experiment in Azure** (Exécuter en tant qu’expérience dans Azure).
1. Cliquez sur l’icône Run Experiment (Exécuter l’expérience) dans la barre d’outils.
1. Cliquez avec le bouton droit sur un nœud de configuration d’exécution.
1. Utilisez la palette de commandes VS Code pour exécuter **Azure ML: Run Experiment** (Exécuter l’expérience).

Pour exécuter une expérience Azure Machine Learning :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail Azure Machine Learning.

1. Sous le nœud de l’espace de travail, développez le nœud **Experiments** (Expériences) et cliquez avec le bouton droit sur l’expérience à exécuter.

1. Sélectionnez **Run Experiment** (Exécuter l’expérience).

1. Choisissez le nom du fichier Python que vous souhaitez exécuter pour entraîner votre modèle et appuyez sur Entrée pour envoyer l’exécution. Remarque : Le fichier choisi doit résider dans le dossier que vous avez précédemment ouvert dans VS Code.

1. Une fois l’exécution envoyée, un **nœud Run** (Exécution) apparaît en dessous de l’expérience choisie. Utilisez ce nœud pour superviser l’état de vos exécutions. Remarque : Vous devrez peut-être actualiser régulièrement la fenêtre pour voir l’état le plus récent.

Voici un exemple montrant comment exécuter une expérience sur le calcul que vous avez créé plus haut :

[![Exécuter une expérience localement](./media/how-to-vscode-tools/run-experiment.gif)](./media/how-to-vscode-tools/run-experiment.gif#lightbox)

### <a name="deploy-and-manage-models"></a>Déployer et gérer des modèles
Azure Machine Learning vous permet de déployer et de gérer vos modèles Machine Learning dans le cloud et en périphérie.

#### <a name="register-your-model-to-azure-machine-learning-from-visual-studio-code"></a>Enregistrer votre modèle auprès d’Azure Machine Learning à partir de Visual Studio Code

Maintenant que vous avez effectué l’apprentissage de votre modèle, vous pouvez l’inscrire dans votre espace de travail. Vous pouvez suivre et déployer des modèles enregistrés.

Pour enregistrer votre modèle :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez l’abonnement Azure et l’espace de travail Azure Machine Learning.

1. Sous le nœud d’espace de travail, cliquez avec le bouton droit sur **Modèles**, puis choisissez **Register Model** (Inscrire le modèle).

1. Dans la palette de commandes, entrez un nom de modèle dans le champ.

1. À partir de cette liste, choisissez si vous souhaitez charger un **fichier de modèle** (pour les modèles uniques) ou un **dossier modèle** (pour les modèles avec plusieurs fichiers, par exemple TensorFlow).

1. Sélectionnez votre dossier ou fichier.

1. Lorsque vous avez terminé de configurer les propriétés de votre modèle, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre**.

Voici un exemple montrant comment enregistrer votre modèle auprès d’Azure Machine Learning :

[![Inscription d’un modèle dans AML](./media/how-to-vscode-tools/register-model.gif)](./media/how-to-vscode-tools/register-model.gif#lightbox)


#### <a name="deploy-your-service-from-visual-studio-code"></a>Déployer votre service à partir de Visual Studio Code

Dans Visual Studio Code, vous pouvez déployer votre service web sur :
+ Azure Container Instances (ACI) à des fins de test.
+ Azure Kubernetes Service (AKS) en production.

Vous n’avez pas besoin de créer un conteneur ACI en vue de le tester au préalable, car les conteneurs ACI sont créés au fur et à mesure des besoins. Toutefois, vous devez configurer les clusters AKS à l’avance. Pour plus d’informations, consultez [Déployer des modèles avec Azure Machine Learning](how-to-deploy-and-where.md).

Pour déployer un service web :

1. Dans la barre d’activités Visual Studio Code, sélectionnez l’icône Azure. La barre latérale Azure Machine Learning apparaît.

1. Dans l’arborescence, développez votre abonnement Azure et l’espace de travail Azure Machine Learning.

1. Sous le nœud d’espace de travail, développez le nœud **Modèles**.

1. Cliquez sur le modèle que vous souhaitez déployer avec le bouton droit, puis choisissez **Deploy Service from Registered Model** (Déployer le service depuis le modèle inscrit) dans le menu contextuel.

1. Dans la palette de commandes, choisissez la cible de calcul sur laquelle effectuer le déploiement.

1. Dans la palette de commandes, entrez un nom pour ce service dans le champ.

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de script.

1. Dans la palette de commandes, appuyez sur la touche entrée de votre clavier pour parcourir et sélectionner le fichier de dépendance Conda.

1. Lorsque vous avez terminé de configurer les propriétés de votre service, dans le coin inférieur droit de la fenêtre, sélectionnez **Soumettre** pour procéder au déploiement. Dans le fichier de propriétés de service, vous pouvez spécifier un fichier docker local ou un fichier schema.json.

Le service web est désormais déployé.

Voici un exemple de déploiement d'un service web :

[![Déployer un service web](./media/how-to-vscode-tools/create-image.gif)](./media/how-to-vscode-tools/create-image.gif#lightbox)

### <a name="experiment-with-additional-features"></a>Expérimenter des fonctionnalités supplémentaires

Vous pouvez utiliser la palette de commandes pour accéder à de nombreuses fonctionnalités Azure Machine Learning dans Visual Studio Code. Pour appeler la palette de commandes, appuyez sur Ctrl+Maj+P. À partir de là, vous pouvez rechercher d’autres fonctionnalités Azure ML de l’extension.

[![Raccourcis clavier d’Azure Machine Learning pour Visual Studio Code](./media/how-to-vscode-tools/commands.gif)](./media/how-to-vscode-tools/commands.gif#lightbox)

## <a name="next-steps"></a>Étapes suivantes

* Pour une procédure détaillée sur l’apprentissage automatique avec Azure Machine Learning en dehors de Visual Studio Code, consultez le [tutoriel : Former des modèles avec Azure Machine Learning](tutorial-train-models-with-aml.md).
* Pour une procédure détaillée sur la modification, l’exécution et le débogage de code localement, consultez le [didacticiel Python Hello World](https://code.visualstudio.com/docs/Python/Python-tutorial).
