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
ms.date: 07/27/2018
ms.openlocfilehash: 12f0f17a7c25696d2c8ff5b427f4b103617e5678
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "47010730"
---
Dans une fenêtre d’invite ou d’interpréteur de commandes, créez et activez l’environnement du gestionnaire de package conda avec numpy et cython. Cet exemple utilise Python 3.6.

  + Sous Windows :
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       conda activate myenv
       ```

  + Sur Linux ou MacOS :
       ```sh 
       conda create -n myenv Python=3.6 cython numpy
       source activate myenv
       ```

Installez le kit SDK.
   ```sh 
   pip install azureml-sdk
   ```
