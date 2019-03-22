---
title: Fichier Include
description: Fichier Include
services: active-directory
author: curtand
ms.service: active-directory
ms.topic: include
ms.date: 02/21/2019
ms.author: curtand
ms.custom: include file
ms.openlocfilehash: 38f2dd301ddc2a5f8d28322856b2011bd2034c30
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554595"
---
Présentation des contraintes d’utilisation et autres limites de service pour le service Azure Active Directory (Azure AD).

| Catégorie | Limites |
| --- | --- |
| Répertoires | Un seul utilisateur peut appartenir à un maximum de 500 annuaires Azure AD en tant que membre ou invité.<br/>Un seul utilisateur peut créer un maximum de 20 annuaires. |
| Domaines | Vous ne pouvez pas ajouter plus de 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec Active Directory en local, vous pouvez ajouter pas plus de 450 noms de domaine dans chaque répertoire. |
| Objets |<ul><li>L’édition gratuite d’Azure Active Directory permet de créer 500 000 objets au maximum dans un répertoire.</li><li>Un utilisateur non administrateur peut créer jusqu’à 250 objets. Les objets actifs et les objets supprimés qui sont disponibles pour la restauration de ce quota sont comptabilisées. Uniquement les objets supprimés qui ont été supprimés, il y a moins de 30 jours sont disponibles pour la restauration. Objets supprimés qui ne sont plus disponibles pour la restauration sont comptabilisées ce quota sur la valeur un quart pendant 30 jours. Peut-être [affecter un rôle d’administrateur](../articles/active-directory/users-groups-roles/directory-assign-admin-roles.md) pour les utilisateurs non-administrateurs qui sont susceptibles à plusieurs reprises dépasser ce quota au cours de leurs fonctions régulières.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir un maximum de 256 caractères. </li><li>Extensions de type binaire sont limitées à 256 octets.</li><li>Uniquement 100 valeurs d’extension, à travers *tous les* types et *tous les* , les applications peuvent être écrites dans n’importe quel objet unique.</li><li>Seules les entités utilisateur, groupe, TenantDetail, Device, Application et principal du service peuvent être étendues avec le type de chaîne ou des attributs à valeur unique de type binaire.</li><li>Extensions de schéma sont uniquement disponibles dans l’aperçu de la version 1.21 API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| APPLICATIONS |100 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>100 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Un nombre quelconque d'objets peuvent être membres d'un groupe unique.</li><li>Un utilisateur peut être membre de n’importe quel nombre de groupes.</li><li>Le nombre de membres dans un groupe que vous pouvez synchroniser à partir de votre annuaire local Active Directory à Azure Active Directory à l’aide d’Azure AD Connect est limité à 50 000 membres.</li></ul> |
| Volet d'accès |<ul><li>Il n’existe aucune limite au nombre d’applications qui peuvent être consultés dans le volet d’accès par utilisateur. Cela s’applique aux utilisateurs affectés des licences pour Azure AD Premium ou Enterprise Mobility Suite.</li><li>Un maximum de 10 vignettes d’application peut être consulté dans le volet d’accès pour chaque utilisateur. Cette limite s’applique aux utilisateurs les licences sont attribuées gratuitement ou Azure AD Basic éditions d’Azure Active Directory. Les exemples de vignettes de l’application de zone, Salesforce ou Dropbox. Cette limite ne s’applique pas aux comptes d’administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Un objet peut faire partie de 30 unités administratives maximum. |
| Rôles et autorisations d'administrateur | <li>Un groupe ne peut pas être ajouté en tant que propriétaire.<li>Un groupe ne peut pas être affecté à un rôle.<li>Par défaut utilisateur autorisations ne peuvent pas être modifiées à l’exception des commutateurs de locataire, qui sont des paramètres utilisateur dans Azure AD. |
