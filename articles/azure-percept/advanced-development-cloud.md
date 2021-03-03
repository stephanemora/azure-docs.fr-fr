---
title: Bien démarrer avec le développement avancé d’Azure Percept dans le cloud
description: Bien démarrer avec le développement avancé dans le cloud avec Jupyter Notebook et Azure Machine Learning
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 408040ea68273a29ed9be87b60bd0cc6da736a05
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101658763"
---
# <a name="getting-started-with-advanced-development-in-the-cloud-via-jupyter-notebooks-and-azure-machine-learning"></a>Bien démarrer avec le développement avancé dans le cloud avec Jupyter Notebook et Azure Machine Learning

Cet article vous guide tout au long du processus de configuration d’un espace de travail Azure Machine Learning, du chargement d’un exemple Jupyter Notebook préconfiguré dans l’espace de travail, de la création d’une instance de calcul et de l’exécution des cellules du notebook dans l’espace de travail.

Le [notebook](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) effectue un apprentissage de transfert à l’aide d’un modèle TensorFlow préentraîné (MobileNetSSDV2Lite) sur Azure ML en Python avec un jeu de données personnalisé pour détecter les bols.

Le notebook montre comment démarrer à partir du [jeu de données COCO](https://cocodataset.org/#home), comment le filtrer jusqu’à la classe d’intérêt (bols) et comment le convertir au format approprié. Vous pouvez également utiliser l’outil d’étiquetage open source [VoTT 2](https://github.com/microsoft/VoTT) pour créer et étiqueter des cadres englobants au format PASCAL VOC.

Après avoir réentraîné le modèle sur le jeu de données personnalisé, vous pouvez le déployer sur votre DK Azure Percept à l’aide de la méthode de mise à jour des jumeaux de module.

Vous pouvez ensuite vérifier l’inférence de modèle en examinant le flux RTSP en direct à partir du SoM Azure Percept Vision. Le réentraînement et le déploiement des modèles sont effectués dans le notebook dans le cloud.

## <a name="prerequisites"></a>Prérequis

- [Abonnement Azure Machine Learning](https://azure.microsoft.com/free/services/machine-learning/)
- [DK Azure Percept avec le SoM Azure Percept Vision connecté](./overview-azure-percept-dk.md)
- [Expérience d’intégration du DK Azure Percept](./quickstart-percept-dk-set-up.md) terminée

## <a name="download-azure-percept-github-repository"></a>Télécharger le dépôt GitHub Azure Percept

1. Accédez au [dépôt GitHub Azure Percept](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview).

1. Clonez le dépôt ou téléchargez le fichier ZIP. Près du haut de l’écran, cliquez sur **Code** -> **Télécharger le ZIP**.

    :::image type="content" source="./media/advanced-development-cloud/download-zip.png" alt-text="Écran de téléchargement de GitHub.":::

## <a name="set-up-azure-machine-learning-portal-and-notebook"></a>Configurer le portail et le notebook Azure Machine Learning

1. Accédez au [portail Azure Machine Learning](https://ml.azure.com).

1. Sélectionnez votre annuaire, votre abonnement Azure et votre espace de travail Machine Learning dans les menus déroulants, puis cliquez sur **Démarrer**.

    :::image type="content" source="./media/advanced-development-cloud/machine-learning-portal-get-started.png" alt-text="Écran de démarrage d’Azure ML.":::

    Si vous n’avez pas encore d’espace de travail Azure Machine Learning, cliquez sur **Créer un espace de travail**. Dans le nouvel onglet du navigateur, effectuez les étapes suivantes :

    1. Sélectionnez votre abonnement Azure.
    1. Sélectionnez votre groupe de ressources.
    1. attribuez un nom à votre espace de travail.
    1. Sélectionnez votre région.
    1. Sélectionnez l’édition de votre espace de travail.
    1. Cliquez sur **Vérifier et créer**.
    1. Dans la page suivante, passez en revue vos sélections, puis cliquez sur **Créer**.

        :::image type="content" source="./media/advanced-development-cloud/workspace-review-and-create.png" alt-text="Écran de création de l’espace de travail dans Azure ML.":::

    Patientez quelques minutes pendant la création de l’espace de travail. Une fois l’espace de travail créé, vous pouvez voir des coches vertes en regard de vos ressources et **Votre déploiement est terminé** en haut de la page de présentation de Machine Learning Services.

    :::image type="content" source="./media/advanced-development-cloud/workspace-creation-complete-inline.png" alt-text="Confirmation de la création de l’espace de travail." lightbox= "./media/advanced-development-cloud/workspace-creation-complete.png":::

    Une fois votre espace de travail créé, revenez à l’onglet du portail Machine Learning, puis cliquez sur **Démarrer**.

1. Dans la page d’accueil de l’espace de travail Machine Learning, cliquez sur **Notebooks** dans le volet gauche.

    :::image type="content" source="./media/advanced-development-cloud/notebook.png" alt-text="Page d’accueil d’Azure ML.":::

1. Sous l’onglet **Mes fichiers**, cliquez sur la flèche verticale pour charger votre fichier .ipynb.

    :::image type="content" source="./media/advanced-development-cloud/upload-files.png" alt-text="Page d’accueil avec l’icône Charger un fichier mise en évidence.":::

1. Dans votre copie locale du dépôt Azure Percept, accédez au [fichier Transferlearningusing_SSDLiteV2 Model.ipynb](https://github.com/microsoft/Project-Santa-Cruz-Private-Preview/blob/main/Sample-Scripts-and-Notebooks/Official/Machine%20Learning%20Notebooks/Transferlearningusing_SSDLiteV2%20Model.ipynb) et sélectionnez-le. Cliquez sur **Ouvrir**. Dans la fenêtre **Charger des fichiers** , cochez la case en regard de **J’approuve le contenu de ce fichier**, puis cliquez sur **Charger**.

    :::image type="content" source="./media/advanced-development-cloud/select-file.png" alt-text="Écran de sélection de fichier.":::

1. Dans la barre de menus en haut à droite, vérifiez l’état de votre **Calcul**. Si aucun calcul n’est trouvé, cliquez sur l’icône **+** pour en créer un.

    :::image type="content" source="./media/advanced-development-cloud/no-computes-found-inline.png" alt-text="État de calcul avec l’icône + mise en évidence." lightbox= "./media/advanced-development-cloud/no-computes-found.png":::

1. Dans la fenêtre **Nouvelle instance de calcul**, entrez un **Nom de calcul**, choisissez un **Type de machine virtuelle**, puis sélectionnez une **Taille de machine virtuelle**. Cliquez sur **Créer**.

    :::image type="content" source="./media/advanced-development-cloud/new-compute-instance.png" alt-text="Écran de création d’une instance de calcul.":::

    Une fois que vous avez cliqué sur **Créer**, l’état de votre **Calcul** affiche un cercle bleu, puis **\<your compute name> - En cours de création**.

    :::image type="content" source="./media/advanced-development-cloud/compute-creating.png" alt-text="État du calcul quand la création du calcul est toujours en cours.":::

    L’état de votre **Calcul** affiche un cercle vert, puis **\<your compute name> - En cours d’exécution** une fois la création du calcul terminée.

    :::image type="content" source="./media/advanced-development-cloud/compute-running.png" alt-text="L’état du calcul indiquant que la création du calcul est terminée.":::

1. Une fois le calcul en cours d’exécution, sélectionnez le noyau **Python 3.6 - Azure ML** dans le menu déroulant en regard de l’icône **+** .

## <a name="working-with-the-notebook"></a>Utilisation du notebook

Vous êtes maintenant prêt à exécuter le notebook pour entraîner votre détecteur de bol personnalisé et le déployer sur votre devkit. Veillez à exécuter chaque cellule du notebook individuellement, certaines d’entre elles nécessitant des paramètres d’entrée avant l’exécution du script. Les cellules qui nécessitent des paramètres d’entrée peuvent être modifiées directement dans le notebook. Pour exécuter une cellule, cliquez sur l’icône de lecture à gauche de la cellule :

:::image type="content" source="./media/advanced-development-cloud/run-cell-inline.png" alt-text="Icône de cellule mise en évidence dans un notebook." lightbox= "./media/advanced-development-cloud/run-cell.png":::

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir d’autres exemples de notebooks Azure Machine Learning service, consultez ce [dépôt](https://github.com/Azure/MachineLearningNotebooks/tree/2aa7c53b0ce84e67565d77e484987714fdaed36e/how-to-use-azureml).
