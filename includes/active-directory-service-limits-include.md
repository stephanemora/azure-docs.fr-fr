---
title: Fichier Include
description: Fichier Include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/19/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 619dd7f3b01e2e7ce71e945fce77aa73cb87f264
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56443335"
---
Présentation des contraintes d’utilisation et autres limites de service pour le service Azure Active Directory (Azure AD).

| Catégorie | Limites |
| --- | --- |
| Répertoires | Un seul utilisateur peut appartenir à un maximum de 500 annuaires Azure AD en tant que membre ou invité.<br/>Un seul utilisateur peut créer un maximum de 20 annuaires. |
| Domaines | Vous ne pouvez pas ajouter plus de 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec le répertoire Active Directory local, vous ne pouvez pas ajouter plus de 450 noms de domaine dans chaque répertoire. |
| Objets |<ul><li>L’édition gratuite d’Azure Active Directory permet de créer 500 000 objets au maximum dans un répertoire.</li><li>Un utilisateur non administrateur peut créer jusqu’à 250 objets.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir jusqu’à 256 caractères. </li><li>Les extensions de type binaire sont limitées à 256 octets.</li><li>100 valeurs d'extension (pour TOUT type et TOUTE application) peuvent être écrites pour un objet unique.</li><li>Actuellement, seules les entités « Utilisateur », « Groupe », « TenantDetail », « Périphérique », « Application » et « ServicePrincipal » peuvent être étendues avec des attributs à valeur unique de type « binaire » ou « chaîne ».</li><li>Les extensions de schéma sont disponibles uniquement dans l’aperçu de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| APPLICATIONS |100 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>100 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Un nombre quelconque d'objets peuvent être membres d'un groupe unique.</li><li>Un utilisateur peut être membre de n’importe quel nombre de groupes.</li><li>Le nombre de membres dans un groupe, que vous pouvez synchroniser à partir de votre annuaire Active Directory local vers Azure Active Directory à l’aide d’Azure AD Connect, est limité à 50 000.</li></ul> |
| Volet d'accès |<ul><li>Le nombre d’applications affichables dans le Panneau d’accès par utilisateur final pour les abonnés à Azure Active Directory Premium ou Enterprise Mobility Suite est illimité.</li><li>Un maximum de 10 vignettes d’application (exemples : Box, Salesforce, ou Dropbox) peuvent être consultées dans le Panneau d’accès pour chaque utilisateur disposant d’une licence de l’édition gratuite ou de base d’Azure Active Directory. Cette limite ne s'applique pas aux comptes d'administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Un objet peut faire partie de 30 unités administratives maximum. |
| Rôles et autorisations d'administrateur | <li>Un groupe ne peut pas être ajouté en tant que propriétaire<li>Un groupe ne peut pas être affecté à un rôle<li>Impossible de modifier les autorisations utilisateur par défaut au-delà des commutateurs de client (paramètres utilisateur dans Azure AD) |
