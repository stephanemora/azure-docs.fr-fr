---
title: Comment gérer une identité managée affectée par l’utilisateur à l’aide du Portail Azure
description: Instructions étape par étape pour créer, répertorier et supprimer une identité managée affectée par l’utilisateur.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: bd6327aa5c16d57c550025667659f9245a5b0b65
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2018
ms.locfileid: "42140060"
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-the-azure-portal"></a>Créer, répertorier ou supprimer une identité affectée par l’utilisateur à l’aide du Portail Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Managed Service Identity fournit aux services Azure une identité administrée dans Azure Active Directory. Vous pouvez utiliser cette identité pour vous authentifier auprès de services prenant en charge l’authentification Azure AD, sans avoir recours à des informations d’identification dans votre code. 

Dans cet article, vous allez découvrir comment créer, répertorier et supprimer une identité affectée par l’utilisateur par le biais du Portail Azure.

## <a name="prerequisites"></a>Prérequis

- Si vous ne connaissez pas Managed Service Identity, consultez la [section Vue d’ensemble](overview.md). **Veillez à consulter [la différence entre les identité affectées par le système et celles affectées par l’utilisateur](overview.md#how-does-it-work)**.
- Si vous n’avez pas encore de compte Azure, [inscrivez-vous à un essai gratuit](https://azure.microsoft.com/free/) avant de continuer.
- Pour effectuer les opérations de gestion dans cet article, votre compte doit disposer des attributions des rôles suivants :
    - [Contributeur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-contributor), pour créer, lire (lister), mettre à jour et supprimer une identité affectée par l’utilisateur.
    - [Opérateur d’identité managée](/azure/role-based-access-control/built-in-roles#managed-identity-operator) pour lire (lister) les propriétés d’une identité affectée par l’utilisateur.

## <a name="create-a-user-assigned-managed-identity"></a>Créer une identité managée attribuée à l’utilisateur

1. Connectez-vous au [Portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure afin de créer l’identité managée affectée par l’utilisateur.
2. Dans la zone de recherche, saisissez *Identités managées*, puis sous **Services**, cliquez sur **Identités managées**.
3. Cliquez sur **Ajouter** et entrez des valeurs dans les champs suivants dans le volet d’identité **Créer une identité managée affectée par l’utilisateur** :
   - **Nom de la ressource** : il s’agit du nom de votre identité affectée par l’utilisateur, par exemple UAI1.
   - **Abonnement** : sélectionnez l’abonnement sous lequel l’identité affectée par l’utilisateur sera créée
   - **Groupe de ressources** : créer un groupe de ressources pour contenir votre identité affectée par l’utilisateur ou choisissez **Utiliser l’existant** pour créer l’identité managée affectée par l’utilisateur dans un groupe de ressources existant.
   - **Emplacement** : choisissez un emplacement où déployer l’identité managée affectée par l’utilisateur, par exemple **USA Ouest**.
4. Cliquez sur **Créer**.

![Créer une identité managée attribuée à l’utilisateur](./media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)

## <a name="list-user-assigned-identities"></a>Lister les identités attribuées à l’utilisateur

1. Connectez-vous au [Portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure afin de répertorier les identités managées affectées par l’utilisateur.
2. Dans la zone de recherche, saisissez *Identités managées*, puis sous Services, cliquez sur **Identités managées**.
3. Une liste d’identités managées affectées par l’utilisateur pour votre abonnement est retournée.  Pour afficher les détails d’une affectation utilisateur, cliquez sur son nom.

![Répertorier les identités managées affectées par l’utilisateur](./media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-identity"></a>Supprimer une identité attribuée à l’utilisateur

1. Connectez-vous au [Portail Azure](https://portal.azure.com) à l’aide d’un compte associé à l’abonnement Azure afin de supprimer une identité managée affectée par l’utilisateur.
2. Sélectionnez l’identité affectée par l’utilisateur et cliquez sur **Supprimer**.
3. Sous la zone de confirmation, sélectionnez **Oui**.

![Supprimer l’identité managée affectée par l’utilisateur](./media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)