---
title: Principes fondamentaux de l’authentification NIST et Azure Active Directory
description: Cet article définit la terminologie, et décrit les Modules de plateforme sécurisée (TPM) et les facteurs d'authentification NIST.
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
ms.openlocfilehash: eacdb19dcd5121f1d7fecdf6a3cefbafd609c9a9
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111889829"
---
# <a name="nist-authentication-basics"></a>Principe fondamentaux de l'authentification NIST 

Pour comprendre les directives du National Institute of Standards and Technology (NIST), vous devez vous familiariser avec la terminologie. Vous devez également comprendre la technologie TPM et les facteurs d'authentification. Ces informations sont fournies dans le présent article. 

## <a name="terminology"></a>Terminologie

La terminologie suivante est utilisée dans ces articles NIST.

|Terme| Définition. Les termes *en italique* sont définis dans ce tableau.|
| - | - |
| Assertion| Instruction d'un *vérificateur* à destination d'une *partie de confiance* qui contient des informations sur l'*abonné*. Une assertion peut contenir des attributs vérifiés. |
|Authentification| Processus de vérification de l’identité d’un *sujet*. |
| Facteur d’authentification| L'authentification peut porter sur quelque chose que vous connaissez, quelque chose que vous avez ou quelque chose que vous êtes. Chaque *authentificateur* possède un ou plusieurs facteurs d'authentification. |
| Authenticator| L'authentificateur correspond à quelque chose que le *demandeur* possède et contrôle, et qui est utilisé pour authentifier l'identité de ce *demandeur*. |
| Demandeur| *Sujet* dont l'identité doit être vérifiée par le biais d'un ou plusieurs protocoles d'*authentification*. |
|Informations d'identification| Un sujet ou une structure de données qui lie de manière forcée une identité à au moins un *authentificateur* détenu et contrôlé par un *abonné*. |
| Fournisseur de services d'informations d'identification (CSP)| Entité approuvée qui émet ou inscrit des *authentificateurs d’abonnés* et émet des *informations d’identification* électroniques aux *abonnés*. |
|Partie de confiance| Entité qui s'appuie sur l'*assertion d'un vérificateur*, ou sur les *authentificateurs* et les *informations d'identification* d'un demandeur, généralement pour accorder l'accès à un système. |
|  Objet| Personne, organisation, appareil, matériel, réseau, logiciel ou service. |
| Abonné| Tiers ayant reçu des *informations d’identification* ou un *authentificateur* de la part d’un *fournisseur de services d’informations d’identification*. |
|Module de plateforme sécurisée  | Un module TPM est un module inviolable qui effectue des opérations de chiffrement, y compris la génération de clés. |
|  Vérificateur| Entité qui vérifie l’identité du *demandeur* en vérifiant la propriété et le contrôle des *authentificateurs* du demandeur. |


## <a name="about-trusted-platform-module-technology"></a>À propos de la technologie de Module de plateforme sécurisée (TPM)

La technologie de Module de plateforme sécurisée (TPM, Trusted Platform Module) est conçue pour fournir des fonctions de sécurité basées sur le matériel. Une puce TPM (ou module TPM matériel) est un processeur de chiffrement sécurisé qui vous permet d'effectuer des actions telles que la génération de clés de chiffrement, leur stockage et la limitation de l'utilisation de celles-ci. 

Microsoft fournit des informations importantes sur le fonctionnement des modules TPM avec Windows. Pour plus d'informations, consultez [Module de plateforme sécurisée (TPM)](/windows/security/information-protection/tpm/trusted-platform-module-top-node). 

Un module TPM logiciel est un émulateur qui reproduit la fonctionnalité d'un module TPM matériel. 

 ## <a name="authentication-factors-and-their-strengths"></a>Facteurs d’authentification et points forts

Les facteurs d'authentification se décomposent en trois catégories :

