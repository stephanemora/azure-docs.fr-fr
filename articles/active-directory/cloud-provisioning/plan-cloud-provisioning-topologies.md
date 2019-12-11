---
title: Topologies et scénarios pris en charge par le provisionnement cloud Azure AD Connect
description: Cette rubrique décrit les prérequis et la configuration matérielle requise pour le provisionnement cloud.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 142974423816b07d754a5425017aedc3195e2f4e
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795376"
---
# <a name="azure-ad-connect-cloud-provisioning-supported-topologies-and-scenarios"></a>Topologies et scénarios pris en charge par le provisionnement cloud Azure AD Connect
Cet article décrit diverses topologies Azure Active Directory (Azure AD) et locales qui utilisent le provisionnement cloud Azure AD Connect. Cet article contient uniquement des configurations et des scénarios pris en charge.

> [!IMPORTANT]
> Microsoft ne prend pas en charge la modification ou l’utilisation du provisionnement cloud Azure AD Connect en dehors des configurations ou des actions documentées de façon formelle. Ces configurations ou actions peuvent entraîner un provisionnement cloud Azure AD Connect incohérent ou non pris en charge. Par conséquent, Microsoft ne peut pas fournir de support technique pour ces déploiements.

## <a name="things-to-remember-about-all-scenarios-and-topologies"></a>Points à retenir concernant tous les scénarios et topologies
La liste suivante regroupe les informations à garder à l’esprit lors du choix d’une solution.

- Les utilisateurs et les groupes doivent être identifiés de manière unique sur l’ensemble des forêts.
- La correspondance entre les forêts ne peut pas se produire avec le provisionnement cloud.
- Un utilisateur ou un groupe ne doit être représenté qu’une seule fois dans toutes les forêts.
- L’ancrage source des objets est choisi automatiquement.  Il utilise ms-DS-ConsistencyGuid s’il est présent, sinon ObjectGUID est utilisé.
- Vous ne pouvez pas changer l’attribut utilisé pour l’ancre source.



## <a name="multi-forest-single-azure-ad-tenant"></a>Plusieurs forêts, un seul locataire Azure AD
![Topologie pour plusieurs forêts et un seul locataire](media/plan-cloud-provisioning-topologies/multi-forest.png)

La topologie la plus courante est constituée de plusieurs forêts Active Directory, avec un ou plusieurs domaines, et un seul locataire Azure AD.  

## <a name="existing-forest-with-azure-ad-connect-new-forest-with-cloud-provisioning"></a>Forêt existante avec Azure AD Connect, nouvelle forêt avec le provisionnement cloud
![Topologie pour une forêt unique et un locataire unique](media/plan-cloud-provisioning-topologies/existing-forest-new-forest.png)

Ce scénario est similaire au scénario à plusieurs forêts, à ceci près qu’il implique un environnement Azure AD Connect existant, auquel est apportée ensuite une nouvelle forêt à l’aide du provisionnement cloud Azure AD Connect.  Pour obtenir un exemple de ce scénario, consultez [Tutoriel : Une forêt existante avec un seul locataire Azure AD](tutorial-existing-forest.md)

## <a name="piloting-azure-ad-connect-cloud-provisioning-in-an-existing-hybrid-ad-forest"></a>Pilotage du provisionnement cloud Azure AD Connect dans une forêt Active Directory hybride existante
![Topologie d’une seule forêt et d’un seul locataire](media/plan-cloud-provisioning-topologies/migrate.png) Le scénario de pilotage implique l’existence d’Azure AD Connect et du provisionnement cloud Azure AD Connect dans la même forêt, en étendant les utilisateurs et les groupes en conséquence. REMARQUE :  Un objet doit se trouver dans la portée d’un seul de ces outils. 

Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Piloter le provisionnement cloud Azure AD Connect dans une forêt Active Directory synchronisée existante](tutorial-pilot-aadc-aadccp.md)

## <a name="single-forest-single-azure-ad-tenant"></a>Une seule forêt, un seul client Azure AD
![Topologie pour une forêt unique et un locataire unique](media/plan-cloud-provisioning-topologies/single-forest.png)

La topologie la plus simple est une forêt locale unique, avec un ou plusieurs domaines, et un locataire Azure AD unique.  Pour obtenir un exemple de ce scénario, consultez le [Tutoriel : Une seule forêt avec un seul locataire Azure AD](tutorial-single-forest.md)

## <a name="next-steps"></a>Étapes suivantes 

- [Présentation du provisionnement](what-is-provisioning.md)
- [Présentation du provisionnement cloud Azure AD Connect](what-is-cloud-provisioning.md)

