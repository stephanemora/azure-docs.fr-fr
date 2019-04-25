---
title: Prise en main d’enregistrement combiné pour Azure AD SSPR et une authentification multifacteur (version préliminaire) - Azure Active Directory
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
ms.openlocfilehash: 3baf2690ae07b87bb4d5dba30fcd20f62a1a4506
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60358065"
---
# <a name="enable-combined-security-information-registration-preview"></a>Activer combinée sécurité informations inscription (version préliminaire)

Avant d’activer la nouvelle expérience, consultez l’article [combinée d’inscription des informations de sécurité (version préliminaire)](concept-registration-mfa-sspr-combined.md) afin de bien comprendre les fonctionnalités et les effets de cette fonctionnalité.

![Expérience améliorée de sécurité combinée d’informations d’inscription](media/howto-registration-mfa-sspr-combined/combined-security-info-more-required.png)

|     |
| --- |
| L’inscription d’informations de sécurité combinée pour Azure multi-Factor Authentication et de réinitialisation de mot de passe libre-service d’Azure Active Directory (Azure AD) est une fonctionnalité en préversion publique d’Azure AD. Pour plus d’informations sur les préversions, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

## <a name="enable-combined-registration"></a>Activer une inscription combinée

Suivez ces étapes pour activer l’inscription combinée :

1. Connectez-vous au portail Azure en tant qu’un administrateur de l’utilisateur ou un administrateur global.
2. Accédez à **Azure Active Directory** > **paramètres utilisateur** > **gérer les paramètres des fonctionnalités en version préliminaire de panneau accès**.
3. Sous **les utilisateurs peuvent utiliser les fonctionnalités de l’inscription et la gestion des informations de sécurité en version préliminaire - Actualiser**, choisir d’activer pour un **sélectionnés** groupe d’utilisateurs ou pour **tous les** utilisateurs.

   ![Activer l’expérience de préversion des info de sécurité combinée pour tous les utilisateurs](media/howto-registration-mfa-sspr-combined/combined-security-info-enable.png)

> [!IMPORTANT]
> À compter de mars 2019, les options d’appel téléphonique ne sont pas disponibles pour l’authentification multifacteur et les utilisateurs SSPR dans les locataires gratuit/essai Azure AD. Les messages SMS ne sont pas affectés par cette modification. Les options d’appel téléphonique seront également disponibles pour les utilisateurs de payé des locataires Azure AD.

> [!NOTE]
> Une fois que vous activez combinée d’inscription, les utilisateurs qui inscrivent ou confirmer son numéro de téléphone ou application mobile via la nouvelle expérience peut les utiliser pour l’authentification multifacteur et SSPR, si ces méthodes sont activées dans le multi-Factor Authentication et SSPR stratégies. Si vous désactivez ensuite cette expérience, les utilisateurs qui vont à l’inscription SSPR précédente page à `https:/aka.ms/ssprsetup` devront effectuer l’authentification multifacteur pour accéder à la page.

Si vous avez configuré la liste sites aux zones attribution dans Internet Explorer, les sites suivants doivent être dans la même zone :

* [https://login.microsoftonline.com](https://login.microsoftonline.com)
* [https://mysignins.microsoft.com](https://mysignins.microsoft.com)
* [https://account.activedirectory.windowsazure.com](https://account.activedirectory.windowsazure.com)

## <a name="next-steps"></a>Étapes suivantes

[Méthodes disponibles pour l’authentification multifacteur et SSPR](concept-authentication-methods.md)

[Configurer la réinitialisation du mot de passe libre-service](howto-sspr-deployment.md)

[Configurer l’authentification multifacteur Azure](howto-mfa-getstarted.md)

[Résolution des problèmes combinée d’enregistrement d’informations de sécurité](howto-registration-mfa-sspr-combined-troubleshoot.md)