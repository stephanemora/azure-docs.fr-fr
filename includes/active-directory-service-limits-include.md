---
title: Fichier Include
description: Fichier Include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 06/20/2018
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 5529a4510b6d9f005dc46165e1be799585e5a153
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755887"
---
Présentation des contraintes d'utilisation et autres limites de service pour le service Azure Active Directory.

| Catégorie | Limites |
| --- | --- |
| Répertoires |Un utilisateur peut être associé à un maximum de 500 répertoires Azure Active Directory.<br />Exemples de combinaisons possibles : <ul> <li>Un utilisateur unique crée 500 répertoires.</li><li>Un utilisateur unique est ajouté à 500 répertoires en tant que membre.</li><li>Un utilisateur unique crée 250 répertoires et est ajouté ultérieurement par d'autres à 250 autres répertoires.</li></ul> |
| Domaines | Vous ne pouvez pas ajouter plus de 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec le répertoire Active Directory local, vous ne pouvez pas ajouter plus de 450 noms de domaine dans chaque répertoire. |
| Objets |<ul><li>L’édition gratuite d’Azure Active Directory permet de créer 500 000 objets au maximum dans un répertoire.</li><li>Un utilisateur non administrateur peut créer jusqu’à 250 objets.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir jusqu’à 256 caractères. </li><li>Les extensions de type binaire sont limitées à 256 octets.</li><li>100 valeurs d'extension (pour TOUT type et TOUTE application) peuvent être écrites pour un objet unique.</li><li>Actuellement, seules les entités « Utilisateur », « Groupe », « TenantDetail », « Périphérique », « Application » et « ServicePrincipal » peuvent être étendues avec des attributs à valeur unique de type « binaire » ou « chaîne ».</li><li>Les extensions de schéma sont disponibles uniquement dans l’aperçu de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| APPLICATIONS |100 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>100 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Un nombre quelconque d'objets peuvent être membres d'un groupe unique dans Azure Active Directory.</li><li>Le nombre de membres dans un groupe, que vous pouvez synchroniser à partir de votre annuaire Active Directory local vers Azure Active Directory est limité à 15 000, avec Azure Active Directory Synchronization (DirSync).</li><li>Le nombre de membres dans un groupe, que vous pouvez synchroniser à partir de votre annuaire Active Directory local vers Azure Active Directory à l’aide d’Azure AD Connect, est limité à 50 000.</li></ul> |
| Volet d'accès |<ul><li>Le nombre d’applications affichables dans le Panneau d’accès par utilisateur final pour les abonnés à Azure Active Directory Premium ou Enterprise Mobility Suite est illimité.</li><li>Un maximum de 10 vignettes d’application (exemples : Box, Salesforce, ou Dropbox) peuvent être consultées dans le Panneau d’accès pour chaque utilisateur disposant d’une licence de l’édition gratuite ou de base d’Azure Active Directory. Cette limite ne s'applique pas aux comptes d'administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Un objet peut faire partie de 30 unités administratives maximum. |
