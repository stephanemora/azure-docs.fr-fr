---
title: Authentification par certificat Android - Azure Active Directory
description: En savoir plus sur les scénarios pris en charge et la configuration requise pour la configuration de l’authentification par certificat dans les solutions avec les appareils Android
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9f38c160a72b6c414cdde7d29e4056a4068cdc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848814"
---
# <a name="azure-active-directory-certificate-based-authentication-on-android"></a>Authentification par certificat Azure Active Directory sur Android

Les appareils Android peuvent utiliser l’authentification basée sur les certificats pour s’authentifier auprès d’Azure Active Directory à l’aide d’un certificat client, lorsqu’ils se connectent à :

* Des applications mobiles Office, telles que Microsoft Outlook et Microsoft Word ;
* Des clients Exchange ActiveSync (EAS).

La configuration de cette fonctionnalité élimine le besoin d’entrer un nom d’utilisateur et un mot de passe dans certaines applications de messagerie et Microsoft Office sur votre appareil mobile.

Cette rubrique vous indique la configuration requise et les scénarios pris en charge pour la configuration de l’authentification par certificat sur un appareil iOS (Android) pour les utilisateurs des locataires des plans Office 365 Enterprise, Business, Education, US Government, Chine et Allemagne.

Cette fonctionnalité est disponible en version préliminaire dans les plans Office 365 US Government Defense et US Government Federal.

## <a name="microsoft-mobile-applications-support"></a>Prise en charge des applications mobiles Microsoft

| Applications | Support |
| --- | --- |
| Application Azure Information Protection |![La coche indique la prise en charge de cette application][1] |
| Portail d’entreprise Intune |![La coche indique la prise en charge de cette application][1] |
| Microsoft Teams |![La coche indique la prise en charge de cette application][1] |
| OneNote |![La coche indique la prise en charge de cette application][1] |
| OneDrive |![La coche indique la prise en charge de cette application][1] |
| Outlook |![La coche indique la prise en charge de cette application][1] |
| Power BI |![La coche indique la prise en charge de cette application][1] |
| Skype Entreprise |![La coche indique la prise en charge de cette application][1] |
| Word / Excel / PowerPoint |![La coche indique la prise en charge de cette application][1] |
| Yammer |![La coche indique la prise en charge de cette application][1] |

### <a name="implementation-requirements"></a>Exigences d’implémentation

La version du système d’exploitation de l’appareil doit être Android 5.0 (Lollipop) ou toute version ultérieure.

Un serveur de fédération doit être configuré.

Pour qu’Azure Active Directory révoque un certificat client, le jeton ADFS doit posséder les déclarations suivantes :

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>` (le numéro de série du certificat client)
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>` (la chaîne de l’émetteur du certificat client)

Azure Active Directory ajoute ces déclarations au jeton d’actualisation si elles sont disponibles dans le jeton ADFS (ou n’importe quel autre jeton SAML). Lorsque le jeton d’actualisation doit être validé, ces informations sont utilisées pour vérifier la révocation.

Une bonne pratique consiste à ajouter les informations suivantes aux pages d’erreurs ADFS de votre entreprise :

* La configuration requise pour l’installation de Microsoft Authenticator sur Android
* Instructions sur l’obtention d’un certificat utilisateur.

Pour plus d’informations, consultez [Personnalisation des pages de connexion AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Certaines applications Office (avec l’authentification moderne activée) envoient « *prompt=login* » à Azure AD dans leur demande. Par défaut, Azure AD traduit « *prompt=login* » dans la requête aux services ADFS en « *wauth=usernamepassworduri* » (demande aux services ADFS d’effectuer l’authentification U/P) et « *wfresh=0* » (demande aux services ADFS d’ignorer l’état d’authentification unique et d’effectuer une nouvelle authentification). Si vous souhaitez activer l’authentification par certificat pour ces applications, vous devez modifier le comportement par défaut d’Azure AD. Définissez « *PromptLoginBehavior* » dans vos paramètres de domaine fédéré sur « *Désactivé* ».
Vous pouvez utiliser l’applet de commande [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) pour effectuer cette tâche :

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`

## <a name="exchange-activesync-clients-support"></a>Prise en charge des clients Exchange ActiveSync

Certaines applications Exchange ActiveSync sur Android 5.0 (Lollipop) ou version ultérieure sont prises en charge. Pour déterminer si votre application de messagerie prend en charge cette fonctionnalité, contactez le développeur de votre application.

## <a name="next-steps"></a>Étapes suivantes

Si vous souhaitez configurer l’authentification par certificat dans votre environnement, consultez [Bien démarrer avec l’authentification par certificat sur Android](active-directory-certificate-based-authentication-get-started.md) pour obtenir des instructions.

<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-android/ic195031.png
