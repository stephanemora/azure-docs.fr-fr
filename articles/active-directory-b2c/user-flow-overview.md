---
title: Flux d’utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Découvrez plus d’informations sur le framework de stratégie extensible d’Azure Active Directory B2C et la création de différents flux d’utilisateur.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 846819c3a05baf4ef1174d4e086f09893e834b2a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76850724"
---
# <a name="user-flows-in-azure-active-directory-b2c"></a>Flux d’utilisateur dans Azure Active Directory B2C

Le framework de stratégie extensible d’Azure Active Directory B2C (Azure AD B2C) est le pilier central du service. Les stratégies décrivent entièrement les expériences relatives à l’identité, comme l’inscription, la connexion ou la modification de profil. Pour vous aider à configurer les tâches d’identité les plus courantes, le portail Azure AD B2C inclut des stratégies configurables prédéfinies appelées **flux d’utilisateur**.

## <a name="what-are-user-flows"></a>Qu’est-ce qu’un flux d’utilisateur ?

Un flux d’utilisateur vous permet de contrôler les comportements dans vos applications en configurant les paramètres suivants :

- Types de compte utilisés pour la connexion, comme les comptes de réseaux sociaux de type Facebook ou les comptes locaux
- Attributs à collecter auprès du consommateur, comme le prénom, le code postal et la pointure
- Azure Multi-Factor Authentication
- Personnalisation de l’interface utilisateur
- Informations que l’application reçoit sous forme de revendications dans un jeton

Vous pouvez créer plusieurs flux d’utilisateur de types différents dans votre locataire et les utiliser dans vos applications selon vos besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications. Cette flexibilité vous permet de définir et de modifier les expériences relatives à l’identité avec peu ou pas de modification du code. Votre application déclenche un flux d’utilisateur à l’aide d’une demande d’authentification HTTP standard qui inclut un paramètre de flux d’utilisateur. La réponse reçue est un [jeton](tokens-overview.md) personnalisé.

Les exemples suivants montrent le paramètre de chaîne de requête « p » qui spécifie le flux d’utilisateur à utiliser :

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siup                                       // Your sign-up user flow
```

```
https://contosob2c.b2clogin.com/contosob2c.onmicrosoft.com/oauth2/v2.0/authorize?
client_id=2d4d11a2-f814-46a7-890a-274a72a7309e      // Your registered Application ID
&redirect_uri=https%3A%2F%2Flocalhost%3A44321%2F    // Your registered Reply URL, url encoded
&response_mode=form_post                            // 'query', 'form_post' or 'fragment'
&response_type=id_token
&scope=openid
&nonce=dummy
&state=12345                                        // Any value provided by your application
&p=b2c_1_siin                                       // Your sign-in user flow
```

## <a name="user-flow-versions"></a>Versions de flux utilisateur

Dans le portail Azure, de nouvelles [versions de flux d’utilisateur](user-flow-versions.md) sont ajoutées en continu. Quand vous commencez à utiliser Azure AD B2C, des flux d’utilisateur testés vous sont recommandés. Quand vous créez un flux d’utilisateur, vous choisissez celui dont vous avez besoin sous l’onglet **Recommandé**.

Les flux d’utilisateur suivants sont actuellement recommandés :

- **Inscription et connexion** : Gère les expériences d’inscription et de connexion avec une seule configuration. Les utilisateurs sont dirigés vers le chemin approprié en fonction du contexte. Nous vous recommandons d’utiliser ce flux d’utilisateur plutôt qu’un flux d’utilisateur d’**inscription** ou un flux d’utilisateur de **connexion**.
- **Modification de profil** : Permet aux utilisateurs de modifier leurs informations de profil.
- **Réinitialisation de mot de passe** : Vous permet de configurer si et comment les utilisateurs peuvent réinitialiser leur mot de passe.

## <a name="linking-user-flows"></a>Liaison de flux d’utilisateur

Un flux d’utilisateur **Inscription ou connexion** avec des comptes locaux comprend un lien **Mot de passe oublié ?** sur la première page. Lorsque vous cliquez sur ce lien, le flux d’utilisateur de réinitialisation du mot de passe n’est pas automatiquement déclenché.

À la place, le code d’erreur `AADB2C90118` est retourné à votre application. Votre application doit gérer ce code d’erreur en exécutant un flux d’utilisateur spécifique qui réinitialise le mot de passe. Pour voir un exemple, examinons un [simple exemple ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI) qui illustre la liaison des flux d’utilisateur.

## <a name="email-address-storage"></a>Stockage d’adresse e-mail

Vous pouvez avoir besoin d’une adresse e-mail dans le cadre d’un flux d’utilisateur. Si l’utilisateur s’authentifie avec un fournisseur d’identité de réseau social, l’adresse e-mail est stockée dans la propriété **otherMails**. Si un compte local est basé sur un nom d’utilisateur, l’adresse e-mail est stockée dans une propriété détaillée d’authentification forte. Si un compte local est basé sur une adresse e-mail, l’adresse e-mail est stockée dans la propriété **signInNames**.

L’adresse e-mail n’est pas forcément vérifiée dans tous les cas. Un administrateur de locataire peut désactiver la vérification d’e-mail dans les stratégies de base des comptes locaux. Même si la vérification de l’adresse e-mail est activée, les adresses ne sont pas vérifiés si elles proviennent d’un fournisseur d’identité de réseau social et qu’elles n’ont pas été changées.

Seules les propriétés **otherMails** et **signInNames** sont exposées avec l’API Graph Active Directory. L’adresse e-mail dans la propriété détaillée de l’authentification forte n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes

Pour créer les flux d’utilisateur recommandés, suivez les instructions du [Tutoriel : Créer un flux d’utilisateur](tutorial-create-user-flows.md).


