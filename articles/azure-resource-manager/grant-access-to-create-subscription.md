---
title: Accorder l’accès pour créer des abonnements Azure Enterprise | Microsoft Docs
description: Découvrez comment autoriser un utilisateur ou à un principal de service à créer des abonnements Azure Enterprise par programmation.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: jureid
ms.openlocfilehash: 742658e36da956c46bd932b59903e68786c65b93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794564"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)

En tant que client Azure en [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), vous pouvez autoriser un autre utilisateur ou principal de service à créer des abonnements facturés sur votre compte. Dans cet article, vous allez apprendre à utiliser le [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) pour partager la capacité de créer des abonnements et à effectuer un audit des créations d’abonnements. Vous devez disposer du rôle Propriétaire pour le compte que vous souhaitez partager.

Pour créer un abonnement, consultez [Créer des abonnements Azure Enterprise par programmation (préversion)](programmatically-create-subscription.md).

## <a name="grant-subscription-creation-access-to-a-user-or-group"></a>Accorder l’accès de la création d’abonnement à un utilisateur ou un groupe

Pour créer des abonnements sous un compte d’inscription, les utilisateurs doivent avoir le [rôle de propriétaire RBAC](../role-based-access-control/built-in-roles.md#owner) sur ce compte. Vous pouvez accorder un utilisateur ou un groupe d’utilisateurs le rôle de propriétaire RBAC sur un compte d’inscription en procédant comme suit :

### <a name="1-get-the-object-id-of-the-enrollment-account-you-want-to-grant-access-to"></a>1. Obtenir l’ID d’objet du compte d’inscription que vous souhaitez accorder l’accès à

Pour accorder à d’autres utilisateurs le rôle de propriétaire RBAC sur un compte d’inscription, vous devez être le propriétaire du compte ou du compte de propriétaire RBAC.

### <a name="resttabrest"></a>[REST](#tab/rest)

Pour répertorier tous les comptes d’inscription à que vous avez accès la demande :

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure retourne la liste de tous les comptes d’inscription auxquels vous avez accès :

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Utilisez le `principalName` propriété pour identifier le compte que vous souhaitez accorder l’accès propriétaire RBAC à. Copie le `name` de ce compte. Par exemple, si vous souhaitez accorder l’accès propriétaire RBAC à le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Il s’agit de l’ID d’objet du compte d’inscription. Collez cette valeur quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ouvrez [Azure Cloud Shell](https://shell.azure.com/) et sélectionnez PowerShell.

Utilisez l’applet de commande [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure répond avec une liste des comptes d’inscription que vous avez accès à :

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

Utilisez le `principalName` propriété pour identifier le compte que vous souhaitez accorder l’accès propriétaire RBAC à. Copie le `ObjectId` de ce compte. Par exemple, si vous souhaitez accorder l’accès propriétaire RBAC à le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Collez cet ID d’objet quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que le `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Utilisez la commande [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurecli-interactive 
az billing enrollment-account list
```

Azure répond avec une liste des comptes d’inscription que vous avez accès à :

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]

```

Utilisez le `principalName` propriété pour identifier le compte que vous souhaitez accorder l’accès propriétaire RBAC à. Copie le `name` de ce compte. Par exemple, si vous souhaitez accorder l’accès propriétaire RBAC à le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Il s’agit de l’ID d’objet du compte d’inscription. Collez cette valeur quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

<a id="userObjectId"></a>

### <a name="2-get-object-id-of-the-user-or-group-you-want-to-give-the-rbac-owner-role-to"></a>2. Obtenir l’ID d’objet de l’utilisateur ou le groupe que vous souhaitez attribuer le rôle de propriétaire RBAC pour

1. Dans le portail Azure, recherchez sur **Azure Active Directory**.
1. Si vous souhaitez accorder un accès utilisateur, cliquez sur **utilisateurs** dans le menu de gauche. Si vous souhaitez accorder l’accès à un groupe, cliquez sur **groupes**.
1. Sélectionnez l’utilisateur ou groupe que vous souhaitez attribuer le rôle de propriétaire RBAC pour.
1. Si vous avez sélectionné un utilisateur, vous trouverez l’ID d’objet dans la page de profil. Si vous avez sélectionné un groupe, l’ID d’objet sera dans la page de présentation. Copie le **ObjectID** en cliquant sur l’icône à droite de la zone de texte. Collez ce quelque part afin que vous puissiez l’utiliser à l’étape suivante en tant que `userObjectId`.

### <a name="3-grant-the-user-or-group-the-rbac-owner-role-on-the-enrollment-account"></a>3. Accordez à l’utilisateur ou le rôle de propriétaire RBAC sur le compte d’inscription de groupe

En utilisant les valeurs que vous avez collectées dans les deux premières étapes, accordez à l’utilisateur ou le rôle de propriétaire RBAC sur le compte d’inscription de groupe.

### <a name="resttabrest-2"></a>[REST](#tab/rest-2)

Exécutez la commande suivante, en remplaçant ```<enrollmentAccountObjectId>``` avec la `name` vous avez copié dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Remplacez ```<userObjectId>``` portant l’ID d’objet que vous avez copiée à partir de la deuxième étape.

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

Quand le rôle de propriétaire est correctement attribué au niveau de l’étendue du compte d’inscription, Azure retourne les informations de l’attribution de rôle :

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

### <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Exécutez la commande suivante [New-AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) commande, en remplaçant ```<enrollmentAccountObjectId>``` avec la `ObjectId` collectées dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Remplacez ```<userObjectId>``` avec l’objet ID collectées dans la deuxième étape.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

### <a name="azure-clitabazure-cli-2"></a>[Interface de ligne de commande Azure](#tab/azure-cli-2)

Exécutez la commande suivante [créer d’attribution de rôle az](../active-directory/role-based-access-control-manage-access-azure-cli.md) commande, en remplaçant ```<enrollmentAccountObjectId>``` avec la `name` vous avez copié dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Remplacez ```<userObjectId>``` avec l’objet ID collectées dans la deuxième étape.

```azurecli-interactive
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

----

Une fois qu’un utilisateur devient propriétaire RBAC pour votre compte d’inscription, ils peuvent [créer par programmation des abonnements](programmatically-create-subscription.md) sous celui-ci. Un abonnement créé par un utilisateur délégué a toujours le propriétaire du compte d’origine en tant qu’administrateur de Service, mais propose également de l’utilisateur délégué en tant que propriétaire RBAC par défaut.

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditer qui a créé des abonnements à l’aide des journaux d’activité

Pour effectuer le suivi des abonnements créés par le biais de cette API, utilisez [l’API des journaux d’activité de locataire](/rest/api/monitor/tenantactivitylogs). Il est impossible d’utiliser le portail Azure, l’interface CLI ou PowerShell pour effectuer le suivi de la création d’abonnement.

1. En tant qu’administrateur locataire du locataire Azure AD, [élevez l’accès](../active-directory/role-based-access-control-tenant-admin-access.md), puis affectez un rôle de lecteur à l’utilisateur d’audit sur l’étendue `/providers/microsoft.insights/eventtypes/management`.
1. En tant qu’utilisateur d’audit, appelez [l’API des journaux d’activité de locataire](/rest/api/monitor/tenantactivitylogs) pour voir les activités de création d’abonnement. Exemple :

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> Pour appeler facilement cette API à partir de la ligne de commande, essayez [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Étapes suivantes

* Maintenant que l’utilisateur ou le principal de service a l’autorisation de créer un abonnement, vous pouvez utiliser cette identité sur [créer des abonnements Azure Enterprise par programmation](programmatically-create-subscription.md).
* Pour obtenir un exemple de création d’abonnements à l’aide de .NET, consultez [l’exemple de code sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Pour en savoir plus sur Azure Resource Manager et ses API, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).
* Pour obtenir une aide complète sur les bonnes pratiques de gouvernance d’abonnements dans les grandes entreprises, consultez [Structure d’entreprise Azure : gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption-guide/subscription-governance)
