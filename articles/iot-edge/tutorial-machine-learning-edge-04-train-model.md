---
title: 'Didacticiel : Entraîner et déployer un modèle - Machine Learning sur Azure IoT Edge'
description: Dans ce tutoriel, vous allez entraîner un modèle Machine Learning à l’aide d’Azure Machine Learning. Vous allez ensuite créer un package de ce modèle sous la forme d’une image conteneur pouvant être déployée en tant que module Azure IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9c0613d319c0c82fa61d75ed016d68d38dfcea8d
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74701829"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Didacticiel : Entraîner et déployer un modèle Azure Machine Learning

> [!NOTE]
> Cet article fait partie d’une série d’un tutoriel décrivant l’utilisation d’Azure Machine Learning sur IoT Edge. Si vous êtes arrivé directement à cet article, nous vous encourageons à commencer par le [premier article](tutorial-machine-learning-edge-01-intro.md) de la série afin d’obtenir de meilleurs résultats.

Dans cet article, nous utilisons d’abord Azure Notebooks pour former un modèle d’apprentissage automatique à l’aide d’Azure Machine Learning, puis nous empaquetons ce modèle sous forme d’image de conteneur qui peut être déployée en tant que module Azure IoT Edge. Azure Notebooks bénéficient d’un espace de travail Azure Machine Learning, qui est un bloc fondamental utilisé pour expérimenter, former et déployer des modèles d’apprentissage automatique.

Les activités de cette partie du tutoriel sont réparties sur deux notebooks.

* **01-turbomoteur\_regression.ipynb** : Ce notebook passe en revue les étapes de formation et de publication d’un modèle à l’aide d’Azure Machine Learning. Globalement, les étapes impliquées sont les suivantes :

  1. Télécharger, préparer et explorer les données d’apprentissage
  2. Utiliser l’espace de travail du service pour créer et exécuter une expérience d’apprentissage automatique
  3. Évaluer les résultats du modèle issus de l’expérience
  4. Publier le meilleur modèle dans l’espace de travail du service

* **02-turbofan\_deploy\_model.ipynb :** Ce notebook prend le modèle créé dans le notebook précédent et l’utilise pour créer une image de conteneur prête à être déployée sur un appareil Azure IoT Edge.

  1. Créer un script de scoring pour le modèle
  2. Créer et publier l’image
  3. Déployer l’image en tant que service web sur Azure Container Instance
  4. Utiliser le service web pour valider le modèle et le travail de l’image comme prévu

Les étapes de cet article peuvent être généralement effectuées par les scientifiques de données.

## <a name="set-up-azure-notebooks"></a>Configurer Azure Notebooks

Nous utilisons Azure Notebooks pour héberger les deux Notebooks Jupyter et les fichiers de prise en charge. Ici, nous créons et configurons un projet Azure Notebooks. Si vous n’avez pas utilisé Jupyter et/ou Azure Notebooks, voici quelques documents de présentation :

