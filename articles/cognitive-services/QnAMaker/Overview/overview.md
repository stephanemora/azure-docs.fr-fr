---
title: Qu’est-ce que QnA Maker ?
titleSuffix: Azure Cognitive Services
description: QnA Maker est un service API informatique qui utilise l’intelligence personnalisée du Machine Learning pour répondre au mieux aux questions en langage naturel d’un utilisateur.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 04/05/2019
ms.author: tulasim
ms.openlocfilehash: 963769315302ba4e7d1600253b617c7cb0f02bc5
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794257"
---
# <a name="what-is-qna-maker"></a>Qu’est-ce que QnA Maker ?

QnA Maker est un service API informatique qui crée un calque conversation, question et réponse sur vos données. 

QnA Maker vous permet de créer une base de connaissances à partir de vos contenus semi-structurés tels que la Foire aux questions (FAQ), les URL, les manuels produits, les documents supports ainsi que les questions et réponses personnalisées. Le service QnA Maker répond aux questions en langage naturel de vos utilisateurs en les faisant correspondre avec la meilleure réponse possible à partir des entités QnA de votre base de connaissances.

Le [portail Web](https://qnamaker.ai) facile à utiliser vous permet de créer, de gérer, d’entraîner et de publier votre service sans aucune expérience en développement. Une fois le service publié sur un point de terminaison, une application client comme un chatbot peut gérer la conversation avec un utilisateur pour répondre aux questions. 

![Vue d'ensemble](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Processus clés de QnA Maker

QnA Maker fournit deux services essentiels pour vos données :

* **Extraction** : les données de question-réponse structurées sont extraites de [sources de données](../Concepts/data-sources-supported.md) structurées et semi-structurées comme des FAQ et des guides d’utilisation de produits. Cette extraction peut se faire lors de la [création](https://aka.ms/qnamaker-docs-createkb) de la base de connaissance ou ultérieurement, lors du processus de modification.

* **Mise en correspondance** : une fois votre base de connaissances [entraînée et testée](https://aka.ms/qnamaker-docs-trainkb), vous la [publiez](https://aka.ms/qnamaker-docs-publishkb). Cela active un point de terminaison vers votre base de connaissances QnA Maker, que vous pouvez ensuite utiliser dans votre robot ou application client. Ce point de terminaison accepte une question utilisateur et répond avec la meilleure réponse dans la base de connaissances, ainsi qu’avec un score de confiance pour la correspondance.

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>Architecture de QnA Maker

L’architecture de QnA Maker est constituée des deux composants suivants :

1. **Services de gestion QnA Maker** : expérience de gestion d’une base de connaissances QnA Maker, qui inclut la création initiale, la mise à jour, l’entraînement et la publication. Ces activités peuvent être effectuées via le [portail](https://qnamaker.ai) ou les [API de gestion](https://go.microsoft.com/fwlink/?linkid=2092179). 

2. **Données et runtime QnA Maker** : ce composant est déployé dans le cadre de votre abonnement Azure dans la région spécifiée. Le contenu de votre base de connaissances client est stocké dans [Recherche Azure](https://azure.microsoft.com/services/search/), et le point de terminaison est déployé en tant que [service d’application](https://azure.microsoft.com/services/app-service/). Vous pouvez également choisir de déployer une ressource [Application Insights](https://azure.microsoft.com/services/application-insights/) à des fins d’analyse.

![Architecture](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Présentation du service

- Une expérience complète **sans code** pour [créer un bot](../Quickstarts/create-publish-knowledge-base.md#create-a-bot) à partir d’une base de connaissances.
- **Aucune limitation de requêtes réseau pour les prédictions**. Vous payez pour héberger le service et non pour le nombre de transactions. Pour plus d’informations, consultez la [page sur la tarification](https://aka.ms/qnamaker-docs-pricing).
- **Mise à l’échelle au besoin**. Choisissez les références SKU appropriées pour les composants individuels, selon votre scénario. Découvrez comment [choisir la capacité](https://aka.ms/qnamaker-docs-capacity) pour votre service QnA Maker.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un service QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
