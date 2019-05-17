---
title: Configurer la gestion de session d’authentification avec l’accès conditionnel Azure Active Directory
description: Personnaliser la configuration de session d’authentification Azure AD, y compris l’authentification utilisateur dans la persistance de session de fréquence et de navigateur.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45ea34a673b46dda2ec9606952c707a13d1b72d9
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65766869"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configurer la gestion de session d’authentification avec l’accès conditionnel

Dans les déploiements complexes, les organisations peuvent avoir besoin pour limiter les sessions d’authentification. Certains scénarios peuvent inclure :

* Accès aux ressources à partir d’un appareil non géré ou partagé
* Accès à des informations sensibles à partir d’un réseau externe
* Utilisateurs ayant un impact élevé
* Applications métier critiques

Contrôles d’accès conditionnel permettent de créer des stratégies qui ciblent des cas d’usage spécifiques au sein de votre organisation sans affecter tous les utilisateurs.

Avant de plonger dans les détails sur la façon de configurer la stratégie, nous allons examiner la configuration par défaut.

## <a name="user-sign-in-frequency"></a>Fréquence de connexion de l’utilisateur

Fréquence de la connexion définit la période de temps avant que l’utilisateur est invité à se connecter à nouveau lorsque vous tentez d’accéder à une ressource.

La configuration par défaut de Azure Active Directory (Azure AD) pour la connexion utilisateur fréquence est une fenêtre cumulative de 90 jours. Demander aux utilisateurs pour les informations d’identification souvent semble être une chose de raisonnable à faire, mais il peut se retourner contre : les utilisateurs qui sont formés à entrer leurs informations d’identification sans réflexion peut involontairement les fournissent à une invite d’informations d’identification malveillant.

Il peut sembler alarmer ne pas demander d’un utilisateur à se reconnecter pendant 90 jours, en réalité que toute violation des stratégies informatiques révoquent la session. Certains exemples incluent (mais ne sont pas limitées à) une modification de mot de passe, un appareil conforme ou compte désactiver. Vous pouvez également explicitement [révoquer les sessions des utilisateurs à l’aide de PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). La configuration par défaut de Azure AD dépend de « ne me demander aux utilisateurs de fournir leurs informations d’identification si l’état de sécurité de leurs sessions n’a pas changé ».

Fréquence de connexion paramètre fonctionne avec les applications qui ont implémenté des protocoles OATH2 ou OIDC selon les normes. La plupart des applications natives de Microsoft pour Windows, Mac et mobiles conforme avec le paramètre.

## <a name="persistence-of-browsing-sessions"></a>Persistance des sessions de navigation

Une session de navigateur persistant permet aux utilisateurs de rester connecté après la fermeture et réouverture de la fenêtre du navigateur.

