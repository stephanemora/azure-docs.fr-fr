---
title: Obtenir et supprimer des comptes depuis le cache de jeton avec MSAL pour Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Découvrez comment afficher et supprimer des comptes depuis le cache de jeton à l’aide de la bibliothèque d’authentification Microsoft pour Java.
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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452482"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Obtenir et supprimer des comptes du cache de jeton à l’aide de MSAL pour Java

MSAL pour Java fournit un cache de jeton en mémoire par défaut. Le cache de jeton en mémoire dure le temps que dure l’instance de l’application.

## <a name="see-which-accounts-are-in-the-cache"></a>Afficher les comptes qui se trouvent dans le cache

Vous pouvez vérifier quels comptes se trouvent dans le cache en appelant `PublicClientApplication.getAccounts()`, comme indiqué dans l’exemple suivant :

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Supprimer des comptes du cache

Pour supprimer un compte du cache, recherchez le compte qui doit être supprimé, puis appelez `PublicClientApplicatoin.removeAccount()`, comme indiqué dans l’exemple suivant :

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>En savoir plus

Si vous utilisez MSAL pour Java, découvrez la [Sérialisation du cache de jeton personnalisé dans MSAL pour Java](msal-java-token-cache-serialization.md).
