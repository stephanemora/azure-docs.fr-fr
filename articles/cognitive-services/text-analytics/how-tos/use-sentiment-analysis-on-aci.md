---
title: Exécuter des Azure Container Instances
titleSuffix: Text Analytics - Azure Cognitive Services
description: Déployez les conteneurs Analyse de texte avec l’image Analyse des sentiments sur Azure Container Instance, puis testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: dapine
ms.openlocfilehash: a82b5a1cbed662289d3a406a61fdd19a3ca8861b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67454786"
---
# <a name="deploy-a-sentiment-analysis-container-to-azure-container-instances-aci"></a>Déployer un conteneur Analyse des sentiments sur Azure Container Instances (ACI)

Découvrez comment déployer les conteneurs [Analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) Cognitive Services avec l’image Analyse des sentiments sur [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI). Cette procédure illustre la création d’une ressource Analyse de texte, la création d’une image Analyse des sentiments associée et la possibilité de procéder à l’orchestration des deux depuis un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

## <a name="prerequisites"></a>Prérequis

* Utilisez un abonnement Azure. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances (ACI)](../../containers/includes/create-aci-resource.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

## <a name="next-steps"></a>Étapes suivantes 

* Utiliser davantage de [conteneurs Cognitive Services](../../cognitive-services-container-support.md)
* Utiliser le [service connecté Analytique de texte](../vs-text-connected-service.md)