La valeur par défaut de Azure AD pour la persistance de session de navigateur permet aux utilisateurs sur les appareils personnels à choisir s’il faut conserver la session en affichant un « Restez connecté ? » invite après une authentification réussie. Si la persistance de navigateur est configuré dans AD FS en utilisant les instructions dans l’article [AD FS Single Sign-On Settings](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), nous conformer à cette stratégie et conserver Azure session AD également. Vous pouvez également définir si les utilisateurs de votre client voir le « restez connecté ? » invite en modifiant le paramètre approprié dans le volet du portail Azure en utilisant les instructions dans l’article de la société [personnaliser votre page de connexion Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configuration des contrôles de session d’authentification

Accès conditionnel est une fonctionnalité d’Azure AD Premium et requiert une licence premium. Pour plus d’informations sur l’accès conditionnel, voir [Présentation de l’accès conditionnel dans Azure Active Directory](overview.md#license-requirements-for-using-conditional-access).

> [!WARNING]
> Si vous utilisez le [les durées de vie de jeton configurables](../develop/active-directory-configurable-token-lifetimes.md) fonctionnalité actuellement en version préliminaire publique, veuillez noter que nous ne prennent en charge deux stratégies différentes pour la même combinaison de l’utilisateur ou une application de création : une avec cette fonctionnalité et un autre avec fonctionnalité de durée de vie de jeton configurable. Microsoft prévoit de mettre hors service de la fonctionnalité de durée de vie de jeton configurable le 15 octobre et remplacez-la par la fonctionnalité de gestion de session d’authentification accès conditionnel.  

### <a name="policy-1-sign-in-frequency-control"></a>Stratégie 1 : Contrôle de fréquence de la connexion

1. Créer une nouvelle stratégie
1. Choisissez toutes les conditions d’environnement du client, y compris les applications de cloud cible.

   > [!NOTE]
   > Il est recommandé de définir la fréquence d’invite d’authentification égal pour les applications Microsoft Office clées comme Exchange Online et SharePoint Online pour une meilleure expérience utilisateur.

1. Accédez à **contrôles d’accès** > **Session** et cliquez sur **fréquence de la connexion**
1. Entrez la valeur requise de jours et heures dans la première zone de texte
1. Sélectionnez une valeur de **heures** ou **jours** à partir de la liste déroulante
1. Enregistrer la stratégie

![Stratégie d’accès conditionnel configuré pour la connexion de fréquence](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Sur Azure AD inscrite Windows appareils se connecter à l’appareil est considéré comme une invite de commandes. Par exemple, si vous avez configuré la connexion de la fréquence à 24 heures pour les applications Office, les utilisateurs sur Azure AD inscrits Windows appareils satisfont la stratégie de fréquence de connexion en vous connectant à l’appareil et ne seront pas invités à nouveau lors de l’ouverture des applications Office.

Si vous avez configuré la fréquence de connexion différente pour les applications web différent qui s’exécutent dans la même session de navigateur, la stratégie plus stricte est appliquée aux deux applications, car toutes les applications en cours d’exécution dans la même session de navigateur partagent un jeton de session unique.

### <a name="policy-2-persistent-browser-session"></a>Stratégie 2 : Session de navigateur persistant

1. Créer une nouvelle stratégie
1. Choisissez toutes les conditions requises.

   > [!NOTE]
   > Veuillez noter que ce contrôle nécessite de choisir « Toutes les applications Cloud » en tant que condition. Persistance de session de navigateur est contrôlé par le jeton de session d’authentification. Tous les onglets dans une session de navigateur partagent un jeton de session unique et par conséquent, tous doivent partager l’état de persistance.

1. Accédez à **contrôles d’accès** > **Session** et cliquez sur **session de navigateur persistant**
1. Sélectionnez une valeur dans la liste déroulante
1. Enregistrer la stratégie

![Stratégie d’accès conditionnel configuré pour le navigateur persistant](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Configuration de Session de navigateur persistante dans l’accès conditionnel Azure AD va remplacer le « restez connecté ? » la définition de la personnalisation volet dans le portail Azure pour l’utilisateur même si vous avez configuré les deux stratégies d’entreprise.

## <a name="validation"></a>Validation

Utilisez l’outil de simulation pour simuler une connexion à partir de l’utilisateur à l’application cible et d’autres conditions en fonction de la configuration de votre stratégie. Les contrôles de gestion de session d’authentification s’affichent dans le résultat de l’outil.

![Que se passe-t-il si résultats de l’outil de l’accès conditionnel](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Déploiement de stratégie

Pour vous assurer que votre stratégie fonctionne comme prévu, la meilleure pratique recommandée consiste à la tester avant de la déployer en production. Dans l’idéal, utilisez un locataire de test pour vérifier si votre nouvelle stratégie fonctionne comme prévu. Pour plus d’informations, consultez l’article [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Étapes suivantes

* Si vous souhaitez savoir comment configurer une stratégie d’accès conditionnel, consultez l’article [requièrent une authentification Multifacteur pour des applications spécifiques avec un accès conditionnel Azure Active Directory](app-based-mfa.md).
* Si vous êtes prêt à configurer des stratégies d’accès conditionnel pour votre environnement, consultez l’article [meilleures pratiques pour l’accès conditionnel dans Azure Active Directory](best-practices.md).
