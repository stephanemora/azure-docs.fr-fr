---
title: Bien démarrer avec le développement avancé d’Azure Percept au niveau local
description: Bien démarrer avec le développement Machine Learning local à l’aide de VS Code et de Jupyter Notebook sur Azure ML
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 88e71ac21177a13d30176212e97442c63272eca6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658806"
---
# <a name="getting-started-with-machine-learning-development-using-vs-code--jupyter-notebooks-on-azureml"></a>Bien démarrer avec le développement Machine Learning local à l’aide de VS Code et de Jupyter Notebook sur Azure ML

Cet article vous guide tout au long du processus de configuration d’un espace de travail Azure Machine Learning, de création d’une instance de calcul, de configuration d’un environnement de développement Visual Studio Code sur votre ordinateur local et d’exécution des cellules d’un exemple de notebook Jupyter préconfiguré dans VS Code.

Le [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) effectue un apprentissage de transfert à l’aide d’un modèle TensorFlow préentraîné (MobileNetSSDV2Lite) sur AzureML en Python avec un jeu de données personnalisé pour détecter les bols.

Le notebook montre comment démarrer à partir du [jeu de données COCO](https://cocodataset.org/#home), comment le filtrer jusqu’à la classe d’intérêt (bols) et comment le convertir au format approprié. Vous pouvez également utiliser l’outil d’étiquetage open source [VoTT 2](https://github.com/microsoft/VoTT) pour créer et étiqueter des cadres englobants au format PASCAL VOC.

Après avoir réentraîné le modèle sur le jeu de données personnalisé, vous pouvez le déployer sur votre DK Azure Percept à l’aide de la méthode de mise à jour des jumeaux de module. Vous pouvez ensuite vérifier l’inférence de modèle en examinant le flux RTSP en direct à partir du SoM Azure Percept Vision. Le réentraînement et le déploiement des modèles sont effectués dans le notebook par le biais de votre instance de calcul distante.

## <a name="prerequisites"></a>Prérequis

- [Abonnement Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [DK Azure Percept avec le SoM Azure Percept Vision connecté](./overview-azure-percept-dk.md)
- [Expérience d’intégration du DK Azure Percept](./quickstart-percept-dk-set-up.md) terminée

## <a name="download-azure-percept-github-repository"></a>Télécharger le dépôt GitHub Azure Percept

1. Accédez au [dépôt GitHub Azure Percept DK](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).
1. Clonez le dépôt ou téléchargez le fichier ZIP. Près du haut de l’écran, cliquez sur **Code** -> **Télécharger le ZIP**.

    :::image type="content" source="./media/advanced-development-local/download-zip.png" alt-text="Écran de téléchargement de GitHub.":::

## <a name="create-an-azure-machine-learning-workspace-and-remote-compute-instance"></a>Créer un espace de travail Azure Machine Learning et une instance de calcul distante

1. Accédez au [portail Azure Machine Learning](https://ml.azure.com).
1. Sélectionnez votre annuaire, votre abonnement Azure et votre espace de travail Machine Learning dans les menus déroulants, puis cliquez sur **Démarrer**.

    :::image type="content" source="./media/advanced-development-local/machine-learning-portal-get-started.png" alt-text="Écran de démarrage d’Azure ML.":::

    Si vous n’avez pas encore d’espace de travail Azure Machine Learning, cliquez sur **Créer un espace de travail**. Dans le nouvel onglet du navigateur, effectuez les étapes suivantes :

    1. Sélectionnez votre abonnement Azure.
    1. Sélectionnez votre groupe de ressources.
    1. attribuez un nom à votre espace de travail.
    1. Sélectionnez votre région.
    1. Sélectionnez l’édition de votre espace de travail.
    1. Cliquez sur **Vérifier et créer**.
    1. Dans la page suivante, passez en revue vos sélections, puis cliquez sur **Créer**.

        :::image type="content" source="./media/advanced-development-local/workspace-review-and-create.png" alt-text="Écran de création de l’espace de travail dans Azure ML.":::

    Patientez quelques minutes pendant la création de l’espace de travail. Une fois l’espace de travail créé, vous pouvez voir des coches vertes en regard de vos ressources et **Votre déploiement est terminé** en haut de la page de présentation de Machine Learning Services.

    :::image type="content" source="./media/advanced-development-local/workspace-creation-complete-inline.png" alt-text="Confirmation de la création de l’espace de travail." lightbox= "./media/advanced-development-local/workspace-creation-complete.png":::

    Une fois votre espace de travail créé, revenez à l’onglet du portail Machine Learning, puis cliquez sur **Démarrer**.

1. Dans la page d’accueil de l’espace de travail Machine Learning, cliquez sur **Calcul** dans le volet gauche.

1. En l’absence d’instance de calcul, cliquez sur **Nouveau** pour créer un calcul Processeur ou Processeur graphique. Dans la fenêtre **Nouvelle instance de calcul**, entrez un **Nom de calcul**, choisissez un **Type de machine virtuelle**, puis sélectionnez une **Taille de machine virtuelle**. Cliquez sur **Créer**.

    :::image type="content" source="./media/advanced-development-local/new-compute-instance.png" alt-text="Écran de création d’une instance de calcul.":::

    Une fois que vous avez cliqué sur **Créer**, la création de l’instance de calcul prend quelques minutes. L’état de votre **Calcul** affiche un cercle vert, puis **\<your compute name> - En cours d’exécution** une fois la création du calcul terminée. Cette instance de calcul exécute le serveur Jupyter et sera utilisée dans le cadre de ce tutoriel.

## <a name="visual-studio-code-development-environment-setup"></a>Configuration de l’environnement de développement Visual Studio Code

1. Installez les outils obligatoires.

    1. Option 1 :

        Utilisez le [Dev Tools Pack Installer](./dev-tools-installer.md) pour installer les packages suivants :

        - Visual Studio Code
        - Python 3.5, 3.6 ou 3.7
        - Miniconda3
        - Intel OpenVino Toolkit 2020.2

        Remarque : Intel OpenVino Toolkit est listé comme package facultatif dans le Dev Tools Pack Installer, mais il est nécessaire pour utiliser le SOM Azure Percept Vision du kit de développement (DK) Azure Percept.

    1. Option n°2 :

        Si vous préférez ne pas utiliser Dev Tools Pack Installer, installez manuellement les packages suivants en cliquant sur les liens ci-dessous et en suivant les instructions de téléchargement et d’installation spécifiées :

        - [Visual Studio Code](https://code.visualstudio.com/)
        - [Python 3.5, 3.6 ou 3.7](https://www.python.org/)
        - [Miniconda3](https://docs.conda.io/en/latest/miniconda.html)
        - [Intel OpenVino Toolkit 2020.2](https://docs.openvinotoolkit.org/)

    Remarque : Si vous avez déjà installé la distribution Anaconda complète, vous n’avez pas besoin d’installer Miniconda. Si vous préférez ne pas utiliser Anaconda ou Miniconda, vous pouvez créer également un environnement virtuel Python et installer les packages nécessaires pour exécuter le développement de votre modèle IA avec PIP.

1. Lancez Visual Studio Code.
1. Configurez un environnement de science des données :

    - Option 1 - Connectez-vous à une instance de calcul distante Machine Learning (nouvelle ou existante) sur laquelle les packages ML sont déjà organisés. **Il s’agit de l’option que nous utiliserons dans ce tutoriel**.

    - Option 2 - Configurer un environnement conda sur un ordinateur local.
        1. Ouvrez une invite de commandes Anaconda ou Miniconda et exécutez la commande suivante pour créer un environnement nommé **myenv** avec les packages spécifiés :

            `conda create -n myenv python=3.7 pandas jupyter seaborn scikit-learn keras tensorflow=1.15`

            Pour plus d’informations sur la création et la gestion des environnements Anaconda, consultez la [documentation d’Anaconda](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html).

1. Créez un espace de travail VS Code :

    1. Créez un dossier dans un emplacement convenant à votre espace de travail Visual Studio Code. Nommez-le **myworkspace**.
    1. Ouvrez **myworkspace** dans Visual Studio Code en cliquant sur **Fichier** > **Ouvrir le dossier** > **Sélectionner le dossier**.
    1. Dans l’Explorateur de fichiers, accédez au [fichier Transferlearningusing_SSDLiteV2 Model.ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) et sélectionnez-le à partir de votre copie locale du dépôt Azure Percept DK. Copiez ce fichier de notebook dans le dossier myworkspace.

## <a name="azure-integration-with-visual-studio-code"></a>Intégration d’Azure à Visual Studio Code

1. Si vous ne l’avez pas déjà fait, inscrivez-vous pour obtenir la [version gratuite ou payante d’Azure Machine Learning](https://aka.ms/AMLFree).

1. Installez les extensions Azure suivantes pour VS Code :
    - [Extension Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)
    - Extension Python Insiders. Dans VS Code, accédez à **Afficher** -> **Palette de commandes** Dans la palette de commandes, entrez et sélectionnez **Python: Switch to Insiders Daily Channel**.
    - [Extension Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account). Quand vous y êtes invité, rechargez votre fenêtre dans VS Code.
    - [Extension Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).
    - [Extension Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

1. Connectez-vous à votre compte Azure dans Visual Studio Code pour provisionner des ressources et exécuter des charges de travail sur Azure.
    1. Ouvrez la palette de commandes dans Visual Studio Code en sélectionnant **Afficher** > **Palette de commandes** dans la barre de menus.
    1. Entrez **Azure: Sign In** dans la palette de commandes pour démarrer le processus de connexion.

        :::image type="content" source="./media/advanced-development-local/transfer-learning-azure-sign-in-inline.png" alt-text="Écran de connexion Azure." lightbox= "./media/advanced-development-local/transfer-learning-azure-sign-in.png":::

    1. Activez l’extension Python et le compte Azure en cliquant sur l’icône Azure à gauche de VS Code.

        :::image type="content" source="./media/advanced-development-local/azure-icon.png" alt-text="Icône Azure dans VS Code.":::

    1. Ouvrez le notebook Transferlearningusing_SSDLiteV2 Model_VSCode.ipynb à partir du dossier **myworkspace**.
    1. Ouvrez la palette de commandes. Entrez et sélectionnez **Python: specify local or remote Jupyter server for connections**.
    1. Vous devriez voir une liste d’options de connexion parmi lesquelles vous pouvez faire votre choix. Sélectionnez **Instances de calcul Azure ML**.

        :::image type="content" source="./media/advanced-development-local/azure-machine-learning-compute-instances.png" alt-text="Options d’instance de calcul Azure ML dans VS Code.":::

    1. Suivez les invites guidées pour choisir un abonnement, un espace de travail et une instance de calcul distante. Sélectionnez l’espace de travail et l’instance de calcul distante créés précédemment dans ce tutoriel. Vous pouvez également créer une instance de calcul.
    1. Après avoir sélectionné une instance de calcul, vous êtes invité à recharger la fenêtre de VS Code. Une fois le rechargement terminé, sélectionnez le noyau **Python 3.6 - AzureML** . Vous pouvez maintenant exécuter n’importe quelle cellule dans votre notebook. L’exécution d’une cellule de notebook instancie la connexion entre votre notebook et votre instance de calcul. La barre d’outils du notebook est mise à jour pour refléter l’instance de calcul sur laquelle vous travaillez.

        :::image type="content" source="./media/advanced-development-local/kernel-inline.png" alt-text="Sélection du noyau dans VS Code." lightbox= "./media/advanced-development-local/kernel.png":::

## <a name="working-with-the-notebook"></a>Utilisation du notebook

Vous êtes maintenant prêt à exécuter le notebook pour entraîner votre détecteur de bol personnalisé dans VS Code et le déployer sur votre devkit. Veillez à exécuter chaque cellule du notebook individuellement, certaines d’entre elles nécessitant des paramètres d’entrée avant l’exécution du script. Les cellules qui nécessitent des paramètres d’entrée peuvent être modifiées directement dans le notebook. Pour exécuter une cellule, cliquez sur l’icône de lecture à gauche de la cellule :

:::image type="content" source="./media/advanced-development-local/run-cell-1.png" alt-text="Icône de cellule mise en évidence dans un notebook.":::

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de notebooks Azure Machine Learning service, consultez ce [dépôt](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
