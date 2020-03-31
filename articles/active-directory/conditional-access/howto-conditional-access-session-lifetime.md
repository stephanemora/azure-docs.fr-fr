---
title: Configurer la gestion de la session d’authentification - Azure Active Directory
description: Personnalisez la configuration de la session d’authentification Azure AD, entre autres la fréquence de connexion de l’utilisateur et la persistance de la session de navigateur.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e9c0c88064c00c97de7dc58a500910e81c04eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79230793"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurer la gestion de session d’authentification avec l’accès conditionnel

Dans les déploiements complexes, les organisations peuvent avoir besoin de limiter les sessions d’authentification. Certains scénarios peuvent inclure les éléments suivants :

* Accès aux ressources à partir d’un appareil non managé ou partagé
* Accès à des informations sensibles depuis un réseau externe
* Utilisateurs à fort impact
* Applications métier critiques

Les contrôles d’accès conditionnel permettent de créer des stratégies qui ciblent des cas d’usage particuliers au sein de votre organisation, sans affecter tous les utilisateurs.

Avant de plonger dans les détails sur la façon de configurer la stratégie, examinons la configuration par défaut.

## <a name="user-sign-in-frequency"></a>Fréquence de connexion de l’utilisateur

La fréquence de connexion définit la durée à l’issue de laquelle un utilisateur est invité à se reconnecter lorsqu’il tente d’accéder à une ressource.

La configuration par défaut d’Azure Active Directory (Azure AD) pour la fréquence de connexion utilisateur est une fenêtre cumulative de 90 jours. Demander des informations d’identification aux utilisateurs semble souvent une chose sensée à faire, mais celle-ci peut avoir l’effet inverse que celui prévu : les utilisateurs qui sont habitués à entrer leurs informations d’identification machinalement peuvent involontairement les fournir à une invite de demande d’informations d’identification malveillante.

Il peut paraître alarmant de ne pas demander à un utilisateur de se reconnecter, en réalité toute violation des stratégies informatiques révoquera la session. Certains exemples incluent (mais ne sont pas limités à) une modification de mot de passe, un appareil non conforme ou une désactivation de compte. Vous pouvez aussi explicitement [révoquer les sessions des utilisateurs avec PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). La configuration Azure AD par défaut se résume à « ne pas demander aux utilisateurs de fournir leurs informations d’identification si l’état de la sécurité de leurs sessions n’a pas changé ».

Le paramètre de fréquence de connexion fonctionne avec les applications qui ont implémenté les protocoles OAUTH2 ou OIDC conformément aux standards. La plupart des applications natives de Microsoft pour Windows, Mac et Mobile, notamment les applications web suivantes, sont conformes au paramètre.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Portail d’administration O365
- Exchange Online
- SharePoint et OneDrive
- Client web Teams
- Dynamics CRM en ligne
- Portail Azure

### <a name="user-sign-in-frequency-and-device-identities"></a>Fréquence de connexion des utilisateurs et identités des appareils

Si vous avez des appareils joints à Azure AD, joints à Hybrid Azure AD ou inscrits auprès d’Azure AD, quand un utilisateur déverrouille son appareil ou se connecte de façon interactive, cet événement satisfait également à la stratégie de fréquence de connexion. Dans les 2 exemples suivants, la fréquence de connexion de l’utilisateur est définie sur 1 heure :

Exemple 1 :

- À 00:00, un utilisateur se connecte à son appareil Windows 10 joint à Azure AD et commence à travailler sur un document stocké sur SharePoint Online.
- L’utilisateur continue à travailler au même document sur son appareil pendant une heure.
- À 01:00, l’utilisateur est invité à se reconnecter en fonction de la fréquence de connexion spécifiée dans la stratégie d’accès conditionnel configurée par son administrateur.

Exemple 2 :

- À 00:00, un utilisateur se connecte à son appareil Windows 10 joint à Azure AD et commence à travailler sur un document stocké sur SharePoint Online.
- À 00:30, l’utilisateur se lève et fait une pause en bloquant son appareil.
- À 00:45, l’utilisateur revient de sa pause et déverrouille l’appareil.
- À 01:45, l’utilisateur est invité à se reconnecter en fonction de la fréquence de connexion spécifiée dans la stratégie d’accès conditionnel configurée par son administrateur, car la dernière connexion s’est faite à 00:45.

## <a name="persistence-of-browsing-sessions"></a>Persistance des sessions de navigation

Une session de navigateur persistante permet aux utilisateurs de rester connectés après la fermeture et la réouverture de la fenêtre du navigateur.

