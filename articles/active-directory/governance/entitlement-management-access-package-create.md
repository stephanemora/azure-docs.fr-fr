---
title: Créer un nouveau package d’accès dans la gestion des droits Azure AD (version préliminaire) - Azure Active Directory
description: Découvrez comment créer un nouveau package d’accès de ressources que vous souhaitez partager dans la gestion des droits Azure Active Directory (version préliminaire).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/24/2019
ms.author: rolyon
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad6570a3f30e40e4074502a8ce85bf739f58d3f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866445"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management-preview"></a>Créer un nouveau package d’accès dans la gestion des droits Azure AD (version préliminaire)

> [!IMPORTANT]
> Gestion des habilitations Azure Active Directory (Azure AD) est actuellement en version préliminaire publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Un package d’accès vous permet de vous permettent d’effectuer une installation unique des ressources et des stratégies qui administre automatiquement l’accès pour la durée de vie du package d’accès. Cet article décrit comment créer un nouveau package d’accès.

## <a name="overview"></a>Présentation

Tous les packages d’accès doivent être placés dans un conteneur appelé un catalogue. Un catalogue définit les ressources auxquelles vous pouvez ajouter à votre package d’accès. Si vous ne spécifiez pas un catalogue, votre package d’accès est placé dans le catalogue général. Actuellement, vous ne pouvez pas déplacer un package d’accès existant à un autre catalogue.

Tous les packages d’accès doivent avoir au moins une stratégie. Stratégies de spécifient qui peut demander le package de l’accès et également les paramètres d’approbation et d’expiration. Lorsque vous créez un nouveau package d’accès, vous pouvez créer une stratégie initiale pour les utilisateurs dans votre répertoire, pour les utilisateurs non inclus dans votre annuaire, les affectations directes d’administrateur uniquement, ou vous pouvez choisir de créer la stratégie ultérieurement.

Le diagramme suivant illustre la procédure à suivre pour créer un nouveau package d’accès.

![Créer un processus de package d’accès](./media/entitlement-management-access-package-create/access-package-process.png)

## <a name="start-new-access-package"></a>Démarrer le nouveau package d’accès

**Rôle :** Utilisateur administrateur ou propriétaire du catalogue

