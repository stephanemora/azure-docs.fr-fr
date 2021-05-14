---
title: Obtention du niveau AAL3 NIST avec Azure Active Directory
description: Conseils pour obtenir le niveau d’assurance 3 (AAL 3) de l’authentificateur NIST avec Azure Active Directory.
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
ms.openlocfilehash: 1d7ebba72877d5c4e5df111ab574e28156003bf9
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293984"
---
# <a name="achieving-nist-authenticator-assurance-level-3-with-the-azure-active-directory"></a>Obtention du niveau d’assurance 3 de l’authentificateur NIST avec Azure Active Directory

Cet article vous guide pour obtenir le niveau d’assurance 3 (AAL) de l’authentificateur NIST (National Institute of Standards and Technology). Ressources que vous souhaiterez peut-être consulter avant d’atteindre le niveau AAL 3 :
* [Présentation du NIST](nist-overview.md) - Comprendre les différents niveaux AAL.
* [Principes fondamentaux de l’authentification](nist-authentication-basics.md) - Terminologie importante et types d’authentification.
* [Types d’authentificateurs NIST](nist-authenticator-types.md) - Comprendre les différents types d’authentificateurs.
* [AAL NIST](nist-about-authenticator-assurance-levels.md) - Composants des niveaux AAL et méthode de mappage Microsoft Azure Active Directory.

## <a name="permitted-authenticator-types"></a>Types d’authentificateurs autorisés
Microsoft propose des méthodes d’authentification qui vous permettent de répondre aux types d’authentificateurs NIST requis. Veuillez consulter nos recommandations.
 

| Méthodes Azure AD Authentication| Type d’authentificateur NIST |
| - | -|
| **Méthodes recommandées**|    |
| Clé de sécurité FIDO2 **OU**<br> Carte à puce (AD FS) **OU**<br>Windows Hello Entreprise avec module TPM matériel| Matériel de chiffrement multifacteur |
| **Autres méthodes**|   |
| Mot de passe **ET**<br>(Jonction Azure AD Hybride avec module TPM **OU** <br> Jonction Azure AD avec module TPM)| Secret mémorisé **+** Matériel de chiffrement monofacteur |
| Mot de passe **ET**<br>Mot de passe monofacteur à usage unique (par l’intermédiaire de fabricants de mots de passe à usage unique)**OU**<br>Jonction Azure AD Hybride avec module TPM logiciel **OU** <br> Jonction Azure AD avec module TPM logiciel **OU**<br> Appareil géré conforme| Secret mémorisé **ET**<br>Mot de passe monofacteur à usage unique **ET**<br>Logiciel de chiffrement monofacteur |

### <a name="our-recommendations"></a>Nos recommandations 

Nous vous recommandons d’utiliser un authentificateur matériel de chiffrement multifacteur pour atteindre AAL3. L’authentification sans mot de passe élimine la plus grande surface d’attaque, à savoir, le mot de passe, et offre aux utilisateurs une méthode d’authentification simplifiée. Si votre organisation est entièrement basée sur le cloud, nous vous recommandons d’utiliser des clés de sécurité FIDO2.

Notez que les clés FIDO2 et Windows Hello Entreprise n’ont pas été validés au niveau de sécurité FIPS 140 requis et que les clients fédéraux doivent procéder à l’évaluation des risques avant d’accepter ces authentificateurs en tant qu’AAL3.

Pour obtenir des instructions détaillées, consultez [Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-deployment).

