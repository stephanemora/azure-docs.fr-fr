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
ms.date: 12/08/2018
ms.author: juliako
ms.openlocfilehash: 882f4650c0a3d558ee06c96658b779f9f0c76f76
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2019
ms.locfileid: "54322482"
---
# <a name="get-a-signing-key-from-the-existing-policy"></a>Obtenir une clé de signature à partir de la stratégie existante

L’un des principes de conception clés de l’API v3 est de renforcer la sécurité de l’API. Les API v3 ne retournent pas de secrets ou d’informations d’identification lors d’opérations **Get** ou **List**. Les clés sont toujours null, vides ou purgées de la réponse. Vous devez appeler une méthode d’action distincte pour obtenir des informations d’identification ou des secrets. Les actions distinctes vous permettent de définir différentes autorisations de sécurité RBAC dans le cas où certaines API récupèrent ou affichent des secrets tandis que d’autres non. Pour plus d’informations sur la gestion de l’accès à l’aide de RBAC, consultez [Utiliser RBAC pour gérer l’accès](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-rest).

Les exemples incluent : 

* ne pas retourner des valeurs ContentKey dans le Get de l’élément StreamingLocator ; 
* ne pas retourner les clés de restriction dans le Get de la stratégie ContentKeyPolicy ; 
* ne pas retourner la partie de la chaîne de requête de l’URL (pour supprimer la signature) des URL d’entrée HTTP des tâches.

L'exemple présenté dans cet article explique comment utiliser .NET pour obtenir une clé de signature à partir de la stratégie existante. 
 
## <a name="download"></a>Download 

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
