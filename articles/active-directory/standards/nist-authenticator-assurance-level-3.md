---
title: Atteindre le niveau AAL3 NIST à l’aide d’Azure Active Directory
description: Cet article fournit une aide sur l’obtention du niveau d’assurance de l’authentificateur 3 NIST (AAL3 NIST) à l’aide d’Azure Active Directory.
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
ms.openlocfilehash: 210213321dfba3bf734af498db6567cf1c6b4281
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889703"
---
# <a name="achieve-nist-authenticator-assurance-level-3-by-using-azure-active-directory"></a>Atteindre le niveau d’assurance de l’authentificateur 3 NIST à l’aide d’Azure Active Directory

Cet article vous aide à atteindre le niveau d’assurance de l’authentificateur 3 du National Institute of Standards and Technology (AAL NIST). Vous pouvez consulter ces ressources avant d’essayer d’atteindre le niveau AAL3 :
* [Présentation du NIST](nist-overview.md) : Comprendre les différents niveaux AAL.
* [Principes fondamentaux de l’authentification](nist-authentication-basics.md) : Terminologie importante et types d’authentification.
* [Types d’authentificateurs NIST](nist-authenticator-types.md) : Comprendre les différents types d’authentificateurs.
* [Niveaux AAL NIST](nist-about-authenticator-assurance-levels.md) : Présentation des composants des niveaux AAL et des méthodes d’authentification Azure Active Directory (Azure AD) correspondantes.

## <a name="permitted-authenticator-types"></a>Types d’authentificateurs autorisés
Microsoft propose des méthodes d’authentification qui vous permettent de répondre aux types d’authentificateurs NIST requis. Cette section présente les recommandations de Microsoft.
 

| Méthodes d’authentification Azure AD| Type d’authentificateur NIST |
| - | -|
| **Méthodes recommandées**|    |
| Clé de sécurité FIDO2<br>ou<br> Carte à puce (services de fédération Active Directory [AD FS])<br>ou<br>Windows Hello Entreprise avec module TPM matériel| Matériel de chiffrement multifacteur |
| **Autres méthodes**|   |
| Mot de passe<br> et<br>(Jointure hybride Azure AD avec module TPM matériel <br>ou <br> Jointure Azure AD avec module TPM matériel)| Secret mémorisé<br>et<br> Matériel de chiffrement monofacteur |
| Mot de passe <br>et<br>(Matériel de mot de passe monofacteur à usage unique [par l’intermédiaire de fabricants de mots de passe à usage unique] <br>ou<br>Jonction hybride Azure AD avec module TPM logiciel <br>ou <br> Jonction Azure AD avec module TPM logiciel <br>ou<br> Appareil géré conforme)| Secret mémorisé <br>et<br>Matériel de mot de passe monofacteur à usage unique<br> et<br>Logiciel de chiffrement monofacteur |

### <a name="our-recommendations"></a>Nos recommandations 

Nous vous recommandons d’utiliser un authentificateur matériel de chiffrement multifacteur pour atteindre AAL3. L’authentification sans mot de passe élimine la plus grande surface d’attaque, à savoir le mot de passe, et offre aux utilisateurs une méthode d’authentification simplifiée. Si votre organisation est entièrement informatique, nous vous recommandons d’utiliser des clés de sécurité FIDO2.

Notez que les clés FIDO2 et Windows Hello Entreprise n’ont pas été validés au niveau de sécurité FIPS 140 requis. Les clients fédéraux doivent donc procéder à une évaluation des risques avant d’accepter ces authentificateurs comme niveaux AAL3.

Pour obtenir des instructions détaillées, consultez [Planifier un déploiement d’authentification sans mot de passe dans Azure Active Directory](../authentication/howto-authentication-passwordless-deployment.md).

Pour plus d’informations sur l’implémentation de Windows Hello Entreprise, consultez le [Guide de déploiement de Windows Hello Entreprise](/windows/security/identity-protection/hello-for-business/hello-deployment-guide).

## <a name="fips-140-validation"></a>Validation FIPS 140

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement Windows validé par le niveau 1 de la norme FIPS 140   
pour toutes ses opérations de chiffrement liées à l’authentification. Il s’agit donc d’un vérificateur conforme à la norme FIPS-140.

