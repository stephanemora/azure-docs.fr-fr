---
title: Guide pratique pour créer un rôle d’inscription pour Azure Stack
description: Comment créer un rôle personnalisé pour éviter d’utiliser un administrateur général pour l’inscription.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 4858e33023ff0f94bb9fb879b01846184df2491e
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652142"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Créer un rôle d’inscription pour Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Pour les scénarios où vous ne voulez pas accorder des autorisations de propriétaire dans l’abonnement Azure, vous pouvez créer un rôle personnalisé visant à affecter des autorisations à un compte d’utilisateur pour inscrire votre service Azure Stack.

> [!WARNING]
> Il s’agit d’une fonctionnalité présentant certains risques de sécurité. Utilisez-la dans des scénarios où vous voulez que des contraintes empêchent les modifications accidentelles de l’abonnement Azure. Quand ce rôle personnalisé est délégué à un utilisateur, celui-ci dispose de droits permettant de modifier les autorisations et d’élever les droits. Affectez à ce rôle uniquement des utilisateurs auxquels vous faites confiance.

Lors de l’inscription d’Azure Stack, le compte d’inscription nécessite les autorisations Azure Active Directory et les autorisations de l’abonnement Azure suivantes :

* **Autorisations d’inscription d’application dans votre locataire Azure Active Directory :** Les administrateurs ont des autorisations d’inscription d’application. L’autorisation pour les utilisateurs est un paramètre global pour tous les utilisateurs du locataire. Pour voir ou changer le paramètre, consultez [Créer une application et un principal du service Azure AD pouvant accéder aux ressources](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    Le paramètre *L’utilisateur peut inscrire des applications* doit être défini sur **Oui** pour permettre à un compte d’utilisateur d’inscrire Azure Stack. Si le paramètre d’inscription d’applications est défini sur **Non**, vous ne pouvez pas utiliser un compte d’utilisateur : vous devez utiliser un compte d’administrateur général pour inscrire Azure Stack.

* **Un ensemble d’autorisations d’abonnement Azure suffisantes :** Les utilisateurs du groupe Propriétaires ont des autorisations suffisantes. Pour les autres comptes, vous pouvez affecter l’ensemble d’autorisations en attribuant un rôle personnalisé comme indiqué dans les sections suivantes.

## <a name="create-a-custom-role-using-powershell"></a>Créer un rôle personnalisé avec PowerShell

Pour créer un rôle personnalisé, vous devez disposer de l’autorisation `Microsoft.Authorization/roleDefinitions/write` sur toutes les `AssignableScopes`, comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator). Utilisez le modèle JSON suivant pour simplifier la définition du rôle personnalisé. Le modèle crée un rôle personnalisé qui permet l’accès en lecture et en écriture nécessaire pour l’inscription d’Azure Stack.

1. Créez un fichier JSON. Par exemple, `C:\CustomRoles\registrationrole.json`
2. Ajoutez le code JSON suivant au fichier. Remplacez <SubscriptionID> par l’identifiant de votre abonnement Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. Dans PowerShell, connectez-vous à Azure pour utiliser Azure Resource Manager. Quand vous y êtes invité, authentifiez-vous avec un compte disposant d’autorisations suffisantes, comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Pour ajouter le rôle à l’abonnement, utilisez **New-AzureRmRoleDefinition** en spécifiant le fichier de modèle JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Affecter un utilisateur au rôle d’inscription

Une fois le rôle personnalisé d’inscription créé, attribuez-le aux utilisateurs qui inscrivent Azure Stack.

1. Connectez-vous avec le compte qui a des autorisations suffisantes sur l’abonnement Azure pour déléguer des droits, comme [Propriétaire](../role-based-access-control/built-in-roles.md#owner) ou [Administrateur de l’accès utilisateur](../role-based-access-control/built-in-roles.md#user-access-administrator).
2. Dans **Abonnements**, sélectionnez **Contrôle d’accès (IAM) > Ajouter une attribution de rôle**.
3. Dans **Rôle**, choisissez le rôle personnalisé que vous avez créé, *Rôle d’inscription Azure Stack*.
4. Sélectionnez les utilisateurs que vous voulez affecter au rôle.
5. Sélectionnez **Enregistrer** pour affecter les utilisateurs sélectionnés au rôle.

    ![Sélectionner des utilisateurs à affecter au rôle](media/azure-stack-registration-role/assign-role.png)

Pour plus d’informations sur l’utilisation de rôles personnalisés, consultez [Gérer les accès à l’aide du contrôle d’accès en fonction du rôle et du portail Azure](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Étapes suivantes

[Inscrire Azure Stack auprès d’Azure](azure-stack-registration.md)
