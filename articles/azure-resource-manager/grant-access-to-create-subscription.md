---
title: Accorder l’accès pour créer des abonnements Azure Enterprise | Microsoft Docs
description: Découvrez comment autoriser un utilisateur ou à un principal de service à créer des abonnements Azure Enterprise par programmation.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: jlian
ms.openlocfilehash: 4c5d505f431ef684b73adc04629464883d336a5b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35237049"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)

En tant que client Azure en [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), vous pouvez autoriser un autre utilisateur ou principal de service à créer des abonnements facturés sur votre compte. Dans cet article, vous allez apprendre à utiliser le [contrôle d’accès en fonction du rôle (RBAC)](../active-directory/role-based-access-control-configure.md) pour partager la capacité de créer des abonnements et à effectuer un audit des créations d’abonnements.

Pour créer un abonnement, consultez [Créer des abonnements Azure Enterprise par programmation (préversion)](programmatically-create-subscription.md).

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Déléguer l’accès à un compte d’inscription à l’aide de RBAC

Pour permettre à un autre utilisateur ou service principal de créer des abonnements sur un compte spécifique, [attribuez-lui un rôle de propriétaire RBAC au niveau de l’étendue du compte d’inscription](../active-directory/role-based-access-control-manage-access-rest.md). L’exemple suivant donne à l’utilisateur dans le locataire dont `principalId` a pour valeur `<userObjectId>` (pour SignUpEngineering@contoso.com) un rôle de propriétaire sur le compte d’inscription. Pour rechercher l’ID de l’inscription de compte et l’ID du principal, consultez [Créer des abonnements Azure Enterprise par programmation (préversion)](programmatically-create-subscription.md).

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez la commande [New-AzureRmRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) pour accorder à un autre utilisateur l’accès en tant que propriétaire à votre compte d’inscription.

```azurepowershell-interactive
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

# <a name="azure-clitabazure-cli"></a>[interface de ligne de commande Azure](#tab/azure-cli)

Utilisez la commande [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) pour accorder à un autre utilisateur l’accès en tant que propriétaire à votre compte d’inscription.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

----

Une fois qu’un utilisateur devient propriétaire RBAC pour votre compte d’inscription, il peut créer des abonnements par programmation sous ce dernier. Un abonnement créé par un utilisateur délégué a toujours le propriétaire de compte d’origine comme administrateur de service, mais il a également l’utilisateur délégué comme propriétaire par défaut. 

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Auditer qui a créé des abonnements à l’aide des journaux d’activité

Pour effectuer le suivi des abonnements créés par le biais de cette API, utilisez [l’API des journaux d’activité de locataire](/rest/api/monitor/tenantactivitylogs). Il est impossible d’utiliser le portail Azure, l’interface CLI ou PowerShell pour effectuer le suivi de la création d’abonnement.

1. En tant qu’administrateur locataire du locataire Azure AD, [élevez l’accès](../active-directory/role-based-access-control-tenant-admin-access.md), puis affectez un rôle de lecteur à l’utilisateur d’audit sur l’étendue `/providers/microsoft.insights/eventtypes/management`.
1. En tant qu’utilisateur d’audit, appelez [l’API des journaux d’activité de locataire](/rest/api/monitor/tenantactivitylogs) pour voir les activités de création d’abonnement. Exemple :

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> [!NOTE]
> Pour appeler facilement cette API à partir de la ligne de commande, essayez [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Étapes suivantes

* Maintenant que l’utilisateur ou le principal de service a l’autorisation de créer un abonnement, vous pouvez utiliser cette identité sur [créer des abonnements Azure Enterprise par programmation](programmatically-create-subscription.md).
* Pour obtenir un exemple de création d’abonnements à l’aide de .NET, consultez [l’exemple de code sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Pour en savoir plus sur Azure Resource Manager et ses API, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).
* Pour en savoir plus sur la gestion d’un nombre élevé d’abonnement avec des groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).
* Pour obtenir une aide complète sur les bonnes pratiques de gouvernance d’abonnements dans les grandes entreprises, consultez [Structure d’entreprise Azure : gouvernance normative de l’abonnement](/azure/architecture/cloud-adoption-guide/subscription-governance)
