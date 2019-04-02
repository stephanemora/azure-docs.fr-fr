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
ms.openlocfilehash: 8d8b314965253dc00b39d0b068b1d6fb3e4aa471
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2019
ms.locfileid: "58395722"
---
1. Suivez les instructions fournies dans [Créer un espace de travail Machine Learning service](../articles/machine-learning/service/setup-create-workspace.md#portal) pour créer un environnement Miniconda, créer un espace de travail et écrire un fichier de configuration d’espace de travail (**aml_config/config.json**).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ajoutez un fichier de configuration d’espace de travail à l’aide de l’une des méthodes suivantes :
    * Copiez le fichier **aml_config/config.json** (que vous avez créé comme prérequis dans le guide de démarrage rapide) dans le répertoire cloné.
    * Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).
1. Démarrez le serveur de blocs-notes de votre répertoire cloné.
    
    ```shell
    jupyter notebook
    ```