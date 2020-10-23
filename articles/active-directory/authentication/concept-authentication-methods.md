---
title: 'Méthodes d’authentification et fonctionnalités : Azure Active Directory'
description: En savoir plus sur les différentes méthodes et fonctionnalités d’authentification disponibles dans Microsoft Azure Active Directory pour contribuer à l’amélioration et à la sécurisation des événements de connexion
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: c5ae0e0d312aa9a959b114d576f887bfa5072f49
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91965503"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Quelles sont les méthodes d’authentification et de vérification disponibles dans Microsoft Azure Active Directory ?

Dans le cadre de l’expérience de connexion pour les comptes Microsoft Azure Active Directory (Azure AD), un utilisateur peut s’authentifier de différentes manières. Un nom d’utilisateur et un mot de passe sont les moyens les plus courant pour qu’un utilisateur fournisse des informations d’identification. Grâce aux fonctionnalités d’authentification et de sécurité modernes d’Azure AD, ce mot de passe de base peut être complété ou remplacé par des méthodes d’authentification plus sécurisées.

![Tableau des avantages et des méthodes d’authentification préférées dans Azure AD](media/concept-authentication-methods/authentication-methods.png)

Les méthodes d’authentification sans mot de passe telles que Windows Hello, les clés de sécurité FIDO2 et l’application Microsoft Authenticator permettent les événements de connexion les plus sécurisés.

Azure Multi-Factor Authentication renforce la sécurité lorsqu’un utilisateur utilise un seul mot de passe pour se connecter. L’utilisateur peut être invité à fournir des formes d’authentification supplémentaires, par exemple répondre à une notification push, entrer un code à partir d’un jeton logiciel ou matériel, ou répondre à un SMS ou à un appel téléphonique.

Pour simplifier l’expérience d’intégration des utilisateurs et s’inscrire pour MFA et SSPR, nous vous recommandons d'[activer l’inscription d’informations de sécurité combinée](howto-registration-mfa-sspr-combined.md). À des fins de résilience, nous vous recommandons de demander aux utilisateurs d’enregistrer plusieurs méthodes d’authentification. Lorsqu’une méthode n’est pas disponible pour un utilisateur lors d’une connexion ou SSPR, il peut choisir de s’authentifier avec une autre méthode. Pour plus d’informations, consultez [Créer une stratégie de gestion du contrôle d’accès résiliente dans Azure AD](concept-resilient-controls.md).

## <a name="authentication-method-strength-and-security"></a>Robustesse et sécurité des méthodes d’authentification

Lorsque vous déployez des fonctionnalités telles qu’Azure Multi-Factor Authentication au sein de votre organisation, passez en revue les méthodes d’authentification disponibles. Optez pour des méthodes qui remplissent ou dépassent vos exigences en termes de sécurité, de facilité d’utilisation et de disponibilité. Dans la mesure du possible, utilisez des méthodes d’authentification avec le niveau de sécurité le plus élevé.

Le tableau suivant décrit les considérations relatives à la sécurité pour les méthodes d’authentification disponibles. La disponibilité indique que l’utilisateur est en mesure d’utiliser la méthode d’authentification, et ne se réfère pas à la disponibilité du service dans Azure AD :

| Méthode d'authentification          | Sécurité | Facilité d'utilisation | Disponibilité |
|--------------------------------|:--------:|:---------:|:------------:|
| Windows Hello Entreprise     | Élevé     | Élevé      | Élevé         |
| Application Microsoft Authenticator    | Élevé     | Élevé      | Élevé         |
| Clés de sécurité FIDO2 (préversion)   | Élevé     | Élevé      | Élevé         |
| Jetons matériels OATH (version préliminaire) | Moyenne   | Moyenne    | Élevé         |
| Jetons logiciels OATH           | Moyenne   | Moyenne    | Élevé         |
| SMS                            | Moyenne   | Élevé      | Moyenne       |
| Voix                          | Moyenne   | Moyenne    | Moyenne       |
| Mot de passe                       | Faible      | Élevé      | Élevé         |

Pour plus d’informations sur la sécurité, consultez [Vulnérabilités d’authentification et vecteurs d’attaque](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124).

