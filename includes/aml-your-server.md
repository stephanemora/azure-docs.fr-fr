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
ms.openlocfilehash: ce8b117a3cbe0e3a5c4265729ccf5c0264241013
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391796"
---
1. Suivez les instructions fournies dans [Créer un espace de travail pour le service Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) pour :
    * Créer un environnement Miniconda
    * Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python
    * Créer un espace de travail
    * Écrivez un fichier de configuration d’espace de travail (**aml_config/config.json**).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

    ```shell
    jupyter notebook
    ```