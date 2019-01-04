---
title: Flux d’utilisateur dans Azure Active Directory B2C | Microsoft Docs
description: Cette rubrique porte sur l’infrastructure de stratégie extensible d’Azure Active Directory B2C et la création de différents types de flux d’utilisateur.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d4a93b04b8ad86a6a6d36a5bdaf3209b49e7a9dc
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52877080"
---
# <a name="azure-active-directory-b2c-user-flows"></a>Azure Active Directory B2C : Flux d’utilisateurs


L’infrastructure de stratégie extensible d’Azure Active Directory (Azure AD) B2C est le pilier central du service. Les stratégies décrivent entièrement les expériences relatives à l’identité des consommateurs, telles que l’inscription, la connexion ou la modification de profil. Pour vous aider à configurer les tâches d’identité les plus courantes, le portail Azure AD B2C inclut des stratégies configurables prédéfinies appelées **flux d’utilisateur**. Par exemple, un flux utilisateur d'inscription vous permet de contrôler les comportements en configurant les paramètres suivants :

* types de compte (réseaux sociaux tels que Facebook et comptes locaux tels que des adresses de courrier) que les clients peuvent utiliser pour s’inscrire à l’application ;
* attributs (par exemple, prénom, code postal et pointure) à recueillir auprès du client lors de l’inscription ;
* utilisation d’Azure Multi-Factor Authentication ;
* aspect de toutes les pages d'inscription ;
* informations (qui apparaissent en tant que revendications dans un jeton) que l’application reçoit lorsque l’exécution du flux d’utilisateur est terminée.

Vous pouvez créer plusieurs flux d’utilisateurs de types différents dans votre client et les utiliser dans vos applications selon vos besoins. Les flux d’utilisateurs peuvent être réutilisés entre les différentes applications. Cette flexibilité permet aux développeurs de définir et de modifier les expériences relatives à l’identité du client avec peu ou pas de modification du code.

Les flux d’utilisateurs sont disponibles à l'utilisation par le biais d'une interface développeur simple. Votre application déclenche un flux d’utilisateur à l’aide d’une requête d'authentification HTTP standard (en transmettant un paramètre de flux d’utilisateur dans la requête) et reçoit un jeton personnalisé en réponse. Par exemple, la seule différence entre les requêtes invoquant un flux d’utilisateur d’inscription et celles invoquant un flux d’utilisateur de connexion réside dans le nom du flux d’utilisateur qui est utilisé dans le paramètre de la chaîne de requête « p » :

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

## <a name="create-a-sign-up-or-sign-in-user-flow"></a>Créer un flux d’utilisateur d’inscription ou de connexion

Ce flux d’utilisateur gère les expériences d’inscription et de connexion des utilisateurs avec une configuration unique. Les utilisateurs sont dirigés vers le chemin d’accès correct (inscription ou connexion) en fonction du contexte. Cet article décrit également le contenu des jetons que l’application recevra en cas d’inscription ou de connexion réussie.  Un exemple de code pour le flux d’utilisateur **d’inscription ou de connexion** est [disponible ici](active-directory-b2c-devquickstarts-web-dotnet-susi.md).  Nous vous recommandons d’utiliser ce flux d’utilisateur plutôt qu’un flux d’utilisateur **d’inscription** ou un flux d’utilisateur de **connexion**.  

[!INCLUDE [active-directory-b2c-create-sign-in-sign-up-policy](../../includes/active-directory-b2c-create-sign-in-sign-up-policy.md)]

## <a name="create-a-sign-up-user-flow"></a>Créer un flux d’utilisateur d’inscription

[!INCLUDE [active-directory-b2c-create-sign-up-policy](../../includes/active-directory-b2c-create-sign-up-policy.md)]

## <a name="create-a-sign-in-user-flow"></a>Créer un flux d’utilisateur de connexion

[!INCLUDE [active-directory-b2c-create-sign-in-policy](../../includes/active-directory-b2c-create-sign-in-policy.md)]

## <a name="create-a-profile-editing-user-flow"></a>Créer un flux d’utilisateur de modification de profil

[!INCLUDE [active-directory-b2c-create-profile-editing-policy](../../includes/active-directory-b2c-create-profile-editing-policy.md)]

## <a name="create-a-password-reset-user-flow"></a>Créer un flux d’utilisateur de réinitialisation du mot de passe

[!INCLUDE [active-directory-b2c-create-password-reset-policy](../../includes/active-directory-b2c-create-password-reset-policy.md)]

## <a name="preview-user-flows"></a>Afficher un aperçu des flux d’utilisateurs

À mesure que nous publions de nouvelles fonctionnalités, certaines d'entre elles risquent de ne pas être disponibles sur les stratégies ou flux d’utilisateurs existants.  Nous prévoyons de remplacer les anciennes versions par la version la plus récente du même type, une fois ces flux d’utilisateurs sont mis à la disposition générale.  Vos stratégies et flux d’utilisateurs existants ne changeront pas, et pour tirer parti de ces nouvelles fonctionnalités, vous devez créer de nouveaux flux d’utilisateurs.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

### <a name="how-do-i-link-a-sign-up-or-sign-in-user-flow-with-a-password-reset-user-flow"></a>Comment lier un flux d’utilisateur d’inscription ou de connexion à un flux d’utilisateur de réinitialisation de mot de passe ?
Lorsque vous créez un flux d’utilisateur **d’inscription ou de connexion** (avec des comptes locaux), vous voyez un lien **Mot de passe oublié ?** sur la première page. Lorsque vous cliquez sur ce lien, le flux d’utilisateur de réinitialisation du mot de passe n’est pas automatiquement déclenché. 

Au lieu de cela, le code d’erreur **`AADB2C90118`** est retourné à votre application. Votre application doit gérer ce code d’erreur en appelant un flux d’utilisateur spécifique pour la réinitialisation du mot de passe. Pour plus d’informations, consultez un [exemple illustrant l’approche de liaison de flux d’utilisateurs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI).

### <a name="should-i-use-a-sign-up-or-sign-in-user-flow-or-a-sign-up-user-flow-and-a-sign-in-user-flow"></a>Dois-je utiliser un flux d’utilisateur d’inscription ou de connexion ou un flux d’utilisateur dédié à l’inscription et un autre à la connexion ?
Nous vous recommandons d’utiliser un flux d’utilisateur **d’inscription ou de connexion** plutôt qu’un flux d’utilisateur **d’inscription** et un flux d’utilisateur **de connexion**.  

Le flux d’utilisateur **d’inscription ou de connexion** offre plus de possibilités que le flux d’utilisateur de **connexion**. Elle vous permet également d’utiliser une personnalisation de l’interface utilisateur de la page et offre une meilleure prise en charge de la localisation. 

Le flux d’utilisateur de **connexion** est recommandé si vous n’avez pas besoin de localiser vos flux d’utilisateurs, mais que vous avez besoin uniquement de fonctionnalités de personnalisation mineures, et souhaitez que la réinitialisation de mot de passe soit intégrée.

## <a name="next-steps"></a>Étapes suivantes
* [Configuration du jeton, de la session et de l’authentification unique](active-directory-b2c-token-session-sso.md)
* [Désactiver la vérification par courrier électronique lors de l’inscription du consommateur](active-directory-b2c-reference-disable-ev.md)