> [!TIP]
> Pour plus de flexibilité et de facilité d’utilisation, nous vous recommandons d’utiliser l’application Microsoft Authenticator. Cette méthode d’authentification offre une expérience utilisateur optimale, ainsi que plusieurs modes (authentification sans mot de passe, notifications push MFA et codes OATH, notamment).

## <a name="how-each-authentication-method-works"></a>Fonctionnement de chaque méthode d’authentification

Certaines méthodes d’authentification peuvent être utilisées en tant que facteur principal lorsque vous vous connectez à une application ou un appareil, par exemple à l’aide d’une clé de sécurité FIDO2 ou d’un mot de passe. D’autres méthodes d’authentification sont uniquement disponibles en tant que facteur secondaire lorsque vous utilisez Azure Multi-Factor Authentication ou SSPR.

Le tableau suivant décrit quand une méthode d’authentification peut être utilisée lors d’un événement de connexion :

| Méthode                         | Authentification principale | Authentification secondaire  |
|--------------------------------|:----------------------:|:-------------------------:|
| Windows Hello Entreprise     | Oui                    | MFA                       |
| Application Microsoft Authenticator    | Oui (préversion)          | Authentification multifacteur et réinitialisation de mot de passe en libre-service              |
| Clés de sécurité FIDO2 (préversion)   | Oui                    | MFA                       |
| Jetons matériels OATH (version préliminaire) | Non                     | MFA                       |
| Jetons logiciels OATH           | Non                     | MFA                       |
| SMS                            | Oui (préversion)          | Authentification multifacteur et réinitialisation de mot de passe en libre-service              |
| Appel vocal                     | Non                     | Authentification multifacteur et réinitialisation de mot de passe en libre-service              |
| Mot de passe                       | Oui                    |                           |

Toutes ces méthodes d’authentification peuvent être configurées dans le portail Azure, et de plus en plus à l’aide des [API REST Microsoft Graph bêta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Pour en savoir plus sur le fonctionnement de chaque méthode d’authentification, consultez les articles conceptuels suivants :

* [Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-overview)
* [Application Microsoft Authenticator](concept-authentication-authenticator-app.md)
* [Clés de sécurité FIDO2 (préversion)](concept-authentication-passwordless.md#fido2-security-keys)
* [Jetons matériels OATH (version préliminaire)](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)
* [Jetons logiciels OATH](concept-authentication-oath-tokens.md#oath-software-tokens)
* Connexion et [vérification](howto-authentication-sms-signin.md) par [SMS](concept-authentication-phone-options.md#mobile-phone-verification)
* [Vérification par appel téléphonique](concept-authentication-phone-options.md)
* Mot de passe

> [!NOTE]
> Dans Azure AD, un mot de passe constitue souvent l’une des méthodes d’authentification principales. Vous ne pouvez pas désactiver la méthode d’authentification par mot de passe. Si vous utilisez un mot de passe en tant que facteur d’authentification principal, renforcez la sécurité des événements de connexion à l’aide d’Azure Multi-Factor Authentication.

Les méthodes de vérification supplémentaires suivantes peuvent être utilisées dans certains scénarios :

* [Mots de passe d’application](howto-mfa-app-passwords.md) : utilisés pour les anciennes applications qui ne prennent pas en charge l’authentification moderne et peuvent être configurés pour Azure Multi-Factor Authentication par l’utilisateur.
* [Questions de sécurité](concept-authentication-security-questions.md) : utilisées uniquement pour SSPR
* [Adresse e-mail](concept-sspr-howitworks.md#authentication-methods) : utilisée uniquement pour SSPR

## <a name="next-steps"></a>Étapes suivantes

Pour commencer, consultez le [tutoriel sur la réinitialisation du mot de passe en libre-service][tutorial-sspr] et [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Pour en savoir plus sur les concepts de SSPR, consultez [Fonctionnement de la réinitialisation de mot de passe en libre-service dans Azure AD][concept-sspr].

Pour plus d’informations sur les concepts MFA, consultez [Azure Multi-Factor Authentication : fonctionnement][concept-mfa].

Apprenez-en plus sur la configuration des méthodes d’authentification à l’aide de l’[API REST Microsoft Graph bêta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Pour connaître les méthodes d’authentification en cours d’utilisation, consultez [Analyse de la méthode d’authentification Azure Multi-Factor Authentication avec PowerShell](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
