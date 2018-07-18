---
title: Didacticiel sur l’API Émotion avec Python | Microsoft Docs
description: Utilisez un bloc-notes Jupyter pour apprendre à utiliser l’API Émotion de Cognitive Services avec Python. Visualisez vos résultats à l’aide de bibliothèques populaires.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 70c8ca48c651601f3d7cbb3717c32bfe112176fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368520"
---
# <a name="emotion-api-using-python-tutorial"></a>Didacticiel sur l’API Émotion avec Python

> [!IMPORTANT]
> La préversion de l’API Vidéo a pris fin le 30 octobre 2017. Essayez la nouvelle [préversion de l’API Vidéo Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) pour extraire facilement des insights des vidéos et améliorer les expériences de découverte de contenu, telles que les résultats de recherche, en détectant le texte parlé, les visages, les personnes et les émotions. [Plus d’informations](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview)

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
