---
title: Atteindre le niveau NIST AAL1 avec Azure Active Directory
description: Conseils pour atteindre le niveau NIST AAL 1 (Authenticator Assurance Level 1) avec Azure Active Directory
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
ms.openlocfilehash: 640c401beb42b07465f533fa14b5b7fd4b0200e5
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293980"
---
# <a name="achieving-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Atteindre le niveau NIST Authenticator Assurance Level 1 avec Azure Active Directory

Le NIST (National Institute of Standards and Technology) développe les exigences techniques qui permettent aux agences fédérales américaines d'implémenter des solutions d'identité. Les organisations travaillant avec les agences fédérales ont également l'obligation de satisfaire ces exigences. Cet article explique comment atteindre le niveau NIST AAL 1. 

Ressources que vous souhaiterez peut-être consulter avant de tenter d'atteindre le niveau AAL 1 :
* [Présentation du NIST](nist-overview.md) : présentation des différents niveaux AAL.
* [Principes fondamentaux de l'authentification](nist-authentication-basics.md) : terminologie importante et types d'authentification.
* [Types d'authentificateurs NIST](nist-authenticator-types.md) : présentation des différents types d'authentificateurs.
* [Niveaux NIST AAL](nist-about-authenticator-assurance-levels.md) : composants des niveaux AAL, mise en correspondance des méthodes d'authentification Microsoft Azure Active Directory avec ceux-ci, et présentation des modules de plateforme sécurisée (TPM). 

## <a name="permitted-authenticator-types"></a>Types d'authentificateurs autorisés

 N'importe quel [authentificateur autorisé](nist-authenticator-types.md) à un ou plusieurs facteurs du NIST peut être utilisé pour atteindre le niveau AAL1. Le tableau suivant répertorie ceux qui ne sont pas couverts dans [AAL2](nist-authenticator-assurance-level-2.md) et [AAL3](nist-authenticator-assurance-level-2.md).

| Méthode d'authentification Azure AD| Type d'authentificateur NIST |
| - | - |
| Mot de passe |Secret mémorisé |
| Téléphone (SMS)|  Hors bande |
|  Clé de sécurité FIDO 2 <br>Application Microsoft Authenticator pour iOS (sans mot de passe)<br>Windows Hello Entreprise avec le module TPM logiciel <br>Carte à puce (ADFS) |  Logiciel de chiffrement multifacteur |

> [!TIP]
> Nous vous recommandons de respecter au moins le niveau AAL 2, à moins que des raisons commerciales, des normes sectorielles ou des exigences de conformité ne vous obligent à respecter le niveau AAL3.

## <a name="fips-140-validation"></a>Validation FIPS 140

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement validé global Windows FIPS 140 de niveau 1   
pour toutes ses opérations de chiffrement liées à l'authentification. Il s'agit donc d'un vérificateur conforme à la norme FIPS 140, comme l'exigent les agences gouvernementales.

## <a name="man-in-the-middle-mitm-resistance"></a>Résistance aux attaques MitM 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé authentifié afin de résister aux attaques MitM (ou attaques de l'intercepteur). Cela répond aux exigences de résistance aux attaques MitM des niveaux AAL1, AAL2 et AAL3.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau NIST AAL1 avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau NIST AAL2 avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau NIST AAL3 avec Azure AD](nist-authenticator-assurance-level-3.md) 