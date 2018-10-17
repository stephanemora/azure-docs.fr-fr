---
title: 'Didacticiel : Reconnaissance des émotions sur un visage dans une image - API Émotion, Python'
titlesuffix: Azure Cognitive Services
description: Utilisez un bloc-notes Jupyter pour apprendre à utiliser l’API Émotion avec Python. Visualisez vos résultats à l’aide de bibliothèques populaires.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: tutorial
ms.date: 05/23/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 31e346cd9a3f43f8181ebee4474ae6c9ee2cc6fc
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/03/2018
ms.locfileid: "48237855"
---
# <a name="tutorial-use-the-emotion-api-with-a-jupyter-notebook--python"></a>Didacticiel : Utiliser l’API Émotion avec le bloc-notes Jupyter et Python.

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
