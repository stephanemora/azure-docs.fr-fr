---
title: Référence des événements à risque Azure Active Directory Identity Protection | Microsoft Docs
description: Référence des événements à risque Azure Active Directory Identity Protection.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 14f7fc83-f4bb-41bf-b6f1-a9bb97717c34
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2018
ms.author: markvi
ms.reviewer: raluthra
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1509f40b88e3dc9c51bd00ed379c5b0130230a99
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/13/2019
ms.locfileid: "56178836"
---
# <a name="azure-active-directory-identity-protection-risk-events-reference"></a>Référence des événements à risque Azure Active Directory Identity Protection

La grande majorité des violations de sécurité ont lieu lorsque des cybercriminels parviennent à accéder à un environnement en volant l’identité d’un utilisateur. Détecter les identités compromises n’est pas chose aisée. Azure Active Directory utilise les algorithmes Machine Learning et des modèles heuristiques adaptatifs pour détecter les actions suspectes liées aux comptes de votre utilisateur. Chaque action suspecte détectée est stockée dans un enregistrement appelé événement à risque.


## <a name="anonymous-ip-address"></a>Adresse IP anonyme

**Type de détection :** Temps réel  
**Ancien nom :** Connexions depuis une adresse IP anonyme


Ce type d’événement à risque indique des connexions à partir d’une adresse IP anonyme (par exemple, navigateur Tor, réseaux privés virtuels anonymiseurs).
Ces adresses IP sont généralement utilisées par des acteurs souhaitant masquer leur télémétrie de connexion (adresse IP, emplacement, appareil, etc.) dans un but potentiellement malveillant.


## <a name="atypical-travel"></a>Voyage inhabituel

**Type de détection :** Hors ligne  
**Ancien nom :** Voyage impossible vers des emplacements inhabituels


Ce type d’événement à risque identifie deux connexions depuis des emplacements géographiquement distants, dont l’un au moins un est inhabituel pour l’utilisateur compte tenu de son comportement passé. Entre autres facteurs, cet algorithme Machine Learning prend en compte le délai écoulé entre les deux connexions et le temps nécessaire pour se déplacer du premier emplacement au second, ce qui indique qu’un autre utilisateur utilise les mêmes informations d’identification.

L’algorithme ignore les « faux positifs » évidents contribuant aux conditions de voyage impossible, tels que les VPN et les emplacements régulièrement utilisés par d’autres membres de l’organisation. Le système présente une période d’apprentissage initiale la plus proche de 14 jours ou de 10 connexions lui servant à assimiler le comportement de connexion des nouveaux utilisateurs.


## <a name="leaked-credentials"></a>Informations d’identification divulguées

**Type de détection :** Hors ligne  
**Ancien nom :** Utilisateurs avec des informations d’identification volées


Ce type d’événement à risque indique que les informations d’identification valides de l’utilisateur ont fuité.
Souvent, lorsque les cybercriminels compromettent les mots de passe valides d’utilisateurs légitimes, ils le font dans le but de les rendre publics. En général, les mots de passe volés sont publiés sur le « dark web », ou bien ils sont échangés ou vendus sur le marché noir. Le service Microsoft chargé des informations d’identification volées acquiert les paires nom d’utilisateur/mot de passe en surveillant les sites publics et les sites du « dark web », et en travaillant avec :

- Des chercheurs

- Les lois en vigueur

- Les équipes de sécurité Microsoft

- D’autres sources approuvées

Lorsque le service acquiert des informations d’identification utilisateur sur le dark web, de sites collés ou des sources ci-dessus, elles sont comparées aux informations d’identification valides actuelles des utilisateurs d’Azure AD pour rechercher des correspondances valides.


## <a name="malware-linked-ip-address"></a>Adresse IP liée à un programme malveillant

**Type de détection :** Hors ligne  
**Ancien nom :** Connexions depuis des appareils infectés


Ce type d’événement à risque indique les connexions depuis des adresses IP infectées par des logiciels malveillants, qui sont connus pour communiquer activement avec un serveur robot, grâce à la mise en corrélation des adresses IP des appareils des utilisateurs avec des adresses ayant été en contact avec un serveur robot lorsqu’il était actif.


## <a name="unfamiliar-sign-in-properties"></a>Propriétés de connexion inhabituelles

**Type de détection :** Temps réel  
**Ancien nom :** Connexions depuis des emplacements non connus

Ce type d’événement à risque prend en compte les propriétés de connexion antérieures (par exemple, appareil, emplacement, réseau) pour déterminer les connexions avec des propriétés inhabituelles. Le système stocke les propriétés d’emplacements précédents utilisés par un utilisateur et les considère comme « habituels ». L’événement à risque est déclenché lorsque la connexion se fait avec des propriétés qui ne figurent pas dans la liste des propriétés habituelles. Le système a une période d’apprentissage initiale de 30 jours, durant laquelle il ne signale pas les nouvelles détections.
Nous exécutons également cette détection pour l’authentification de base (ou les protocoles existants). Étant donné que ces protocoles ne proposent pas les propriétés modernes, telles que l’ID client, les données de télémétrie pour réduire le nombre de faux positifs sont limitées. Nous recommandons à nos clients de passer à l’authentification moderne.

