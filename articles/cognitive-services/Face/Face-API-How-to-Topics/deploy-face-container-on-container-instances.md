---
title: Exécuter un conteneur Visage dans Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur Visage sur Azure Container Instance, et testez-le dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: a5354581be519172c498e57d25510f9fc5c0daa4
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911254"
---
# <a name="deploy-the-face-container-to-azure-container-instances"></a>Déployer le conteneur Visage sur Azure Container Instances

> [!IMPORTANT]
> La limite pour les utilisateurs du conteneur Visage a été atteinte. Nous n’acceptons actuellement aucune nouvelle application pour le conteneur Visage.

Découvrez comment déployer le conteneur [Visage](../face-how-to-install-containers.md) de Cognitive Services sur Azure [Container Instances](../../../container-instances/index.yml). Cette procédure illustre la création d’une ressource Azure Face. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Face resource](../includes/create-face-resource.md)]

[!INCLUDE [Create an Face container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../../containers/includes/containers-next-steps.md)]