---
title: 'Tutoriel : Entraîner et déployer un modèle - Machine Learning sur Azure IoT Edge'
description: Dans ce tutoriel, vous allez effectuer l’apprentissage d’un modèle Machine Learning à l’aide d’Azure Machine Learning. Vous allez ensuite créer un package de ce modèle sous la forme d’une image conteneur qui pourra être déployée en tant que module Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 73e4edd25946f71cfb6d14a379d5b004343cc4e3
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367234"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutoriel : Entraîner et déployer un modèle Azure Machine Learning

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Dans cet article, vous allez effectuer les tâches suivantes :

* Utiliser Azure Machine Learning Studio pour entraîner un modèle Machine Learning.
* Empaqueter le modèle entraîné sous forme d’image conteneur
* Déployer l’image conteneur en tant que module Azure IoT Edge

Machine Learning Studio est un socle utilisé pour expérimenter des modèles Machine Learning, effectuer leur apprentissage et les déployer.

Les étapes de cet article peuvent être généralement effectuées par les scientifiques de données.

Dans cette section du tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer des notebooks Jupyter dans un espace de travail Azure Machine Learning pour effectuer l’apprentissage d’un modèle Machine Learning.
> * Conteneuriser le modèle Machine Learning entraîné.
> * Créer un module IoT Edge à partir du modèle Machine Learning conteneurisé.

## <a name="prerequisites"></a>Prérequis

Cet article fait partie d’une série de tutoriels sur l’utilisation de Machine Learning sur IoT Edge. Chaque article de la série s’appuie sur le travail réalisé dans le cadre de l’article précédent. Si vous êtes tombé directement sur cet article, consultez le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série.

## <a name="set-up-azure-machine-learning"></a>Configurer Azure Machine Learning

Nous utilisons Machine Learning Studio pour héberger les deux notebooks Jupyter et les fichiers qui les accompagnent. Ici, nous créons et configurons un projet Machine Learning. Si vous n’avez jamais utilisé Jupyter ou Machine Learning Studio, voici deux documents de présentation :

