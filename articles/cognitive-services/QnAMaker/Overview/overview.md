---
title: Qu’est-ce que QnA Maker ?
titleSuffix: Azure Cognitive Services
description: QnA Maker est un service API informatique qui utilise l’intelligence personnalisée du Machine Learning pour répondre au mieux aux questions en langage naturel d’un utilisateur.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: overview
ms.date: 10/09/2018
ms.author: tulasim
ms.openlocfilehash: 742c18815445b038e85c33a96743790491976945
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901105"
---
# <a name="what-is-qna-maker"></a>Qu’est-ce que QnA Maker ?

QnA Maker est un service de base de connaissances permettant de répondre aux questions à l’aide d’une intelligence personnalisée de Machine Learning qui analyse le langage naturel de l’utilisateur de façon à déterminer la meilleure réponse à une question.

QnA Maker vous permet de faire fonctionner le service informatique avec vos contenus semi-structurés tels que les documents de Foire aux questions (FAQ), les URL, les manuels produits et les questions et réponses personnalisées. Le [portail Web](https://qnamaker.ai) facile à utiliser vous permet de créer, de gérer, d’entraîner et de publier votre service sans aucune expérience en développement. Une fois le service publié sur un point de terminaison, une application client comme un chatbot peut gérer la conversation avec un utilisateur pour répondre aux questions. 

![Vue d’ensemble](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>Processus clés de QnA Maker

Un QnA Maker fournit deux services essentiels pour vos données :

* **Extraction** : les données de question-réponse structurées sont extraites de [sources de données](../Concepts/data-sources-supported.md) semi-structurées comme des FAQ et des guides d’utilisation de produits. Cette extraction peut se faire lors de la [création](https://aka.ms/qnamaker-docs-createkb) de la base de connaissance ou ultérieurement, lors du processus de modification.

* **Mise en correspondance** : une fois que votre base de connaissances est passée par l’étape [d’apprentissage et de test](https://aka.ms/qnamaker-docs-trainkb), vous la [publiez](https://aka.ms/qnamaker-docs-publishkb). Cela active un point de terminaison vers votre base de connaissances QnA Maker, que vous pouvez ensuite utiliser dans votre robot ou application client. Ce point de terminaison accepte une question utilisateur et répond avec la meilleure réponse dans la base de connaissances, ainsi qu’avec un score de confiance pour la correspondance.

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

QnA Maker comprend les services API suivants :

1. **Services de gestion QnA Maker** : l’expérience de gestion d’une base de connaissances QnA Maker, qui inclut la création initiale, la mise à jour, l’apprentissage et la publication. Ces activités peuvent être effectuées via le [portail](https://qnamaker.ai) ou les [API de gestion](https://aka.ms/qnamaker-v4-apis). 

2. **Service de prédiction QnA Maker** : ce service est déployé dans le cadre de votre abonnement Azure dans la région spécifiée. LLe contenu de la base de connaissances client est stocké dans [Recherche Azure](https://azure.microsoft.com/services/search/), et le point de terminaison est déployé en tant que [service d’application](https://azure.microsoft.com/services/app-service/). Vous pouvez également choisir de déployer une ressource [Application Insights](https://azure.microsoft.com/services/application-insights/) à des fins d’analyse.

![Architecture](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>Présentation du service

- Une expérience complète **sans code** pour [créer un robot de FAQ](https://aka.ms/qnamaker-docs-create-faqbot).
- **Aucune limitation de requêtes réseau pour les prédictions**. Vous payez pour héberger le service et non pour le nombre de transactions. Pour plus d’informations, consultez la [page sur la tarification](https://aka.ms/qnamaker-docs-pricing).
- **Mise à l’échelle au besoin**. Choisissez les références SKU appropriées pour les composants individuels, selon votre scénario. Découvrez comment [choisir la capacité](https://aka.ms/qnamaker-docs-capacity) pour votre service QnA Maker.
- **Conformité totale des données**. Les composants du service de prédiction sont déployés dans votre abonnement Azure et au sein de la limite de conformité.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un service QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
