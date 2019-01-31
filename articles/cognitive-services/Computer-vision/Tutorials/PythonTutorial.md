---
title: 'Didacticiel : Effectuer des opérations sur les images - Python'
titlesuffix: Azure Cognitive Services
description: Découvrez comment utiliser l’API Vision par ordinateur avec Python à l’aide de bloc-notes Jupyter. Visualisez vos résultats à l’aide de bibliothèques populaires.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 11/06/2018
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 3d3cc6138e6823fad11db5b6bd8edbb2d7398d00
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225685"
---
# <a name="tutorial-computer-vision-api-python"></a>Tutoriel : API Vision par ordinateur avec Python

Ce didacticiel vous montre comment utiliser l’API Vision par ordinateur dans Python et comment visualiser vos résultats à l’aide de bibliothèques populaires. Vous allez utiliser Jupyter pour exécuter le didacticiel. Pour savoir comment prendre en main les blocs-notes Jupyter interactifs, consultez la [documentation Jupyter](https://jupyter.readthedocs.io/en/latest/index.html).

## <a name="prerequisites"></a>Prérequis

- [Python 2.7+ ou 3.5+](https://www.python.org/downloads/)
- Outil [pip](https://pip.pypa.io/en/stable/installing/)
- [Bloc-notes Jupyter](https://jupyter.org/install) installé

## <a name="open-the-tutorial-notebook-in-jupyter"></a>Ouvrir le bloc-notes du didacticiel dans Jupyter 

1. Accédez référentiel GitHub [Cognitive Python Vision](https://github.com/Microsoft/Cognitive-Vision-Python). 
2. Cliquez sur le bouton vert pour cloner ou télécharger le référentiel. 
3. Ouvrez une invite de commandes et accédez au dossier **Cognitive-Vision-Python\Jupyter Notebook**.
1. Assurez-vous que vous disposez de toutes les bibliothèques nécessaires en exécutant la commande `pip install requests opencv-python numpy matplotlib` à partir de l’invite de commandes.
1. Démarrez Jupyter en exécutant la commande `jupyter notebook` à partir de l’invite de commandes.
1. Dans la fenêtre Jupyter, cliquez sur _Computer Vision API Example.ipynb_ pour ouvrir le bloc-notes du didacticiel.

## <a name="run-the-tutorial"></a>Exécuter le didacticiel

Pour utiliser ce bloc-notes, vous avez besoin d’une clé d’abonnement pour l’API Vision par ordinateur. Rendez-vous sur la [page d’inscription](https://azure.microsoft.com/try/cognitive-services/) pour vous inscrire. Sur la page de **connexion**, utilisez votre compte Microsoft pour vous authentifier et vous abonner afin d’obtenir des clés gratuites. À la suite du processus d’inscription, collez votre clé dans la section `Variables` du bloc-notes (reproduite ci-dessous). La clé primaire ou secondaire fonctionne. Veillez à placer la clé entre guillemets pour créer une chaîne.

Vous devez également vous assurer que le champ `_region` correspond à la région de votre abonnement.

```python
# Variables
_region = 'westcentralus' #Here you enter the region of your subscription
_url = 'https://{}.api.cognitive.microsoft.com/vision/v2.0/analyze'.format(_region)
_key = None #Here you have to paste your primary key
_maxNumRetries = 10
```

Lorsque vous exécuterez le didacticiel, vous pourrez ajouter des images à analyser, à la fois à partir d’une URL et du stockage local. Le script affichera les images et les informations d’analyse dans le bloc-notes.
