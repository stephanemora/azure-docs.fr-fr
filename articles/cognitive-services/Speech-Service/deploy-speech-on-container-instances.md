---
title: Exécuter Azure Container Instances - Service Speech
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur du service Speech dans Azure Container Instance, et testez-le dans un navigateur web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: aahi
ms.openlocfilehash: 5a85086ea8d969dc70f4cd24b25a00765da285eb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89425804"
---
# <a name="deploy-the-speech-service-container-to-azure-container-instances"></a>Déployer le conteneur du service Speech dans Azure Container Instances

Découvrez comment déployer le conteneur [Service Speech](speech-container-howto.md) Cognitive Services dans Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure montre la création d’une ressource du service Speech Azure. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d’abord compléter et envoyer le [formulaire de demande de conteneurs Cognitive Services Speech](https://aka.ms/csgate/) pour demander l’accès au conteneur. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Speech service resource](includes/create-speech-resource.md)]

[!INCLUDE [Create a Speech service container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
