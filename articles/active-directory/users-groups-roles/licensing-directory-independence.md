---
title: Caractéristiques de l’interaction de locataires multiples – Azure AD | Microsoft Docs
description: Compréhension de l’indépendance des données de vos organisations Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/29/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1db812fb9c1503771bec0b1795ee8c15bb70014a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2020
ms.locfileid: "84727702"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Comprendre l’interaction entre plusieurs organisations Azure Active Directory

Dans Azure Active Directory (Azure AD), chaque organisation Azure AD est entièrement indépendante, c’est-à-dire une homologue logiquement indépendante des autres organisations Azure AD que vous gérez. Cette indépendance entre les organisations vaut pour les ressources, l’administration et la synchronisation. Il n’existe pas de relation parent-enfant entre les organisations.

## <a name="resource-independence"></a>Indépendance des ressources

* Si vous créez ou supprimez une ressource Azure AD dans une organisation, cela est sans effet sur les ressources d’une autre organisation, à l’exception partielle des utilisateurs externes.
* Si vous inscrivez l’un de vos noms de domaine auprès d’une organisation, il ne peut pas être utilisé par une autre organisation.

## <a name="administrative-independence"></a>Indépendance de l’administration

Si un utilisateur non-administrateur de l’organisation « Contoso » crée l’organisation de test « Test », alors :

* Par défaut, l’utilisateur qui crée une organisation est ajouté comme utilisateur externe dans cette nouvelle organisation et se voit attribuer le rôle d’administrateur général dans cette organisation.
* Les administrateurs de l’organisation « Contoso » n’ont pas de privilèges d’administration directs sur l’organisation « Test », à moins qu’un administrateur de « Test » leur ait spécifiquement accordé ces privilèges. Cependant, les administrateurs de « Contoso » peuvent contrôler l’accès à l’organisation « Test » s’ils contrôlent le compte d’utilisateur qui a créé « Test ».
* Si vous ajoutez ou supprimez un rôle Azure AD pour un utilisateur d’une organisation, ce changement n’affecte pas les rôles attribués à l’utilisateur dans une autre organisation Azure AD.

## <a name="synchronization-independence"></a>Indépendance de la synchronisation

Vous pouvez configurer chaque organisation Azure AD de manière indépendante de sorte que les données soient synchronisées à partir d’une même instance de :

* l’outil Azure AD Connect, pour synchroniser les données avec une seule forêt AD ;
* le connecteur Azure Active Directory pour Forefront Identity Manager, pour synchroniser les données avec une ou plusieurs forêts locales et/ou des sources de données non Azure AD.

## <a name="add-an-azure-ad-organization"></a>Ajouter une organisation Azure AD

Pour ajouter une organisation Azure AD dans le portail Azure, connectez-vous au [portail Azure](https://portal.azure.com) avec un compte qui est un administrateur général Azure AD et sélectionnez **Nouveau**.

> [!NOTE]
> Contrairement aux autres ressources Azure, vos organisations Azure AD ne sont pas des ressources enfants d’un abonnement Azure. Si votre abonnement Azure est annulé ou qu’il est arrivé à expiration, vous pouvez toujours accéder aux données de votre organisation Azure AD via Azure PowerShell, l’API Microsoft Graph ou le centre d’administration Microsoft 365. Vous pouvez également [associer un autre abonnement à l’organisation](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir les bonnes pratiques et les considérations relatives aux licences Azure AD, consultez [Qu’est-ce que la gestion des licences Azure Active Directory ?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
