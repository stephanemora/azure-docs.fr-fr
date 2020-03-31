---
title: Exécuter le service Azure Kubernetes - Analyse de texte
titleSuffix: Azure Cognitive Services
description: Déployez l'image de conteneur Analyse de texte sur Azure Kubernetes Service, puis testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: 3264ec5a83277e6bb4befad46cd1337175e911c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74383508"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Déployer un conteneur Analyse de texte dans Azure Kubernetes Service

Découvrez comment déployer l'image de conteneur [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services Azure sur Azure Kubernetes Service (AKS). Cette procédure illustre la création d’une ressource Analyse de texte, la création d’une image Analyse des sentiments associée et l’orchestration des deux depuis un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, pour les faire se concentrer sur le développement d’applications.

## <a name="prerequisites"></a>Conditions préalables requises

Cette procédure nécessite plusieurs outils qui doivent être installés et exécutés localement. N’utilisez pas Azure Cloud Shell. Vous avez besoin des éléments suivants :

* Un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.
* Un éditeur de code, par exemple [Visual Studio Code](https://code.visualstudio.com/download).
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installé.
* The [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) installé.
* Une ressource Azure avec le niveau de tarification approprié. Certains niveaux tarifaires ne fonctionnent pas avec ce conteneur :
    * La ressource **Analyse de texte Azure** uniquement avec les niveaux tarifaires F0 ou Standard.
    * La ressource **Azure Cognitive Services** avec un niveau tarifaire S0.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Extraction d’expressions clés](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Détection de la langue](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Analyse des sentiments](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>Étapes suivantes

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* Utiliser le [service connecté Analytique de texte](../vs-text-connected-service.md)
