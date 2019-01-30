---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Découvrez comment Azure AD Identity Protection vous permet de limiter la capacité d’un cybercriminel à exploiter une identité ou un appareil compromis et de sécuriser une identité ou un appareil déjà identifié comme potentiellement ou effectivement compromis.
services: active-directory
keywords: azure active directory identity protection, cloud app discovery, gestion d’applications, sécurité, risque, niveau de risque, vulnérabilité, stratégie de sécurité
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.component: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2018
ms.author: markvi
ms.reviewer: raluthra
ms.openlocfilehash: 73b76561829e6d50918881427894ea310920fce6
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54465197"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Présentation d’Azure Active Directory Identity Protection

Azure Active Directory Identity Protection est une fonctionnalité de l’édition Azure AD Premium P2 qui vous permet de :

- Détecter les vulnérabilités potentielles qui affectent les identités de votre organisation

- Configurer des réponses automatiques aux actions suspectes détectées qui sont liées aux identités de votre organisation  

- Examiner les incidents suspects et prendre les mesures appropriées pour les résoudre   


## <a name="get-started"></a>Prise en main

Microsoft sécurise les identités basées sur le cloud depuis plus de dix ans. Avec Azure Active Directory Identity Protection, vous pouvez dans votre environnement utiliser les mêmes systèmes de protection que Microsoft pour sécuriser les identités.

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Aujourd’hui, les attaquants arrivent de plus en plus à exploiter les failles de fournisseurs tiers et utilisent des attaques par hameçonnage (ou « phishing ») sophistiquées toujours plus efficaces. Dès qu’un cybercriminel accède à un compte d’utilisateur, même si les privilèges de celui-ci sont faibles, il peut facilement accéder à des ressources importantes pour l’entreprise de manière latérale.

Par conséquent, vous devez :

- Protéger toutes les identités, quel que soit leur niveau de privilège

- Empêcher proactivement le détournement des identités compromises

Détecter les identités compromises n’est pas chose aisée. Azure Active Directory utilise des algorithmes de Machine Learning et des modèles heuristiques adaptatifs pour détecter les anomalies et les incidents suspects qui révèlent des identités potentiellement compromises. Grâce à ces données, Identity Protection génère des rapports et des alertes qui vous permettent d’évaluer les problèmes détectés et de prendre les mesures de correction ou d’atténuation qui s’imposent.

Mais Azure Active Directory Identity Protection est bien plus qu’un outil de surveillance et de création de rapports. Pour protéger les identités de votre organisation, vous pouvez configurer des stratégies qui répondent automatiquement aux problèmes détectés lorsqu’un niveau de risque spécifié est atteint. Outre les autres contrôles d’accès conditionnel fournis par Azure Active Directory et EMS, ces stratégies peuvent automatiquement bloquer ou déclencher des mesures de correction adaptatives qui incluent des réinitialisations de mot de passe et la mise en œuvre de l’authentification multifacteur.


#### <a name="identity-protection-capabilities"></a>Fonctionnalités d’Identity Protection

**Détection des vulnérabilités et des comptes à risque :**  

* Recommandations personnalisées visant à améliorer la posture de sécurité globale en mettant en évidence les vulnérabilités
* Calcul des niveaux de risque à la connexion
* Calcul du niveau de risque des utilisateurs


**Examen des événements à risque :**

* Envoi de notifications pour les événements à risque
* Examen des événements à risque à l’aide d’informations contextuelles et pertinentes
* Workflows de base pour le suivi des investigations
* Accès rapide à des mesures de correction telles que la réinitialisation de mot de passe

**Stratégies d’accès conditionnel en fonction des risques :**

* Stratégie pour atténuer les connexions à risque en bloquant les connexions ou en imposant des demandes d’authentification multifacteur
* Stratégie pour bloquer ou sécuriser les comptes d’utilisateurs à risque
* Stratégie pour exiger que les utilisateurs s’inscrivent à l’authentification multifacteur



## <a name="identity-protection-roles"></a>Rôles de protection des identités (Identity Protection)

Pour équilibrer la charge des activités de gestion en ce qui concerne votre implémentation de la protection des identités, vous pouvez attribuer plusieurs rôles. Azure AD Identity Protection prend en charge 3 rôles d’annuaire :

| Rôle                         | Peut                          | Ne peut pas
| :--                          | ---                                |  ---   |
| Administrateur général         | Accès complet à Identity Protection, Onboard Identity Protection| |
| Administrateur de sécurité       | Accès complet à Identity Protection | Onboard Identity Protection, réinitialiser les mots de passe pour un utilisateur |
| Lecteur de sécurité              | Accès en lecture seule à Identity Protection | Onboard Identity Protection, résoudre les utilisateurs, configurer les stratégies, réinitialiser les mots de passe |




Pour plus d’informations, voir [Attribution de rôles d’administrateur dans Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)



## <a name="detection"></a>Détection

### <a name="vulnerabilities"></a>Vulnérabilités

Azure Active Directory Identity Protection analyse votre configuration et détecte les vulnérabilités qui peuvent avoir un impact sur les identités de vos utilisateurs. Pour en savoir plus, consultez [Vulnérabilités détectées par Azure Active Directory Identity Protection](vulnerabilities.md).

### <a name="risk-events"></a>Événements à risque

Azure Active Directory utilise les algorithmes de Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux identités de votre utilisateur. Le système crée un enregistrement pour chaque action suspecte détectée. Ces enregistrements sont également appelés événements à risque.  
Pour en savoir plus, consultez [Événements à risque dans Azure Active Directory](../active-directory-identity-protection-risk-events.md).


## <a name="investigation"></a>Investigation

Votre parcours dans Identity Protection commence généralement par le tableau de bord d’Identity Protection.

![Correction](./media/overview/1000.png "Correction")

Le tableau de bord vous donne accès à :

* des rapports comme **Utilisateurs associés à un indicateur de risque**, **Événements à risque** et **Vulnérabilités** ;
* des paramètres vous permettant notamment de configurer vos **stratégies de sécurité**, vos **notifications** et **l’inscription à l’authentification multifacteur**.

Il s’agit généralement de votre point de départ pour l’investigation, le processus consistant à vérifier les activités, les journaux et les autres informations pertinentes concernant un événement à risque, afin de déterminer s’il est nécessaire d’appliquer des mesures de correction ou d’atténuation, de comprendre comment l’identité a été compromise et d’identifier la manière dont l’identité compromise a été exploitée.

Vous pouvez lier vos activités d’investigation aux [notifications](notifications.md) d’Azure Active Directory Protection envoyées par courrier électronique.



## <a name="policies"></a>Stratégies

Pour implémenter des réponses automatiques, Azure Active Directory Identity Protection offre trois stratégies :

- [Stratégie d’inscription à l’authentification multifacteur](howto-mfa-policy.md)

- [Stratégie de risque d’utilisateur](howto-user-risk-policy.md)

- [Stratégie en matière de risque à la connexion](howto-sign-in-risk-policy.md)


## <a name="next-steps"></a>Étapes suivantes

- [Channel 9 : Azure AD et Identity Show : Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

- [Activer Azure Active Directory Identity Protection](enable.md)

