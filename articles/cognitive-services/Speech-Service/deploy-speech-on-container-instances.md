---
title: Exécuter Azure Container Instances - Reconnaissance vocale
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur Speech Service sur Azure Container Instance, et testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 41589a12f16f330188e2971e3069eb715d1a7163
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559723"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Déployer le conteneur Speech Service sur Azure Container Instances

Découvrez comment déployer le conteneur [Speech Service](speech-container-howto.md) de Cognitive Services sur Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Azure Speech Service. Puis nous nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d'abord compléter et envoyer le [formulaire de demande de conteneurs Cognitive Services Speech](https://aka.ms/speechcontainerspreview/) pour demander l'accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech Service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create an Speech Service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
