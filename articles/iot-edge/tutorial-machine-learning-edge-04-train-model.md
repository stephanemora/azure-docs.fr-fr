---
title: 'Tutoriel : Entraîner et déployer un modèle - Machine Learning sur Azure IoT Edge'
description: Dans ce tutoriel, vous allez entraîner un modèle Machine Learning à l’aide d’Azure Machine Learning. Vous allez ensuite créer un package de ce modèle sous la forme d’une image conteneur pouvant être déployée en tant que module Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 2/10/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 5f576d28d30907f3834600d0a6a5c152025cf912
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77133532"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Tutoriel : Entraîner et déployer un modèle Azure Machine Learning

> [!NOTE]
> Cet article fait partie d’une série décrivant l’utilisation d’Azure Machine Learning sur IoT Edge. Si vous êtes arrivé directement à cet article, nous vous encourageons à commencer par le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série afin d’obtenir de meilleurs résultats.

Dans cet article, vous allez effectuer les tâches suivantes :

* Utiliser Azure Notebooks pour entraîner un modèle Machine Learning
* Empaqueter le modèle entraîné sous forme d’image conteneur
* Déployer l’image conteneur en tant que module Azure IoT Edge

Azure Notebooks bénéficie d’un espace de travail Azure Machine Learning, module essentiel pour expérimenter, entraîner et déployer des modèles Machine Learning

Les étapes de cet article peuvent être généralement effectuées par les scientifiques de données.

## <a name="set-up-azure-notebooks"></a>Configurer Azure Notebooks

Nous utilisons Azure Notebooks pour héberger les deux Notebooks Jupyter et les fichiers de prise en charge. Ici, nous créons et configurons un projet Azure Notebooks. Si vous n’avez pas utilisé Jupyter et/ou Azure Notebooks, voici quelques documents de présentation :