1. Connectez-vous au [Portail Azure](https://portal.azure.com).

1. Cliquez sur **Azure Active Directory** puis cliquez sur **gouvernance des identités**.

1. Dans le menu de gauche, cliquez sur **accéder aux packages**.

    ![Gestion des habilitations dans le portail Azure](./media/entitlement-management-shared/elm-access-packages.png)

1. Cliquez sur **nouveau package accès**.

## <a name="basics"></a>Concepts de base

Sur le **notions de base** onglet, vous nommez le package de l’accès, puis spécifier quel catalogue pour créer le package d’accès dans.

1. Entrez un nom d’affichage et une description pour le package de l’accès. Les utilisateurs verront ces informations lorsqu’ils envoient une demande pour le package de l’accès.

1. Dans le **catalogue** liste déroulante, sélectionnez le catalogue que vous souhaitez créer l’accès au package dans. Par exemple, vous pouvez avoir un propriétaire du catalogue qui gère toutes les ressources marketing qui peuvent être demandées. Dans ce cas, vous pouvez sélectionner le catalogue de marketing.

    Vous verrez seulement les catalogues vous êtes autorisé à créer des packages d’accès dans. Pour créer le package de l’accès dans un catalogue existant, vous devez être au moins un administrateur de l’utilisateur, le propriétaire du catalogue ou le Gestionnaire de package de l’accès.

    ![Package d’accès - principes de base](./media/entitlement-management-access-package-create/basics.png)

    Si vous souhaitez créer votre package d’accès dans un nouveau catalogue, cliquez sur **créer un nouveau**. Entrez le nom du catalogue et la description, puis activez **créer**.

    Le package d’accès que vous créez et toutes les ressources qu’il seront ajoutés dans le nouveau catalogue. En outre, vous deviendrez alors automatiquement le premier propriétaire du catalogue. Vous pouvez ajouter des propriétaires de catalogue supplémentaires.

    Pour créer un nouveau catalogue, vous devez être au moins un administrateur de l’utilisateur ou le créateur du catalogue.

1. Cliquez sur **Suivant**.

## <a name="resource-roles"></a>Rôles de ressources

Sur le **rôles de ressources** onglet, vous sélectionnez les ressources à inclure dans le package de l’accès.

1. Cliquez sur le type de ressource que vous souhaitez ajouter (**groupes**, **Applications**, ou **sites SharePoint**).

1. Dans le volet de sélection qui s’affiche, sélectionnez une ou plusieurs ressources dans la liste.

    ![Package d’accès - rôles de ressources](./media/entitlement-management-access-package-create/resource-roles.png)

    Si vous créez le package d’accès dans le catalogue général ou un nouveau catalogue, vous serez en mesure de récupérer n’importe quelle ressource à partir du répertoire que vous possédez. Vous devez être au moins un administrateur de l’utilisateur ou le créateur du catalogue.

    Si vous créez le package d’accès dans un catalogue existant, vous pouvez sélectionner n’importe quelle ressource est déjà dans le catalogue, sans être propriétaire d’elle.

    Si vous êtes un administrateur de l’utilisateur ou le propriétaire du catalogue, vous avez la possibilité supplémentaire de la sélection de ressources que vous possédez et qui ne sont pas encore dans le catalogue. Si vous sélectionnez ressources pas actuellement dans le catalogue sélectionné, ces ressources seront également ajoutés au catalogue des autres administrateurs de catalogue créer des packages avec accès. Si vous souhaitez uniquement sélectionner les ressources qui se trouvent actuellement dans le catalogue sélectionné, vérifiez le **uniquement voir** case à cocher en haut de la sélectionnez panoramique.

1. Une fois que vous avez sélectionné les ressources, dans le **rôle** liste, sélectionnez le rôle que vous souhaitez que les utilisateurs à affecter pour la ressource.

    ![Package d’accès - sélection du rôle de ressources](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Cliquez sur **Suivant**.

## <a name="policy"></a>Stratégie

Sur le **stratégie** onglet, vous créez la première stratégie pour spécifier qui peut demander le package de l’accès et également les paramètres d’approbation et d’expiration. Une version ultérieure, vous pouvez créer des stratégies plus afin d’autoriser d’autres groupes d’utilisateurs pour demander le package de l’accès avec leurs propres paramètres d’approbation et d’expiration. Vous pouvez également choisir de créer la stratégie ultérieurement.

1. Définir le **créer la première stratégie** activer/désactiver pour **maintenant** ou **ultérieurement**.

    ![Package - stratégie d’accès](./media/entitlement-management-access-package-create/policy.png)

1. Si vous sélectionnez **ultérieurement**, passez à la [révision + créer](#review--create) section pour créer votre package d’accès.

1. Si vous sélectionnez **maintenant**, effectuez les étapes de l’une des sections suivantes de la stratégie.

[!INCLUDE [Entitlement management policy](../../../includes/active-directory-entitlement-management-policy.md)]

## <a name="review--create"></a>Vérifier + créer

Sur le **examiner + créer** onglet, vous pouvez consulter vos paramètres et la vérification des erreurs de validation.

1. Passez en revue les paramètres du package de l’accès

    ![Package d’accès - activer la stratégie de paramètre de stratégie](./media/entitlement-management-access-package-create/review-create.png)

1. Cliquez sur **créer** pour créer le package de l’accès.

    Le nouveau package d’accès apparaît dans la liste des packages d’accès.

## <a name="next-steps"></a>Étapes suivantes

- [Modifier et gérer un package d’accès existant](entitlement-management-access-package-edit.md)
- [Créer et gérer un catalogue](entitlement-management-catalog-create.md)
