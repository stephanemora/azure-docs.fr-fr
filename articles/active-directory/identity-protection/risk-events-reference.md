---
title: Informations de référence sur les détections d’événements à risque Azure Active Directory Identity Protection | Microsoft Docs
description: Informations de référence sur les détections d’événements à risque Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: reference
ms.date: 01/25/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1005ac1853568222dea83a0f12293945825cf6d
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127572"
---
# <a name="azure-active-directory-identity-protection-risk-detections-reference"></a>Informations de référence sur les détections d’événements à risque Azure Active Directory Identity Protection

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Détecter les identités compromises n’est pas chose aisée. Azure Active Directory utilise les algorithmes Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux comptes de votre utilisateur. Chaque action suspecte détectée est stockée dans un enregistrement appelé détection d’événement à risque.

## <a name="anonymous-ip-address"></a>Adresse IP anonyme

**Type de détection :** Temps réel  
**Ancien nom :** Connexions depuis une adresse IP anonyme

Ce type de détection d’événement à risque indique des connexions à partir d’une adresse IP anonyme (par exemple, navigateur Tor, réseaux privés virtuels anonymiseurs).
Ces adresses IP sont généralement utilisées par des acteurs souhaitant masquer leur télémétrie de connexion (adresse IP, emplacement, appareil, etc.) dans un but potentiellement malveillant.

## <a name="atypical-travel"></a>Voyage inhabituel

**Type de détection :** Hors ligne  
**Ancien nom :** Voyage impossible vers des emplacements inhabituels

Ce type de détection d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. Entre autres facteurs, cet algorithme Machine Learning prend en compte le délai écoulé entre les deux connexions et le temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification.

L’algorithme ignore les « faux positifs » évidents contribuant aux conditions de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale la plus proche de 14 jours ou de 10 connexions lui servant à assimiler le comportement de connexion des nouveaux utilisateurs.

## <a name="leaked-credentials"></a>Informations d’identification divulguées

**Type de détection :** Hors ligne  
**Ancien nom :** Utilisateurs avec des informations d’identification volées

Ce type de détection d’événement à risque indique que les informations d’identification valides de l’utilisateur ont fuité.
Souvent, lorsque les cybercriminels compromettent les mots de passe valides d’utilisateurs légitimes, ils le font dans le but de les rendre publics. En général, les mots de passe volés sont publiés sur le « dark web », ou bien ils sont échangés ou vendus sur le marché noir. Le service Microsoft chargé des informations d’identification volées acquiert les paires nom d’utilisateur/mot de passe en surveillant les sites publics et les sites du « dark web », et en travaillant avec :

- Des chercheurs
- Les lois en vigueur
- Les équipes de sécurité Microsoft
- D’autres sources approuvées

Lorsque le service acquiert des informations d’identification utilisateur sur le dark web, de sites collés ou des sources ci-dessus, elles sont comparées aux informations d’identification valides actuelles des utilisateurs d’Azure AD pour rechercher des correspondances valides.

## <a name="malware-linked-ip-address"></a>Adresse IP liée à un programme malveillant

**Type de détection :** Hors ligne  
**Ancien nom :** Connexions depuis des appareils infectés

Ce type de détection d’événement à risque indique les connexions depuis des adresses IP infectées par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur bot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot lorsqu’il était actif.

## <a name="unfamiliar-sign-in-properties"></a>Propriétés de connexion inhabituelles

**Type de détection :** Temps réel  
**Ancien nom :** Connexions depuis des emplacements non connus

Ce type de détection d’événement à risque prend en compte l’historique des connexions antérieures (IP, latitude / longitude et NSA) pour rechercher des connexions anormales. Le système stocke les informations sur les emplacements précédents d’un utilisateur et considère ces emplacements comme « connus ». La détection d’événement à risque est déclenchée quand la connexion a lieu depuis un emplacement qui ne figure pas déjà dans la liste des emplacements connus. Les utilisateurs nouvellement créés seront en « mode d’apprentissage » pendant une période de temps durant laquelle les détections d’événements à risque des propriétés de connexion inconnues seront désactivées alors que nos algorithmes apprennent le comportement de l’utilisateur. La durée du mode d’apprentissage est dynamique et varie selon le temps qu’il faut à l’algorithme pour collecter suffisamment d’informations sur les modèles de connexion de l’utilisateur. La durée minimale est de 5 jours. Un utilisateur peut revenir en mode d’apprentissage après une longue période d’inactivité. Le système ignore également les connexions depuis les appareils connus et les emplacements géographiquement proches d’un emplacement connu. 

Nous exécutons également cette détection pour l’authentification de base (ou les protocoles existants). Étant donné que ces protocoles ne proposent pas les propriétés modernes, telles que l’ID client, les données de télémétrie pour réduire le nombre de faux positifs sont limitées. Nous recommandons à nos clients de passer à l’authentification moderne.

## <a name="azure-ad-threat-intelligence"></a>Azure AD Threat Intelligence

**Type de détection :** Hors ligne <br>
**Ancien nom :** Cette détection s’affichera dans les rapports Azure AD Identity Protection hérités (utilisateurs avec indicateur de risque, détections d’événements à risque) comme « Utilisateurs dont les informations d’identification ont fuité ».

Ce type de détection d’événement à risque indique une activité utilisateur inhabituelle pour l’utilisateur donné ou qui est cohérente avec des modèles d’attaque connus selon les sources internes et externes de Microsoft Threat Intelligence.

## <a name="admin-confirmed-user-compromised"></a>L’administrateur a confirmé que cet utilisateur est compromis

**Type de détection :** Hors ligne <br>
Cette détection indique qu’un administrateur a sélectionné « Confirmer que l’utilisateur est compromis » dans l’interface utilisateur Utilisateurs à risque ou à l’aide de l’API riskyUsers. Pour voir quel administrateur a confirmé que cet utilisateur est compromis, consultez l’historique des risques de l’utilisateur (par le biais de l’interface utilisateur ou de l’API).

## <a name="malicious-ip-address"></a>Adresse IP malveillante

**Type de détection :** Hors ligne <br>
Cette détection indique une connexion à partir d’une adresse IP malveillante. Une adresse IP est considérée comme malveillante en fonction des éléments suivants :
-   Taux d’échec élevé (en raison d’informations d’identification non valides reçues de l’adresse IP)
-   Autres sources sur la réputation de l’adresse IP

## <a name="additional-risk-detected"></a>Risque supplémentaire détecté

**Type de détection :** En temps réel ou hors connexion <br>
Cette détection indique que l’une des détections Premium ci-dessus a eu lieu. Étant donné que les détections Premium ne sont visibles que pour les clients Azure AD Premium P2, elles s’intitulent « Risque supplémentaire détecté » pour les clients non-P2.
