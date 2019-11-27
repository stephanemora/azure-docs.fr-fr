---
title: Sérialisation personnalisée du cache de jetons dans MSAL.NET pour Java
titleSuffix: Microsoft identity platform
description: Découvrez comment sérialiser le cache de jetons pour MSAL pour Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bd8cb07d30aa5223cf3d78d636f0d593fefbecf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905162"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Sérialisation du cache de jetons personnalisé dans MSAL.NET pour Java (MSAL4J)

Pour obtenir une application de cache de jeton persistant, vous devez personnaliser la sérialisation. Les classes et interfaces Java concernées par la sérialisation du cache de jeton sont les suivantes :

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html) :  interface représentant le cache du jeton de sécurité.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html) : interface représentant l’opération d’exécution du code avant et après l’accès. Vous utiliseriez @Override *beforeCacheAccess* et *afterCacheAccess* avec la logique responsable de la sérialisation et de la désérialisation du cache.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html) : interface représentant le contexte dans lequel l’accès au cache de jeton est effectué. 

Voici une implémentation naïve de la sérialisation personnalisée de la sérialisation/désérialisation du cache de jetons. Ne copiez pas et ne collez pas ceci dans un environnement de production.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>En savoir plus

En savoir plus sur [Obtenir et supprimer des comptes depuis le cache de jeton à l’aide de MSAL pour Java](msal-java-get-remove-accounts-token-cache.md).