La valeur par défaut d’Azure AD pour la persistance de la session du navigateur permet aux utilisateurs se servant d’appareils personnels de choisir s’il faut conserver la session en affichant une invite « Restez connecté ? » après une authentification réussie. Si la persistance du navigateur est configurée dans AD FS en suivant les instructions de l’article [Paramètres de l’authentification unique AD FS](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), nous allons nous conformer à cette stratégie et conserver également la session Azure AD. Vous pouvez également définir si les utilisateurs de votre locataire voient ou non l’invite « Restez connecté ? » en modifiant le paramètre approprié dans le volet de la marque de société du portail Azure. Pour cela, suivez les instructions dans l’article [Personnaliser votre page de connexion Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configuration des contrôles de la session d’authentification

L’accès conditionnel est une fonctionnalité d’Azure AD Premium et nécessite une licence Premium. Pour plus d’informations sur l’accès conditionnel, voir [Qu’est-ce que l’accès conditionnel dans Azure Active Directory ?](overview.md#license-requirements)

> [!WARNING]
> Si vous utilisez la fonctionnalité de [durée de vie de jeton configurable](../develop/active-directory-configurable-token-lifetimes.md) actuellement en préversion publique, veuillez noter que nous ne prenons pas en charge la création de deux stratégies différentes pour la même combinaison d’utilisateur ou d’application : une avec cette fonctionnalité, l’autre avec une fonctionnalité de durée de vie de jeton configurable. Microsoft prévoit de mettre hors service la fonctionnalité de durée de vie de jeton configurable le 1e mai 2020, et de la remplacer par la fonctionnalité de gestion de session d’authentification par accès conditionnel.  

### <a name="policy-1-sign-in-frequency-control"></a>Stratégie 1 : Contrôle de la fréquence de connexion

1. Créez une stratégie.
1. Choisissez toutes les conditions nécessaires à l’environnement du client, y compris les applications de cloud cibles.

   > [!NOTE]
   > Il est recommandé de définir une fréquence d’invite d’authentification égale pour les applications Microsoft Office clés, comme Exchange Online et SharePoint Online, afin d’offrir une meilleure expérience utilisateur.

1. Accédez à **Contrôles d’accès** > **Session** et cliquez sur **Fréquence de connexion**.
1. Entrez la valeur voulue de jours et d’heures dans la première zone de texte.
1. Sélectionnez une valeur d’**Heures** ou de **Jours** à partir de la liste déroulante.
1. Enregistrez votre stratégie.

![Stratégie d’accès conditionnel configurée pour la fréquence de connexion](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Sur les appareils Windows inscrits dans Azure AD, connectez-vous à l’appareil qui est considéré invite. Par exemple, si vous avez configuré la Fréquence de connexion sur 24 heures pour les applications Office, les utilisateurs sur les appareils Windows inscrits dans Azure AD satisferont à la stratégie de fréquence de connexion en se connectant à l’appareil, et ils ne seront donc pas invités à le faire de nouveau lors de l’ouverture des applications Office.

Si vous avez configuré une autre fréquence de connexion pour différentes applications web qui s’exécutent dans la même session de navigateur, la stratégie la plus stricte sera appliquée aux deux applications, car toutes les applications en cours d’exécution dans la même session de navigateur partagent un jeton de session unique.

### <a name="policy-2-persistent-browser-session"></a>Stratégie 2 : Session de navigateur persistante

1. Créez une stratégie.
1. Choisissez toutes les conditions nécessaires.

   > [!NOTE]
   > Veuillez noter que ce contrôle demande de choisir « Toutes les applications Cloud » en tant que condition. La persistance de la session de navigateur est contrôlée par le jeton de session d’authentification. Tous les onglets d’une session de navigateur partagent un jeton de session unique, ils doivent donc tous partager l’état de persistance.

1. Accédez à **Contrôles d’accès** > **Session** et cliquez sur **Session de navigateur persistante**
1. Sélectionnez une valeur dans la liste déroulante.
1. Enregistrez votre stratégie.

![Stratégie d’accès conditionnel configurée pour le navigateur persistant](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> La configuration de la session de navigateur persistante dans l’accès conditionnel Azure AD remplacera le paramètre « Restez connecté ? » dans le volet de la marque de société du portail Azure, pour le même utilisateur, si vous avez configuré les deux stratégies.

## <a name="validation"></a>Validation

Utilisez l’outil What-If (Scénarios) pour simuler une connexion de l’utilisateur vers l’application cible et d’autres conditions en fonction de la configuration de votre stratégie. Les contrôles de gestion de session d’authentification s’affichent dans le résultat de l’outil.

![Résultats de l’outil « What If » pour l’accès conditionnel](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Déploiement de stratégie

Pour vous assurer que votre stratégie fonctionne comme prévu, la meilleure pratique recommandée consiste à la tester avant de la déployer en production. Dans l’idéal, utilisez un locataire de test pour vérifier si votre nouvelle stratégie fonctionne comme prévu. Pour plus d’informations, consultez l’article [Bonnes pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour savoir comment configurer une stratégie d’accès conditionnel, consultez l’article [Exiger une authentification multifacteur (MFA) pour des applications spécifiques disposant d’un accès conditionnel à Azure Active Directory](app-based-mfa.md).
* Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez l’article [Bonnes pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).
