---
title: Atteindre le niveau AAL2 NIST avec Azure Active Directory
description: Conseils pour atteindre le niveau d’assurance 2 (AAL2) de l’authentificateur NIST avec Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: barbaraselden
ms.author: baselden
manager: mtillman
ms.reviewer: martinco
ms.date: 4/26/2021
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 168ea35d32a02d512ef088ace098d2938a57c022
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889757"
---
# <a name="achieve-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Atteindre le niveau d’assurance 2 de l’authentificateur NIST avec Azure Active Directory

L’institut NIST (National Institute of Standards and Technology) développe des spécifications techniques pour les agences fédérales américaines qui implémentent des solutions d’identité. Les organisations travaillant avec les agences fédérales ont également l'obligation de répondre à ces spécifications. 

Avant de tenter d’atteindre le niveau d’assurance 2 (AAL2) de l’authentificateur, il est utile de consulter les ressources suivantes :
* [Présentation du NIST](nist-overview.md) : Comprendre les différents niveaux AAL.
* [Principes fondamentaux de l’authentification](nist-authentication-basics.md) : Terminologie importante et types d’authentification.
* [Types d’authentificateurs NIST](nist-authenticator-types.md) : Comprendre les différents types d’authentificateurs.
* [Niveaux AAL du NIST](nist-about-authenticator-assurance-levels.md) : Présentation des composants des niveaux AAL et des méthodes d’authentification Azure AD (Azure Active Directory) correspondantes.

## <a name="permitted-authenticator-types"></a>Types d’authentificateurs autorisés

Le tableau suivant détaille les types d’authentificateurs autorisés pour le niveau AAL2 :

| Méthode d'authentification Azure AD| Type d'authentificateur NIST | 
| - | - |
| **Méthodes recommandées** |   | 
| Application Microsoft Authenticator pour iOS (sans mot de passe)<br>Windows Hello Entreprise avec le module TPM logiciel | Logiciel de chiffrement multifacteur |
| Clé de sécurité FIDO 2<br>Application Microsoft Authenticator pour Android (sans mot de passe)<br>Windows Hello Entreprise avec module TPM matériel<br>Smartcard (Services de fédération Active Directory) | Matériel de chiffrement multifacteur |
| **Autres méthodes** |  |
| Mot de passe + Téléphone (SMS) | Secret mémorisé + Hors bande |
| Mot de passe + Application Microsoft Authenticator (OTP)<br>Mot de passe + SF OTP | Secret mémorisé + Mot de passe monofacteur à usage unique |
| Mot de passe + Jonction Azure AD avec module TPM logiciel <br>Mot de passe + Appareil mobile conforme<br>Mot de passe + Jonction Azure AD Hybride avec module TPM logiciel <br>Mot de passe + Application Microsoft Authenticator (notification) | Secret mémorisé + Logiciel de chiffrement monofacteur |
| Mot de passe + Jonction Azure AD avec module TPM matériel <br>Mot de passe + Jonction Azure AD Hybride avec module TPM matériel | Secret mémorisé + Matériel de chiffrement monofacteur |


### <a name="our-recommendations"></a>Nos recommandations

Pour atteindre le niveau AAL2, utilisez des authentificateurs matériels ou logiciels de chiffrement multifacteur. L’authentification sans mot de passe élimine la plus grande surface d’attaque (à savoir le mot de passe) et offre aux utilisateurs une méthode d’authentification simplifiée. 

Pour obtenir des instructions détaillées sur la sélection d’une méthode d’authentification sans mot de passe, consultez [Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Pour plus d’informations sur l’implémentation de Windows Hello Entreprise, consultez le [Guide de déploiement de Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validation FIPS 140

Les sections suivantes décrivent comment obtenir la validation FIPS 140.

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement validé global FIPS 140 de niveau 1 Windows pour toutes ses opérations de chiffrement liées à l’authentification. Il s’agit donc d’un vérificateur conforme à la norme FIPS 140, comme l’exigent les administrations publiques.

### <a name="authenticator-requirements"></a>Exigences liées à l’authentificateur

Les authentificateurs de chiffrement des administrations publiques doivent obtenir une validation globale FIPS 140 de niveau 1. Cette validation n’est pas requise pour les autres types d’organisations. Les authentificateurs Azure AD suivants répondent à la configuration requise lorsqu’ils sont exécutés sur [Windows en mode de fonctionnement approuvé FIPS 140](/windows/security/threat-protection/fips-140-validation) :

* Mot de passe

* Jonction Azure AD avec module TPM logiciel ou matériel

* Jonction Azure AD Hybride avec module TPM logiciel ou matériel

* Windows Hello Entreprise avec module TPM logiciel ou matériel

* Smartcard (Services de fédération Active Directory) 

L’application Microsoft Authenticator utilise le chiffrement approuvé FIPS 140 quel que soit son mode de fonctionnement (notification, OTP et sans mot de passe), mais elle n’a pas la validation FIPS 140 de niveau 1.

Les fournisseurs de clés de sécurité FIDO2 sont chacun à des étapes différentes de leur certification FIPS, et certains d’entre eux ont terminé le processus de validation. Nous vous recommandons de consulter la [liste des fournisseurs de clés FIDO2 pris en charge](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) et de vérifier auprès de votre fournisseur l’état actuel de leur validation FIPS.


## <a name="reauthentication"></a>Réauthentification 

Au niveau AAL2, NIST requiert une réauthentification toutes les 12 heures, quelle que soit l’activité de l’utilisateur. La réauthentification est également requise après une période d’inactivité de 30 minutes ou plus. La présentation d’un élément que vous connaissez ou d’un élément vous concernant est requise, car le secret de session est une donnée personnelle.

Pour répondre à la configuration requise à des fins de réauthentification, quelle que soit l’activité de l’utilisateur, Microsoft recommande de configurer la [fréquence de connexion de l’utilisateur](../conditional-access/howto-conditional-access-session-lifetime.md) sur 12 heures. 

NIST autorise également l’utilisation de contrôles de compensation pour confirmer la présence de l’abonné :

* Vous pouvez définir un délai d’inactivité de session de 30 minutes en verrouillant l’appareil au niveau du système d’exploitation à l’aide de Microsoft System Center Configuration Manager, d’objets de stratégie de groupe (GPO) ou d’Intune. Vous devez également exiger une authentification locale pour que l’abonné le déverrouille.

* Le délai d’expiration de l’activité peut être atteint en exécutant une tâche planifiée (avec Configuration Manager, GPO ou Intune) qui verrouille l’ordinateur au bout de 12 heures d’inactivité, quelle que soit l’activité.

## <a name="man-in-the-middle-resistance"></a>Résistance aux attaques man-in-the-middle (MitM) 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé authentifié afin de résister aux attaques MitM, également appelées attaques de l'intercepteur ou attaques de l’homme du milieu. Cela répond aux exigences de résistance aux attaques MitM des niveaux AAL1, AAL2 et AAL3.

## <a name="replay-resistance"></a>Résistance à la réexécution

Toutes les méthodes d’authentification Azure AD du niveau AAL2 utilisent des nonces ou des défis. Les méthodes offrent une bonne résistance aux attaques par relecture, car le vérificateur détecte facilement les transactions d’authentification relues. En effet, ces transactions ne contiennent pas de valeurs nonce ni de données de chronologie appropriées.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md)