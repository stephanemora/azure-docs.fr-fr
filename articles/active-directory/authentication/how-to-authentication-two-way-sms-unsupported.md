---
title: Fin de la prise en charge des SMS bidirectionnels – Azure Active Directory
description: Explique comment activer une autre méthode pour les utilisateurs qui continuent d’utiliser des SMS bidirectionnels.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101689026"
---
# <a name="two-way-sms-unsupported"></a>SMS bidirectionnel non pris en charge

Les SMS bidirectionnels pour le serveur Multi-Factor Authentication (MFA) Azure AD, déconseillés dès 2018, ne sont plus pris en charge depuis le 24 février 2021. Les administrateurs doivent activer une autre méthode pour les utilisateurs qui continuent d’utiliser les SMS bidirectionnels.

Les notifications par e-mail et les notifications Service Health sur le portail Azure (toasts du portail) ont été envoyées à des administrateurs désignés les 8 décembre 2020 et 28 janvier 2021. Les alertes sont allées aux rôles RBAC Propriétaire, Copropriétaire, Administrateur et Administrateur de service liés aux abonnements. Si vous avez déjà effectué les étapes ci-après, aucune action n’est nécessaire.

## <a name="required-actions"></a>Actions requises

1. Activez l’application mobile pour vos utilisateurs si ce n’est déjà fait. Pour plus d’informations, consultez [Activer l’authentification d’application mobile avec un serveur MFA](howto-mfaserver-deploy-mobileapp.md).
1. Invitez vos utilisateurs finaux à visiter le [portail utilisateur](howto-mfaserver-deploy-userportal.md) de votre serveur MFA pour activer l’application mobile. L’[application Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) est l’option de vérification recommandée, car elle est plus sécurisée que les SMS bidirectionnels. Pour plus d’informations, consultez [Il est temps de dire au revoir aux méthodes d’authentification par téléphone](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752).
1. Modifiez les paramètres utilisateur pour la méthode d’authentification par défaut de SMS bidirectionnel en application mobile.

## <a name="faq"></a>Questions fréquentes (FAQ)

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>Que se passe-t-il si je ne modifie pas la méthode par défaut de SMS bidirectionnel en application mobile ?
Les SMS bidirectionnels échouent depuis le 24 février 2021. Les utilisateurs voient s’afficher un message d’erreur quand ils tentent de se connecter et de passer l’authentification multifacteur.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Comment faire pour modifier les paramètres utilisateur de SMS bidirectionnel en application mobile ?

Pour modifier les paramètres utilisateur, procédez comme suit :

1. Dans le serveur MFA, filtrez la liste des utilisateurs pour les SMS bidirectionnels.
1. Sélectionnez tous les utilisateurs.
1. Ouvrez la boîte de dialogue Modifier des utilisateurs.
1. Modifiez les utilisateurs de SMS en Application mobile.

   ![Capture d’écran des utilisateurs finaux](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Mes utilisateurs doivent-ils faire quelque chose ? Si oui, comment ?
Oui. Vos utilisateurs finaux doivent visiter le portail utilisateur de votre serveur MFA pour activer l’application mobile si ce n’est déjà fait. À l’issue de l’étape 3, les utilisateurs n’ayant pas visité le portail utilisateur pour configurer l’application mobile ne pourront plus se connecter tant qu’ils n’auront pas visité le portail pour se réinscrire.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>Que se passe-t-il si mes utilisateurs ne peuvent pas installer l’application mobile ? Quelles sont les autres options à leur disposition ?
L’alternative au SMS bidirectionnel ou à l’application mobile est un appel téléphonique. Toutefois, l’application Microsoft Authenticator est la méthode de vérification recommandée.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Les SMS unidirectionnels seront-ils également déconseillés ?
Non, seuls les SMS bidirectionnel sont déconseillés. Pour le serveur MFA, les SMS unidirectionnels fonctionnent dans un sous-ensemble de scénarios :

- Adaptateur AD FS
- Authentification IIS (nécessite le portail utilisateur et une configuration)
- RADIUS (nécessite que les clients RADIUS prennent en charge le challenge d’accès et que le protocole PAP soit utilisé)

En raison de certaines limitations à l’utilisation de SMS unidirectionnels, l’application mobile constitue une meilleure solution, car elle ne nécessite pas d’invite de code de vérification.
Si vous souhaitez continuer d’utiliser des SMS unidirectionnels dans certains scénarios, vous pouvez conserver cette option mais, dans la section **Paramètres de la société**, sous l’onglet **Général**, vous devez modifier l’option **Paramètres par défaut de l’utilisateur pour le SMS** en **Unidirectionnel** au lieu de **Bidirectionnel**. Enfin, si la synchronisation d’annuaires est définie par défaut sur SMS, vous devez la modifier en Unidirectionnel au lieu de Bidirectionnel.

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Comment puis-je vérifier les utilisateurs qui continuent d’utiliser des SMS bidirectionnels ?
Pour afficher la liste de ces utilisateurs, démarrez le **Serveur MFA**, sélectionnez la section **Utilisateurs**, cliquez sur **Filtrer la liste des utilisateurs** puis filtrez sur **Message texte bidirectionnel**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Comment masquer l’option SMS bidirectionnel dans le portail MFA pour empêcher les utilisateurs de la sélectionner à l’avenir ?
Dans le portail utilisateur du serveur MFA, cliquez sur **Paramètres**. Vous pouvez effacer l’option **Message texte** pour la rendre indisponible. Il en va de même dans la section **AD FS** si vous utilisez AD FS pour l’inscription d’utilisateur.