![Graphique fournissant des exemples de facteurs d'authentification, regroupés en trois catégories : quelque chose que vous connaissez, quelque chose que vous avez, et quelque chose que vous êtes.](media/nist-authentication-basics/nist-authentication-basics-0.png)

La force d'un facteur d'authentification est déterminée par la certitude que vous pouvez avoir qu'il s'agit d'une chose que seul l'abonné connaît, possède ou est.

Le NIST fournit des indications limitées sur la force relative des facteurs d'authentification. La suite de cette section décrit la façon dont nous évaluons ces points forts chez Microsoft. 

**Quelque chose que vous connaissez**. Les mots de passe (*la chose la plus courante que vous connaissez*) représentent la plus grande surface d'attaque. Les mesures d'atténuation suivantes améliorent la confiance dans l'affinité avec l'abonné. Elles sont efficaces pour prévenir les attaques par mot de passe comme les attaques par force brute, l'écoute clandestine et le piratage psychologique :

* [Exigences en termes de « complexité » du mot de passe](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [Mots de passe interdits](../authentication/tutorial-configure-custom-password-protection.md)

* [Informations d'identification fuitées](../identity-protection/overview-identity-protection.md)

* [Stockage haché sécurisé](https://aka.ms/AADDataWhitepaper)

* [Verrouillage de compte](../authentication/howto-password-smart-lockout.md)

**Quelque chose que vous avez**. La force de la catégorie *quelque chose que vous avez* repose sur la probabilité que l'abonné garde cette chose en sa possession et sur la difficulté pour un attaquant d'y accéder. Par exemple, lorsque vous essayez de vous protéger contre les menaces internes, un appareil mobile personnel ou une clé matérielle aura une plus grande affinité. Il sera donc plus sûr qu'un ordinateur situé dans un bureau.

**Quelque chose que vous êtes**. La facilité avec laquelle un attaquant peut obtenir une copie de *quelque chose que vous êtes* ou usurper un élément biométrique, est importante. NIST élabore une infrastructure pour la biométrie. Pour le moment, le NIST n'accepte pas la biométrie comme méthode d'authentification distincte. Il doit s’agir d’un facteur dans le cadre d’une authentification multifacteur. Cette précaution est en place parce que les données biométriques sont de nature probabiliste. Autrement dit, elles utilisent des algorithmes qui déterminent la probabilité d'affinité. Les données biométriques ne fournissent pas nécessairement une correspondance exacte, comme le font les mots de passe. Pour plus d'informations, consultez [Force de la fonction pour les authentificateurs – Biométrie](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B). 

SOFA-B tente de présenter un cadre permettant de quantifier la force de la biométrie pour ce qui suit :
- Taux de fausses correspondances
- Taux de faux échecs
- Taux d'erreurs de détection des attaques de présentation
- Effort nécessaire pour mener une attaque 

## <a name="single-factor-authentication"></a>‎Authentification monofacteur

Vous pouvez implémenter l'authentification monofacteur en utilisant un authentificateur monofacteur qui vérifie *quelque chose que vous connaissez* ou *quelque chose que vous êtes*. Un facteur correspondant à *quelque chose que vous êtes* est accepté en tant que facteur d'authentification, mais pas en tant qu'authentificateur proprement dit. 

![Graphique illustrant le fonctionnement de l'authentification monofacteur.](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multifactor-authentication"></a>Authentification multifacteur

Vous pouvez implémenter l'authentification multifacteur à l'aide d'un ou deux authentificateurs monofacteurs. Un authentificateur multifacteur requiert deux facteurs d'authentification pour accomplir une seule transaction d'authentification.

### <a name="multifactor-authentication-by-using-two-single-factor-authenticators"></a>Authentification multifacteur à l'aide de deux authentificateurs monofacteurs

L'authentification multifacteur requiert deux facteurs d'authentification distincts. Il peut s'agir d'authentificateurs indépendants. Par exemple : 

* Secret mémorisé (mot de passe) et hors bande (SMS)

* Secret mémorisé (mot de passe) et mot de passe à usage unique (matériel ou logiciel)

Ces méthodes accomplissent deux transactions d'authentification indépendantes avec Azure Active Directory (Azure AD).

![Graphique décrivant l'authentification multifacteur via deux authentificateurs distincts.](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multifactor-authentication-by-using-a-single-multifactor-authenticator"></a>Authentification multifacteur à l'aide d'un seul authentificateur multifacteur

L'authentification multifacteur requiert un facteur d'authentification (*quelque chose que vous connaissez* ou *quelque chose que vous êtes*) pour déverrouiller un deuxième facteur d'authentification. L'expérience utilisateur est généralement plus simple qu'avec plusieurs authentificateurs indépendants.

![Graphique illustrant l'authentification multifacteur à l'aide d'un seul authentificateur multifacteur.](media/nist-authentication-basics/nist-authentication-basics-3a.png)

Par exemple, l'application Microsoft Authenticator utilisée en mode sans mot de passe. Avec cette méthode, l'utilisateur tente d'accéder à une ressource sécurisée (partie de confiance) et reçoit une notification sur l'application Authenticator. L'utilisateur répond à la notification en fournissant des données biométriques (*quelque chose que vous êtes*) ou un code PIN (*quelque chose que vous connaissez*). Ce facteur déverrouille la clé de chiffrement du téléphone (*quelque chose que vous avez*), que le vérificateur valide ensuite.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST à l'aide d'Azure Active Directory](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST à l’aide d’Azure Active Directory](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST à l'aide d’Azure Active Directory](nist-authenticator-assurance-level-3.md)