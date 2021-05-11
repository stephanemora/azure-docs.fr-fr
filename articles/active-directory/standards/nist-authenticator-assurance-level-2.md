---
title: Atteindre le niveau AAL2 NIST avec Azure Active Directory
description: Conseils pour atteindre le niveau d’assurance 2 (AAL 2) de l’authentificateur NIST avec Azure Active Directory.
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
ms.openlocfilehash: aaa40141eaa0617c34a0ae8c4a7cf396d624c1b6
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293985"
---
# <a name="achieving-nist-authenticator-assurance-level-2-with-azure-active-directory"></a>Atteindre le niveau d’assurance 2 de l’authentificateur NIST avec Azure Active Directory

Le NIST (National Institute of Standards and Technology) développe les exigences techniques qui permettent aux agences fédérales américaines d'implémenter des solutions d'identité. Les organisations travaillant avec les agences fédérales ont également l'obligation de satisfaire ces exigences. Cet article explique comment atteindre le niveau AAL 2 NIST. 

Ressources que vous souhaiterez peut-être consulter avant d’atteindre le niveau AAL 2 :
* [Présentation du NIST](nist-overview.md) - Comprendre les différents niveaux AAL.
* [Principes fondamentaux de l’authentification](nist-authentication-basics.md) - Terminologie importante et types d’authentification.
* [Types d’authentificateurs NIST](nist-authenticator-types.md) - Comprendre les différents types d’authentificateurs.
* [AAL NIST](nist-about-authenticator-assurance-levels.md) - Composants des niveaux AAL et méthode de mappage Microsoft Azure Active Directory.

## <a name="permitted-authenticator-types"></a>Types d’authentificateurs autorisés


| Méthode d'authentification Azure AD| Type d’authentificateur NIST | 
| - | - |
| **Méthodes recommandées** |   | 
| Application Microsoft Authenticator pour iOS (sans mot de passe)<br>Windows Hello Entreprise avec module TPM logiciel | Logiciel de chiffrement multifacteur |
| Clé de sécurité FIDO 2<br>Application Microsoft Authenticator pour Android (sans mot de passe)<br>Windows Hello Entreprise avec module TPM matériel<br>Carte à puce (ADFS) | Matériel de chiffrement multifacteur |
| **Autres méthodes** |  |
| Mot de passe + Téléphone (SMS) | Secret mémorisé + Hors bande |
| Mot de passe + Application Microsoft Authenticator (OTP)<br>Mot de passe + SF OTP | Secret mémorisé + Mot de passe monofacteur à usage unique |
| Mot de passe + Jonction Azure AD avec module TPM logiciel <br>Mot de passe + Appareil mobile conforme<br>Mot de passe + Jonction Azure AD Hybride avec module TPM logiciel <br>Mot de passe + Application Microsoft Authenticator (notification) | Secret mémorisé + Logiciel de chiffrement monofacteur |
| Mot de passe + Jonction Azure AD avec module TPM matériel <br>Mot de passe + Jonction Azure AD Hybride avec module TPM matériel | Secret mémorisé + Matériel de chiffrement monofacteur |


### <a name="our-recommendations"></a>Nos recommandations

Nous vous recommandons d’utiliser des authentificateurs matériels ou logiciels de chiffrement multifacteur pour atteindre AAL2. L’authentification sans mot de passe élimine la plus grande surface d’attaque, à savoir, le mot de passe, et offre aux utilisateurs une méthode d’authentification simplifiée. 

Pour obtenir des instructions détaillées sur la sélection d’une méthode d’authentification sans mot de passe, consultez [Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment).

Pour plus d’informations sur l’implémentation de Windows Hello Entreprise, consultez le [Guide de déploiement de Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validation FIPS 140

Les informations suivantes vous guident pour obtenir la validation FIPS 140.

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement validé global Windows FIPS 140 de niveau 1   
‎pour toutes ses opérations de chiffrement liées à l'authentification. Il s'agit donc d'un vérificateur conforme à la norme FIPS 140, comme l'exigent les agences gouvernementales.

### <a name="authenticator-requirements"></a>Exigences liées à l’authentificateur

*Les authentificateurs de chiffrement des agences gouvernementales sont requis pour valider FIPS 140, niveau 1 global*. Ils ne sont pas obligatoires pour les organismes non gouvernementaux. Les authentificateurs Azure AD suivants répondent à la configuration requise lorsqu’ils sont exécutés sur [Windows en mode de fonctionnement approuvé FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation).

* Mot de passe

* Jonction Azure AD avec module TPM logiciel ou matériel

* Jonction Azure AD Hybride avec module TPM logiciel ou matériel

* Windows Hello Entreprise avec module TPM logiciel ou matériel

* Carte à puce (ADFS) 

Actuellement, les clés de sécurité FIDO2 et l’application Microsoft Authenticator (dans tous ses modes, notification, OTP et sans mot de passe) ne répondent pas aux conditions requises par les agences gouvernementales pour la validation FIPS 140, niveau 1 global.

* L’application Microsoft Authenticator utilise le chiffrement approuvé FIPS 140, mais il n’est pas validé pour FIPS 140, niveau 1 global. 

* Les clés FIDO2 relèvent d’une innovation très récente et, en tant que telles, sont toujours en cours de certification FIPS.

## <a name="reauthentication"></a>Réauthentification 

Le niveau AAL2 NIST requiert une réauthentification toutes les 12 heures, quelle que soit l’activité de l’utilisateur, et après une période d’inactivité de 30 minutes ou plus. La présentation de quelque chose que vous connaissez ou de quelque chose que vous êtes est requise car le secret de session est quelque chose que vous avez.

Pour répondre à la configuration requise à des fins de réauthentification, quelle que soit l’activité de l’utilisateur, Microsoft recommande de configurer la [fréquence de connexion de l’utilisateur](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime) sur 12 heures. 

NIST autorise également l’utilisation de contrôles de compensation pour confirmer la présence de l’abonné :

* Le délai d’inactivité de session de 30 minutes peut être atteint en verrouillant l’appareil au niveau du système d’exploitation en utilisant Microsoft System Center Configuration Manager (SCCM), des objets de stratégie de groupe (GPO) ou Intune. Vous devez également exiger une authentification locale pour que l’abonné le déverrouille.

* Le délai d’expiration de l’activité peut être atteint en exécutant une tâche planifiée (en utilisant SCCM, GPO ou Intune) qui verrouille l’ordinateur après 12 heures, quelle que soit l’activité.

## <a name="man-in-the-middle-mitm-resistance"></a>Résistance aux attaques MitM 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé authentifié afin de résister aux attaques MitM (ou attaques de l'intercepteur). Cela répond aux exigences de résistance aux attaques MitM des niveaux AAL1, AAL2 et AAL3.

## <a name="replay-resistance"></a>Résistance à la relecture

Toutes les méthodes d’authentification Azure AD répondant à AAL2 utilisent des nonce ou des défis et sont résistantes aux attaques par relecture, car le vérificateur détecte facilement les transactions d’authentification relues, celles-ci ne contenant pas de nonce ou de données de chronologie appropriées.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md)  