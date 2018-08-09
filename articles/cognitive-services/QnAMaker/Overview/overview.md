---
title: En savoir plus sur QnA Maker - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: QnA Maker vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré comme des documents de FAQ ou des URL et guides d’utilisation de produits.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "36295143"
---
# <a name="what-is-qna-maker"></a>Qu’est-ce que QnA Maker ?

[QnA Maker](https://qnamaker.ai) vous permet de mettre en place un service de questions-réponses à partir de votre contenu semi-structuré comme des documents de FAQ ou des URL et guides d’utilisation de produits. Vous pouvez créer un modèle de questions et de réponses flexible en fonction des interrogations des utilisateurs, qui fournit des réponses que vous allez apprendre à un robot pour qu’il les utilise de façon naturelle.

Une interface utilisateur graphique facile à utiliser vous permet de créer, de gérer, d’effectuer l’apprentissage et d’activer votre service sans expérience de développement.

![Vue d’ensemble](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>Points forts

- Une expérience complète **sans code** pour [créer un robot de FAQ](https://aka.ms/qnamaker-docs-create-faqbot).
- **Plus aucune limitation de bande passante**. Vous payez pour héberger le service et non pour le nombre de transactions. Pour plus d’informations, consultez la [page sur la tarification](https://aka.ms/qnamaker-docs-pricing).
- **Effectuez une mise à l’échelle en fonction de vos besoins**. Choisissez les références SKU appropriées pour les composants individuels, selon votre scénario. Découvrez comment [choisir la capacité](https://aka.ms/qnamaker-docs-capacity) pour votre service QnA Maker.
- **Conformité totale des données**. Les données et composants de runtime sont déployés dans l’abonnement Azure de l’utilisateur et au sein de leur limite de conformité. Lisez ce qui suit pour plus de détails.

## <a name="key-qna-maker-processes"></a>Processus clés de QnA Maker

Un QnA Maker fournit deux services essentiels pour vos données :

* **Extraction** : les données de question-réponse structurées sont extraites de sources de données semi-structurées comme des FAQ et des guides d’utilisation de produits. Cette extraction est effectuée lors de la création de la base de connaissances. Créez votre base de connaissances [ici](https://aka.ms/qnamaker-docs-createkb).

* **Mise en correspondance** : une fois que votre base de connaissances est passée par l’étape [d’apprentissage et de test](https://aka.ms/qnamaker-docs-trainkb), vous la [publiez](https://aka.ms/qnamaker-docs-publishkb). Cela active un point de terminaison vers votre base de connaissances QnA Maker, que vous pouvez ensuite utiliser dans votre robot ou application. Ce point de terminaison accepte une question utilisateur et répond avec la meilleure correspondance question-réponse dans la base de connaissances, ainsi qu’avec un score de confiance pour la correspondance.

## <a name="qna-maker-architecture"></a>Architecture de QnA Maker

La pile QnA Maker se compose des éléments suivants :

1. **Services de gestion QnA Maker (plan de contrôle)** : l’expérience de gestion d’une base de connaissances QnA Maker, qui inclut la création initiale, la mise à jour, l’apprentissage et la publication. Ces activités peuvent être effectuées via le [portail](https://qnamaker.ai) ou les [API de gestion](https://aka.ms/qnamaker-v4-apis). Les services de gestion communiquent avec le composant de runtime ci-dessous.

2. **Runtime QnA Maker (plan de données)** : les données et le runtime sont déployés dans l’abonnement Azure de l’utilisateur dans une région de son choix. Le contenu des questions-réponses client est stocké dans [Recherche Azure](https://azure.microsoft.com/services/search/), et le runtime est déployé en tant que ressource [App Service](https://azure.microsoft.com/services/app-service/). Vous pouvez également choisir de déployer une ressource [Application Insights](https://azure.microsoft.com/services/application-insights/) à des fins d’analyse.

![Architecture](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer un service QnA Maker](../how-to/set-up-qnamaker-service-azure.md)
