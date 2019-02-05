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
ms.openlocfilehash: 12d1576c3bfbf96c0445fcd2a6f0bc37d6a68f11
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302180"
---
1. Suivez le [Guide de démarrage rapide Python d’Azure Machine Learning](../articles/machine-learning/service/quickstart-create-workspace-with-python.md) pour créer un espace de travail.  Si vous le souhaitez, vous pouvez ignorer la section **Utiliser le bloc-notes**.
1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```
1. Ajoutez un fichier de configuration d’espace de travail à l’aide de l’une des méthodes suivantes :
    * Copiez le fichier **aml_config\config.json** (que vous avez créé comme prérequis dans le guide de démarrage rapide) dans le répertoire cloné.
    * Créez un espace de travail à l’aide du code du bloc-notes [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb) dans votre répertoire cloné.
1. Démarrez le serveur de blocs-notes de votre répertoire cloné.
    
    ```shell
    jupyter notebook
    ```