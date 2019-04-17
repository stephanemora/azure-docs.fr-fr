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
ms.date: 03/20/2019
ms.author: juliako
ms.custom: ''
ms.openlocfilehash: 4f9911259cc4da02429971ef6bddfa62080a9fe6
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044404"
---
# <a name="start-developing-with-media-services-v3-api-using-sdkstools"></a>Commencer à développer avec l’API Media Services v3 avec des SDK/outils

En tant que développeur, vous pouvez utiliser [l’API REST](https://aka.ms/ams-v3-rest-ref) ou les bibliothèques clientes de Media Services qui vous permettent d’interagir avec l’API REST afin de créer, gérer et mettre à jour facilement les workflows multimédias personnalisés. L’API [Media Services v3](https://aka.ms/ams-v3-rest-sdk) s’appuie sur la spécification OpenAPI (anciennement appelée Swagger).

Cette rubrique fournit des liens vers les SDK, les outils et la documentation. Il fournit également des informations utiles pour différents environnements de développement.

> [!NOTE]
> Les kits SDK Azure Media Services v3 ne sont pas garantis thread-safe. Lorsque vous développez une application multithread, vous devez ajouter votre propre logique de synchronisation de thread pour protéger le client, ou utiliser un objet AzureMediaServicesClient différent pour chaque thread. Vous devez également faire attention aux problèmes liés au multithreading provoqués par les objets facultatifs qui sont fournis au client par votre code (comme une instance HttpClient dans .NET, par exemple).

## <a name="prerequisites"></a>Prérequis

Pour commencer à développer sur Media Services, vous avez besoin des éléments suivants :

- Un abonnement Azure actif. Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) avant de commencer.
- [En savoir plus sur les concepts fondamentaux](concepts-overview.md)
- [Créer un compte Media Services - CLI](create-account-cli-how-to.md)

## <a name="start-developing-with-sdks"></a>Commencer à développer avec des kits SDK

### <a name="net"></a>.NET

Utilisez le [Kit SDK .NET](https://aka.ms/ams-v3-dotnet-sdk).

Explorez la documentation [Informations de référence sur .NET](https://aka.ms/ams-v3-dotnet-ref) de Media Services.

### <a name="java"></a>Java

Utilisez le [SDK Java](https://aka.ms/ams-v3-java-sdk).

Passez en revue la documentation [Informations de référence sur Java](https://aka.ms/ams-v3-java-ref) de Media Services.

### <a name="nodejs"></a>Node.js

Utilisez le [SDK Node.js](https://aka.ms/ams-v3-nodejs-sdk).

Explorez la documentation [Informations de référence sur Node.js](https://aka.ms/ams-v3-nodejs-ref) de Media Services et regardez les [exemples](https://github.com/Azure-Samples/media-services-v3-node-tutorials) qui montrent comment utiliser l’API Media Services avec node.js.

### <a name="python"></a>Python

Utilisez le [SDK Python](https://aka.ms/ams-v3-python-sdk).

Passez en revue la documentation [Informations de référence sur Python](https://aka.ms/ams-v3-python-ref) de Media Services.

### <a name="go"></a>Go

Utilisez le [SDK Go](https://aka.ms/ams-v3-go-sdk).

Passez en revue la documentation [Informations de référence sur Go](https://aka.ms/ams-v3-go-ref) de Media Services.

### <a name="ruby"></a>Ruby

Utilisez le [SDK Ruby](https://aka.ms/ams-v3-ruby-sdk).

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) est un outil disponible pour les clients Windows qui souhaitent en savoir plus sur Media Services. AMSE est une application Winforms/C# qui charge, télécharge, encode, diffuse en continu du contenu VOD et en direct avec Media Services. L’outil AMSE est destiné aux clients qui souhaitent tester Media Services sans écrire de code. Le code AMSE est fourni en tant que ressource pour les clients qui souhaitent développer avec Media Services.

AMSE est un projet Open Source, son support est assuré par la Communauté (les problèmes peuvent être signalés sur https://github.com/Azure/Azure-Media-Services-Explorer/issues). Ce projet a adopté le [Code de conduite open source de Microsoft](https://opensource.microsoft.com/codeofconduct/). Pour plus d’informations, consultez les [Questions fréquentes (FAQ) sur le code de conduite](https://opensource.microsoft.com/codeofconduct/faq/) ou envoyez vos questions ou vos commentaires à opencode@microsoft.com.

## <a name="next-steps"></a>Étapes suivantes

[Vue d’ensemble](media-services-overview.md)
