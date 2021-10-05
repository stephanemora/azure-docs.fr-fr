---
title: Migration vers MSAL.NET et Microsoft. Identity. Web
titleSuffix: Microsoft identity platform
description: Découvrez pourquoi et comment migrer d’Azure AD Authentication Library pour .NET (ADAL.NET) vers Microsoft Authentication Library pour .NET (MSAL.NET) ou Microsoft.Identity.Web
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2021
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev, has-adal-ref
ms.openlocfilehash: 5a3dd6265c9fbefb85cf72b80473538983c682b7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214700"
---
# <a name="migrating-applications-to-msalnet-or-microsoftidentityweb"></a>Migration d’applications vers MSAL.NET ou Microsoft.Identity.Web

## <a name="why-migrate-to-msalnet-or-microsoftidentityweb"></a>Pourquoi faut-il migrer vers MSAL.NET ou Microsoft.Identity.Web

Microsoft Authentication Library pour .NET (MSAL.NET) et Azure AD Authentication Library pour .NET (ADAL.NET) sont tous deux utilisés pour authentifier des entités Azure AD et demander des jetons auprès d’Azure AD. Jusqu'à présent, la plupart des développeurs ont demandé des jetons à la plateforme Azure AD pour les développeurs (v1.0) à l'aide de la bibliothèque Azure AD Authentification (ADAL). Ces jetons sont utilisés pour authentifier les identités Azure AD (comptes professionnels et scolaires). 

MSAL offre des avantages par rapport à ADAL. Certains avantages sont listés ci-après :

- Vous pouvez authentifier un ensemble plus large d’identités Microsoft : les comptes professionnels ou scolaires, les comptes Microsoft personnels et les comptes sociaux ou locaux avec Azure AD B2C,
- vos utilisateurs bénéficieront de la meilleure expérience d’authentification unique ;
- votre application peut activer le consentement incrémentiel, accès conditionnel ;
- vous bénéficiez d’innovations continues en termes de sécurité et de résilience ;
- votre application implémente les meilleures pratiques en termes de résilience et de sécurité.

**MSAL.NET ou Microsoft.Identity.Web sont désormais les bibliothèques d’authentification recommandées à utiliser avec la plateforme d’identités Microsoft**. Aucune nouvelle fonctionnalité ne sera implémentée sur ADAL.NET. Les efforts se focalisent sur l'amélioration de MSAL.NET. Pour plus d’informations, consultez l’annonce : [Mettre à jour vos applications pour utiliser la bibliothèque d’authentification Microsoft et l’API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)

## <a name="should-you-migrate-to-msalnet-or-to-microsoftidentityweb"></a>Faut-il migrer vers MSAL.NET ou vers Microsoft.Identity.Web ?

Avant d’entrer dans les détails de MSAL.NET par rapport à ADAL.NET, vous pouvez vérifier si vous voulez utiliser MSAL.NET ou une abstraction de niveau supérieur comme [Microsoft.Identity.Web](microsoft-identity-web.md).

Pour plus d’informations sur l’arbre de décision ci-dessous, consultez [MSAL.net ou Microsoft. Identity. Web ?](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-or-Microsoft.Identity.Web)

![« Schéma expliquant comment déterminer si vous devez utiliser MSAL.NET ou Microsoft.Identity.Web, ou les deux, lors de la migration à partir d’ADAL.NET »](media/msal-net-migration/decision-diagram.png)

<!-- 1P
## Examples of 1P Migrations

[See examples](https://identitydivision.visualstudio.com/DevEx/_wiki/wikis/DevEx.wiki/20413/1P-ADAL.NET-to-MSAL.NET-migration-examples) of other 1P teams who have already, or are currently, migrating from ADAL to one of the MSAL+ solutions above. See their code, and in some cases read about their migration story.
 -->
 
## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [applications clientes monopages, publiques et confidentielles](msal-client-applications.md).
- Découvrez comment [migrer des applications clientes confidentielles basées sur ASP.NET MVC ou .net classic à partir de ADAL.NET vers MSAL.NET](msal-net-migration-confidential-client.md).
- Découvrez comment [migrer des applications clientes publiques basées sur .NET ou .NET classic à partir de ADAL.NET vers MSAL.NET](msal-net-migration-public-client.md).
- En savoir plus sur les [Différences entre les applications ADAL.NET et MSAL.NET](msal-net-differences-adal-net.md).
- Découvrez comment migrer des applications clientes confidentielles basées sur ASP.NET Core à partir de ADAL.NET à Microsoft.Identity.Web :
  -  [Applications web](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apps#migrating-from-previous-versions--adding-authentication)
  -  [API web](https://github.com/AzureAD/microsoft-identity-web/wiki/web-apis)