### <a name="authenticator-requirements"></a>Exigences liées à l’authentificateur

Les authentificateurs matériels de chiffrement monofacteur et multifacteur présentent des exigences distinctes. 

Les authentificateurs **matériels de chiffrement monofacteur** doivent être :

* Niveau 1 Global (ou supérieur) de la norme FIPS 140.

* Niveau 3 Sécurité physique (ou supérieur) de la norme FIPS 140.

Les appareils avec jointure Azure AD et jointure hybride Azure AD répondent à cette exigence dans les situations suivantes : 

* Vous exécutez [Windows dans un mode de fonctionnement approuvé par la norme FIPS-140](/windows/security/threat-protection/fips-140-validation). 

* Sur une machine équipée d’un module TPM de niveau 1 Global (ou supérieur) de la norme FIPS 140 avec le niveau 3 Sécurité physique de la norme FIPS 140. 

   * Recherchez les modules TPM conformes à l’aide des termes « Trusted Platform Module » et « TPM » sur la page du [programme de validation des modules de chiffrement](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

Contactez le fournisseur de votre appareil mobile pour en savoir plus sur son respect de la norme FIPS 140.

Les authentificateurs **matériels de chiffrement multifacteur** doivent être : 

* Niveau 2 Global (ou supérieur) de la norme FIPS 140.

* Niveau 3 Sécurité physique (ou supérieur) de la norme FIPS 140.

Les clés de sécurité FIDO2, les cartes à puce et Windows Hello Entreprise peuvent vous aider à répondre à ces exigences.

* Les fournisseurs de clés FIDO2 sont chacun à des étapes différentes de leur certification FIPS, et certains d’entre eux ont terminé le processus de validation. Nous vous recommandons de consulter la [liste des fournisseurs de clés FIDO2 pris en charge](../authentication/concept-authentication-passwordless.md#fido2-security-key-providers) et de vérifier auprès de votre fournisseur l’état actuel de sa validation FIPS.

* Les cartes à puce sont une technologie éprouvée. Les produits de plusieurs fournisseurs répondent aux exigences de la norme FIPS.

   * Pour en savoir plus, consultez la page du [programme de validation des modules de chiffrement](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search).

**Windows Hello Entreprise**

La norme FIPS 140 exige que l’ensemble de la limite de chiffrement, y compris les logiciels, les microprogrammes et le matériel, fasse l’objet d’une évaluation. Les systèmes d’exploitation Windows sont des plateformes informatiques ouvertes qui peuvent être associées à des milliers de combinaisons de matériel. Microsoft n’est pas en mesure de gérer les certifications FIPS pour chaque combinaison. Vous devez évaluer les certifications individuelles suivantes des composants dans le cadre de votre évaluation des risques pour l’utilisation de Windows Hello Entreprise comme authentificateur AAL3 :

* **Windows 10 et Windows Server** utilisent le [profil de protection v 4.2.1 approuvé par le gouvernement américain pour les systèmes d’exploitation à usage général](https://www.niap-ccevs.org/Profile/Info.cfm?PPID=442&id=442) du National Information Assurance Partnership (NIAP). Le NIAP supervise un programme national visant à évaluer la conformité des produits informatiques du commerce (COTS) aux critères communs internationaux. 

* La **bibliothèque de chiffrement de Windows** [a atteint le niveau 1 Global des normes FIPS dans le cadre du programme de validation des modules de chiffrement NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/Certificate/3544) (CMVP). Le programme CMVP, issu des efforts conjoints entre le NIST et le centre canadien pour la cybersécurité, valide les modules de chiffrement par rapport aux normes FIPS. 

* Choisissez un **module TPM** conforme au niveau 2 Global et au niveau 3 Sécurité physique de la norme FIPS 140. Il incombe à votre organisation de s’assurer que le module TPM que vous utilisez répond aux exigences du niveau AAL que vous souhaitez atteindre.   
‎Pour savoir quels modules TPM répondent aux normes actuelles, accédez à la page du [programme de validation des modules de chiffrement du centre des ressources de sécurité informatique du NIST](https://csrc.nist.gov/Projects/cryptographic-module-validation-program/validated-modules/Search). Dans la zone **Module Name** (Nom du module), entrez **Trusted Platform Module** (Module de plateforme sécurisée). La liste qui en résulte contient les modules TPM répondant aux normes actuelles.

## <a name="reauthentication"></a>Réauthentification 

Au niveau AAL3, NIST requiert une réauthentification toutes les 12 heures, quelle que soit l’activité de l’utilisateur. La réauthentification est également requise après une période d’inactivité de 15 minutes ou plus. La présentation des deux facteurs est nécessaire.

Pour répondre à la configuration requise à des fins de réauthentification, quelle que soit l’activité de l’utilisateur, Microsoft recommande de configurer la [fréquence de connexion de l’utilisateur](../conditional-access/howto-conditional-access-session-lifetime.md) sur 12 heures. 

NIST autorise également l’utilisation de contrôles compensatoires pour confirmer la présence de l’abonné :

* Vous pouvez définir un délai d’inactivité de la session de 15 minutes en verrouillant l’appareil au niveau du système d’exploitation. Pour ce faire, vous pouvez utiliser Microsoft Endpoint Configuration Manager, Group Policy Object (GPO) ou Intune. Vous devez également exiger une authentification locale pour que l’abonné le déverrouille.

* Vous pouvez obtenir l’expiration du délai en exécutant une tâche planifiée (avec Configuration Manager, GPO ou Intune) qui verrouille l’ordinateur après 12 heures, quelle que soit l’activité.

## <a name="man-in-the-middle-resistance"></a>Résistance aux attaques de l’intercepteur 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé et authentifié afin de résister aux attaques de l’intercepteur. Cette configuration répond aux exigences de résistance aux attaques de l’intercepteur des niveaux AAL1, AAL2 et AAL3.

## <a name="verifier-impersonation-resistance"></a>Résistance à l’emprunt d’identité du vérificateur

Toutes les méthodes d’authentification Azure AD qui répondent aux exigences du niveau AAL3 utilisent des authentificateurs de chiffrement qui lient la sortie de l’authentificateur à la session spécifique en cours d’authentification. Pour ce faire, elles utilisent une clé privée contrôlée par le demandeur et dont la clé publique est connue du vérificateur. Cette configuration satisfait les exigences du niveau AAL3 en matière de résistance à l’emprunt d’identité du vérificateur.

## <a name="verifier-compromise-resistance"></a>Résistance aux compromissions du vérificateur

Toutes les méthodes d’authentification Azure AD qui répondent aux exigences du niveau AAL3 effectuent l’une des opérations suivantes :
- Utiliser un authentificateur de chiffrement qui requiert que le vérificateur stocke une clé publique correspondant à une clé privée détenue par l’authentificateur. 
- Stocker la sortie prévue de l’authentificateur à l’aide d’algorithmes de hachage validés par la norme FIPS-140. 

Pour plus d’informations, consultez [Considérations relatives à la sécurité des données Azure AD](https://aka.ms/AADDataWhitepaper).

## <a name="replay-resistance"></a>Résistance à la réexécution

Toutes les méthodes d’authentification Azure AD qui répondent aux exigences du niveau AAL3 utilisent des nonces ou des défis. Ces méthodes offrent une bonne résistance aux attaques par réexécution, car le vérificateur détectera facilement les transactions d’authentification réexécutées. En effet, ces transactions ne contiennent pas le nonce ni les données d’actualité appropriés.

## <a name="authentication-intent"></a>Intention d’authentification

L’objectif de l’intention d’authentification consiste à rendre plus difficile l’utilisation des authentificateurs physiques directement connectés (comme les appareils de chiffrement multifacteur) à l’insu du sujet. Par exemple, par un programme malveillant au niveau du point de terminaison.

NIST autorise l’utilisation de contrôles de compensation pour atténuer les risques liés aux logiciels malveillants. Tout appareil conforme à Intune qui exécute Windows Defender System Guard et Windows Defender ATP répond à cette exigence d’atténuation des risques.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST à l’aide d’Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST à l’aide d’Azure Active Directory](nist-authenticator-assurance-level-2.md)