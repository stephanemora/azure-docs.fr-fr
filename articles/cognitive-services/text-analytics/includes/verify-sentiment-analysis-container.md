---
title: Support pour les conteneurs
titleSuffix: Azure Cognitive Services
description: Découvrez comment vérifier l’instance de conteneur d’analyse des sentiments.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: f68d9c7098f2b1ca782e2522c632c2e267b35336
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67455120"
---
## <a name="verify-the-sentiment-analysis-container-instance"></a>Vérifier l’instance de conteneur Analyse des sentiments

1. Sélectionnez l’onglet **Vue d’ensemble** et copiez l’adresse IP.
1. Ouvrez un nouvel onglet de navigateur et utilisez l’adresse IP, par exemple, `http://<IP-address>:5000 (http://55.55.55.55:5000`). La page d’accueil du conteneur est présentée, vous informant que le conteneur est en cours d’exécution.

    ![Afficher la page d’accueil du conteneur pour vérifier qu’il est en cours d’exécution](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Sélectionnez le lien **Description de l’API de service** pour accéder à la page Swagger des conteneurs.

1. Choisissez une des API **POST** et sélectionnez **Essayer**.  Les paramètres sont affichés, y compris l’exemple d’entrée :

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Remplacez l’entrée par le code JSON suivant :

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
        }
      ]
    }
    ```

1. Définissez **showStats** sur true.

1. Sélectionnez **Exécuter** pour déterminer le sentiment du texte.

    Le modèle inclus dans le conteneur génère un score compris entre 0 et 1, où 0 est négatif et 1 est positif.

    La réponse JSON retournée inclut le sentiment pour l’entrée de texte mise à jour :

    ```json
    {
      "documents": [
      {
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Nous pouvons maintenant corréler le document `id` de code JSON des charges utiles de réponse avec le document de charge utile de la requête d’origine `id` et constater un score de plus de `.98` indiquant un sentiment très positif.