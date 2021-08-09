---
title: Atteindre le niveau AAL1 NIST avec Azure Active Directory
description: Aide pour atteindre le niveau d’assurance de l’authentificateur 1 (AAL1) NIST avec Azure Active Directory.
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
ms.openlocfilehash: d38dfd79eefe8b7b1bfb8119a62b139b654f2fe5
ms.sourcegitcommit: 9ad20581c9fe2c35339acc34d74d0d9cb38eb9aa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2021
ms.locfileid: "110540468"
---
# <a name="achieve-nist-authenticator-assurance-level-1-with-azure-active-directory"></a>Atteindre le niveau d’assurance de l’authentificateur 1 NIST avec Azure Active Directory

Le National Institute of Standards and Technology (NIST) développe les exigences techniques pour les agences fédérales américaines qui implémentent des solutions d’identité. Les organisations travaillant avec les agences fédérales ont également l'obligation de satisfaire ces exigences. 

Avant de tenter d’atteindre le niveau d’assurance de l’authentificateur 1 (AAL1), il est utile de consulter les ressources suivantes :
* [Vue d’ensemble de NIST](nist-overview.md) : comprendre les différents niveaux AAL.
* [Principes fondamentaux de l’authentification](nist-authentication-basics.md) : terminologie et types d’authentification importants.
* [Types d’authentificateurs NIST](nist-authenticator-types.md) : comprendre les différents types d’authentificateurs.
* [Niveaux AAL NIST](nist-about-authenticator-assurance-levels.md) : composants des niveaux AAL, comment les méthodes d’authentification Azure Active Directory (Azure AD) se mappent avec ceux-ci et compréhension des modules de plateforme sécurisée (TPM). 

## <a name="permitted-authenticator-types"></a>Types d’authentificateurs autorisés

 Pour atteindre le niveau AAL1, vous pouvez utiliser n’importe quel [authentificateur autorisé](nist-authenticator-types.md) à facteur unique ou multifacteur NIST. Le tableau suivant répertorie les authentificateurs qui ne sont pas couverts dans [AAL2](nist-authenticator-assurance-level-2.md) et [AAL3](nist-authenticator-assurance-level-2.md).

| Méthode d’authentification Azure AD| Type d’authentificateur NIST |
| - | - |
| Mot de passe |Secret mémorisé |
| Téléphone (SMS)|  Hors bande |
|  Clé de sécurité FIDO 2 <br>Application Microsoft Authenticator pour iOS (sans mot de passe)<br>Windows Hello Entreprise avec le module TPM logiciel <br>Smartcard (services de fédération Active Directory) |  Logiciel de chiffrement multifacteur |

> [!TIP]
> Nous vous recommandons de respecter au moins le niveau AAL2. Respectez le niveau AAL3 si nécessaire pour des raisons professionnelles, des normes industrielles ou des exigences de conformité.

## <a name="fips-140-validation"></a>Validation FIPS 140

### <a name="verifier-requirements"></a>Exigences liées au vérificateur

Azure AD utilise le module de chiffrement validé global FIPS 140 de niveau 1 Windows pour toutes ses opérations de chiffrement liées à l’authentification. Il s’agit donc d’un vérificateur conforme à la norme FIPS 140, comme l’exigent les agences gouvernementales.

## <a name="man-in-the-middle-resistance"></a>Résistance aux attaques de l’intercepteur 

Toutes les communications entre le demandeur et Azure AD passent par un canal protégé et authentifié afin de résister aux attaques de l’intercepteur. Cela répond aux exigences de résistance aux attaques de l’intercepteur des niveaux AAL1, AAL2 et AAL3.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md) 