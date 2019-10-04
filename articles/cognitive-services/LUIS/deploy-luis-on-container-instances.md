---
title: Exécuter des Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur LUIS sur Azure Container Instance, et testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 1d19d80bbc334a376f77eb285349fb1a87a91a54
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711278"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Déployer le conteneur Language Understanding (LUIS) sur Azure Container Instances

Découvrez comment déployer le conteneur [LUIS](luis-container-howto.md) de Cognitive Services sur Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Détecteur d'anomalies. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container Instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
