---
title: Afficher un aperçu des rôles d’administrateur Azure avec des autorisations personnalisables - Azure Active Directory | Microsoft Docs
description: Affichez un aperçu des rôles Azure AD personnalisés pour la délégation de la gestion des identités. Gérez les rôles Azure dans le Portail Azure, PowerShell ou l’API Graph.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82638e3e102f7b8e39cd797960a11f3193132bc1
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779385"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Rôles d’administrateurs personnalisés Azure Active Directory (préversion)

Cet article décrit le nouveau contrôle d’accès en fonction du rôle (RBAC) personnalisé et les étendues de ressources dans Azure Active Directory (Azure AD). Les rôles RBAC personnalisés regroupent les autorisations sous-jacentes des [rôles intégrés](directory-assign-admin-roles.md), ce qui vous permet de créer et d’organiser vos propres rôles personnalisés. Les étendues de ressources vous permettent d’attribuer le rôle personnalisé pour gérer certaines ressources (par exemple une application) sans donner accès à toutes les ressources (toutes les applications).

L’octroi d’une autorisation à l’aide de rôles RBAC personnalisés est un processus en deux étapes. Tout d’abord, créez une définition de rôle personnalisée et ajoutez-lui des autorisations à partir d’une liste prédéfinie. Il s’agit des mêmes autorisations que celles utilisées dans les rôles intégrés. Une fois que vous avez créé votre rôle, attribuez-le à une personne en créant une attribution de rôle. Ce processus en deux étapes vous permet de créer un rôle et de l’attribuer plusieurs fois à différentes étendues. Un rôle personnalisé peut être affecté au niveau de l’étendue du répertoire ou au niveau de l’étendue de l’objet. Un exemple d’étendue d’objet est une application unique. De cette façon, le même rôle peut être affecté à Catherine sur toutes les applications du répertoire, puis Naveen sur l’application Contoso Expense Reports.

La première version des rôles RBAC personnalisés inclut la possibilité de créer un rôle pour attribuer des autorisations de gestion des inscriptions d’applications. Au fil du temps, des autorisations supplémentaires pour les ressources de l’organisation, telles que les applications d’entreprise, les utilisateurs et les appareils, sont ajoutées.

Fonctionnalités en préversion :

- Mises à jour de l’interface utilisateur du portail pour créer et gérer des rôles personnalisés et les attribuer aux utilisateurs au niveau de l’organisation
- Un module PowerShell en préversion avec de nouvelles cmdlets pour :
  - Créer et gérer des rôles personnalisés
  - Attribuer des rôles personnalisés avec une étendue d’inscription à l’échelle de l’organisation ou de chaque application
  - Attribuer des rôles intégrés au niveau de l’étendue de l’organisation (parité avec les cmdlets en disponibilité générale)
  - Prise en charge des API Graph Azure AD

Le contrôle d’accès en fonction du rôle Azure AD est une fonctionnalité en préversion publique d’Azure AD et est disponible avec n’importe quel plan de licence Azure AD payant. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="understand-azure-ad-role-based-access-control"></a>Présentation du contrôle d’accès en fonction du rôle Azure AD

Le contrôle d’accès en fonction du rôle Azure AD vous permet d’attribuer des rôles personnalisés pour autoriser des actions autorisées sur un seul type de ressource Azure AD. L’accès en fonction du rôle Azure AD fonctionne sur des concepts similaires au contrôle d’accès en fonction du rôle Azure ([Azure RBAC](../../role-based-access-control/overview.md)) pour l’accès aux ressources Azure, mais le contrôle d’accès en fonction du rôle Azure AD est basé sur Microsoft Graph alors que Azure RBAC est basé sur Azure Resource Manager. Toutefois, les deux systèmes basent leurs fonctions sur les attributions de rôles.

### <a name="role-assignments"></a>Affectations de rôles

La façon dont vous contrôlez l’accès à l’aide du contrôle d’accès en fonction du rôle Azure AD consiste à créer des **attributions de rôles**, qui sont utilisées pour appliquer des autorisations. Une attribution de rôle se compose de trois éléments :

- Principal de sécurité
- Définition de rôle
- Étendue des ressources

La création d’une attribution de rôle permet d’accorder un accès, qui peut être révoqué par la suppression d’une attribution de rôle. Vous pouvez [créer des attributions de rôles](roles-create-custom.md) à l’aide du portail Azure, d’Azure AD PowerShell et de l’API Graph. Vous pouvez [afficher séparément les attributions d’un rôle personnalisé](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview).

Le diagramme suivant montre un exemple d’attribution de rôle. Dans cet exemple, Chris Green s’est vu attribuer le rôle d’[administrateur d’application](directory-assign-admin-roles.md#application-administrator) dans l’étendue de l’application Salesforce. Chris n’a pas accès à la gestion des autres applications, à moins qu’elles fassent partie d’une attribution de rôle différente.

![L’attribution de rôle est la manière dont les autorisations sont appliquées et comporte trois parties.](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Principal de sécurité

Un principal de sécurité représente l’utilisateur ou le principal du service qui doit être attribué à l’accès aux ressources Azure AD. Un *utilisateur* est une personne disposant d’un profil utilisateur dans Azure Active Directory. Un *principal de service* est une identité de sécurité utilisée par des applications ou des services permettant d’accéder aux ressources Azure AD spécifiques.

Un principal de sécurité est similaire à une identité d’utilisateur, car il représente un nom d’utilisateur et un mot de passe ou un certificat, mais pour une application ou un service au lieu d’un utilisateur.

### <a name="role"></a>Role

Une définition de rôle, ou rôle, est une collection d’autorisations. Une définition de rôle répertorie les opérations qui peuvent être effectuées sur des ressources Azure AD, telles que créer, lire, mettre à jour et supprimer. Il existe deux types de rôles dans Azure AD :

- Les rôles intégrés créés par Microsoft qui ne peuvent pas être modifiés. Le rôle intégré Administrateur général dispose de toutes les autorisations sur toutes les ressources Azure AD.
- Rôles personnalisés créés et gérés par votre organisation.

### <a name="scope"></a>Étendue

Une étendue est la restriction des actions autorisées à une ressource de Azure AD particulière. Lorsque vous attribuez un rôle, vous pouvez spécifier une étendue qui limite les actions autorisées de l’administrateur à une ressource spécifique. Par exemple, si vous souhaitez accorder à un développeur un rôle personnalisé, mais uniquement pour gérer une inscription d’application spécifique, vous pouvez inclure l’inscription d’application spécifique en tant qu’étendue dans l’attribution de rôle.

  > [!Note]
  > Des rôles personnalisés peuvent être attribués au niveau de l’étendue du répertoire et des ressources dans l’étendue. Ils ne peuvent pas encore être attribués au niveau de l’étendue de l’unité administrative.
  > Les rôles intégrés peuvent être attribués à l’étendue du répertoire et, dans certains cas, à l’étendue de l’unité administrative. Ils ne peuvent pas encore être attribués au niveau de l’étendue d’objet.

## <a name="required-license-plan"></a>Plan de licence obligatoire

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Étapes suivantes

- Créer des attributions de rôles personnalisés à l’aide du [portail Azure, d’Azure AD PowerShell et de l’API Graph](roles-create-custom.md)
- [Afficher les attributions d’un rôle personnalisé](roles-view-assignments.md#view-the-assignments-of-a-role-with-single-application-scope-using-the-azure-ad-portal-preview)
