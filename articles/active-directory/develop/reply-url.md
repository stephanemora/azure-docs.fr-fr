---
title: Restrictions des URI de redirection et des URL de réponse - Plateforme d’identités Microsoft | Azure
description: Limitations et restrictions des URL de réponse/URI de redirection
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 1367bf32eea58b828c00ee23a59a32a2fec699ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983093"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Limitations et restrictions des URL de réponse/URI de redirection

Un URI de redirection ou une URL de réponse correspond à l'emplacement vers lequel le serveur d'autorisation enverra l'utilisateur une fois l'application autorisée et un code d'autorisation ou un jeton d'accès attribué. Le code ou le jeton est contenu dans l'URI de redirection ou le jeton de réponse. Il est donc important que vous inscriviez l'emplacement qui convient dans le cadre du processus d'inscription de l'application.

 Les restrictions suivantes s’appliquent aux URL de réponse :

    * L’URL de réponse doit commencer par le schéma `https`.
    * L’URL de réponse respecte la casse. Sa casse doit correspondre à celle du chemin d’URL de votre application en cours d’exécution. Par exemple, si votre application comprend `.../abc/response-oidc` dans son chemin, ne spécifiez pas `.../ABC/response-oidc` dans l’URL de réponse. Étant donné que le navigateur web considère que les chemins respectent la casse, les cookies associés à `.../abc/response-oidc` peuvent être exclus s’ils sont redirigés vers l’URL `.../ABC/response-oidc` qui ne correspond pas à la casse.
    
## <a name="maximum-number-of-redirect-uris"></a>Nombre maximal d'URI de redirection

Le tableau suivant indique le nombre maximal d'URI de redirection que vous pouvez ajouter lorsque vous inscrivez votre application.

| Comptes en cours de connexion | Nombre maximal d'URI de redirection | Description |
|--------------------------|---------------------------------|-------------|
| Comptes professionnels et scolaires Microsoft d'un locataire Azure Active Directory (Azure AD) d'une organisation | 256 | Dans le manifeste de l'application, le champ `signInAudience` est défini sur *AzureADMyOrg* ou *AzureADMultipleOrgs* |
| Comptes personnels ou professionnels et scolaires Microsoft | 100 | Dans le manifeste de l'application, le champ `signInAudience` est défini sur *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Longueur maximale d’URI

Vous pouvez utiliser un maximum de 256 caractères pour chaque URI de redirection que vous ajoutez à une inscription d’application.

## <a name="supported-schemes"></a>Schémas pris en charge
Le modèle d’application Azure AD prend aujourd’hui en charge les schémas HTTP et HTTPS pour les applications qui connectent des comptes professionnels ou scolaires Microsoft dans tout locataire Azure Active Directory (Azure AD) de l’organisation. C’est-à-dire que, dans le manifeste de l'application, le champ `signInAudience` est défini sur *AzureADMyOrg* ou *AzureADMultipleOrgs*. Pour les applications qui connectent des comptes Microsoft personnels et des comptes professionnels et scolaires (c’est-à-dire que `signInAudience` est défini sur *AzureADandPersonalMicrosoftAccount*), seul le schéma HTTPS est autorisé.

