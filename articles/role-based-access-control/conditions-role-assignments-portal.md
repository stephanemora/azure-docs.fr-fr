---
title: Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide du portail Azure (préversion) - RBAC Azure
description: Découvrez comment ajouter, modifier, consulter ou supprimer des conditions de contrôle d’accès en fonction des attributs (ABAC) dans les attributions de rôle Azure à l’aide du portail Azure et du contrôle d’accès en fonction du rôle Azure (RBAC Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2021
ms.author: rolyon
ms.openlocfilehash: fd13fa81d3b6081468bc4071ffe3c41bf3294c7a
ms.sourcegitcommit: 3de22db010c5efa9e11cffd44a3715723c36696a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/10/2021
ms.locfileid: "109656653"
---
# <a name="add-or-edit-azure-role-assignment-conditions-using-the-azure-portal-preview"></a>Ajouter ou modifier des conditions d’attribution de rôle Azure à l’aide du portail Azure (préversion)

> [!IMPORTANT]
> Azure ABAC et les conditions d’attribution de rôle Azure sont actuellement en préversion.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge.
> Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Une [condition d’attribution de rôle Azure](conditions-overview.md) est une vérification supplémentaire que vous pouvez éventuellement ajouter à votre attribution de rôle pour fournir un contrôle d’accès plus précis. Par exemple, vous pouvez ajouter une condition qui oblige un objet à porter une étiquette spécifique pour être lu. Cet article explique comment ajouter, modifier, consulter ou supprimer des conditions pour vos attributions de rôle à l’aide du portail Azure.

## <a name="prerequisites"></a>Prérequis

Pour plus d’informations sur les prérequis à l’ajout ou à la modification des conditions d’attribution de rôle, consultez [Prérequis aux conditions](conditions-prerequisites.md).


## <a name="step-1-determine-the-condition-you-need"></a>Étape 1 : Déterminer les conditions dont vous avez besoin

Pour déterminer les conditions dont vous avez besoin, passez en revue les exemples fournis dans [Exemples de conditions d’attribution de rôle Azure](../storage/common/storage-auth-abac-examples.md).

Actuellement, il est possible d’ajouter des conditions à des attributions de rôle intégrées ou personnalisées qui ont des [actions de données Blob de stockage](../storage/common/storage-auth-abac-attributes.md). Sont inclus les rôles intégrés suivants :

- [Contributeur aux données Blob du stockage](built-in-roles.md#storage-blob-data-contributor)
- [Propriétaire des données Blob du stockage](built-in-roles.md#storage-blob-data-owner)
- [Lecteur des données blob du stockage](built-in-roles.md#storage-blob-data-reader)

## <a name="step-2-choose-how-to-add-condition"></a>Étape 2 : Choisir comment ajouter une condition

Il existe deux façons d’ajouter une condition. Vous pouvez ajouter une condition quand vous ajoutez une nouvelle attribution de rôle ou vous pouvez ajouter une condition à une attribution de rôle existante.

### <a name="new-role-assignment"></a>Nouvelle affectation de rôle

1. Effectuez les étapes indiquant comment [attribuer des rôles Azure à l’aide du portail Azure](role-assignments-portal.md).

1. Sous l’onglet **Condition**, cliquez sur **Ajouter une condition**.

    Si vous ne voyez pas l’onglet Condition, vérifiez que vous avez sélectionné un rôle qui prend en charge les conditions.

   ![Capture d’écran de la page Ajouter une attribution de rôle avec l’onglet Ajouter une condition pour une expérience en préversion.](./media/shared/condition.png)

    La page Ajouter une condition d’attribution de rôle s’affiche.

### <a name="existing-role-assignment"></a>Attribution de rôle existante

1. Dans le portail Azure, ouvrez le **Contrôle d’accès (IAM)** dans l’étendue où vous voulez ajouter une condition. Par exemple, vous pouvez ouvrir un abonnement, un groupe de ressources ou une ressource.

    Actuellement, vous ne pouvez pas utiliser le portail Azure pour ajouter, consulter, modifier ou supprimer une condition dans l’étendue d’un groupe d’administration.

1. Cliquez sur l’onglet **Attributions de rôles** afin d’afficher toutes les attributions de rôles pour cette étendue.

1. Recherchez une attribution de rôle qui a des actions de données de stockage auxquelles vous voulez ajouter une condition.

1. Dans la colonne **Condition**, cliquez sur **Ajouter**.

    Si vous ne voyez pas le lien Ajouter, vérifiez que vous examinez la même étendue que celle de l’attribution de rôle.

    ![Liste des attributions de rôles avec une colonne Condition.](./media/conditions-role-assignments-portal/condition-role-assignments-list.png)

    La page Ajouter une condition d’attribution de rôle s’affiche.

## <a name="step-3-review-basics"></a>Étape 3 : Passer en revue les informations de base

Une fois la page Ajouter une condition d’attribution de rôle ouverte, vous pouvez passer en revue les informations de base de la condition. **Rôle** indique le rôle auquel la condition sera ajoutée.

1. Pour l’option **Type d’éditeur**, laissez la valeur **Visuel** par défaut sélectionnée.

    Une fois que vous avez ajouté une condition, vous pouvez basculer entre Visuel et Code.

1. (Facultatif) Si la zone **Description** s’affiche, entrez une description.

    En fonction de la façon dont vous avez choisi d’ajouter une condition, il est possible que la zone Description ne s’affiche pas. Une description peut vous aider à comprendre et à mémoriser la condition.

    ![Page Ajouter une condition d’attribution de rôle montrant la description et le type d’éditeur.](./media/conditions-role-assignments-portal/condition-basics.png)

## <a name="step-4-add-actions"></a>Étape 4 : Ajouter des actions

1. Dans la section **Ajouter une action**, cliquez sur **Ajouter une action**.

    Le volet Sélectionner une action s’affiche. Ce volet est une liste filtrée d’actions de données basée sur l’attribution de rôle destinée à être la cible de votre condition. Pour plus d’informations, consultez [Format et syntaxe des conditions d’attribution de rôle Azure](conditions-format.md#actions).

    ![Sélectionnez un volet Actions pour une condition avec une action sélectionnée.](./media/conditions-role-assignments-portal/condition-actions-select.png)

1. Sélectionnez les actions que vous voulez autoriser si la condition est vraie.

    Si vous sélectionnez plusieurs actions pour une même condition, il peut y avoir moins d’attributs à choisir pour votre condition, car ceux-ci doivent être disponibles sur les actions sélectionnées.

1. Cliquez sur **Sélectionner**.

    Les actions sélectionnées s’affichent dans la liste des actions.

## <a name="step-5-build-expressions"></a>Étape 5 : Générer des expressions

1. Dans la section **Générer l’expression**, cliquez sur **Ajouter une expression**.

    La section Expression se développe.

1. Dans la liste Source d’attribut, sélectionnez l’emplacement où l’attribut peut se trouver.

    - **Ressource** indique que l’attribut est sur la ressource, comme un nom de conteneur.
    - **Demande** indique que l’attribut fait partie de la demande d’action, comme la définition de la balise d’index d’objet blob.

1. Dans la liste Attribut, sélectionnez un attribut pour le côté gauche de l’expression. Pour plus d’informations, consultez [Format et syntaxe des conditions d’attribution de rôle Azure](conditions-format.md#attributes).

    En fonction de l’attribut que vous sélectionnez, des zones peuvent être ajoutées pour spécifier des détails d’attribut supplémentaires.

1. Dans la liste Opérateur, sélectionnez un opérateur.

1. Dans la zone Valeur, entrez une valeur pour le côté droit de l’expression.

    ![Section Générer l’expression avec des valeurs pour les balises d’index d’objet blob.](./media/conditions-role-assignments-portal/condition-expressions.png)

## <a name="step-6-review-and-add-condition"></a>Étape 6 : Examiner et ajouter une condition

1. Faites défiler la liste jusqu’à **Type d’éditeur**, puis cliquez sur **Code**.

    La condition s’affiche sous forme de code. Vous pouvez apporter des changements à la condition dans cet éditeur de code. Pour revenir à l’éditeur visuel, cliquez sur **Visuel**.

    ![Condition affichée dans l’éditeur de code avec des actions sélectionnées et une expression ajoutée.](./media/conditions-role-assignments-portal/condition-code.png)

1. Cliquez sur **Enregistrer** pour ajouter la condition à l’attribution de rôle.

## <a name="view-edit-or-delete-a-condition"></a>Consulter, modifier ou supprimer une condition

1. Dans le portail Azure, ouvrez **Contrôle d’accès (IAM)** pour l’attribution de rôle ayant une condition que vous voulez consulter, modifier ou supprimer.

1. Cliquez sur l’onglet **Attributions de rôle**, puis recherchez l’attribution de rôle.

1. Dans la colonne **Condition**, cliquez sur **Afficher/Modifier**.

    Si vous ne voyez pas le lien Afficher/Modifier, vérifiez que vous examinez la même étendue que celle de l’attribution de rôle.

    ![Liste des attributions de rôle avec un lien Afficher/Modifier pour la condition.](./media/conditions-role-assignments-portal/condition-role-assignments-list-edit.png)

    La page Ajouter une condition d’attribution de rôle s’affiche.

1. Utilisez l’éditeur pour consulter ou modifier la condition.

    ![Condition affichée dans l’éditeur après avoir cliqué sur le lien Afficher/Modifier.](./media/conditions-role-assignments-portal/condition-edit.png)

1. Lorsque vous avez terminé, cliquez sur **Enregistrer**. Pour supprimer l’intégralité de la condition, cliquez sur **Supprimer la condition**. La suppression de la condition ne supprime pas l’attribution de rôle.

## <a name="next-steps"></a>Étapes suivantes

- [Exemples de conditions d’attribution de rôle Azure (préversion)](../storage/common/storage-auth-abac-examples.md)
- [Tutoriel : Ajouter une condition d’attribution de rôle pour restreindre l’accès aux objets blob à l’aide du portail Azure (préversion)](../storage/common/storage-auth-abac-portal.md)
- [Résoudre les problèmes liés aux de conditions d’attribution de rôle Azure (préversion)](conditions-troubleshoot.md)
