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
ms.date: 7/5/2019
ms.author: dapine
ms.openlocfilehash: 4e16eb2ad6c2634569f981ddcdce753ecaa43f2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446538"
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
