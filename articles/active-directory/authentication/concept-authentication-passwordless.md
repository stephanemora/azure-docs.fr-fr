---
title: Authentification sans mot de passe pour Azure Active Directory (préversion)
description: Découvrir les options de connexion sans mot de passe à Azure Active Directory à l’aide de clés de sécurité FIDO2 ou de l’application Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d426fb743e6b1ce5d279544f12bcb490d529f9
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/26/2020
ms.locfileid: "76756791"
---
# <a name="passwordless-authentication-options"></a>Options d’authentification sans mot de passe

L’authentification multifacteur (MFA) est un excellent moyen de sécuriser votre organisation, mais les utilisateurs sont souvent déçus de la couche de sécurité supplémentaire en plus de devoir se rappeler leurs mots de passe. Les méthodes d’authentification sans mot de passe sont plus pratiques, car le mot de passe est supprimé et remplacé par quelque chose que vous avez, en plus de quelque chose que vous êtes ou que vous savez.

|   | Quelque chose que vous avez | Quelque chose que vous êtes ou savez |
| --- | --- | --- |
| Sans mot de passe | Appareil Windows 10, téléphone ou clé de sécurité | Biométrie ou code confidentiel |

Chaque organisation a des besoins différents en matière d’authentification. Microsoft propose trois options d’authentification sans mot de passe :

- Windows Hello Entreprise
- Application Microsoft Authenticator
- Clés de sécurité FIDO2

