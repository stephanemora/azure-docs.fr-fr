---
title: 'Tutoriel : Détection d’anomalie, Python'
titlesuffix: Azure Cognitive Services
description: Explorez un bloc-notes Python qui utilise l’API Détection d’anomalies. Envoyez des points de données d’origine à l’API et obtenez la valeur attendue et des points d’anomalies.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: d1e9afc32625cdbf97f576ee091d7dc03271e2fc
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164804"
---
# <a name="tutorial-anomaly-detection-with-python-application"></a>Tutoriel : Détection d’anomalie avec une application Python

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ce tutoriel montre comment utiliser l’API Détection d’anomalies dans Python et comment visualiser vos résultats à l’aide de bibliothèques connues. Vous allez utiliser Jupyter pour exécuter le tutoriel, ainsi que vos propres données avec votre clé d’abonnement. Pour savoir comment utiliser les blocs-notes Jupyter interactifs, consultez la [documentation Jupyter](http://jupyter.readthedocs.io/en/latest/index.html). 

## <a name="prerequisites"></a>Prérequis

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>S’abonner à la Détection d’anomalies et obtenir une clé d’abonnement 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="download-the-example-code"></a>Télécharger l’exemple de code

1. Accédez au [bloc-notes du didacticiel dans GitHub](https://github.com/MicrosoftAnomalyDetection/python-sample).
2. Cliquez sur le bouton vert pour cloner ou télécharger le tutoriel. 

## <a name="opening-the-tutorial-notebook-in-jupyter"></a>Ouverture du bloc-notes du tutoriel dans Jupyter

1. Ouvrez une invite de commandes et accédez au dossier python-sample.
2. Exécutez la commande Jupyter notebook à l’invite de commandes pour démarrer Jupyter.
3. Dans la fenêtre Jupyter, cliquez sur <em>Anomaly Detection API Example.ipynb</em> pour ouvrir le bloc-notes du tutoriel.   

## <a name="running-the-tutorial"></a>Exécution du tutoriel

Pour utiliser ce bloc-notes, vous avez besoin d’une clé d’abonnement pour l’API Détection d’anomalies. Accédez à la page d’inscription pour vous inscrire. Dans la page de connexion, utilisez votre compte Microsoft pour vous authentifier et vous abonner afin d’obtenir vos clés gratuites. Une fois le processus d’inscription terminé, collez votre clé dans la section des variables du blocs-notes (reproduite ci-dessous). La clé primaire ou secondaire fonctionne. Veillez à placer la clé entre guillemets pour créer une chaîne.

```Python

            # Variables
            endpoint = 'https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection'
            subscription_key = None #Here you have to paste your primary key

```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence d’API REST](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
