---
title: Création d'une stratégie d’accès conditionnel - Azure Active Directory
description: Quelles sont les options disponibles pour créer une stratégie d’accès conditionnel et que signifient-elles ?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f3e815f541ad4cfabc22d917ca9cecba47b50f
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077937"
---
# <a name="building-a-conditional-access-policy"></a>Créer une stratégie d’accès conditionnel

Comme expliqué dans l’article [Qu’est-ce que l’accès conditionnel ?](overview.md), une stratégie d'accès conditionnel est une instruction if-then d'**affectations** et de **contrôles d'accès**. Une stratégie d’accès conditionnel regroupe des signaux pour prendre des décisions et appliquer des stratégies organisationnelles.

Comment une organisation crée-t-elle ces stratégies ? Qu’est-ce qui est requis ?

![Accès conditionnel (signaux + décisions + application = stratégies)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Affectations

La partie Affectations contrôle les personnes, les éléments et l’emplacement de la stratégie d’accès conditionnel.

### <a name="users-and-groups"></a>Utilisateurs et groupes

Les utilisateurs et les groupes attribuent les personnes que la stratégie inclura et exclura. Cette affectation peut inclure tous les utilisateurs, des groupes d’utilisateurs spécifiques, des rôles d’annuaire ou des utilisateurs invités externes. 

### <a name="cloud-apps-or-actions"></a>Applications ou actions cloud

Les applications ou actions cloud peuvent inclure ou exclure des applications cloud ou des actions utilisateur qui seront soumises à la stratégie.

### <a name="conditions"></a>Conditions

Une stratégie peut contenir plusieurs conditions.

#### <a name="sign-in-risk"></a>Risque à la connexion

Pour les organisations dotées d'[Azure AD Identity Protection](../identity-protection/overview.md), les détections de risques générées peuvent influencer vos stratégies d’accès conditionnel.

#### <a name="device-platforms"></a>Plateformes d’appareils

Les organisations utilisant plusieurs plateformes de système d’exploitation d'appareil peuvent souhaiter appliquer des stratégies spécifiques sur différentes plateformes. 

Les informations utilisées pour calculer la plateforme d'appareil proviennent de sources non vérifiées, telles que des chaînes d'agent utilisateur qui peuvent être modifiées.

#### <a name="locations"></a>Emplacements

Les données d’emplacement sont fournies par les données de géolocalisation IP. Les administrateurs peuvent choisir de définir des emplacements et d’en marquer certains comme approuvés, par exemple les emplacements réseau de leur organisation.

#### <a name="client-apps"></a>Applications clientes

Par défaut, les stratégies d’accès conditionnel s’appliquent aux applications de navigateur, aux applications mobiles et aux clients de bureau qui prennent en charge l’authentification moderne. 

Cette condition d’affectation permet aux stratégies d’accès conditionnel de cibler des applications clientes spécifiques n’utilisant pas l’authentification moderne. Ces applications incluent les clients Exchange ActiveSync, les anciennes applications Office n’utilisant pas l’authentification moderne et les protocoles de messagerie comme IMAP, MAPI, POP et SMTP.

#### <a name="device-state"></a>État de l’appareil

Ce contrôle est utilisé pour exclure des appareils hybrides Azure AD joints ou marqués comme conformes dans Intune. Cette exclusion permet de bloquer les appareils non gérés. 

## <a name="access-controls"></a>Contrôles d’accès

La partie Contrôles d’accès de la stratégie d’accès conditionnel contrôle la manière dont une stratégie est appliquée.

### <a name="grant"></a>Grant (Autoriser)

#### <a name="block-access"></a>Bloquer l’accès

Le blocage permet de bloquer l'accès d'affectations spécifiées. Le contrôle de blocage est un puissant outil et doit être utilisé moyennant les connaissances appropriées.

#### <a name="grant-access"></a>Accorder l'accès

Le contrôle d'octroi peut déclencher l’application d’un ou de plusieurs contrôles. 

- Exiger une authentification multifacteur (Microsoft Azure Multi-Factor Authentication)
- Exiger que l'appareil soit marqué comme conforme (Intune)
- Exiger un appareil joint Azure AD Hybride
- Demander une application cliente approuvée
- Exiger une stratégie de protection des applications

Les administrateurs peuvent choisir d’exiger un des contrôles précédents ou tous les contrôles sélectionnés à l’aide des options suivantes. La valeur par défaut pour plusieurs contrôles consiste à tous les exiger.

- Exiger tous les contrôles sélectionnés
- Exiger un des contrôles sélectionnés

### <a name="session"></a>session

Les contrôles de session peuvent limiter l’expérience 

- Utiliser les restrictions appliquées par l’application
   - Fonctionne actuellement avec Exchange Online et SharePoint Online uniquement.
      - Transmet des informations sur l’appareil pour permettre le contrôle de l’expérience qui octroie un accès total ou limité.
- Utiliser le contrôle d'application par accès conditionnel
   - Utilise les signaux de Microsoft Cloud App Security pour effectuer des opérations telles que : 
      - Bloquer le téléchargement, couper, copier et imprimer des documents sensibles.
      - Surveiller un comportement de session à risque.
      - Exiger l’étiquetage des fichiers sensibles.
- Fréquence de connexion
   - Possibilité de modifier la fréquence de connexion par défaut pour l’authentification moderne.
- Session de navigateur persistante
   - Permet aux utilisateurs de rester connectés après la fermeture et la réouverture de la fenêtre du navigateur.

## <a name="simple-policies"></a>Stratégies simples

Une stratégie d’accès conditionnel doit contenir au moins les éléments suivants à appliquer :

- **Nom** de la stratégie.
- **Affectations**
   - **Utilisateurs et/ou groupes** auxquels appliquer la stratégie.
   - **Applications ou actions cloud** auxquelles appliquer la stratégie.
- **Contrôles d’accès**
   - **Octroyer** ou **Bloquer** des contrôles

![Stratégie d’accès conditionnel vide](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

## <a name="next-steps"></a>Étapes suivantes

[Simuler le comportement de connexion à l’aide de l’outil What If pour l’accès conditionnel](troubleshoot-conditional-access-what-if.md)

[Stratégies d’accès conditionnel courantes](concept-conditional-access-policy-common.md)

[Planification d’un déploiement Azure Multi-Factor Authentication basé sur le cloud](../authentication/howto-mfa-getstarted.md)

[Gestion de la conformité des appareils avec Intune](https://docs.microsoft.com/intune/device-compliance-get-started)

[Microsoft Cloud App Security et accès conditionnel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