![Authentification : Sécurité et commodité](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello Entreprise

Windows Hello Entreprise est idéal pour les professionnels de l’information qui disposent de leur propre PC Windows. Les données biométriques et le code confidentiel sont directement liés au PC de l’utilisateur ; personne à part son propriétaire ne peut donc y accéder. Avec l’intégration de l’infrastructure à clé publique (PKI)et la prise en charge intégrée de l’authentification unique (SSO), Windows Hello Entreprise propose une méthode d’accès pratique aux ressources d’entreprise localement et dans le cloud.

Le [guide de planification](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello Entreprise peut vous aider à déterminer le type de déploiement de Windows Hello Entreprise dont vous avez besoin, ainsi que les options à prendre en compte.

## <a name="microsoft-authenticator-app"></a>Application Microsoft Authenticator

Autorisez le téléphone de votre employé à devenir une méthode d’authentification sans mot de passe. Vous utilisez peut-être déjà l’application Microsoft Authenticator comme une option pratique d’authentification multifacteur en plus d’un mot de passe. Vous pouvez également utiliser l’application Authenticator comme option sans mot de passe.

![Connectez-vous à Microsoft Edge avec l’application Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

L’application Authenticator transforme n’importe quel téléphone iOS ou Android en informations d’identification fortes et sans mot de passe. Les utilisateurs peuvent se connecter à toute plateforme ou tout navigateur en obtenant une notification sur leur téléphone, en faisant correspondre un numéro affiché sur l’écran à celui affiché sur leur téléphone, puis en utilisant leur leurs données biométriques (toucher ou visage) ou un code confidentiel pour confirmer.

## <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

Les clés de sécurité FIDO2 sont une méthode d’authentification sans mot de passe basée sur une norme. Elles ne peuvent pas être usurpées et peuvent s’afficher dans n’importe quel facteur de forme. Fast Identity Online (FIDO) est une norme ouverte d’authentification sans mot de passe. Elle permet aux utilisateurs et aux organisations de tirer parti de la norme pour se connecter à leurs ressources sans nom d’utilisateur ou mot de passe, en utilisant une clé de sécurité externe ou une clé de plateforme intégrée à un appareil.

Avec la préversion publique, les employés peuvent utiliser des clés de sécurité pour se connecter à leurs appareils Windows 10 joints à Azure AD et bénéficier de l’authentification unique sur leurs ressources cloud et locales. Les utilisateurs peuvent également se connecter aux navigateurs pris en charge. Les clés de sécurité FIDO2 constituent une excellente solution pour les entreprises qui sont très sensibles à la sécurité ou ayant des scénarios ou des employés qui ne sont pas prêts à ou capables d’utiliser leur téléphone comme deuxième facteur.

![Connectez-vous à Microsoft Edge avec une clé de sécurité](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Bien qu’il existe de nombreuses clés FIDO2 certifiées par la FIDO Alliance, Microsoft exige que certaines des extensions facultatives de la spécification FIDO2 CTAP (Client-to-Authenticator Protocol) soient implémentées par le fournisseur, afin de garantir une sécurité maximale et la meilleure expérience possible.

Une clé de sécurité **doit** implémenter les fonctionnalités et extensions du protocole FIDO2 CTAP suivantes pour être compatible avec Microsoft :

| # | Fonctionnalité/Extension de confiance | Pourquoi cette fonctionnalité ou extension est-elle nécessaire ? |
| --- | --- | --- |
| 1 | Clé résidente | Cette fonctionnalité permet à la clé de sécurité d’être portable ; vos informations d’identification sont stockées sur la clé de sécurité. |
| 2 | Code confidentiel client | Cette fonctionnalité vous permet de protéger vos informations d’identification avec un deuxième facteur et s’applique aux clés de sécurité qui n’ont pas d’interface utilisateur. |
| 3 | hmac-secret | Cette extension garantit que vous pouvez vous connecter à votre appareil lorsqu’il est hors connexion ou en mode avion. |
| 4 | Plusieurs comptes par fournisseur de ressources | Cette fonctionnalité garantit que vous pouvez utiliser la même clé de sécurité dans plusieurs services, comme un compte Microsoft et Azure Active Directory. |

Les fournisseurs suivants offrent des clés de sécurité FIDO2 de différents facteurs de forme, qui sont connues pour être compatibles avec l’expérience sans mot de passe. Nous vous encourageons à évaluer les propriétés de sécurité de ces clés en contactant le fournisseur, ainsi que la FIDO Alliance.

| Fournisseur | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |

> [!NOTE]
> Si vous achetez des clés de sécurité NFC et que vous prévoyez de les utiliser, vous devez disposer d’un lecteur NFC pris en charge pour la clé de sécurité. Le lecteur NFC n’est pas une exigence ou une limitation Azure. Pour obtenir la liste des lecteurs NFC pris en charge, contactez le fournisseur de votre clé de sécurité NFC.

Si vous êtes fournisseur et que vous voulez que votre appareil figure dans la liste des appareils pris en charge, contactez [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

## <a name="what-scenarios-work-with-the-preview"></a>Scénarios fonctionnant avec la préversion

- Les administrateurs peuvent activer des méthodes d’authentification sans mot de passe pour leur locataire
- Les administrateurs peuvent cibler tous les utilisateurs ou sélectionner des utilisateurs/groupes au sein de leur locataire pour chaque méthode
- Les utilisateurs finaux peuvent inscrire et gérer ces méthodes d’authentification sans mot de passe sur le portail de leur compte
- Les utilisateurs finaux peuvent se connecter avec ces méthodes d’authentification sans mot de passe
   - Application Microsoft Authenticator : fonctionne dans les scénarios où l’authentification Azure AD est utilisée, notamment sur tous les navigateurs, pendant la configuration de Windows 10 (OOBE) et avec les applications mobiles intégrées sur n’importe quel système d’exploitation.
   - Clés de sécurité : fonctionnent sur l’écran de verrouillage de Windows 10 et des navigateurs web pris en charge, comme Microsoft Edge.

## <a name="next-steps"></a>Étapes suivantes

[Activer les options de clé de sécurité FIDO2 sans mot de passe dans votre organisation](howto-authentication-passwordless-security-key.md)

[Activer les options sans mot de passe par téléphone dans votre organisation](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Liens externes

[FIDO Alliance](https://fidoalliance.org/)

[Spécification de FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
