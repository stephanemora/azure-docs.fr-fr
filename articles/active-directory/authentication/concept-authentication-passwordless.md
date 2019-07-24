---
title: Authentification sans mot de passe pour Azure Active Directory (préversion)
description: Authentification sans mot de passe pour Azure AD à l’aide de clés de sécurité FIDO2 ou de l’application Microsoft Authenticator (préversion)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d80b359be0a6249327ba1ba1d51ffbc330bb073
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711966"
---
# <a name="what-is-passwordless"></a>Présentation de l’authentification sans mot de passe

L’authentification multifacteur (MFA) est un excellent moyen de sécuriser votre organisation, mais les utilisateurs se lasseront de la couche supplémentaire en plus de devoir se souvenir de leurs mots de passe. Les méthodes d’authentification sans mot de passe sont plus pratiques, car le mot de passe est supprimé et remplacé par quelque chose que vous avez et quelque chose que vous savez.

|   | Quelque chose que vous avez | Quelque chose que vous êtes ou savez |
| --- | --- | --- |
| Sans mot de passe | Téléphone ou clé de sécurité | Biométrie ou code confidentiel |

Nous reconnaissons que chaque organisation a des besoins différents lorsqu’il s’agit de l’authentification. Microsoft propose actuellement Windows Hello, notre première expérience sans mot de passe pour les PC Windows. Nous avons ajouté de nouvelles informations d’identification pour la famille sans mot de passe : L’application Microsoft Authenticator les clés de sécurité FIDO2.

## <a name="microsoft-authenticator-app"></a>Application Microsoft Authenticator

Autorisez le téléphone de votre employé à devenir une méthode d’authentification sans mot de passe. Vous utilisez peut-être déjà l’application Microsoft Authenticator comme une option pratique d’authentification multifacteur en plus d’un mot de passe. Elle est désormais disponible en tant qu’option sans mot de passe.

Elle transforme n’importe quel téléphone iOS ou Android en méthode d’identification forte et sans mot de passe permettant aux utilisateurs de se connecter à toute plateforme ou tout navigateur en obtenant une notification sur leur téléphone, en faisant correspondre un numéro affiché sur l’écran à celui affiché sur le téléphone puis en utilisant leurs données biométriques (toucher ou visage) ou un code confidentiel pour confirmer.

## <a name="fido2-security-keys"></a>Clés de sécurité FIDO2

Les clés de sécurité FIDO2 sont une méthode d’authentification sans mot de passe basée sur une norme. Elles ne peuvent pas être usurpées et peuvent s’afficher dans n’importe quel facteur de forme. Fast Identity Online (FIDO) est une norme ouverte d’authentification sans mot de passe. Elle permet aux utilisateurs et aux organisations de tirer parti de la norme pour se connecter à leurs ressources sans nom d’utilisateur ou mot de passe, à l’aide d’une clé de sécurité externe ou d’une clé de plateforme intégrée à un appareil.

Avec la préversion publique, les employés peuvent utiliser des clés de sécurité externes pour se connecter à leurs machines Windows 10 (version 1809 ou ultérieure) jointes à Azure Active Directory et bénéficier de l’authentification unique pour leurs ressources cloud. Ils peuvent également se connecter sur les navigateurs pris en charge.

Bien qu’il existe de nombreuses clés FIDO2 certifiées par la FIDO Alliance, Microsoft exige que certaines des extensions facultatives de la spécification FIDO2 CTAP soient implémentées par le fournisseur afin de garantir une sécurité maximale et la meilleure expérience possible.

Une clé de sécurité **doit** implémenter les fonctionnalités et extensions du protocole FIDO2 CTAP suivantes pour être compatible avec Microsoft :

| # | Fonctionnalité/Extension de confiance | Pourquoi est-ce nécessaire ? |
| --- | --- | --- |
| 1 | Clé résidente | Cette fonctionnalité permet à la clé de sécurité d’être portable ; vos informations d’identification sont stockées sur la clé de sécurité. |
| 2 | Code confidentiel client | Cette fonctionnalité vous permet de protéger vos informations d’identification avec un deuxième facteur et s’applique aux clés de sécurité qui n’ont pas d’interface utilisateur. |
| 3 | hmac-secret | Cette extension garantit que vous pouvez vous connecter à votre appareil lorsqu’il est hors connexion ou en mode avion. |
| 4 | Plusieurs comptes par fournisseur de ressources | Cette fonctionnalité garantit que vous pouvez utiliser la même clé de sécurité dans plusieurs services, comme un compte Microsoft et Azure Active Directory. |

Les fournisseurs suivants offrent des clés de sécurité FIDO2 de différents facteurs de forme qui sont connues pour être compatibles avec l’expérience sans mot de passe. Microsoft encourage les clients à évaluer les propriétés de sécurité de ces clés en contactant le fournisseur, ainsi que la FIDO Alliance.

| Fournisseur | Contact |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| HID | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Ensurity | [https://ensurity.com/contact-us.html](https://ensurity.com/contact-us.html) |
| eWBM | [https://www.ewbm.com/page/sub1_5](https://www.ewbm.com/page/sub1_5) |

Si vous êtes un fournisseur et que vous voulez que votre appareil figure dans cette liste, contactez [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com).

Les clés de sécurité FIDO2 constituent une excellente solution pour les entreprises qui sont très sensibles à la sécurité ou ayant des scénarios ou des employés qui ne sont pas prêts à ou capables d’utiliser leur téléphone comme deuxième facteur.

## <a name="what-scenarios-work-with-the-preview"></a>Scénarios fonctionnant avec la préversion

1. Les administrateurs peuvent activer des méthodes d’authentification sans mot de passe pour leur locataire
1. Les administrateurs peuvent cibler tous les utilisateurs ou sélectionner des utilisateurs/groupes au sein de leur locataire pour chaque méthode
1. Les utilisateurs finaux peuvent inscrire et gérer ces méthodes d’authentification sans mot de passe sur le portail de leur compte
1. Les utilisateurs finaux peuvent se connecter avec ces méthodes d’authentification sans mot de passe
   1. Application Microsoft Authenticator : Fonctionnera dans chaque scénario dans lequel l’authentification Azure AD est utilisée, y compris sur tous les navigateurs, pendant la configuration de Windows 10 (OOBE) et avec les applications mobiles intégrées sur n’importe quel système d’exploitation.
   1. Clés de sécurité : Fonctionnent sur l’écran de verrouillage Windows 10 version 1809 ou ultérieure et les navigateurs web pris en charge, comme Microsoft Edge.

## <a name="next-steps"></a>Étapes suivantes

[Activer les options sans mot de passe dans votre organisation](howto-authentication-passwordless-enable.md)

### <a name="external-links"></a>Liens externes

[FIDO Alliance](https://fidoalliance.org/)

[Spécification de FIDO2 CTAP](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)
