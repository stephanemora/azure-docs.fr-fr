---
title: Niveaux d’assurance d’authentificateur NIST avec Azure Active Directory
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
ms.openlocfilehash: 72462bf14fb8c287335e0497cbaa00102521b310
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889847"
---
# <a name="about-authenticator-assurance-levels"></a>À propos des niveaux d’assurance d’authentificateur

L’institut National Institute of Standards and Technology (NIST) élabore les exigences techniques pour les agences fédérales américaines qui implémentent des solutions d’identité. [NIST SP 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html) définit les recommandations techniques relatives à l’implémentation de l’authentification numérique. Il s’appuie pour cela sur une infrastructure de niveaux d’assurance d’authentificateur (AAL, Authenticator Assurance Levels). Les niveaux AAL caractérisent la force de l’authentification d’une identité numérique. Ce guide couvre également la gestion du cycle de vie des authentificateurs, dont leur révocation. 

La norme inclut des exigences de niveau AAL pour les catégories d’exigence suivantes :

* Types d’authentificateurs autorisés

* Le niveau de vérification FIPS 140 (Federal Information Processing Standards 140) (les exigences FIPS 140 sont respectées par [FIPS 140-2](https://csrc.nist.gov/publications/detail/fips/140/2/final) ou les révisions plus récentes)

* Réauthentification

* Contrôles de sécurité

* Résistance à l’intercepteur

* Résistance à l’emprunt d’identité du vérificateur (résistance à l’hameçonnage)

* Résistance aux compromissions du vérificateur

* Résistance à la réexécution

* Intention d’authentification

* Stratégie de rétention des enregistrements

* Contrôles de la confidentialité

## <a name="apply-nist-aals-in-your-environment"></a>Appliquer les niveaux AAL NIST dans votre environnement

> [!TIP]
> Nous vous recommandons de respecter au moins le niveau AAL2. Respectez le niveau AAL3 si nécessaire pour des raisons professionnelles, des normes industrielles ou des exigences de conformité.

En général, le niveau AAL1 n’est pas recommandé, car il accepte les solutions par mot de passe seulement, et les mots de passe sont la forme d’authentification la plus facile à compromettre. Pour plus d’informations, consultez le billet de blog [Votre mot de pa$$e n’a pas d’importance](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984). 

Bien que le NIST n’exige pas de résistance à l’emprunt d’identité de vérificateur (également appelé hameçonnage d’informations d’identification) jusqu’au niveau AAL3, nous vous recommandons vivement de traiter cette menace à tous les niveaux. Vous pouvez sélectionner des authentificateurs qui fournissent une résistance à l’emprunt d’identité du vérificateur, par exemple, en exigeant que les appareils soient joints à Azure Active Directory (Azure AD) ou à Azure AD Hybride. Si vous utilisez Office 365, vous pouvez vous servir d’Office 365 – Protection avancée contre les menaces, en particulier de ses [stratégies anti-hameçonnage](/microsoft-365/security/office-365-security/set-up-anti-phishing-policies).

À mesure que vous évaluez le niveau AAL NIST approprié pour votre organisation, déterminez si l’ensemble de celle-ci doit respecter les normes NIST. S’il est possible de séparer des groupes spécifiques d’utilisateurs et de ressources, vous pouvez appliquer les configurations de niveau AAL NIST à un groupe spécifique d’utilisateurs et de ressources. 

## <a name="security-controls-privacy-controls-records-retention-policy"></a>Contrôles de sécurité, contrôles de confidentialité, stratégie de conservation des enregistrements

Azure et Azure Government ont obtenu du Joint Authorization Board une autorisation d’exploitation provisoire (P-ATO) de [niveau Impact élevé NIST SP 800-53](https://nvd.nist.gov/800-53/Rev4/impact/high). Ce niveau représente la barre la barre la plus élevée pour l’accréditation FedRAMP, et autorise l’utilisation d’Azure et Azure Government pour traiter des données très sensibles.

Ces certifications Azure et Azure Government satisfont aux exigences en matière de contrôles de sécurité et de conformité, ainsi que de stratégie de rétention des enregistrements des niveaux AAL1, AAL2 et AAL3.

L’audit FedRAMP d’Azure et d’Azure Government incluait le système de gestion de la sécurité des informations qui englobe l’infrastructure, le développement, les opérations, la gestion et la prise en charge des services concernés. Une fois qu’une autorisation P-ATO est accordée, un fournisseur de services cloud doit encore obtenir une autorisation d’exploitation (ATO) de l’organisme gouvernemental avec lequel il travaille. Pour Azure, un organisme gouvernemental ou des organisations travaillant avec celui-ci peuvent utiliser l’autorisation P-ATO d’Azure dans leur propre processus d’autorisation de sécurité. Ils peuvent également s’appuyer sur cette autorisation comme base pour l’émission d’une autorisation ATO répondant également aux exigences FedRAMP.

Azure continue de prendre en charge plus de services de niveaux d’impact élevé FedRAMP que n’importe quel autre fournisseur de cloud. Et même si FedRAMP High dans le cloud public Azure répond aux besoins de nombreux clients du secteur public des États-Unis, les organismes ayant des exigences plus strictes peuvent s’appuyer sur Azure Government. Azure Government fournit des protections supplémentaires, telles que le filtrage renforcé du personnel. Microsoft répertorie tous les services publics Azure actuellement disponibles dans Azure Government jusqu’à la limite de FedRAMP High, ainsi que les services planifiés pour l’année en cours.

De plus, Microsoft s’engage pleinement à [protéger et à gérer les données des clients](https://www.microsoft.com/trust-center/privacy/data-management) avec des stratégies de conservation des enregistrements clairement énoncées. En tant qu’entreprise mondiale comptant des clients dans presque tous les pays du monde, Microsoft dispose d’une solide portefeuille de solutions de conformité pour vous aider. Pour voir la liste complète de nos offres de conformité, consultez [Offre de conformité Microsoft](/compliance/regulatory/offering-home). 

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md)