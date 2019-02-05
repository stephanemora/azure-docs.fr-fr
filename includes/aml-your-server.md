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
ms.openlocfilehash: 18ba86ce7876ba8275eb4853e4fc9ea0f35fa186
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302185"
---
1. Suivez le [Guide de démarrage rapide Python d’Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) pour installer le SDK et créer un espace de travail.  Si vous le souhaitez, vous pouvez ignorer la section **Utiliser le bloc-notes**.
1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ajoutez un fichier de configuration d’espace de travail à l’aide de l’une des méthodes suivantes :
    * Copiez le fichier **aml_config\config.json** (que vous avez créé comme prérequis dans le guide de démarrage rapide) dans le répertoire cloné.
    * Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Démarrez le serveur de blocs-notes de votre répertoire cloné.
    
    ```shell
    jupyter notebook
    ```