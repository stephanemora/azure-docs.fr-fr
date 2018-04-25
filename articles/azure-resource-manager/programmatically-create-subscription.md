---
title: Créer des abonnements Azure Enterprise par programmation| Microsoft Docs
description: Découvrez comment créer des abonnements Azure Enterprise ou Enterprise Dev/Test supplémentaires par programmation.
services: azure-resource-manager
author: jlian
manager: jlian
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/18/2018
ms.author: jlian
ms.openlocfilehash: 5ec67c0d51d24a7bb93a3f11bf3b73a223e8b583
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/19/2018
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Créer des abonnements Azure Enterprise par programmation (préversion)

En tant que client Azure bénéficiant d’un [Contrat Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA, Enterprise Agreement), vous pouvez créer des abonnements EA (MS-AZR-0017P) et EA Dev/Test (MS-AZR-0148P) par programmation. Pour donner à un autre utilisateur ou principal de service l’autorisation de créer des abonnements facturés sur votre compte, accordez-leur un accès [RBAC (contrôle d’accès en fonction du rôle )](../active-directory/role-based-access-control-configure.md) à votre compte d’inscription. 

> [!IMPORTANT]
> Le ou les abonnements Azure créés par le biais de cette API sont régis par le contrat aux termes duquel vous avez obtenu des services Microsoft Azure auprès de Microsoft ou d’un revendeur agréé. Pour en savoir plus, consultez [Informations Juridiques Microsoft Azure](https://azure.microsoft.com/support/legal/).

Dans cet article, vous allez :

> [!div class="checklist"]
> * Apprendre à créer des abonnements par programmation en utilisant Azure Resource Manager
> * Comprendre comment utiliser RBAC pour partager la possibilité de créer des abonnements facturés sur votre compte EA

Consultez également [l’exemple de code .NET sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).

## <a name="ask-your-ea-enrollment-admin-to-add-you-as-account-owner"></a>Demander à votre administrateur d’inscription de Contrat Entreprise de vous ajouter en tant que propriétaire de compte

Pour commencer, demandez à votre administrateur d’inscription de [vous ajouter en tant que propriétaire de compte à l’aide du portail EA](https://ea.azure.com/helpdocs/addNewAccount) (connexion requise). Suivez les instructions de l’e-mail d’invitation que vous recevez pour créer manuellement un premier abonnement.

> [!IMPORTANT]
> Vous devez confirmer la propriété du compte et créer manuellement un premier abonnement EA avant de passer à l’étape suivante. Ajouter simplement le compte à l’inscription ne suffit pas.

## <a name="find-accounts-you-have-access-to"></a>Rechercher les comptes auxquels vous avez accès

Une fois que vous êtes ajouté à une inscription de Contrat Entreprise Azure en tant propriétaire de compte, Azure utilise la relation entre le compte et l’inscription pour déterminer où facturer les frais d’abonnement. Pour créer des abonnements, déterminez d’abord les comptes d’inscription auxquels vous avez accès. Si vous êtes actuellement propriétaire de compte EA et que vous essayez d’utiliser cette API, Azure vérifie les conditions suivantes :

- Votre compte a été ajouté à une inscription EA
- Vous avez un ou plusieurs abonnements EA ou EA Dev/Test, ce qui signifie que vous avez effectué une inscription manuelle au moins une fois
- Vous êtes connecté au *répertoire de base* du propriétaire de compte, qui est le répertoire dans lequel les abonnements sont créés par défaut

Si les deux conditions ci-dessus sont remplies, une ressource `enrollmentAccount` est retournée et vous pouvez commencer à créer des abonnements sous ce compte. Tous les abonnements créés sous le compte sont facturés à l’inscription EA à laquelle appartient celui-ci.

# <a name="resttabrest"></a>[REST](#tab/rest)

Demande pour obtenir la liste de tous les comptes d’inscription :

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure retourne la liste de tous les comptes d’inscription auxquels vous avez accès :

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Utilisez la [commande Get-EnrollmentAccount](/powershell/module/azurerm.billing/get-azurermenrollmentaccount) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurepowershell-interactive
Get-EnrollmentAccount
```

Azure retourne la liste des ID d’objet et des adresses e-mail des comptes.

```azurepowershell
ObjectId                               | PrincipalName
<enrollmentAccountId>   | MobileOnboardingEng@contoso.com
<enrollmentAccountId>   | MobileBackendEng@contoso.com
```

# <a name="azure-clitabazure-cli"></a>[interface de ligne de commande Azure](#tab/azure-cli)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Utilisez la commande [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurecli-interactive 
az billing enrollment-account list
```
Azure retourne la liste des ID d’objet et des adresses e-mail des comptes.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileOnboardingEng@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
      "name": "<enrollmentAccountId>",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "MobileBackendEng@contoso.com"
      }
    }
  ]
}
```

---

Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Utilisez l’`id` comme valeur `enrollmentAccount` à utiliser pour créer l’abonnement à l’étape suivante.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Créer des abonnements sous un compte d’inscription spécifique 

L’exemple suivant crée une demande de création d’abonnement nommée *Dev Team Subscription*, et l’offre d’abonnement est *MS-AZR-0017P* (EA normal). Le compte d’inscription est `<enrollmentAccountId>`, qui est le compte d’inscription pour MobileOnboardingEng@contoso.com. L’exemple ajoute également deux utilisateurs en tant que propriétaires RBAC pour l’abonnement.

# <a name="resttabrest"></a>[REST](#tab/rest)

Utilisez l’`id` du `enrollmentAccount` dans le chemin de la demande de création d’abonnement.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nom de l’élément  | Obligatoire | type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Non       | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offerType`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Non        | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |

