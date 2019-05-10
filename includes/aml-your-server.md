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
ms.openlocfilehash: 8d21e41ad487ad17598f2320fab5eebae02309e8
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021745"
---
1. Suivez les instructions fournies dans [Créer un espace de travail pour le service Azure Machine Learning](../articles/machine-learning/service/setup-create-workspace.md#portal) pour :
    * Créer un environnement Miniconda
    * Installer le kit de développement logiciel (SDK) Azure Machine Learning pour Python
    * Créer un espace de travail
    * Écrivez un fichier de configuration d’espace de travail (**aml_config/config.json**).
    
1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Ajoutez un fichier de configuration d’espace de travail en utilisant l’une des méthodes suivantes :
    * Copiez le fichier **aml_config/config.json** que vous avez créé comme prérequis à l'étape 1 dans le répertoire cloné.

    * Dans le [portail Azure](https://ms.portal.azure.com), sélectionnez **Télécharger config.json** à partir de la section **Vue d’ensemble** de votre espace de travail. 

    ![Télécharger config.json](./media/aml-dsvm-server/download-config.png)

    * Créez un espace de travail à l’aide du code de [configuration.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/configuration.ipynb).

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.
    
    ```shell
    jupyter notebook
    ```