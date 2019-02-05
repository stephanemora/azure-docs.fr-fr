---
title: 'Tutoriel : Reconnaître les émotions sur un visage dans une image - API Émotion, Python'
titlesuffix: Azure Cognitive Services
description: Utilisez un bloc-notes Jupyter pour apprendre à utiliser l’API Émotion avec Python. Visualisez vos résultats à l’aide de bibliothèques populaires.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: ea96495fb9fa453f7c7f9d6a870291329d2793b2
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55220533"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Didacticiel : Utiliser l’API Émotion avec Jupyter Notebook et Python.

> [!IMPORTANT]
> L’API Émotion sera déconseillée à partir du 15 février 2019. La fonction de reconnaissance des émotions est maintenant accessible de façon générale dans le cadre de l’[API Visage](https://docs.microsoft.com/azure/cognitive-services/face/). 

Pour faciliter la prise en main de l’API Émotion, le bloc-notes Jupyter associé ci-dessous vous montre comment utiliser l’API dans Python et comment visualiser vos résultats avec des bibliothèques populaires.

[Lien vers le bloc-notes dans GitHub](https://github.com/Microsoft/Cognitive-Emotion-Python/blob/master/Jupyter%20Notebook/Emotion%20Analysis%20Example.ipynb)

### <a name="using-the-jupyter-notebook"></a>Utilisation de Jupyter Notebook

Pour utiliser le bloc-notes de manière interactive, vous devez le cloner et l’exécuter dans Jupyter. Pour savoir comment prendre en main les blocs-notes Jupyter interactifs, consultez les instructions à l’adresse http://jupyter.readthedocs.org/en/latest/install.html.

Pour utiliser ce bloc-notes, vous avez besoin d’une clé d’abonnement pour l’API Émotion. Rendez-vous sur la [page d’inscription](https://azure.microsoft.com/try/cognitive-services/) pour vous inscrire. Sur la page de connexion, utilisez votre compte Microsoft pour vous authentifier et vous abonner afin d’obtenir des clés gratuites. À la suite du processus d’inscription, collez votre clé dans la section des variables du bloc-notes illustrée ci-dessous. La clé primaire ou secondaire fonctionne.

```
Python Example

#Variables

_url = 'https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize'
_key = None #Here you have to paste your primary key
_maxNumRetries = 10

```
