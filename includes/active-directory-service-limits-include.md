---
title: Fichier Include
description: Fichier Include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 05/22/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 3b1019d45f4fee60e0e197f283ef38f4f3fca875
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143064"
---
Présentation des contraintes d’utilisation et autres limites de service pour le service Azure Active Directory (Azure AD).

| Catégorie | Limites |
| --- | --- |
| Répertoires | Un seul utilisateur peut appartenir à un maximum de 500 annuaires Azure AD en tant que membre ou invité.<br/>Un seul utilisateur peut créer un maximum de 20 annuaires. |
| Domaines | Vous ne pouvez pas ajouter plus de 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec l’annuaire Active Directory local, vous ne pouvez pas ajouter plus de 450 noms de domaine dans chaque annuaire. |
| Objets |<ul><li>L’édition gratuite d’Azure Active Directory permet de créer par défaut 50 000 objets au maximum dans un annuaire. Si vous avez au moins un domaine vérifié, le quota de service d’annuaire par défaut dans Azure AD est étendu à 300 000 objets. </li><li>Un utilisateur non administrateur peut créer jusqu’à 250 objets. Les objets actifs et les objets supprimés disponibles pour la restauration sont comptabilisés dans ce quota. Seuls les objets supprimés il y a moins de 30 jours sont disponibles pour la restauration. Les objets supprimés qui ne sont plus disponibles pour la restauration comptent pour un quart de 30 jours dans ce quota. Vous pouvez peut-être [attribuer un rôle Administrateur](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) aux utilisateurs non-administrateurs susceptibles de dépasser ce quota à plusieurs reprises au cours de leurs fonctions normales.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir jusqu’à 256 caractères. </li><li>Les extensions de type binaire sont limitées à 256 octets.</li><li>Vous pouvez écrire dans un objet seulement 100 valeurs d’extension (de *tous* les types et pour *toutes* les applications).</li><li>Actuellement, seules les entités User, Group, TenantDetail, Device, Application et ServicePrincipal peuvent être étendues avec des attributs à valeur unique de type binaire ou chaîne.</li><li>Les extensions de schéma sont disponibles uniquement dans la préversion de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| APPLICATIONS |100 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>100 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Un nombre quelconque d'objets peuvent être membres d'un groupe unique.</li><li>Un utilisateur peut être membre de n’importe quel nombre de groupes.</li><li>Vous ne pouvez pas synchroniser plus de 50 000 membres d’un groupe de votre annuaire Active Directory local vers Azure Active Directory à l’aide d’Azure AD Connect.</li></ul> |
| Proxy d'application | <ul><li>Un maximum de 500 transactions par seconde par l’application de Proxy d’application</li><li>Un maximum de 750 transactions par seconde pour le locataire</li></ul><br/>Une transaction est définie comme une seule requête et réponse http pour une ressource unique. Lors de la limitation, les clients recevront une réponse 429 (trop de demandes). |
| Volet d'accès |<ul><li>Le nombre d’applications visibles dans le volet d’accès n’est pas limité par utilisateur. Cela s’applique aux licences utilisateur d’Azure AD Premium ou d’Enterprise Mobility Suite.</li><li>10 vignettes d’application maximum sont visibles dans le volet d’accès pour chaque utilisateur. Cette limite s’applique aux licences utilisateur de l’édition gratuite ou de l’édition Azure AD de base d’Azure Active Directory. Exemples de vignettes d’application : Box, Salesforce ou Dropbox. Cette limite ne s’applique pas aux comptes Administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Un objet peut faire partie de 30 unités administratives maximum. |
| Rôles et autorisations d'administrateur | <li>Vous ne pouvez pas ajouter un groupe comme propriétaire.<li>Vous ne pouvez pas attribuer un rôle à un groupe.<li>Vous ne pouvez pas changer les autorisations par défaut de l’utilisateur, sauf pour les changements de locataire, qui sont des paramètres utilisateur dans Azure AD. |