* **Démarrage rapide :** [Créer et partager un notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutoriel :** [Créer et exécuter un notebook Jupyter avec Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

L’utilisation d’Azure Notebooks garantit un environnement cohérent pour l’exercice.

> [!NOTE]
> Une fois installé, le service Azure Notebooks est accessible depuis n’importe quelle machine. Pendant la configuration, vous devez utiliser la machine virtuelle de développement, qui contient tous les fichiers dont vous aurez besoin.

### <a name="create-an-azure-notebooks-account"></a>Créer un compte Azure Notebooks

Pour utiliser Azure Notebooks, vous devez créer un compte. Les comptes Azure Notebook sont indépendants des abonnements Azure.

1. Accédez à [Azure Notebooks](https://notebooks.azure.com).

1. Cliquez sur **Se connecter** en haut à droite de la page.

1. Connectez-vous avec votre compte professionnel ou scolaire (Azure Active Directory) ou votre compte personnel (compte Microsoft).

1. Si vous n’avez jamais utilisé Azure Notebooks auparavant, vous serez invité à autoriser l’accès à l’application Azure Notebooks.

1. Créez un ID utilisateur pour Azure Notebooks.

### <a name="upload-jupyter-notebook-files"></a>Charger des fichiers de notebooks Jupyter

Nous allons charger des exemples de fichiers notebooks dans un nouveau projet Azure Notebooks.

1. Dans la page utilisateur de votre nouveau compte, sélectionnez **Mes projets** dans la barre de menus supérieure.

1. Dans la boîte de dialogue **Créer un projet**, fournissez un **nom de projet** qui formera automatiquement l’**ID de projet**.

1. Vous pouvez laisser les cases **Public** et **README** non cochées, car le projet n’a pas besoin d’être public ni d’avoir un fichier readme.

1. Sélectionnez **Create** (Créer).

1. Sélectionnez **Charger** (l’icône en forme de flèche vers le haut), puis **À partir de l’ordinateur**.

1. Sélectionnez **Choisir des fichiers**.

1. Accédez à **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Sélectionnez tous les fichiers de la liste, puis cliquez sur **Ouvrir**.

1. Sélectionnez **Charger** pour commencer le téléchargement, puis sélectionnez **Terminé** une fois le processus terminé.

### <a name="azure-notebook-files"></a>Fichiers de notebooks Azure

Passons en revue les fichiers que vous avez chargés dans votre projet Azure Notebooks. Les activités de cette partie du tutoriel sont réparties entre deux fichiers de notebooks, qui utilisent quelques fichiers de prise en charge.

* **01-turbomoteur\_regression.ipynb** : Ce notebook utilise l’espace de travail du service Machine Learning pour créer et exécuter une expérience Machine Learning. Le notebook implique plus ou moins les étapes suivantes :

  1. Téléchargement des données à partir du compte de stockage Azure qui a été généré par l’atelier d’appareils
  1. Exploration et préparation des données pour l’entraînement et pour le modèle de classifieur
  1. Évaluation du modèle à partir de l’expérience à l’aide d’un jeu de données de test (Test\_FD003.txt)
  1. Publication du meilleur modèle de classifieur dans l’espace de travail du service Machine Learning

* **02-turbofan\_deploy\_model.ipynb :** Ce notebook prend le modèle créé dans le notebook précédent et l’utilise pour créer une image de conteneur prête à être déployée sur un appareil Azure IoT Edge. Le notebook effectue les étapes suivantes :

  1. Création d’un script de scoring pour le modèle
  1. Création d’une image conteneur à l’aide du modèle de classifieur qui a été enregistré dans l’espace de travail du service Machine Learning
  1. Déploiement de l’image en tant que service web sur Azure Container Instance
  1. Utilisation du service web pour valider le modèle et le travail de l’image comme prévu L’image validée sera déployée sur notre périphérique IoT Edge dans la partie [Créer et déployer des modules IoT Edge personnalisés](tutorial-machine-learning-edge-06-custom-modules.md) de ce tutoriel.

* **Test\_FD003.txt :** Ce fichier contient les données que nous utiliserons comme jeu de test lors de la validation de notre classifieur formé. Pour rester simples, nous avons choisi d’utiliser les données de test fournies pour le concours original pour notre jeu de test.

* **Durée de vie restante\_FD003.txt :** Ce fichier contient la durée de vie restante pour le dernier cycle de chaque appareil dans le fichier Test\_FD003.txt. Pour une description détaillée des données, consultez les fichiers readme.txt et Damage Propagation Modeling.pdf disponibles ici : C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan.

* **Utils.py :** Contient un ensemble de fonctions utilitaires Python pour travailler avec des données. Le premier notebook contient une explication détaillée des fonctions.

* **README.md :** Fichier Lisez-moi qui décrit l’utilisation des notebooks.  

## <a name="run-azure-notebooks"></a>Exécuter Azure Notebooks

Maintenant que le projet est créé, vous pouvez exécuter les notebooks. 

1. Dans la page du projet, sélectionnez **01-turbofan\_regression.ipynb**.

    ![Sélectionner le premier notebook à exécuter](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Si vous y êtes invité, choisissez le noyau Python 3.6 dans la boîte de dialogue et sélectionnez **Set Kernel** (Définir le noyau).

1. Si le notebook est répertorié en tant que **Non approuvé**, cliquez sur le widget **Non approuvé** dans l’angle supérieur droit du notebook. Dans la boîte de dialogue qui s’affiche, sélectionnez **Approuver**.

1. Dans le notebook, faites défiler la liste jusqu’à la cellule qui suit les instructions **Set global properties** (Définir des propriétés globales) et qui commence par le code `AZURE_SUBSCRIPTION_ID =`. Ensuite, renseignez les valeurs de votre abonnement, de vos paramètres et de vos ressources Azure.

    ![Définir les propriétés globales dans le notebook](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Exécutez cette cellule en sélectionnant **Exécuter** dans la barre d’outils.

    Quand une cellule est en cours d’exécution, un astérisque entre crochets ([\*]) s’affiche. Lorsque l’opération est terminée dans la cellule, l’astérisque est remplacé par un numéro et une sortie peut s’afficher. Les cellules d’un notebook sont générées de façon séquentielle, et vous ne pouvez exécuter qu’une seule cellule à la fois.

    Suivez les instructions du notebook. Vous pouvez également utiliser les options d’exécution du menu **Cellule** : `Ctrl` + `Enter` pour exécuter une cellule et `Shift` + `Enter` pour exécuter une cellule et passer à la cellule suivante.

    > [!TIP]
    > Pour les opérations de cellule cohérentes, évitez d’exécuter le même notebook à partir de plusieurs onglets de votre navigateur.

1. Faites défiler jusqu’à la cellule qui suit immédiatement le texte de présentation **Créer un espace de travail**, puis exécutez cette cellule. Dans la sortie de la cellule, recherchez le lien qui vous demande de vous connecter pour vous authentifier. 

    ![Invite de connexion pour l’authentification des appareils](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Ouvrez le lien et entrez le code spécifié. Cette procédure de connexion authentifie le notebook Jupyter pour accéder aux ressources Azure à l’aide de l’interface de ligne de commande multiplateforme Microsoft Azure.  

    ![Authentifier l’application à la confirmation de l’appareil](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. À présent, vous pouvez exécuter le reste des cellules. La meilleure solution est d’exécuter toutes les cellules afin que le code qu’elles contiennent s’exécute de manière séquentielle. Sélectionnez **Tout exécuter** dans le menu **Cellule**. Revenez en haut du notebook pour examiner la façon dont les opérations de cellule se sont terminées.

    Dans la section **Explorer les données**, vous pouvez passer en revue les cellules de la sous-section **Sensor readings and RUL** (Mesures des capteurs et durée de vie utile résiduelle) qui affichent des nuages de points représentant les mesures des capteurs.

    ![Nuages de points représentant les mesures des capteurs](media/tutorial-machine-learning-edge-04-train-model/sensor-readings.png)

1. Enregistrez le notebook et revenez à la page de votre projet en cliquant sur le nom de votre projet dans l’angle supérieur droit du notebook ou en retournant dans votre navigateur.

1. Ouvrez **02-turbofan\_deploy\_model.ipynb** et répétez les étapes de cette procédure pour exécuter le deuxième notebook.

1. Enregistrez le notebook et revenez à la page de votre projet en cliquant sur le nom de votre projet dans l’angle supérieur droit du notebook ou en retournant dans votre navigateur.

### <a name="verify-success"></a>Vérifier la réussite de l’exécution

Pour vérifier si les notebooks se sont correctement exécutés, regardez si certains éléments ont été créés.

1. Dans la page de votre projet Azure Notebooks, sélectionnez **Afficher les éléments masqués** pour afficher les noms d’éléments qui commencent par un point.

1. Vérifiez que les fichiers suivants ont été créés :

    | Fichier | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Fichier de configuration utilisé pour créer l’espace de travail Machine Learning |
    | ./aml_config/model_config.json | Fichier de configuration dont nous aurons besoin pour déployer le modèle dans l’espace de travail Machine Learning **turbofanDemo** Azure |
    | myenv.yml| Fournit des informations sur les dépendances pour le modèle Machine Learning déployé.|

1. Dans le portail Azure, vérifiez que l’espace de travail Machine Learning **turboFanDemo** se trouve dans votre groupe de ressources.

### <a name="debugging"></a>Débogage

Vous pouvez insérer des instructions Python dans le notebook pour le débogage, principalement la commande `print()`. Si vous voyez des variables ou des objets qui ne sont pas définis, exécutez les cellules là où ils sont déclarés ou instanciés en premier.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons utilisé deux notebooks Jupyter fonctionnant dans Azure Notebooks pour utiliser les données des turbomoteurs pour former un classifieur de durée de vie restante (RUL), pour enregistrer le classifieur en tant que modèle, pour créer une image de conteneur et pour déployer et tester l’image en tant que service Web.

Passez à l’article suivant pour créer un appareil IoT Edge.

> [!div class="nextstepaction"]
> [Configurer un appareil IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
