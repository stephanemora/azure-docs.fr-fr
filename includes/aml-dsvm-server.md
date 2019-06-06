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
ms.openlocfilehash: a882a874574395095e98079cd0f8aa4a4987c749
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391779"
---
1. [Créer un espace de travail pour Azure Machine Learning service](../articles/machine-learning/service/setup-create-workspace.md).

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