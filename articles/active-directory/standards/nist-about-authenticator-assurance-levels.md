---
title: Niveaux d’assurance de l’authentificateur NIST avec Azure Active Directory
description: Vue d’ensemble des niveaux d’assurance de l’authentificateur, tels qu’ils sont appliqués à Azure Active Directory
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
ms.openlocfilehash: 08660ae2d9f3b3561665489e10b29b51a9c2d512
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293971"
---
# <a name="about-authenticator-assurance-levels"></a>À propos des niveaux d’assurance de l’authentificateur

Le NIST (National Institute of Standards and Technology) développe les spécifications techniques pour les agences fédérales américaines mettant en œuvre des solutions d’identité. [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) définit les recommandations techniques relatives à l’implémentation de l’authentification numérique. Ce document s’appuie pour cela sur une infrastructure dotée de niveaux AAL (AAL, Authenticator Assurance Levels). Les niveaux AAL caractérisent la force de l’authentification d’une identité numérique. Ce guide couvre également la gestion du cycle de vie des authentificateurs, y compris la révocation. 

La norme comprend des exigences AAL pour 11 catégories d’exigence :

* Types d’authentificateurs autorisés

* Le niveau de vérification FIPS 140 (Federal Information Processing Standards 140) (les exigences FIPS 140 sont respectées par [FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) ou les révisions plus récentes)

* Réauthentification

* Contrôles de sécurité

* Résistance à l’intercepteur

* Résistance à l’emprunt d’identité du vérificateur (résistance à l’hameçonnage)

* Résistance aux compromissions du vérificateur

* Résistance à la réexécution

* Intention d’authentification

* Stratégie de conservation des enregistrements

* Contrôles de la confidentialité

## <a name="applying-nist-aals-in-your-environment"></a>Application des niveaux NIST AAL dans votre environnement

> [!TIP]
> Nous vous recommandons de respecter au moins le niveau AAL 2, à moins que des raisons commerciales, des normes industrielles ou des exigences de conformité ne vous obligent à respecter le niveau AAL3.

En général, le niveau AAL1 n’est pas recommandé, car il accepte les solutions par mot de passe seulement, et les mots de passe sont la forme d’authentification la plus facile à compromettre. Consultez [Votre mot de pa$$e n’a pas d’importance](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984). 

Bien que le NIST n’exige pas la résistance à l’emprunt d’identité du vérificateur (également connu sous le nom d’hameçonnage des informations d’identification) jusqu’au niveau AAL3, nous vous recommandons vivement de traiter cette menace à tous les niveaux. Vous pouvez sélectionner des authentificateurs qui fournissent une résistance à l’emprunt d’identité du vérificateur, par exemple en exigeant des appareils à jonction Azure AD ou à jonction hybride Azure AD. Si vous utilisez Office 365, vous pouvez vous servir d’Office 365 - Protection avancée contre les menaces, et plus particulièrement des [stratégies anti-hameçonnage](https://docs.microsoft.com/microsoft-365/security/office-365-security/set-up-anti-phishing-policies?view=o365-worldwide).

À mesure que vous évaluez le niveau NIST AAL approprié pour votre organisation, vous pouvez déterminer si l’ensemble de votre organisation doit respecter les normes du NIST, ou si des groupes spécifiques d’utilisateurs et de ressources peuvent être formés, en n’appliquant les configurations du niveau NIST AAL qu’à un groupe particulier d’utilisateurs et de ressources. 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>Contrôles de sécurité, contrôles de confidentialité, stratégie de conservation des enregistrements

Azure et Azure Government ont obtenu une autorisation d’exploitation provisoire (P-ATO, Provisional Authority to Operate) de [niveau d’impact élevé NIST SP 800-53](https://nvd.nist.gov/800-53/Rev4/impact/high) auprès du Joint Authorization Board, le plus haut degré en matière d’accréditation FedRAMP, qui autorise l’utilisation d’Azure et Azure Government pour traiter des données très sensibles.

Ces certifications Azure et Azure Government satisfont aux exigences en matière de contrôles de sécurité, de contrôles de confidentialité et de stratégie de conservation des enregistrements pour les niveaux AAL1, AAL2 et AAL3.

L’audit FedRAMP d’Azure et d’Azure Government incluait le système de gestion de la sécurité des informations qui englobe l’infrastructure, le développement, les opérations, la gestion et la prise en charge des services concernés. Une fois qu’une autorisation P-ATO est accordée, un fournisseur de services cloud doit toujours obtenir une autorisation d’exploitation (ATO, Authority to Operate) auprès de l’organisme public avec lequel il travaille. Pour Azure, un organisme public ou les organisations travaillant avec celui-ci peuvent utiliser l’autorisation P-ATO d’Azure dans son propre processus d’autorisation de sécurité, et s’appuyer sur cette autorisation comme base pour l’émission d’une autorisation ATO de l’organisme qui répond également aux exigences FedRAMP.

Azure continue de prendre en charge plus de services de niveaux d’impact élevé FedRAMP que n’importe quel autre fournisseur de cloud. Et même si FedRAMP High dans le cloud public Azure répond aux besoins de nombreux clients du secteur public des États-Unis, les organismes ayant des exigences plus rigoureuses continuent de s’appuyer sur Azure Government, qui offre des protections supplémentaires, telles que le contrôle accru du personnel. Microsoft répertorie tous les services publics Azure actuellement disponibles dans Azure Government jusqu’à la limite de FedRAMP High, ainsi que les services planifiés pour l’année en cours.

De plus, Microsoft s’engage pleinement à [protéger et à gérer les données des clients](https://www.microsoft.com/trust-center/privacy/data-management) avec des stratégies de conservation des enregistrements clairement énoncées. En tant qu’entreprise mondiale comptant des clients dans presque tous les pays du monde, Microsoft dispose d’une solide gamme de solutions de conformité pour aider ses clients. Pour consulter la liste complète de nos offres de conformité, consultez [Offre de conformité Microsoft](https://docs.microsoft.com/compliance/regulatory/offering-home). 

## <a name="next-steps"></a>Étapes suivantes 

[Présentation de NIST](nist-overview.md)

[En savoir plus sur les AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d’authentificateurs NIST](nist-authenticator-types.md)

[Obtention du niveau NIST AAL1 avec Azure AD](nist-authenticator-assurance-level-1.md)

[Obtention du niveau NIST AAL2 avec Azure AD](nist-authenticator-assurance-level-2.md)

[Obtention du niveau NIST AAL3 avec Azure AD](nist-authenticator-assurance-level-3.md) 