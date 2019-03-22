---
title: Prise en main d’enregistrement combiné pour Azure AD SSPR et MFA (version préliminaire) - Azure Active Directory
description: Activer combinées à l’authentification multifacteur Azure AD et l’inscription (version préliminaire) de réinitialisation de mot de passe libre-service
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25450d49fd32adf12ac6c8a71671a9cb796b06c4
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317440"
---
# <a name="enable-combined-security-information-registration-preview"></a>Activer combinée sécurité informations inscription (version préliminaire)

Avant d’activer la nouvelle expérience, consultez l’article [combinée d’inscription des informations de sécurité (version préliminaire)](concept-registration-mfa-sspr-combined.md) afin de bien comprendre les fonctionnalités et l’impact de cette fonctionnalité.

![L’inscription d’informations de sécurité combinée améliorer l’expérience demande plus d’informations lors de la connexion. Exemple illustre l’inscription d’application Microsoft Authenticator comme première méthode.](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| L’enregistrement d’informations de sécurité combinée pour la réinitialisation du mot de passe libre-service Azure multi-Factor Authentication et Azure AD est une fonctionnalité en préversion publique d’Azure Active Directory. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="enable-combined-registration"></a>Activer une inscription combinée

Procédez comme suit pour activer l’inscription combinée :

1. Connectez-vous au portail Azure en tant qu’un administrateur de l’utilisateur ou un administrateur global.
2. Accédez à **Azure Active Directory** > **Paramètres utilisateur** > **Gérer les paramètres des fonctionnalités préliminaires du volet d’accès**.
3. Sous **les utilisateurs peuvent utiliser les fonctionnalités de l’inscription et la gestion des informations de sécurité en version préliminaire - Actualiser**, choisir d’activer pour un **sélectionnés** groupe d’utilisateurs ou pour **tous les** utilisateurs.

![Activer l’expérience de préversion des info de sécurité combinée pour tous les utilisateurs dans le portail Azure AD](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> À compter de mars de 2019 les options d’appel téléphonique ne sera pas disponible aux utilisateurs MFA et SSPR dans les locataires gratuit/essai Azure AD. Les messages SMS ne sont pas affectées par cette modification. Appel téléphonique continueront à être disponibles pour les utilisateurs dans payé des locataires Azure AD. Cette modification affecte uniquement les locataires gratuit/essai Azure AD.

> [!NOTE]
> Une fois que vous allez autoriser combinée d’inscription, les utilisateurs qui inscrivent ou vérifier que leur numéro de téléphone ou d’une application mobile via la nouvelle expérience pouvez les utiliser pour l’authentification Multifacteur et SSPR, si ces méthodes sont activées dans les stratégies d’authentification Multifacteur et SSPR. Si vous désactivez ensuite cette expérience, les utilisateurs qui vont à l’inscription SSPR précédente page à `https:/aka.ms/ssprsetup` devront effectuer l’authentification multifacteur pour accéder à la page.

Si vous avez configuré la liste sites aux zones attribution dans Internet Explorer, les sites suivants doivent être dans la même zone :

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Étapes suivantes

[Méthodes disponibles pour l’authentification Multifacteur et SSPR](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe libre-service](howto-sspr-deployment.md)

[Configurer l’authentification multifacteur Azure](howto-mfa-getstarted.md)

[Résolution des problèmes combinée d’enregistrement d’informations de sécurité](howto-registration-mfa-sspr-combined-troubleshoot.md)