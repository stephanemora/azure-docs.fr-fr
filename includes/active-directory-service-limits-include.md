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
ms.openlocfilehash: c58977798fabc120f3a8647774f575bc32515dcd
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219029"
---
Présentation des contraintes d’utilisation et autres limites de service pour le service Azure Active Directory (Azure AD).

| Category | Limites |
| --- | --- |
| Répertoires | Un seul utilisateur peut appartenir à un maximum de 500 annuaires Azure AD en tant que membre ou invité.<br/>Un seul utilisateur peut créer un maximum de 20 annuaires. |
| Domaines | Vous ne pouvez pas ajouter plus de 900 noms de domaine managé. Si vous configurez tous vos domaines pour la fédération avec l’annuaire Active Directory local, vous ne pouvez pas ajouter plus de 450 noms de domaine dans chaque annuaire. |
|Ressources |<ul><li>L’édition gratuite d’Azure Active Directory permet de créer par défaut 50 000 ressources Azure AD au maximum dans un annuaire. Si vous avez au moins un domaine vérifié, le quota de service d’annuaire par défaut dans Azure AD est étendu à 300 000 ressources Azure AD. </li><li>Un utilisateur non-administrateur peut créer jusqu’à 250 ressources Azure AD. Les ressources actives et les ressources supprimées qui peuvent être restaurées sont comptabilisées dans ce quota. Seules les ressources Azure AD supprimées il y a moins de 30 jours peuvent être restaurées. Les ressources Azure AD supprimées qui ne peuvent plus être restaurées comptent pour un quart dans ce quota pendant 30 jours. Si vos développeurs sont susceptibles de dépasser régulièrement ce quota dans le cadre de leurs tâches habituelles, vous pouvez [créer et attribuer un rôle personnalisé](../articles/active-directory/users-groups-roles/roles-quickstart-app-registration-limits.md) avec l’autorisation de créer un nombre illimité d’inscriptions d’applications.</li></ul> |
| Extensions de schéma |<ul><li>Les extensions de type chaîne peuvent avoir jusqu’à 256 caractères. </li><li>Les extensions de type binaire sont limitées à 256 octets.</li><li>Seules 100 valeurs d’extension (de *tous* les types et pour *toutes* les applications) peuvent être écrites dans une ressource Azure AD.</li><li>Actuellement, seules les entités User, Group, TenantDetail, Device, Application et ServicePrincipal peuvent être étendues avec des attributs à valeur unique de type binaire ou chaîne.</li><li>Les extensions de schéma sont disponibles uniquement dans la préversion de la version 1.21 de l’API Graph. L’accès en écriture pour enregistrer une extension doit être accordé à l’application.</li></ul> |
| APPLICATIONS |100 utilisateurs au maximum peuvent être propriétaires d'une seule application. |
| Groupes |<ul><li>100 utilisateurs au maximum peuvent être propriétaires d'un seul groupe.</li><li>Il n’y a pas de limite au nombre de ressources Azure AD qui peuvent faire partie d’un même groupe.</li><li>Un utilisateur peut être membre de n’importe quel nombre de groupes.</li><li>Vous ne pouvez pas synchroniser plus de 50 000 membres d’un groupe de votre annuaire Active Directory local vers Azure Active Directory à l’aide d’Azure AD Connect.</li></ul> |
| Proxy d’application | <ul><li>Un maximum de 500 transactions par seconde par application de proxy d’application</li><li>Un maximum de 750 transactions par seconde pour le locataire</li></ul><br/>Une transaction est définie comme une seule requête et réponse HTTP pour une ressource unique. En cas de limitation, les clients recevront une réponse 429 (trop de demandes). |
| Volet d'accès |<ul><li>Le nombre d’applications visibles dans le volet d’accès n’est pas limité par utilisateur. Cela s’applique aux licences utilisateur d’Azure AD Premium ou d’Enterprise Mobility Suite.</li><li>10 vignettes d’application maximum sont visibles dans le volet d’accès pour chaque utilisateur. Cette limite s’applique aux utilisateurs titulaires de licences Azure Active Directory Free. Exemples de vignettes d’application : Box, Salesforce ou Dropbox. Cette limite ne s’applique pas aux comptes Administrateur.</li></ul> |
| Rapports | 1 000 lignes au maximum peuvent être affichées ou téléchargées dans un rapport. Toutes les données supplémentaires sont tronquées. |
| Unités administratives | Une ressource Azure AD peut faire partie de 30 unités administratives au maximum. |
| Rôles et autorisations d'administrateur | <ul><li>Un groupe ne peut pas être ajouté en tant que [propriétaire](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#object-ownership).</li><li>Un groupe ne peut pas être affecté à un [rôle](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).</li><li>La capacité des utilisateurs à lire les informations d’annuaire des autres utilisateurs ne peut pas être limitée en dehors du commutateur à l’échelle du locataire pour désactiver l’accès de tous les utilisateurs non administrateurs à toutes les informations de répertoire (non recommandé). Pour plus d’informations sur les autorisations par défaut, cliquez [ici](https://docs.microsoft.com/azure/active-directory/fundamentals/users-default-permissions?context=azure/active-directory/users-groups-roles/context/ugr-context#to-restrict-the-default-permissions-for-member-users).</li><li>Il peut s’écouler jusqu'à 15 minutes ou bien falloir une déconnexion/connexion pour que les ajouts et révocations de l’appartenance au rôle d’administrateur prennent effet.</li></ul> |
