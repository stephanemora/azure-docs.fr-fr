---
title: Configuration de la migration de Media Services v2 à v3
description: Cet article vous aidera à configurer votre environnement pour la migration de Azure Media Services v2 vers v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: azure media services, migration, flux, diffusion, direct, kit de développement logiciel (SDK)
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 03/25/2021
ms.author: inhenkel
ms.openlocfilehash: 62723f5c833ca89618a6d9e0781539a13efba4f3
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105959668"
---
# <a name="step-3---set-up-to-migrate-to-the-v3-rest-api-or-client-sdk"></a>Étape 3 : configurer la migration vers l’API REST v3 ou le Kit de développement logiciel (SDK) client

![logo du guide de migration](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![étapes de migration 2](./media/migration-guide/steps-3.svg)

La section suivante décrit les étapes à suivre pour configurer votre environnement afin d’utiliser l’API Media Services v3.

## <a name="sdk-model"></a>Modèle de Kit de développement logiciel (SDK)

L’API v2 comprenait deux kits de développement logiciel (SDK) clients distincts, l’un pour l’API de gestion, qui permettait la création de comptes par programmation, et l’autre pour la gestion des ressources.

Auparavant, les développeurs utilisaient un ID client et une clé secrète client de principal de service Azure, ainsi qu’un point de terminaison d’API REST v2 spécifique pour leur compte AMS.

L’API v3 est basée sur Azure Resource Manager (ARM). Elle utilise des ID et clés de principal de service Azure Active Directory (Azure AD) pour se connecter à l’API. Les développeurs devront créer des principaux de service ou des identités managées pour se connecter à l’API. L’API V3 utilise des points de terminaison ARM standard, ainsi qu’un modèle similaire et cohérent pour tous les autres services Azure.

Les clients qui utilisaient précédemment la version 2015-10-01 de l’API de gestion ARM pour gérer leurs comptes v2 doivent utiliser la version 2020-05-01 de l’API de gestion ARM prise en charge pour l’accès à l’API V3.

## <a name="create-a-new-media-services-account-for-testing"></a>Créer un compte Media Services à des fins de test

Suivez les étapes de démarrage rapide pour la [configuration de votre environnement](how-to-set-azure-subscription.md?tabs=portal) à l’aide du portail Azure. Sélectionnez un accès d’API et une authentification du principal de service pour générer un nouvel ID d’application Azure AD et des secrets à utiliser avec ce compte de test.

[Créez un compte Media Services](account-create-how-to.md?tabs=portal).
[Obtenez des informations d’identification pour accéder à l’API Media Services](access-api-howto.md?tabs=portal).

## <a name="download-client-sdk-of-your-choice-and-set-up-your-environment"></a>Téléchargez le Kit de développement logiciel (SDK) client de votre choix et configurez votre environnement

- Kits de développement logiciel (SDK) disponibles pour  [.NET](/dotnet/api/overview/azure/mediaservices/management), .NET Core,  [Node.js](/javascript/api/overview/azure/mediaservices/management),  [Python](/python/api/overview/azure/mediaservices/management),  [Java](/java/api/overview/azure/mediaservices/management),  [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media) et [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md).
- Intégration d’[Azure CLI](/cli/azure/ams) pour la prise en charge de scripts simples.

> [!NOTE]
> Un Kit de développement logiciel (SDK) PHP de la communauté n’est plus disponible pour Azure Media Services dans v3. Si vous utilisez PHP sur v2, vous devez migrer vers l’API REST directement dans votre code.

## <a name="open-api-specifications"></a>Spécifications de l’API Open

- La version v3 est basée sur une surface d’API unifiée qui expose des fonctionnalités de gestion et d’exploitation qui s’appuient sur Azure Resource Manager. Vous pouvez utiliser des modèles Azure Resource Manager pour créer et déployer des transformations, des points de terminaison de streaming, des événements en direct, etc.

- Le document [spécification OpenAPI](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01) (précédemment appelée Swagger) décrit le schéma de tous les composants de service.

- Tous les kits de développement logiciel (SDK) clients sont dérivés et générés à partir de la spécification de l’API Open publiée sur GitHub. Au moment de la publication de cet article, les dernières spécifications de l’API Open sont conservées publiquement dans GitHub. La version 2020-05-01 est la [dernière mise en production stable](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2020-05-01).

## <a name="rest"></a>[REST](#tab/rest)

Utilisez [Postman](./media-rest-apis-with-postman.md) pour les appels d’API REST Media Services v3.
Lisez les [pages de référence de l’API REST](/rest/api/media/).

Vous devez utiliser la chaîne de version 2020-05-01 dans la collection Postman.

## <a name="net"></a>[.NET](#tab/net)

Lisez l’article [Se connecter à l’API Azure Media Services v3 avec .NET](configure-connect-dotnet-howto.md) pour configurer votre environnement.

Si vous souhaitez simplement installer le dernier Kit de développement logiciel (SDK) à l’aide de PackageManager, utilisez la commande suivante :

```Install-Package Microsoft.Azure.Management.Media```

Ou bien, pour installer le dernier Kit de développement logiciel (SDK) à l’aide de l’interface de ligne de commande .NET, utilisez la commande suivante :

```dotnet add package Microsoft.Azure.Management.Media```

En outre, des exemples .NET complets sont disponibles dans [Azure-Samples/media-services-V3-dotnet](https://github.com/Azure-Samples/media-services-v3-dotnet) pour divers scénarios. Les projets contenus dans ce référentiel montrent comment implémenter différents scénarios Azure Media Services à l’aide de la version v3.

### <a name="get-started-adjusting-your-code"></a>Commencer à ajuster votre code

Recherchez dans votre base de code des instances d’utilisation de `CloudMediaContext` pour commencer le processus de mise à niveau vers l’API v3.

Le code suivant montre comment l’accès à l’API V2 se faisait précédemment à l’aide du Kit de développement logiciel (SDK) .NET v2. Les développeurs commençaient par créer un `CloudMediaContext` et une instance avec un objet `AzureAdTokenCredentials`.

```dotnet

class Program
    {
        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static CloudMediaContext _context = null;

        static void Main(string[] args)
        {
        try
        {
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

```

## <a name="java"></a>[Java](#tab/java)

Lisez l’article [Se connecter à l’API Azure Media Services v3 avec Java](configure-connect-java-howto.md) pour configurer votre environnement.

## <a name="python"></a>[Python](#tab/python)

Lisez l’article [Se connecter à l’API Azure Media Services v3 avec Python](configure-connect-python-howto.md) pour configurer votre environnement.

## <a name="nodejs"></a>[Node.JS](#tab/nodejs)

Lisez l’article [Se connecter à l’API Azure Media Services v3 avec Node.js](configure-connect-nodejs-howto.md) pour configurer votre environnement.

## <a name="ruby"></a>[Ruby](#tab/ruby)

- Procurez-vous le Kit de développement logiciel (SDK) [Ruby](https://github.com/Azure/azure-sdk-for-ruby/blob/master/README.md) sur GitHub.

## <a name="go"></a>[Go](#tab/go)

Téléchargez le Kit de développement logiciel (SDK) [Go](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/mediaservices/mgmt/2018-07-01/media).

---