Dans la réponse, vous obtenez un objet `subscriptionOperation` pour le monitoring. Quand la création de l’abonnement est terminée, l’objet `subscriptionOperation` retourne un objet `subscriptionLink`, qui a l’ID d’abonnement.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Pour utiliser ce module en préversion, installez-le en exécutant `Install-Module AzureRM.Subscription -AllowPrerelease` d’abord. Pour vous assurer que `-AllowPrerelease` fonctionne, installez une version récente de PowerShellGet à partir de la page [Obtenir le module PowerShellGet](/powershell/gallery/psget/get_psget_module).

Utilisez la commande [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview) avec le nom `enrollmentAccount` comme paramètre `EnrollmentAccountObjectId` pour créer un abonnement. 

```azurepowershell-interactive
New-AzureRmSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountId> -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nom de l’élément  | Obligatoire | type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Non       | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `OfferType`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `OwnerObjectId`      | Non        | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `OwnerSignInName`    | Non        | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`.|
| `OwnerApplicationId` | Non        | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`.| 

Pour obtenir la liste complète de tous les paramètres, consultez [New-AzureRmSubscription](/powershell/module/azurerm.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[interface de ligne de commande Azure](#tab/azure-cli)

Pour utiliser cette extension en préversion, installez-la en exécutant `az extension add --name subscription` d’abord.

Utilisez la commande [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) avec le nom `enrollmentAccount` comme paramètre `enrollment_account_name` pour créer un abonnement.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-name "<enrollmentAccountId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nom de l’élément  | Obligatoire | type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Non       | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offer-type`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owner-object-id`      | Non        | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `owner-upn`    | Non        | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`.|
| `owner-spn` | Non        | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`.| 

Pour obtenir la liste complète de tous les paramètres, consultez [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="delegate-access-to-an-enrollment-account-using-rbac"></a>Déléguer l’accès à un compte d’inscription à l’aide de RBAC

Pour permettre à un autre utilisateur ou service principal de créer des abonnements sur un compte spécifique, [attribuez-lui un rôle de propriétaire RBAC au niveau de l’étendue du compte d’inscription](../active-directory/role-based-access-control-manage-access-rest.md). L’exemple suivant donne à l’utilisateur dans le locataire dont `principalId` a pour valeur `<userObjectId>` (pour MobileOnboardingEng@contoso.com) un rôle de propriétaire sur le compte d’inscription. 

# <a name="resttabrest"></a>[REST](#tab/rest)

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

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
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>",
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
New-AzureRmRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
```

# <a name="azure-clitabazure-cli"></a>[interface de ligne de commande Azure](#tab/azure-cli)

Utilisez la commande [az role assignment create](../active-directory/role-based-access-control-manage-access-azure-cli.md) pour accorder à un autre utilisateur l’accès en tant que propriétaire à votre compte d’inscription.

```azurecli-interactive 
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountId>
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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitations de l’API de création d’abonnement Azure Enterprise

- Seuls des abonnements Azure Enterprise peuvent être créés à l’aide de cette API.
- Il existe une limite de 50 abonnements par compte. Au-delà, les abonnements ne peuvent être créés qu’à l’aide du Centre des comptes.
- Il doit y avoir au moins un abonnement EA ou EA Dev/Test sous le compte, ce qui signifie que le propriétaire du compte a effectué une inscription manuelle au moins une fois.
- Les utilisateurs qui ne sont pas propriétaires de compte, mais qui ont été ajoutés à un compte d’inscription par le biais de RBAC, ne peuvent pas créer d’abonnements à l’aide du Centre de gestion.
- Vous ne pouvez pas sélectionner le locataire où l’abonnement doit être créé. L’abonnement est toujours créé dans le locataire de base du propriétaire du compte. Pour déplacer l’abonnement vers un autre locataire, consultez [Changer le locataire d’abonnement](..\active-directory\active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple de création d’abonnements à l’aide de .NET, consultez [l’exemple de code sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Pour en savoir plus sur Azure Resource Manager et ses API, consultez [Vue d’ensemble d’Azure Resource Manager](resource-group-overview.md).
* Pour en savoir plus sur la gestion d’un nombre élevé d’abonnement avec des groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).
* Pour obtenir une aide complète sur les bonnes pratiques de gouvernance d’abonnements dans les grandes entreprises, consultez [Structure d’entreprise Azure : gouvernance normative de l’abonnement](resource-manager-subscription-governance.md)