* **Démarrage rapide :** [Créer et partager un notebook](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **Tutoriel :** [Créer et exécuter un notebook Jupyter avec Python](../notebooks/tutorial-create-run-jupyter-notebook.md)

Comme pour la machine virtuelle de développement, l’utilisation d’Azure Notebooks assure un environnement cohérent pour l’exercice.

> [!NOTE]
> Une fois installé, le service Azure Notebooks est accessible depuis n’importe quelle machine. Pendant l’installation, vous devez utiliser la machine virtuelle de développement, qui contient tous les fichiers dont vous aurez besoin.

### <a name="create-an-azure-notebooks-account"></a>Créer un compte Azure Notebooks

Les comptes Azure Notebook sont indépendants des abonnements Azure. Pour utiliser Azure Notebooks, vous devez créer un compte.

1. Accédez à [Azure Notebooks](https://notebooks.azure.com).

2. Cliquez sur **Se connecter** dans le coin supérieur droit de la page.

3. Connectez-vous avec votre compte professionnel ou scolaire (Azure Active Directory) ou votre compte personnel (compte Microsoft).

4. Si vous n’avez jamais utilisé Azure Notebooks auparavant, vous serez invité à autoriser l’accès à l’application Azure Notebooks.

5. Créez un ID utilisateur pour Azure Notebooks.

### <a name="upload-jupyter-notebooks-files"></a>Charger des fichiers de notebooks Jupyter

Dans cette étape, nous créons un nouveau projet Azure Notebooks et y chargeons des fichiers. Plus précisément, les fichiers que nous chargeons sont :

* **01-turbomoteur\_regression.ipynb** : Fichier de notebook Jupyter qui vous guide à travers le processus de téléchargement des données générées par l’atelier de l’appareil à partir du compte de stockage Azure ; exploration et préparation des données pour former le classifieur ; apprentissage du modèle ; test des données à l’aide du jeu de données de test trouvé dans le fichier Test\_FD003.txt ; et enfin, enregistrement du classifieur de modèle dans l’espace de travail du service Machine Learning.

* **02-turbofan\_deploy\_model.ipynb :** Notebook Jupyter qui vous guide tout au long du processus d’utilisation du modèle de classifieur enregistré dans l’espace de travail du service Machine Learning pour produire une image de conteneur. Une fois l’image créée, le notebook vous guide tout au long du processus de déploiement de l’image en tant que service web afin que vous puissiez valider le fonctionnement attendu. L’image validée sera déployée sur notre périphérique IoT Edge dans la partie [Créer et déployer des modules IoT Edge personnalisés](tutorial-machine-learning-edge-06-custom-modules.md) de ce tutoriel.

* **Test\_FD003.txt :** Ce fichier contient les données que nous utiliserons comme jeu de test lors de la validation de notre classifieur formé. Nous avons choisi d’utiliser les données de test fournies pour le concours original comme jeu de test par souci de simplicité de l’exemple de test.

* **Durée de vie restante\_FD003.txt :** Ce fichier contient la durée de vie restante pour le dernier cycle de chaque appareil dans le fichier Test\_FD003.txt. Consultez les fichiers **readme.txt** et **Damage Propagation Modeling.pdf** dans la source C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan pour une description détaillée des données.

* **Utils.py :** Contient un ensemble de fonctions utilitaires Python pour travailler avec des données. Le premier notebook contient une explication détaillée des fonctions.

* **README.md :** Fichier Lisez-moi qui décrit l’utilisation des notebooks.

Créez un nouveau projet et téléchargez les fichiers sur votre notebook.

1. Sélectionnez **Mes projets** dans la barre de menus du haut.

1. Sélectionnez **+ Nouveau projet**. Donnez un nom et un ID. Il n’est pas nécessaire que le projet soit public ou qu’il ait un fichier readme.

1. Sélectionnez **Charger**, puis **À partir de l’ordinateur**.

1. Sélectionnez **Choisir des fichiers**.

1. Accédez à **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Sélectionnez tous les fichiers, puis cliquez sur **Ouvrir**.

1. Sélectionnez **Charger** pour commencer le téléchargement, puis sélectionnez **Terminé** une fois le processus terminé.

## <a name="run-azure-notebooks"></a>Exécuter Azure Notebooks

Maintenant que le projet est créé, exécutez le notebook **01-turbofan\_regression.ipynb**.

1. Dans la page du projet du turbomoteur, sélectionnez **01-turbofan\_regression.ipynb**.

    ![Sélectionner le premier notebook à exécuter](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

2. Si vous y êtes invité, choisissez le noyau Python 3.6 dans la boîte de dialogue et sélectionnez **Set Kernel** (Définir le noyau).

3. Si le notebook est répertorié en tant que **Non approuvé**, cliquez sur le widget **Non approuvé** dans l’angle supérieur droit du notebook. Dans la boîte de dialogue qui s’affiche, sélectionnez **Approuver**.

4. Suivez les instructions du notebook.

    * `Ctrl + Enter` exécute une cellule.
    * `Shift + Enter` exécute une cellule et accède à la cellule suivante.
    * Quand une cellule est en cours d’exécution, elle se caractérise par un astérisque entre crochets, tel que **[\*]** . Lorsque l’exécution est terminée, l’astérisque est remplacé par un numéro et les résultats pertinents peuvent apparaître ci-dessous. Comme les cellules s’appuient souvent sur le travail des cellules précédentes, une seule cellule s’exécute à la fois.

5. Lorsque vous avez terminé d’exécuter le notebook **01-turbofan\_regression.ipynb**, revenez à la page du projet.

6. Ouvrez **02-turbofan\_deploy\_model.ipynb**et répétez les étapes de cette section pour exécuter le deuxième notebook.

## <a name="next-steps"></a>Étapes suivantes

Dans cet article, nous avons utilisé deux notebooks Jupyter fonctionnant dans Azure Notebooks pour utiliser les données des turbomoteurs pour former un classifieur de durée de vie restante (RUL), pour enregistrer le classifieur en tant que modèle, pour créer une image de conteneur et pour déployer et tester l’image en tant que service Web.

Passez à l’article suivant pour créer un appareil IoT Edge.

> [!div class="nextstepaction"]
> [Configurer un appareil IoT Edge](tutorial-machine-learning-edge-05-configure-edge-device.md)
