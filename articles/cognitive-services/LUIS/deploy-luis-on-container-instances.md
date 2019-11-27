---
title: Déployer un conteneur LUIS sur des instances Azure Container
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur LUIS sur une instance Azure Container, et testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: aecbb9bb94fc251ee0142b611c54d16304793e50
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73901817"
---
# <a name="deploy-the-language-understanding-luis-container-to-azure-container-instances"></a>Déployer le conteneur Language Understanding (LUIS) sur des instances Azure Container

Découvrez comment déployer le conteneur [LUIS](luis-container-howto.md) de Cognitive Services sur des [instances Azure Container](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Détecteur d’anomalies. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../containers/includes/container-prerequisites.md)]

[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

[!INCLUDE [Create LUIS Container instance resource](../containers/includes/create-container-instances-resource.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Next steps](../containers/includes/containers-next-steps.md)]
