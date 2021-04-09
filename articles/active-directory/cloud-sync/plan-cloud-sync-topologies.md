---
title: Topologies et scénarios pris en charge pour la synchronisation cloud Azure AD Connect
description: Découvrez les diverses topologies Azure Active Directory (Azure AD) et locales qui utilisent la synchronisation cloud Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2fd14ed8d149cdc5296229c52ceb74afb2ce7b23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98612469"
---
# <a name="azure-ad-connect-cloud-sync-supported-topologies-and-scenarios"></a>Topologies et scénarios pris en charge pour la synchronisation cloud Azure AD Connect
Cet article décrit diverses topologies Azure Active Directory (Azure AD) et locales qui utilisent la synchronisation cloud Azure AD Connect. Cet article contient uniquement des configurations et des scénarios pris en charge.

> [!IMPORTANT]
> Microsoft ne prend pas en charge la modification ou l’utilisation de la synchronisation cloud Azure AD Connect en dehors des configurations ou des actions documentées de façon formelle. Ces configurations ou actions peuvent entraîner un état de synchronisation cloud Azure AD Connect incohérent ou non pris en charge. Par conséquent, Microsoft ne peut pas fournir de support technique pour ces déploiements.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Points à retenir concernant tous les scénarios et topologies
La liste suivante regroupe les informations à garder à l’esprit lors du choix d’une solution.

- Les utilisateurs et les groupes doivent être identifiés de manière unique sur l’ensemble des forêts.
- La correspondance entre les forêts ne peut pas se produire avec la synchronisation cloud.
- Un utilisateur ou un groupe ne doit être représenté qu’une seule fois dans toutes les forêts.
- L’ancrage source des objets est choisi automatiquement.  Il utilise ms-DS-ConsistencyGuid s’il est présent, sinon ObjectGUID est utilisé.
- Vous ne pouvez pas changer l’attribut utilisé pour l’ancre source.

## <a name="single-forest-single-azure-ad-tenant"></a>Une seule forêt, un seul client Azure AD
![Diagramme montrant la topologie pour une seule forêt et un seul locataire.](media/tutorial-single-forest/diagram-2.png)

La topologie la plus simple est une forêt locale unique, avec un ou plusieurs domaines, et un locataire Azure AD unique.  Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Une seule forêt avec un seul locataire Azure AD](tutorial-single-forest.md)


## <a name="multi-forest-single-azure-ad-tenant"></a>Plusieurs forêts, un seul locataire Azure AD
![Topologie pour plusieurs forêts et un seul locataire](media/plan-cloud-provisioning-topologies/multi-forest-2.png)

Une topologie courante est constituée de plusieurs forêts Active Directory, avec un ou plusieurs domaines, et un seul locataire Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Forêt existante avec Azure AD Connect, nouvelle forêt avec le provisionnement cloud
![Diagramme montrant la topologie pour une forêt existante et une nouvelle forêt.](media/tutorial-existing-forest/existing-forest-new-forest-2.png)

La topologie de ce scénario est similaire à celle du scénario à plusieurs forêts, à ceci près qu’elle implique un environnement Azure AD Connect existant, puis la création d’une nouvelle forêt en utilisant la synchronisation cloud Azure AD Connect.  Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Une forêt existante avec un seul locataire Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-sync-in-an-existing-hybrid-ad-forest"></a>Pilotage de la synchronisation cloud Azure AD Connect dans une forêt Active Directory hybride existante
![Topologie d’une seule forêt et d’un seul locataire](media/tutorial-migrate-aadc-aadccp/diagram-2.png) Le scénario de pilotage implique l’existence d’Azure AD Connect et de la synchronisation cloud Azure AD Connect dans la même forêt, en étendant les utilisateurs et les groupes en conséquence. REMARQUE :  Un objet doit se trouver dans la portée d’un seul de ces outils. 

Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Piloter la synchronisation cloud Azure AD Connect dans une forêt AD synchronisée](tutorial-pilot-aadc-aadccp.md).



## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que la synchronisation cloud Azure AD Connect ?](what-is-cloud-sync.md)

