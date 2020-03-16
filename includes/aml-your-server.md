---
title: Fichier include
description: Fichier include
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: 4bce52ba3320506b85949493407dded1d52415a3
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673654"
---
1. Installez le kit SDK Azure Machine Learning pour Python en suivant les instructions fournies dans l’article sur le [kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Créez un [espace de travail Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Écrire un [ fichier de configuration](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```CLI
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

    ```shell
    jupyter notebook
    ```