* **Notebook Jupyter** : [Utilisation de notebooks Jupyter dans Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine Learning** : [Commencer à utiliser Azure Machine Learning dans des notebooks Jupyter](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> Une fois le service configuré, Machine Learning est accessible depuis n’importe quelle machine. Pendant la configuration, vous devez utiliser la machine virtuelle de développement, qui contient tous les fichiers dont vous aurez besoin.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>Installer l’extension Azure Machine Learning de Visual Studio Code

Cette extension doit être installée dans Visual Studio Code sur la machine virtuelle de développement. Si vous travaillez sur une autre instance, réinstallez l’extension comme indiqué dans [Configurer l’extension de Visual Studio Code](../machine-learning/how-to-setup-vs-code.md).

### <a name="create-an-azure-machine-learning-account"></a>Créer un compte Azure Machine Learning

Pour approvisionner des ressources et exécuter des charges de travail sur Azure, connectez-vous avec les informations d’identification de votre compte Azure.

1. Dans Visual Studio Code, ouvrez la palette de commandes en sélectionnant **Affichage** > **Palette de commandes** dans la barre de menus.

1. Entrez la commande `Azure: Sign In` dans la palette de commandes pour démarrer le processus de connexion. Suivez les instructions pour effectuer la connexion.

1. Créez une instance de calcul Machine Learning pour exécuter votre charge de travail. Dans la palette de commandes, entrez la commande `Azure ML: Create Compute`.
1. Sélectionnez votre abonnement Azure.
1. Sélectionnez **+ Créer un espace de travail Azure ML** et entrez le nom **turbofandemo**.
1. Sélectionnez le groupe de ressources que vous avez utilisé pour cette démonstration.
1. Vous devriez voir la progression de la création de l’espace de travail en bas à droite de votre fenêtre Visual Studio Code : **Création de l’espace de travail : turobofandemo**. Cette opération peut prendre une ou deux minutes.
1. Patientez pendant la création de l’espace de travail. Un message doit indiquer que l’**espace de travail Azure ML turbofandemo a été créé**.

### <a name="upload-jupyter-notebook-files"></a>Charger des fichiers Jupyter Notebook

Nous allons charger des exemples de fichiers notebooks dans un nouvel espace de travail Machine Learning.

1. Accédez à ml.azure.com et connectez-vous.
1. Sélectionnez votre répertoire Microsoft, votre abonnement Azure et l’espace de travail Machine Learning que vous venez de créer.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="Capture d’écran qui montre la sélection de votre espace de travail Azure Machine Learning." :::

1. Une fois que vous êtes connecté à votre espace de travail Machine Learning, accédez à la section **Notebooks** à l’aide du menu sur le côté gauche.
1. Sélectionnez l’onglet **Mes fichiers**.

1. Sélectionnez **Charger** (icône de flèche vers le haut).

1. Accédez à **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Sélectionnez tous les fichiers de la liste, puis sélectionnez **Ouvrir**.

1. Cochez la case **J’ai confiance dans le contenu de ces fichiers**.

1. Sélectionnez **Charger** pour commencer le chargement. Sélectionnez ensuite **Terminé** une fois le processus terminé.

### <a name="jupyter-notebook-files"></a>Fichiers Jupyter Notebook

Passons en revue les fichiers que vous avez chargés dans votre espace de travail Machine Learning. Les activités de cette partie du tutoriel sont réparties entre deux fichiers de notebooks, qui utilisent quelques fichiers de prise en charge.

* **01-turbofan\_regression.ipynb** : Ce notebook utilise l’espace de travail Machine Learning pour créer et exécuter une expérience Machine Learning. Le notebook implique plus ou moins les étapes suivantes :

  1. Téléchargement des données à partir du compte de stockage Azure qui a été généré par l’atelier d’appareils
  1. Exploration et préparation des données, puis utilisation de ces données pour effectuer l’apprentissage du modèle de classifieur
  1. Évaluation du modèle de l’expérience à l’aide d’un jeu de données de test (Test\_FD003.txt)
  1. Publication du meilleur modèle de classifieur dans l’espace de travail Machine Learning

* **02-turbofan\_deploy\_model.ipynb** : Ce notebook prend le modèle créé dans le notebook précédent et l’utilise pour créer une image conteneur prête à être déployée sur un appareil IoT Edge. Le notebook effectue les étapes suivantes :

  1. Création d’un script de scoring pour le modèle
  1. Création d’une image conteneur à l’aide du modèle de classifieur qui a été enregistré dans l’espace de travail Machine Learning
  1. Déploiement de l’image en tant que service web sur Azure Container Instances
  1. Utilisation du service web pour valider le modèle et le travail de l’image comme prévu L’image validée sera déployée sur notre périphérique IoT Edge dans la partie [Créer et déployer des modules IoT Edge personnalisés](tutorial-machine-learning-edge-06-custom-modules.md) de ce tutoriel.

* **Test\_FD003.txt** : Ce fichier contient les données que nous utiliserons comme jeu de test lors de la validation de notre classifieur formé. Pour rester simples, nous avons choisi d’utiliser les données de test fournies pour le concours original pour notre jeu de test.
* **RUL\_FD003.txt** : Ce fichier contient la durée de vie utile restante pour le dernier cycle de chaque appareil du fichier Test\_FD003.txt. Pour une description détaillée des données, consultez les fichiers readme.txt et Damage Propagation Modeling.pdf disponibles ici : C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.
* **Utils.py** : Ce fichier contient un ensemble de fonctions utilitaires Python pour travailler avec des données. Le premier notebook contient une explication détaillée des fonctions.
* **README.md** : Ce fichier Lisez-moi décrit l’utilisation des notebooks.

## <a name="run-the-jupyter-notebooks"></a>Exécuter les notebooks Jupyter

Maintenant que l’espace de travail est créé, vous pouvez exécuter les notebooks.

1. Depuis votre page **Mes fichiers**, sélectionnez **01-turbofan\_regression.ipynb**.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Capture d’écran qui montre la sélection du premier notebook à exécuter.":::

1. Si le notebook est répertorié comme **Non approuvé**, sélectionnez le widget **Non approuvé** dans le coin supérieur droit du notebook. Lorsque la boîte de dialogue apparaît, sélectionnez **Faire confiance**.

1. Pour de meilleurs résultats, lisez la documentation de chaque cellule et exécutez-la individuellement. Sélectionnez **Exécuter** dans la barre d’outils. Plus tard, il vous sera utile d’exécuter plusieurs cellules. Vous pouvez ignorer les avertissements de mise à niveau et d’obsolescence.

    Quand une cellule est en cours d’exécution, un astérisque entre crochets ([\*]) s’affiche. Lorsque l’opération est terminée dans la cellule, l’astérisque est remplacé par un numéro et une sortie peut s’afficher. Les cellules d’un notebook sont générées de façon séquentielle, et vous ne pouvez exécuter qu’une seule cellule à la fois.

    Vous pouvez également utiliser les options d’exécution à partir du menu **Cellule**. Sélectionnez **Ctrl+Entrée** pour exécuter une cellule, et sélectionnez **Maj+Entrée** pour exécuter une cellule et passer à la cellule suivante.

    > [!TIP]
    > Pour les opérations de cellule cohérentes, évitez d’exécuter le même notebook à partir de plusieurs onglets de votre navigateur.

1. Dans la cellule qui suit les instructions **Définir des propriétés globales**, entrez les valeurs de votre abonnement, de vos paramètres et de vos ressources Azure. Ensuite, exécutez la cellule.

    ![Capture d’écran montrant la configuration des propriétés globales dans le notebook.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Dans la cellule précédant **Détails de l’espace de travail**, après son exécution, recherchez le lien qui vous demande de vous connecter pour vous authentifier.

    ![Capture d’écran montrant l’invite de connexion pour l’authentification de l’appareil.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Ouvrez le lien et entrez le code spécifié. Cette procédure de connexion authentifie le notebook Jupyter pour accéder aux ressources Azure à l’aide de l’interface de ligne de commande multiplateforme Microsoft Azure.

    ![Capture d’écran montrant la confirmation de l’authentification de l’application sur l’appareil.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Dans la cellule qui précède **Explore the results** (Explorer les résultats), copiez la valeur de l’ID d’exécution et collez-la pour l’ID d’exécution dans la cellule qui suit **Reconstitute a run** (Reconstituer une exécution).

   ![Capture d’écran montrant la copie de l’ID d’exécution entre les cellules.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Exécutez les cellules restantes dans le notebook.

1. Enregistrez le notebook et revenez à la page de votre projet.

1. Ouvrez **02-turbofan\_deploy\_model.ipynb** et exécutez chaque cellule. Vous devrez vous connecter pour vous authentifier dans la cellule qui suit **Configurer l’espace de travail**.

1. Enregistrez le notebook et revenez à la page de votre projet.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

Pour vérifier si les notebooks se sont correctement exécutés, regardez si certains éléments ont été créés.

1. Sous l’onglet **Mes fichiers** de vos notebooks Machine Learning, sélectionnez **Actualiser**.

1. Vérifiez que les fichiers suivants ont été créés.

    | Fichier | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Fichier config utilisé pour créer l’espace de travail Machine Learning. |
    | ./aml_config/model_config.json | Fichier config dont nous aurons besoin pour déployer le modèle dans l’espace de travail Machine Learning **turbofanDemo** d’Azure. |
    | myenv.yml| Fournit des informations sur les dépendances pour le modèle Machine Learning déployé.|

1. Vérifiez que les ressources Azure suivantes ont été créées. Certains noms de ressource sont suivis de caractères aléatoires.

    | Ressource Azure | Nom |
    | --- | --- |
    | Espace de travail Azure Machine Learning | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Stockage Azure | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Débogage

Vous pouvez insérer des instructions Python dans le notebook pour le débogage, telles que la commande `print()` pour afficher des valeurs. Si vous voyez des variables ou des objets qui ne sont pas définis, exécutez les cellules là où ils sont déclarés ou instanciés en premier.

Vous devrez peut-être supprimer les ressources Azure et les fichiers créés si vous avez besoin de restaurer les notebooks.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Ce tutoriel fait partie d’une série où chaque article s’appuie sur le travail effectué dans les articles précédents. Ne nettoyez pas les ressources avant d’avoir terminé le dernier tutoriel.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons utilisé deux notebooks Jupyter exécutés dans Machine Learning Studio afin d’utiliser les données des appareils turbofan pour :

- Effectuer l’apprentissage d’un classifieur de durée de vie utile restante.
- Enregistrer le classifieur en tant que modèle.
- Créer une image conteneur.
- Déployer et tester l’image en tant que service web.

Passez à l’article suivant pour créer un appareil IoT Edge.

> [!div class="nextstepaction"]
> [Configurer un appareil IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)