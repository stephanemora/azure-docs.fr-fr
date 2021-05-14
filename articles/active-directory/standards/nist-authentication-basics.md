---
title: Principes fondamentaux de l’authentification NIST et Azure Active Directory
description: Explication de la terminologie et des facteurs d’authentification pour NIST.
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
ms.openlocfilehash: 028ce5fc6ae3cef586803e1b04948006b58fd17e
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293986"
---
# <a name="nist-authentication-basics"></a>Principe fondamentaux de l’authentification NIST 

Pour comprendre les instructions du NIST, vous devez connaître la terminologie, ainsi que les concepts des modules de plateforme sécurisée (TPM) et des facteurs d’authentification.

## <a name="terminology"></a>Terminologie

La terminologie suivante est utilisée dans ces articles relatifs à NIST.

|Terme| Définition : les termes *en italique* sont définis dans ce tableau|
| - | - |
| Assertion| Instruction d’un *vérificateur* à destination d’une *partie de confiance* contenant des informations sur l'*abonné*. Peut contenir des attributs vérifiés. |
|Authentification| Processus de vérification de l’identité d’un *sujet*. |
| Facteur d’authentification| Quelque chose que vous connaissez, que vous avez ou que vous êtes : chaque *authentificateur* possède un ou plusieurs facteurs d’authentification. |
| Authenticator| Quelque chose que le *demandeur* possède et contrôle, et utilisé pour authentifier l’identité de ce *demandeur*. |
| Demandeur| Un *objet* dont l’identité doit être vérifiée à l’aide d’un ou de plusieurs protocoles d’authentification. |
|Informations d'identification| Un sujet ou une structure de données qui lie de manière forcée une identité à au moins un *authentificateur* détenu et contrôlé par un *abonné*. |
| Fournisseur de services d’informations d’identification (CSP)| Entité approuvée qui émet ou inscrit des *authentificateurs d’abonnés* et émet des *informations d’identification* électroniques aux *abonnés*. |
|Partie de confiance| Entité qui s’appuie sur *l’assertion d’un vérificateur*, ou sur les *authentificateurs* et les *informations d’identification* d’un demandeur, généralement pour accorder l’accès à un système. |
|  Objet| Personne, organisation, appareil, matériel, réseau, logiciel ou service. |
| Abonné| Tiers ayant reçu des *informations d’identification* ou un *authentificateur* de la part d’un *fournisseur de services d’informations d’identification*. |
|Module de plateforme sécurisée  | Un module TPM est un module inviolable qui effectue des opérations de chiffrement, y compris la génération de clés. |
|  Vérificateur| Entité qui vérifie l’identité du *demandeur* en vérifiant la propriété et le contrôle des *authentificateurs* du demandeur. |


## <a name="about-trusted-platform-modules"></a>À propos des modules TPM 

La technologie de module de plateforme sécurisée (TPM, Trusted Platform Module) est conçue dans le but d’assurer des fonctions matérielles de sécurité. Une puce TPM, ou un module TPM matériel, est un processeur de chiffrement sécurisé qui vous aide à effectuer des actions telles que la génération, le stockage des clés de chiffrement et leur limitation d’utilisation. 

Microsoft fournit des informations importantes sur le fonctionnement des modules TPM avec Microsoft Windows. Pour plus d’informations, consultez cet article relatif au [module de plateforme sécurisée (TPM)](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node). 

Un TPM logiciel est un émulateur qui reproduit cette fonctionnalité. 

 ## <a name="authentication-factors-and-their-strengths"></a>Facteurs d’authentification et points forts

Les facteurs d’authentification peuvent être regroupés en trois catégories. Le tableau suivant présente des exemples de types de facteurs sous chaque regroupement.

![Représentation graphique de quelque chose que vous connaissez, quelque chose que vous avez ou quelque chose que vous êtes.](media/nist-authentication-basics/nist-authentication-basics-0.png)

La force d’un facteur d’authentification dépend de la manière dont nous pouvons être certain qu’il s’agit de quelque chose que seul l’abonné connaît, a ou est.

Les recommandations relatives à la force relative des facteurs d’authentification sont limitées dans NIST. Chez Microsoft, nous évaluons les points forts comme indiqué ci-dessous. 

**Quelque chose que connaissez** : les mots de passe, la chose la plus courante que vous connaissez, représentent la plus grande surface d’attaque. Les atténuations suivantes améliorent la confiance en termes d’affinité envers l’abonné et s’appliquent à la prévention des attaques de mot de passe telles que les attaques par force brute, l’écoute clandestine et l’ingénierie sociale :