Pour plus d’informations sur l’implémentation de Windows Hello Entreprise, consultez le [Guide de déploiement de Windows Hello Entreprise](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validation FIPS 140

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement validé global Windows FIPS 140 de niveau 1   
‎pour toutes ses opérations de chiffrement liées à l'authentification. Il s’agit dès lors d’un vérificateur compatible FIPS 140.

### <a name="authenticator-requirements"></a>Exigences liées à l’authentificateur

Les authentificateurs matériels de chiffrement monofacteur et multifacteur présentent des exigences distinctes. 

Les authentificateurs matériels de chiffrement monofacteur doivent être : 

* FIPS 140 niveau 1 global (ou supérieur)

* Sécurité physique FIPS 140 niveau 3 (ou supérieur)

Les appareils avec jonction Azure AD et Azure AD Hybride répondent à cette exigence dans les situations suivantes : 

* Vous exécutez [Windows en mode d’opération approuvé FIPS 140](https://docs.microsoft.com/windows/security/threat-protection/fips-140-validation) . 

* Sur un ordinateur doté d’un module TPM de niveau 1 (ou version ultérieure) FIPS 140, avec une sécurité physique 140 FIPS de niveau 3. 

   * Recherchez les modules TPM conformes via « Module de plateforme sécurisée (TPM) » et « TPM » sous [Programme de validation du module de chiffrement](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

Contactez le fournisseur de votre appareil mobile pour en savoir plus sur le respect de la norme FIPS 140.

Les authentificateurs **matériels de chiffrement multifacteur** doivent être : 

* FIPS 140, niveau 2 global (ou supérieur)

* Sécurité physique FIPS 140 niveau 3 (ou supérieur)

Les clés de sécurité FIDO2, les cartes à puce et Windows Hello Entreprise peuvent vous aider à répondre à ces exigences.

* Les clés FIDO2 relèvent d’une innovation très récente et, en tant que telles, sont toujours en cours de certification FIPS.

* Les cartes à puce, quant à elles, relèvent d’une technologie éprouvée avec plusieurs produits de fournisseurs répondant aux exigences FIPS.

   * Découvrez plus d’informations sur le [Programme de validation du module de chiffrement](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

**Windows Hello Entreprise**

FIPS 140 requiert la totalité de la limite de chiffrement, logiciel, microprogramme et matériel notamment, pour être dans l’étendue de l’évaluation. Les systèmes d’exploitation Windows sont des plateformes informatiques ouvertes qui peuvent être associées à des milliers de combinaisons de matériel. Par conséquent, Microsoft n’est pas en mesure de gérer les certifications FIPS pour chaque combinaison. Les certifications individuelles suivantes des composants doivent être évaluées dans le cadre de l’évaluation des risques liés à l’utilisation de WHfB en tant qu’authentificateur AAL3 :

* **Microsoft Windows 10 et Microsoft Windows Server** utilisent le [profil de protection du gouvernement des États-Unis pour les systèmes d’exploitation à usage général version 4.2.1](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442). du NIAP (National Information Assurance Partnership). Le NIAP supervise un programme national pour évaluer les produits informatiques commerciaux prêts à l’emploi (COTS) en conformité avec les critères communs internationaux. 

* La **bibliothèque de chiffrement Microsoft Windows** [a atteint le niveau 1 FIPS global du Programme de validation du module de chiffrement NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544) (CMVP). Le programme CMVP, issu des efforts conjoints entre le NIST et le Canadian Center for Cyber Security, valide le module de chiffrement aux normes FIPS. 

* Choisissez un **module TPM** conforme à FIPS 140, niveau 2 en général et FIPS 140, niveau 3 en matière de sécurité physique. **En tant qu’organisation, il est de votre responsabilité de vous assurer que le module TPM que vous utilisez répond aux besoins du niveau AAL que vous souhaitez atteindre**.   
‎Pour savoir quels modules TPM répondent aux normes actuelles, accédez au [Programme de validation du module de chiffrement du centre de ressources de sécurité informatique NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search). Dans le champ Nom du module, entrez « Module de plateforme sécurisée ». La liste qui en résulte contient les modules TPM répondant aux normes actuelles.

## <a name="reauthentication"></a>Réauthentification 

Le niveau AAL3 NIST requiert une réauthentification toutes les 12 heures, quelle que soit l’activité de l’utilisateur, et après une période d’inactivité de 15 minutes ou plus. La présentation des deux facteurs est nécessaire.

Pour répondre à la configuration requise à des fins de réauthentification, quelle que soit l’activité de l’utilisateur, Microsoft recommande de configurer la [fréquence de connexion de l’utilisateur](https://aka.ms/NIST/38) sur 12 heures. 

NIST autorise également l’utilisation de contrôles de compensation pour confirmer la présence de l’abonné :

* Le délai d’inactivité de session de 15 minutes peut être atteint en verrouillant l’appareil au niveau du système d’exploitation en utilisant Microsoft System Center Configuration Manager (SCCM), des objets de stratégie de groupe (GPO) ou Intune. Vous devez également exiger une authentification locale pour que l’abonné le déverrouille.

* Le délai d’expiration de l’activité peut être atteint en exécutant une tâche planifiée (en utilisant SCCM, GPO ou Intune) qui verrouille l’ordinateur après 12 heures, quelle que soit l’activité.

## <a name="man-in-the-middle-mitm-resistance"></a>Résistance aux attaques MitM 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé authentifié afin de résister aux attaques MitM (ou attaques de l'intercepteur). Cela répond aux exigences de résistance aux attaques MitM des niveaux AAL1, AAL2 et AAL3.

## <a name="verifier-impersonation-resistance"></a>Résistance à l’emprunt d’identité du vérificateur

Toutes les méthodes d’authentification Azure AD répondant à AAL3 ont recours à des authentificateurs de chiffrement qui lient la sortie de l’authentificateur à la session spécifique en cours d’authentification. Pour ce faire, elles utilisent une clé privée contrôlée par le demandeur et pour lequel la clé publique est connue du vérificateur. Cela répond aux exigences de résistance à l’emprunt d’identité du vérificateur pour AAL3.

## <a name="verifier-compromise-resistance"></a>Résistance aux compromissions du vérificateur

Toutes les méthodes d’authentification Azure AD répondant à AAL3 utilisent un authentificateur de chiffrement qui implique que le vérificateur stocke une clé publique correspondant à une clé privée détenue par l’authentificateur, ou stocke la sortie de l’authentificateur attendue à l’aide d’algorithmes de hachage validés FIPS 140. Pour plus d’informations, consultez [Considérations relatives à la sécurité des données Azure AD](https://aka.ms/AADDataWhitepaper).

## <a name="replay-resistance"></a>Résistance à la relecture

Toutes les méthodes d’authentification Azure AD répondant à AAL3 utilisent des nonce ou des défis et sont résistantes aux attaques par relecture, car le vérificateur détecte facilement les transactions d’authentification relues, celles-ci ne contenant pas de nonce ou de données de chronologie appropriées.

## <a name="authentication-intent"></a>Intention d’authentification

L’objectif de l’authentification consiste à complexifier l’utilisation des authentificateurs physiques directement connectés (par exemple, les appareils de chiffrement multifacteur) sans connaissance de l’objet, par exemple par un programme malveillant au niveau du point de terminaison.

NIST autorise l’utilisation de contrôles de compensation pour atténuer les risques liés aux logiciels malveillants. Tout appareil conforme à Intune exécutant Windows Defender System Guard et Windows Defender ATP répond à cette exigence d’atténuation des risques.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md) 
