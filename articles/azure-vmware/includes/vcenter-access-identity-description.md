---
title: Description d’identité et d’accès dans vCenter
description: vCenter intègre un utilisateur local intégré nommé cloudadmin titulaire du rôle CloudAdmin.
ms.topic: include
ms.date: 08/31/2021
ms.openlocfilehash: 1fdc81c11b833b1239d37f613732eec42b463e1a
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252558"
---
<!-- used in concepts-run-commands.md and tutorial-configure-identity-source.md -->

Dans Azure VMware Solution, vCenter a un utilisateur local intégré nommé *cloudadmin* titulaire du rôle CloudAdmin. Vous pouvez configurer des utilisateurs et des groupes dans Active Directory (AD) avec le rôle CloudAdmin pour votre cloud privé. En général, le rôle CloudAdmin crée et gère les charges de travail dans votre cloud privé. Toutefois, dans Azure VMware Solution, le rôle CloudAdmin dispose de privilèges vCenter qui diffèrent de ceux d’autres solutions cloud et déploiements locaux VMware.

>[!IMPORTANT]
>L’utilisateur cloudadmin local doit être traité en tant que compte d’accès d’urgence pour les scénarios de « secours » dans votre cloud privé. Il ne s’agit pas d’activités d’administration quotidiennes ou d’intégration avec d’autres services. 

- Dans un déploiement local vCenter et ESXi, l’administrateur a accès au compte administrateur vCenter\@vsphere.local. Il peut également avoir d’autres utilisateurs et groupes AD attribués. 

- Dans un déploiement Azure VMware Solution, l’administrateur n’a pas accès au compte d’utilisateur administrateur. Il peut cependant assigner des utilisateurs et des groupes AD au rôle CloudAdmin dans vCenter.  Le rôle CloudAdmin ne dispose pas des autorisations nécessaires pour ajouter une source d’identité telle qu’un serveur LDAP ou LDAP sécurisé local à vCenter. En revanche, vous pouvez utiliser des commandes d’exécution pour ajouter une source d’identité et attribuer le rôle CloudAdmin à des utilisateurs et à des groupes.
 
L’utilisateur de cloud privé n’ayant pas accès aux composants de gestion spécifiques pris en charge et gérés par Microsoft, il ne peut pas les configurer . Par exemple, les clusters, les hôtes, les magasins de données et les commutateurs virtuels distribués.

>[!NOTE]
>Dans Azure VMware Solution, le domaine SSO *vsphere.local* est fourni en tant que ressource managée pour prendre en charge les opérations de plateforme. Il ne prend pas en charge la création et la gestion de groupes et d’utilisateurs locaux autres que ceux fournis par défaut avec votre cloud privé.