* [Exigences en termes de « complexité » du mot de passe](https://www.microsoft.com/research/wp-content/uploads/2016/06/Microsoft_Password_Guidance-1.pdf)

* [Mots de passe interdits](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-configure-custom-password-protection)

* [Informations d'identification fuitées](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

* [Stockage haché sécurisé](https://aka.ms/AADDataWhitepaper)

* [Verrouillage de compte](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)

**Quelque chose que vous avez** : la force de quelque chose que vous avez repose sur la probabilité que l’abonné conserve cette chose et la difficulté pour un pirate à y accéder. Par exemple, un appareil mobile personnel ou une clé matérielle présentera une affinité plus élevée et, dès lors, se révélera plus sécurisé qu’un ordinateur de bureau à des fins de protection contre les menaces internes.

**Quelque chose que vous êtes** : la facilité avec laquelle une personne malveillante peut obtenir une copie de ce que vous êtes ou usurper un élément biométrique, est importante. NIST élabore une infrastructure pour la biométrie. Aujourd’hui, NIST n’accepte pas la biométrie comme méthode d’authentification distincte. Il doit s’agir d’un facteur dans le cadre d’une authentification multifacteur. Et ce, en raison du fait que la biométrie est, par nature, probabiliste. Autrement dit, elle utilise des algorithmes qui déterminent la probabilité qu’il s’agit de la même personne. Contrairement à un mot de passe, il ne s’agit pas nécessairement d’une correspondance exacte. Consultez ce document relatif à la [Force de la fonction pour les authentificateurs – Biométrie](https://pages.nist.gov/SOFA/SOFA.html) (SOFA-B). SOFA-B tente de présenter une infrastructure pour la quantité de valeur de la biométrie en termes de taux de fausse correspondance, de taux d’échec, de détection d’attaque de présentation et d’effort requis pour lancer une attaque. 

## <a name="single-factor-authentication"></a>‎Authentification monofacteur

L’authentification monofacteur peut être obtenue à l’aide d’un authentificateur monofacteur correspondant à quelque chose que vous connaissez ou quelque chose que vous avez. Bien qu’un facteur d’authentification correspondant à « quelque chose que vous êtes » soit accepté en tant que facteur d’authentification, il ne l’est pas en tant qu’authentificateur. 

![Image conceptuelle de l’authentification monofacteur.](media/nist-authentication-basics/nist-authentication-basics-1.png)

## <a name="multi-factor-authentication"></a>Authentification multifacteur

L’authentification multifacteur peut être obtenue soit par un authentificateur multifacteur, soit par une combinaison de deux authentificateurs monofacteurs. Un authentificateur multifacteur requiert deux facteurs d’authentification pour exécuter une seule transaction d’authentification.

### <a name="multi-factor-authentication-using-two-single-factor-authenticators"></a>Authentification multifacteur utilisant deux authentificateurs monofacteurs

L’authentification multifacteur requiert deux facteurs d’authentification distincts. Il peut s’agir de deux authentificateurs indépendants, tels que : 

* Secret mémorisé [mot de passe] et hors bande [SMS]

* Secret mémorisé [mot de passe] et mot de passe à usage unique [matériel ou logiciel]

Ces méthodes effectuent deux transactions d’authentification indépendantes avec Azure AD.

![Image conceptuelle de l’authentification multifacteur à l’aide de deux authentificateurs distincts.](media/nist-authentication-basics/nist-authentication-basics-2.png)


### <a name="multi-factor-authentication-using-a-single-multi-factor-authenticator"></a>Authentification multifacteur à l’aide d’un seul authentificateur multifacteur

L’authentification multifacteur requiert un facteur d’authentification (quelque chose que vous connaissez ou quelque chose que vous êtes) pour déverrouiller un deuxième facteur d’authentification. Il s’agit généralement d’une expérience utilisateur plus simple que l’utilisation de plusieurs authentificateurs indépendants.

![Image conceptuelle de l’authentification multifacteur avec un seul authentificateur multifacteur.](media/nist-authentication-basics/nist-authentication-basics-3a.png)

Par exemple, l’application Microsoft Authenticator utilisée en mode sans mot de passe. Avec cette méthode, l’utilisateur tente d’accéder à une ressource sécurisée (partie de confiance) et reçoit une notification sur son application d’authentification. L’utilisateur répond à une notification en fournissant un élément biométrique (quelque chose que vous êtes) ou un code PIN (quelque chose que vous connaissez), qui déverrouille la clé de chiffrement sur le téléphone (quelque chose que vous avez), qui est ensuite validée par le vérificateur.

## <a name="next-steps"></a>Étapes suivantes 

[Présentation du NIST](nist-overview.md)

[En savoir plus sur les niveaux AAL](nist-about-authenticator-assurance-levels.md)

[Principes fondamentaux de l’authentification](nist-authentication-basics.md)

[Types d'authentificateurs NIST](nist-authenticator-types.md)

[Atteindre le niveau AAL1 NIST avec Azure AD](nist-authenticator-assurance-level-1.md)

[Atteindre le niveau AAL2 NIST avec Azure AD](nist-authenticator-assurance-level-2.md)

[Atteindre le niveau AAL3 NIST avec Azure AD](nist-authenticator-assurance-level-3.md) 