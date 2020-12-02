---
title: Exécuter Azure Container Instances – Analyse de texte
titleSuffix: Azure Cognitive Services
description: Déployez les conteneurs Analyse de texte dans l’instance de conteneur Azure, puis testez-les dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009918"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Déployer un conteneur Analyse de texte dans Azure Container Instances

Découvrez comment déployer le conteneur [Analyse de texte][install-and-run-containers] de Cognitive Services sur Azure [Container Instances][container-instances]. Cette procédure illustre la création d’une ressource Analyse de texte, la création d’une image Analyse des sentiments associée et la possibilité de procéder à l’orchestration des deux depuis un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

## <a name="prerequisites"></a>Prérequis

* Utilisez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/cognitive-services) avant de commencer.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Analyse de texte pour l’intégrité](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Étapes suivantes 

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* Utiliser le [service connecté Analytique de texte](../index.yml)

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances