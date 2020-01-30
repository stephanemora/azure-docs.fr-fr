---
title: Exécuter le conteneur Form Recognizer dans Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Déployez le conteneur Form Recognizer sur un Azure Container Instance et testez-le dans un navigateur web.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: dapine
ms.openlocfilehash: 5d9ab7d12bd6c5fe59bf521aff2c07446ac2f038
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76717304"
---
# <a name="deploy-the-form-recognizer-container-to-azure-container-instances"></a>Déployer le conteneur Form Recognizer sur Azure Container Instances

Découvrez comment déployer le conteneur [Form Recognizer](form-recognizer-container-howto.md) de Cognitive Services sur Azure [Container Instances](https://docs.microsoft.com/azure/container-instances/). Cette procédure illustre la création d’une ressource Azure Form Recognizer. Puis nous aborderons l’extraction de l’image de conteneur associée. Enfin, nous mettrons en avant la possibilité d’exercer l’orchestration des deux à partir d’un navigateur. L’utilisation de conteneurs peut détourner l’attention des développeurs de la gestion de l’infrastructure, au lieu de se concentrer sur le développement d’applications.

> [!IMPORTANT]
> Les conteneurs Form Recognizer utilisent actuellement la version 1.0 de l’API Form Recognizer. Vous pouvez accéder à la dernière version de l’API en utilisant le service managé à la place.

[!INCLUDE [Prerequisites](../containers/includes/container-preview-prerequisites.md)]

## <a name="request-access-to-the-container-registry"></a>Demander l’accès au registre de conteneurs

Vous devez d’abord compléter et envoyer le [formulaire de demande de conteneurs Form Recognizer de Cognitive Services](https://aka.ms/FormRecognizerRequestAccess) pour solliciter l’accès au conteneur. Cela a également pour effet de vous inscrire à Vision par ordinateur. Vous n’avez pas besoin de vous inscrire séparément à l’aide du formulaire de demande de Vision par ordinateur. 

[!INCLUDE [Request access](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Create a Cognitive Services Form Recognizer resource](includes/create-resource.md)]

[!INCLUDE [Create an Form Recognizer container on Azure Container Instances](../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

[!INCLUDE [Containers Next Steps](../containers/includes/containers-next-steps.md)]
