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
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "79485940"
---
1. Installez le kit SDK Azure Machine Learning pour Python en suivant les instructions fournies dans l’article sur le [kit SDK Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

1. Créez un [espace de travail Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Écrire un [ fichier de configuration](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clonez le [référentiel GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Démarrez le serveur de blocs-notes de votre répertoire cloné.

    ```bash
    jupyter notebook
    ```