> [!NOTE]
> La nouvelle expérience [Inscription d'applications](https://go.microsoft.com/fwlink/?linkid=2083908) ne permet pas aux développeurs d’ajouter des URI avec schéma HTTP dans l’interface utilisateur. L'ajout d'URI HTTP pour les applications se connectant à des comptes professionnels ou scolaires est uniquement pris en charge par le biais de l’éditeur du manifeste d’application. À l’avenir, les nouvelles applications ne pourront pas utiliser de schémas HTTP dans les URI de redirection. Toutefois, les applications plus anciennes dont les URI de redirection contiennent des schémas HTTP continueront de fonctionner. Les développeurs doivent utiliser des schémas HTTPS dans les URI de redirection.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Restrictions relatives aux caractères génériques dans les URI

Les URI avec caractères génériques, comme `https://*.contoso.com`, sont pratiques, mais à éviter. L'utilisation de caractères génériques dans les URI n'est pas sans implications en matière de sécurité. Selon la spécification OAuth 2.0 ([section 3.1.2 de RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)), un URI de point de terminaison de redirection doit être un URI absolu. 

Le modèle d’application Azure AD ne prend pas en charge les URI avec caractères génériques des applications configurées pour se connecter à des comptes personnels et professionnels ou scolaires Microsoft. Cela étant, les URI avec caractères génériques sont autorisés pour les applications configurées pour se connecter à des comptes professionnels ou scolaires dans un locataire Azure AD d'une organisation. 
 
> [!NOTE]
> La nouvelle expérience [Inscription d'applications](https://go.microsoft.com/fwlink/?linkid=2083908) ne permet pas aux développeurs d’ajouter des URI avec caractères génériques dans l’interface utilisateur. L'ajout d'URI avec caractères génériques pour les applications se connectant à des comptes professionnels ou scolaires est uniquement pris en charge par le biais de l’éditeur du manifeste d’application. À l’avenir, les nouvelles applications ne pourront pas utiliser de caractères génériques dans les URI de redirection. Toutefois, les applications plus anciennes dont les URI de redirection contiennent des caractères génériques continueront de fonctionner.

Si votre scénario implique plus d'URI de redirection que la limite maximale autorisée, plutôt que d’ajouter une URI avec caractères génériques, envisagez une des approches suivantes.

### <a name="use-a-state-parameter"></a>Utiliser un paramètre d’état

Si vous avez plusieurs sous-domaines et si votre scénario implique que vous redirigiez des utilisateurs en cas d'authentification réussie vers la même page que celle où ils ont été démarrés, l'utilisation d'un paramètre d'état peut être utile. 

Dans cette approche :

1. Créez un URI de redirection « partagé » par l’application pour traiter les jetons de sécurité que vous recevez du point de terminaison d’autorisation.
1. Votre application peut envoyer des paramètres qui lui sont spécifiques (URL de sous-domaine avec origine de l'utilisateur ou informations sur une marque, par exemple) dans le paramètre d'état. Lorsque vous utilisez un paramètre d’état, protégez-vous contre la falsification de requête intersites (CSRF, Cross Site Request Forgery) comme spécifié dans la [section 10.12 du document RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Les paramètres spécifiques à l’application incluent toutes les informations requises pour permettre à l'application d'offrir une bonne expérience à l’utilisateur, à savoir, concevoir l'état d'application approprié. Le point de terminaison d'autorisation Azure AD supprime le code HTML du paramètre d'état. Aussi, assurez-vous que vous ne transmettez pas de contenu HTML dans ce paramètre.
1. Quand Azure AD envoie une réponse à l'URI de redirection « partagé », il renvoie le paramètre d'état à l'application.
1. L'application peut ensuite utiliser la valeur du paramètre d'état pour déterminer l'URL vers laquelle envoyer l'utilisateur. Veillez à valider la protection CSRF.

> [!NOTE]
> Cette approche permet à un client compromis de modifier les paramètres supplémentaires envoyés dans le paramètre d'état, redirigeant ainsi l'utilisateur vers une URL différente, ce qui correspond à la [menace de redirecteur ouvert](https://tools.ietf.org/html/rfc6819#section-4.2.4) décrite dans le document RFC 6819. Par conséquent, le client doit protéger ces paramètres en chiffrant l'état ou en le vérifiant par un autre moyen, tel que la validation du nom de domaine dans l'URI de redirection par rapport au jeton.

### <a name="add-redirect-uris-to-service-principals"></a>Ajouter des URI de redirection aux principaux de service

Une autre approche consiste à ajouter des URI de redirection aux [principaux de service](app-objects-and-service-principals.md#application-and-service-principal-relationship) qui représentent votre inscription d’application dans n’importe quel locataire Azure AD. Vous pouvez adopter cette approche si vous n'êtes pas en mesure d'utiliser un paramètre d'état ou si votre scénario implique que vous ajoutiez de nouvelles URI de redirection à votre inscription d'application pour chaque nouveau locataire que vous prenez en charge. 

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [manifeste de l'application](reference-app-manifest.md)
