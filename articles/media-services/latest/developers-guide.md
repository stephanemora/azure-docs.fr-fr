---
title: SDK Azure Media Services v3 - Azure
description: Cet article fournit une vue d’ensemble de la façon de commencer à développer avec l’API Media Services v3 avec des SDK/outils.
services: media-services
documentationcenter: na
author: Juliako
manager: femila
editor: ''
tags: ''
keywords: ''
ms.service: media-services
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 02/22/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: b9c89b6cb6dbc6cb217be671eb0e52c0416811ed
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57889843"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Commencer à développer avec l’API Media Services v3 avec des SDK/outils

En tant que développeur, vous pouvez utiliser [l’API REST](https://aka.ms/ams-v3-rest-ref) ou les bibliothèques clientes de Media Services qui vous permettent d’interagir avec l’API REST afin de créer, gérer et mettre à jour facilement les workflows multimédias personnalisés. L’API [Media Services v3](https://aka.ms/ams-v3-rest-sdk) s’appuie sur la spécification OpenAPI (anciennement appelée Swagger).

Cette rubrique fournit des liens vers les SDK, les outils et la documentation. Il fournit également des informations utiles pour différents environnements de développement.

## <a name="prerequisites"></a>Prérequis

Pour commencer à développer sur Media Services, vous avez besoin des éléments suivants :

- Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [Créer un compte Media Services](create-account-cli-how-to.md)

## <a name="start-developing"></a>Commencer à développer

Azure Media Services prend en charge les SDK/outils suivants 

- [Interface de ligne de commande Azure](https://aka.ms/ams-v3-cli) 
- [Kit de développement logiciel (SDK) .NET](https://aka.ms/ams-v3-dotnet-sdk)
- [Kit SDK Java](https://aka.ms/ams-v3-java-sdk)
- [Kit de développement logiciel (SDK) Node.js](https://aka.ms/ams-v3-nodejs-sdk)
- [Kit de développement logiciel (SDK) Python](https://aka.ms/ams-v3-python-sdk)
- [Kit de développement logiciel (SDK) Go](https://aka.ms/ams-v3-go-sdk)
- [Kit de développement logiciel (SDK) Ruby](https://aka.ms/ams-v3-ruby-sdk)

### <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) est un outil disponible pour les clients Windows qui souhaitent en savoir plus sur Media Services. AMSE est une application Winforms/C# qui charge, télécharge, encode, diffuse en continu du contenu VOD et en direct avec Media Services. L’outil AMSE est destiné aux clients qui souhaitent tester Media Services sans écrire de code. Le code AMSE est fourni en tant que ressource pour les clients qui souhaitent développer avec Media Services.

AMSE est un projet Open Source, son support est assuré par la Communauté (les problèmes peuvent être signalés sur https://github.com/Azure/Azure-Media-Services-Explorer/issues). Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, consultez les [Questions fréquentes (FAQ) sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à opencode@microsoft.com.

## <a name="rest"></a>REST

Pour commencer à développer avec l’API REST de Media Services, installez un client REST. Par exemple **Postman**, **Visual Studio Code** avec le plug-in REST ou **Telerik Fiddler**. Nous utilisons [Postman](media-rest-apis-with-postman.md) pour les exemples Media Services v3.

Explorez la documentation [Informations de référence sur l’API REST](https://aka.ms/ams-v3-rest-ref) de Media Services et regardez ces exemples :

- [Tutoriel : Encoder un fichier distant basé sur une URL et diffuser la vidéo en continu - REST](stream-files-tutorial-with-rest.md)
- [Charger des fichiers dans un compte Media Services - REST](upload-files-rest-how-to.md)
- [Créer des filtres avec Media Services - REST](filters-dynamic-manifest-rest-howto.md)
- [API Azure Resource Manager basé sur l’API REST](https://github.com/Azure-Samples/media-services-v3-arm-templates)

<!-- ## CLI -->
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

Explorez la documentation [Informations de référence sur Azure CLI](https://aka.ms/ams-v3-cli-ref) de Media Services et regardez ces exemples :

- [Mettre à l’échelle des unités réservées de média - CLI](media-reserved-units-cli-how-to.md)
- [Créer un compte Media Services - CLI](./scripts/cli-create-account.md) 
- [Réinitialiser les informations d’identification de compte - CLI](./scripts/cli-reset-account-credentials.md)
- [Créer des actifs multimédias - CLI](./scripts/cli-create-asset.md)
- [Charger un fichier - CLI](./scripts/cli-upload-file-asset.md)
- [Créer des transformations - CLI](./scripts/cli-create-transform.md)
- [Créer des travaux - CLI](./scripts/cli-create-jobs.md)
- [Créer une grille d’événement - CLI](./scripts/cli-create-event-grid.md)
- [Publier un actif multimédia - CLI](./scripts/cli-publish-asset.md)
- [Filtrer - CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="net"></a>.NET

Explorez la documentation [Informations de référence sur .NET](https://aka.ms/ams-v3-dotnet-ref) de Media Services et regardez ces exemples :

- [Tutoriel : Charger, encoder et diffuser des vidéos en continu - .NET](stream-files-tutorial-with-api.md) 
- [Tutoriel : Diffuser en direct avec Media Services v3 - .NET](stream-live-tutorial-with-api.md)
- [Tutoriel : Analyser des vidéos avec Media Services v3 - .NET](analyze-videos-tutorial-with-api.md)
- [Créer une entrée de travail à partir d’un fichier local - .NET](job-input-from-local-file-how-to.md)
- [Créer une entrée de travail à partir d’une URL HTTPS - .NET](job-input-from-http-how-to.md)
- [Encoder avec une transformation personnalisée - .NET](customize-encoder-presets-how-to.md)
- [Utiliser le chiffrement dynamique AES-128 et le service de distribution de clés - .NET](protect-with-aes128.md)
- [Utilisation du chiffrement dynamique DRM et du service de remise des licences - .NET](protect-with-drm.md)
- [Obtenir une clé de signature à partir de la stratégie existante - .NET](get-content-key-policy-dotnet-howto.md)
- [Créer des filtres avec Media Services - .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Exemples avancés de vidéo à la demande d’Azure Functions v2 avec Media Services v3](https://aka.ms/ams3functions)

## <a name="java"></a>Java

Passez en revue la documentation [Informations de référence sur Java](https://aka.ms/ams-v3-java-ref) de Media Services.

## <a name="nodejs"></a>Node.js

Explorez la documentation [Informations de référence sur Node.js](https://aka.ms/ams-v3-nodejs-ref) de Media Services et regardez les [exemples](https://github.com/Azure-Samples/media-services-v3-node-tutorials) qui montrent comment utiliser l’API Media Services avec node.js.

## <a name="python"></a>Python

Passez en revue la documentation [Informations de référence sur Python](https://aka.ms/ams-v3-python-ref) de Media Services.

## <a name="go"></a>Go

Passez en revue la documentation [Informations de référence sur Go](https://aka.ms/ams-v3-go-ref) de Media Services.

## <a name="next-steps"></a>Étapes suivantes

- [Créer un compte - CLI](create-account-cli-how-to.md)
- [Accéder aux API - CLI](access-api-cli-how-to.md)

