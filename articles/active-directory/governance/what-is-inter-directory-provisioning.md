---
title: Qu’est-ce que le provisionnement inter-annuaires avec Azure Active Directory ? | Microsoft Docs
description: Présente une vue d’ensemble du provisionnement inter-annuaires d’identités.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbca2d00d10b6595b8a1d13251dd88c65667541
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93134938"
---
# <a name="what-is-inter-directory-provisioning"></a>Qu’est-ce que le provisionnement inter-annuaires ?

Un annuaire est une infrastructure d’informations partagée utilisée pour la localisation, la gestion, l’administration et l’organisation des éléments et ressources réseau.  Microsoft Active Directory et Azure AD sont des exemples d’applications qui utilisent des services d’annuaire.  Les identités permettent aux systèmes d’annuaire de déterminer notamment qui a accès à quoi et qui est autorisé à utiliser des ressources spécifiques.

Le provisionnement inter-annuaires consiste à provisionner une identité entre deux systèmes de services d’annuaire différents.   Le scénario le plus courant pour le provisionnement inter-annuaires est celui où un utilisateur déjà dans Active Directory est provisionné dans Azure AD. Ce provisionnement peut être effectué par des agents tels que la synchronisation Azure AD Connect ou le provisionnement cloud Azure AD Connect.

Le provisionnement inter-annuaires nous permet de créer des environnements d’[identités hybrides](../hybrid/whatis-hybrid-identity.md).


## <a name="what-types-of-inter-directory-provisioning-does-azure-ad-support"></a>Types de provisionnement inter-annuaires pris en charge par Azure AD

Azure AD prend actuellement en charge trois méthodes pour effectuer le provisionnement inter-annuaires. Ces méthodes sont les suivantes :

- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md) : outil Microsoft conçu pour vous permettre d’atteindre et de remplir vos objectifs en matière d’identité hybride, notamment le provisionnement inter-annuaires entre Active Directory et Azure AD.

- [Provisionnement cloud Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) : nouvel agent Microsoft conçu pour vous permettre d’atteindre et de remplir vos objectifs en matière d’identité hybride.  Il propose une expérience de provisionnement inter-annuaires simplifiée entre Active Directory et Azure AD.

- [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016) : solution de gestion des identités et des accès en local de Microsoft qui vous permet de gérer les utilisateurs, informations d’identification, stratégies et accès au sein de votre organisation. De plus, MIM propose un provisionnement inter-annuaires avancé pour obtenir des environnements d’identités hybrides pour Active Directory, Azure AD et d’autres annuaires.

### <a name="key-benefits"></a>Principaux avantages

Cette fonctionnalité de provisionnement inter-annuaires offre les avantages significatifs suivants pour l’entreprise :

- [Synchronisation de hachage de mot de passe](../hybrid/whatis-phs.md) : méthode d’authentification qui synchronise un hachage du mot de passe AD local d’un utilisateur avec Azure AD.
- [Authentification directe](../hybrid/how-to-connect-pta.md) : méthode d’authentification qui permet aux utilisateurs d’utiliser le même mot de passe localement et dans le cloud, mais sans nécessiter l’infrastructure supplémentaire d’un environnement fédéré.
- [Intégration de fédération](../hybrid/how-to-connect-fed-whatis.md) : permet de configurer un environnement hybride à l’aide d’une infrastructure AD FS locale. Elle offre également des fonctionnalités de gestion AD FS telles que le renouvellement de certificat et les déploiements de serveurs AD FS supplémentaires.
- [Synchronisation](../hybrid/how-to-connect-sync-whatis.md) : ce composant est chargé de créer des utilisateurs, des groupes et d’autres objets,  et également de s’assurer que les informations d’identité relatives aux utilisateurs et aux groupes dans votre environnement local correspondent à celles qui se trouvent dans le cloud.  Cette synchronisation inclut également des hachages de mot de passe.
- [Analyse du fonctionnement](../hybrid/whatis-hybrid-identity-health.md) : peut assurer une supervision robuste et offrir un emplacement central dans le portail Azure pour la visualisation de cette activité. 


## <a name="next-steps"></a>Étapes suivantes 
- [Qu’est-ce que la gestion du cycle de vie des identités](what-is-identity-lifecycle-management.md)
- [Présentation du provisionnement](what-is-provisioning.md)
- [Qu’est-ce que le provisionnement axé sur les RH ?](what-is-hr-driven-provisioning.md)
- [Qu’est-ce que le provisionnement d’application ?](what-is-app-provisioning.md)
