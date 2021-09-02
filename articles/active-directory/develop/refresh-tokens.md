---
title: Jetons d’actualisation de la plateforme d'identités Microsoft | Azure
titleSuffix: Microsoft identity platform
description: Découvrez ce que sont les jetons d’actualisation émis par Azure AD.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/25/2021
ms.author: shermanouko
ms.reviewer: mmacy, hirsin
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: e4d2721905d1e344cd0825466e0b0eb08578ef47
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2021
ms.locfileid: "114688107"
---
# <a name="microsoft-identity-platform-refresh-tokens"></a>Jetons d’actualisation de la plateforme d'identités Microsoft

Lorsqu’un client acquiert un jeton d’accès pour accéder à une ressource protégée, il reçoit aussi un jeton d’actualisation. Le jeton d’actualisation permet d’obtenir de nouvelles paires de jetons d’accès/actualisation à l’expiration du jeton d’accès actuel. Les jetons d’actualisation permettent également d’acquérir des jetons d’accès supplémentaires pour d’autres ressources. Ils sont associés à une combinaison utilisateur/client, mais ils ne sont pas liés à une ressource ou à un locataire. Un client peut donc utiliser un jeton d’actualisation pour acquérir des jetons d’accès sur n’importe quelle combinaison ressource/locataire où il a l’autorisation nécessaire. Les jetons d’actualisation sont chiffrés, et seule la plateforme d’identités Microsoft peut les lire.

## <a name="prerequisites"></a>Prérequis

Avant de lire cet article, il est recommandé de consulter les articles suivants :

* [Jetons d’ID de la plateforme d’identités Microsoft](id-tokens.md).
* [Jetons d’accès de la plateforme d’identités Microsoft](access-tokens.md).

## <a name="refresh-token-lifetime"></a>Durée de vie du jeton d’actualisation

Les jetons d’actualisation ont une durée de vie plus longue que les jetons d’accès. La durée de vie par défaut des jetons est de 90 jours et un nouveau jeton les remplace après chaque utilisation. Autrement dit, dès qu’un jeton d’actualisation est utilisé pour acquérir un nouveau jeton d’accès, un nouveau jeton d’actualisation est également émis. La plateforme d’identités Microsoft ne révoque pas les anciens jetons d’actualisation qui ont été utilisés pour récupérer de nouveaux jetons d’accès. Vous pouvez sans problème supprimer l’ancien jeton d’actualisation après l’acquisition d’un nouveau. Les jetons d’actualisation doivent être stockés de manière sécurisée comme les jetons d’accès ou les informations d’identification d’application. 

## <a name="refresh-token-expiration"></a>Expiration des jetons d’actualisation

Les jetons d’actualisation peuvent être révoqués à tout moment, en raison de délais d’attente et de révocations. Votre application doit gérer correctement les rejets par le service de connexion, le cas échéant. Cela s’effectue par l’envoi d’une invite de connexion interactive à l’utilisateur, lequel devra ensuite se reconnecter. 

### <a name="token-timeouts"></a>Délais d’expiration des jetons

Vous ne pouvez pas configurer la durée de vie d’un jeton d’actualisation. Vous ne pouvez pas réduire ou allonger leur durée de vie. Configurez la fréquence de connexion dans l’accès conditionnel pour définir la période de temps avant qu’un utilisateur soit invité à se connecter à nouveau. En savoir plus sur la [configuration de la gestion de session d’authentification avec l’accès conditionnel](../conditional-access/howto-conditional-access-session-lifetime.md).

Tous les jetons d’actualisation ne suivent pas les règles définies dans la stratégie de durée de vie des jetons. En particulier, les jetons d’actualisation utilisés dans les [applications monopages](reference-third-party-cookies-spas.md) ont toujours une durée d’activité limitée à 24 heures, comme si une stratégie `MaxAgeSessionSingleFactor` de 24 heures leur était appliquée. 

### <a name="revocation"></a>Révocation

Les jetons d’actualisation peuvent être révoqués par le serveur en raison d’une modification des informations d’identification, d’une action de l’utilisateur ou d’une action de l’administrateur.  Les jetons d’actualisation se répartissent en deux classes : les jetons émis pour les clients confidentiels (colonne la plus à droite) et les jetons émis pour les clients publics (toutes les autres colonnes).

| Modifier | Cookie basé sur un mot de passe | Jeton basé sur un mot de passe | Cookie non basé sur un mot de passe | Jeton non basé sur un mots de passe | Jeton client confidentiel |
|---|-----------------------|----------------------|---------------------------|--------------------------|---------------------------|
| Le mot de passe expire | Reste actif | Reste actif | Reste actif | Reste actif | Reste actif |
| Mot de passe modifié par l’utilisateur | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’utilisateur effectue SSPR | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’administrateur réinitialise le mot de passe | Révoqué | Révoqué | Reste actif | Reste actif | Reste actif |
| L’utilisateur révoque ses jetons d’actualisation [via PowerShell](/powershell/module/azuread/revoke-azureadsignedinuserallrefreshtoken) | Révoqué | Révoqué | Révoqué | Révoqué | Révoqué |
| L’administrateur révoque tous les jetons d’actualisation d’un utilisateur [via PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) | Révoqué | Révoqué |Révoqué | Révoqué | Révoqué |
| Authentification unique [sur le web](v2-protocols-oidc.md#single-sign-out) | Révoqué | Reste actif | Révoqué | Reste actif | Reste actif |

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur les [durées de vie des jetons configurables](active-directory-configurable-token-lifetimes.md)
* Pour plus d’informations sur les jetons d’actualisation principaux, consultez [Jetons d’actualisation principaux](../devices/concept-primary-refresh-token.md).
