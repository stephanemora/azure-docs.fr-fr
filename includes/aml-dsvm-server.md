---
title: Fichier Include
description: Fichier Include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 01/25/2019
ms.openlocfilehash: ac5656a8efe1dee932bfe753f8bdc06b787e20af
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/28/2019
ms.locfileid: "75529783"
---
1. [Créez un espace de travail Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ajoutez au répertoire cloné un fichier de configuration d’espace de travail à l’aide de l’une des méthodes suivantes :

    * Dans le [portail Azure](https://ms.portal.azure.com), sélectionnez **Télécharger config.json** à partir de la section **Vue d’ensemble** de votre espace de travail. 

    ![Télécharger config.json](./media/aml-dsvm-server/download-config.png)

    * Créez un espace de travail à l’aide du code du bloc-notes [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) dans votre répertoire cloné.

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

    ```shell
    jupyter notebook
    ```