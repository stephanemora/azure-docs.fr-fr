---
title: Obtenir une clé de signature à partir de la stratégie existante à l'aide du kit SDK .NET de Media Services v3 - Azure | Microsoft Docs
description: Cette rubrique explique comment obtenir une clé de signature à partir de la stratégie existante à l'aide du kit SDK .NET de Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 58b6f49f4bbbd93fefb9b616f92baf7ef30f7deb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322632"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obtenir une clé de signature à partir de la stratégie existante

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. les API v3 ne retournent pas de secrets ou des informations d’identification sur **obtenir** ou **liste** operations. Obtenir une explication détaillée ici : Pour plus d’informations, consultez [comptes RBAC et Media Services](rbac-overview.md)

L'exemple présenté dans cet article explique comment utiliser .NET pour obtenir une clé de signature à partir de la stratégie existante. 
 
## <a name="download"></a>Téléchargement 

Clonez un référentiel GitHub contenant l'exemple .NET complet sur votre machine à l'aide de la commande suivante :  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials.git
 ```
 
L'exemple de stratégie ContentKeyPolicy contenant les secrets se trouve dans le dossier [EncryptWithDRM](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/tree/master/AMSV3Tutorials/EncryptWithDRM).

## <a name="get-contentkeypolicy-with-secrets"></a>Obtenir la stratégie ContentKeyPolicy contenant les secrets 

Pour accéder à la clé, utilisez **GetPolicyPropertiesWithSecretsAsync**, comme indiqué dans l'exemple ci-dessous.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/EncryptWithDRM/Program.cs#GetOrCreateContentKeyPolicy)]

## <a name="next-steps"></a>Étapes suivantes

[Concevoir un système de protection de contenu multi-DRM avec contrôle d’accès](design-multi-drm-system-with-access-control.md) 
