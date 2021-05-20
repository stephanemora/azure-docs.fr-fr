---
title: Créer des abonnements Contrat Entreprise Azure programmatiquement avec les API les plus récentes
description: Découvrez comment créer des abonnements Contrat Entreprise Azure programmatiquement avec les versions les plus récentes de l’API REST, de l’interface Azure CLI, d’Azure PowerShell et des modèles Azure Resource Manager.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9f07a4f9c42923ac42735155fb0da21dee3a2353
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109632364"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Créer des abonnements Contrat Entreprise Azure programmatiquement avec les API les plus récentes

Cet article vous aide à créer des abonnements Contrat Entreprise (EA) Azure programmatiquement pour un compte de facturation EA à l’aide des versions d’API les plus récentes. Si vous utilisez toujours la préversion, consultez [Créer des abonnements Azure programmatiquement avec des API en préversion](programmatically-create-subscription-preview.md). 

Dans cet article, vous apprenez à créer des abonnements par programmation en utilisant Azure Resource Manager.

Quand vous créez un abonnement Azure par programmation, cet abonnement est régi par le contrat applicable à votre achat de services Azure auprès de Microsoft ou d’un revendeur agréé. Pour plus d’informations, consultez [Informations légales relatives à Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire sur un compte d’inscription pour créer un abonnement. Vous pouvez obtenir ce rôle de deux manières :

* L’administrateur d’entreprise de votre inscription peut [vous rendre propriétaire d’un compte](https://ea.azure.com/helpdocs/addNewAccount) (connexion obligatoire), ce qui vous rend propriétaire du compte d’inscription.
* Un propriétaire existant du compte d’inscription peut [vous accorder l’accès](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). De la même façon, si vous souhaitez utiliser un principal de service pour créer un abonnement EA, vous devez [autoriser ce principal de service à créer des abonnements](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).  
    Si vous utilisez un nom de principal du service pour créer des abonnements, utilisez l’ObjectId de l’inscription d’application Azure AD comme ObjectId du principal du service à l’aide d’[Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0&preserve-view=true ) ou de l’interface [Azure CLI](/cli/azure/ad/sp?view=azure-cli-latest&preserve-view=true#az_ad_sp_list). Pour plus d’informations sur la demande d’API d’attribution de rôle EA, consultez [Attribuer des rôles à des noms de principal de service Contrat Entreprise Azure](assign-roles-azure-service-principals.md). Cette page comprend la liste des rôles (et des ID de définition de rôle) qui peuvent être affectés à un nom de principal de service.
  > [!NOTE]
  > Veillez à utiliser la bonne version d’API pour accorder des autorisations de propriétaire au compte d’inscription. Pour cet article et pour les API qui y sont documentées, utilisez l’API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Si vous effectuez une migration en vue d’utiliser les nouvelles API, vous devez à nouveau accorder des autorisations de propriétaire à l’aide de [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). La configuration précédente effectuée avec la [version 2015-07-01](grant-access-to-create-subscription.md) n’est pas automatiquement convertie en vue d’être utilisée avec les nouvelles API.

## <a name="find-accounts-you-have-access-to"></a>Rechercher les comptes auxquels vous avez accès

Une fois que vous avez été ajouté à un compte d’inscription associé à un propriétaire de compte, Azure utilise la relation entre le compte et l’inscription pour déterminer où facturer les frais d’abonnement. Tous les abonnements créés sous le compte sont facturés à l’inscription EA à laquelle appartient ce compte. Pour créer des abonnements, vous devez transmettre les valeurs sur le compte d’inscription et les principaux d’utilisateur pour qu’ils obtiennent la propriété de l’abonnement.

Pour exécuter les commandes suivantes, vous devez être connecté au *répertoire de base* du propriétaire de compte, qui est le répertoire dans lequel les abonnements sont créés par défaut.

### <a name="rest"></a>[REST](#tab/rest)

Demande pour obtenir la liste de tous les comptes d’inscription auxquels vous avez accès :

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

En réponse, l’API retourne la liste de tous les comptes d’inscription auxquels vous avez accès :

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

Les valeurs d’une étendue de facturation et celles de `id` sont la même chose. `id`, pour votre compte d’inscription, est l’étendue de facturation sous laquelle la demande d’abonnement est initiée. Il est important de connaître l’ID, car c’est un paramètre obligatoire que vous allez utiliser pour créer un abonnement, plus loin dans cet article.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Utilisez Azure CLI ou l’API REST pour obtenir cette valeur.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Demande pour obtenir la liste de tous les comptes d’inscription auxquels vous avez accès :

```azurecli-interactive
> az billing account list
```

La réponse liste tous les comptes d’inscription auxquels vous avez accès :

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

Les valeurs d’une étendue de facturation et celles de `id` sont la même chose. `id`, pour votre compte d’inscription, est l’étendue de facturation sous laquelle la demande d’abonnement est initiée. Il est important de connaître l’ID, car c’est un paramètre obligatoire que vous allez utiliser pour créer un abonnement, plus loin dans cet article.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Créer des abonnements sous un compte d’inscription spécifique

L’exemple suivant crée un abonnement nommé *Dev Team Subscription* dans le compte d’inscription sélectionné à l’étape précédente. 

### <a name="rest"></a>[REST](#tab/rest)

Appelez l’API PUT pour créer un alias ou une requête de création d’abonnement.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

Dans le corps de la requête, fournissez comme `billingScope` l’`id` de l’un de vos `enrollmentAccounts`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Les valeurs autorisées pour `Workload` sont `Production` et `DevTest`.

#### <a name="response"></a>response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

Vous pouvez exécuter une opération GET sur la même URL pour obtenir l’état de la requête.

### <a name="request"></a>Requête

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>response

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

L’état En cours est retourné comme un état `Accepted` sous `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pour installer la version la plus récente du module contenant la cmdlet `New-AzSubscriptionAlias`, exécutez `Install-Module Az.Subscription`. Pour installer une version récente de PowerShellGet, consultez [Obtenir le module PowerShellGet](/powershell/scripting/gallery/installing-psget).

Exécutez la commande [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) suivante à l’aide de l’étendue de facturation `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

Vous recevez le subscriptionId dans la réponse à la commande.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Commencez par installer cette extension en exécutant `az extension add --name account` et `az extension add --name alias`.

Exécutez la commande [az account alias create](/cli/azure/account/alias#az_account_alias_create) suivante, et fournissez le `billing-scope` et le `id` de l’une de vos `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

Vous recevez le subscriptionId dans la réponse à la commande.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>Utiliser un modèle ARM

La section précédente vous a montré comment créer un abonnement avec PowerShell, Azure CLI ou l’API REST. Si vous devez automatiser la création des abonnements, utilisez plutôt un modèle ARM (Azure Resource Manager).

Le modèle suivant permet de créer un abonnement. Pour `billingScope` , indiquez l’ID du compte d’inscription. Pour `targetManagementGroup`, indiquez le groupe d’administration dans lequel vous souhaitez créer l’abonnement.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Déployez le modèle au [niveau du groupe d’administration](../../azure-resource-manager/templates/deploy-to-management-group.md).

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Avec le corps de la demande :

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitations de l’API de création d’abonnement Azure Enterprise

- Seuls des abonnements Azure Enterprise peuvent être créés à l’aide de l’API.
- Il existe une limite de 2 000 abonnements par compte d’inscription. Au-delà de cette limite, les abonnements supplémentaires pour le compte peuvent être créés uniquement à partir du portail Azure. Pour créer des abonnements supplémentaires via l’API, créez un autre compte d’inscription. Les abonnements annulés, supprimés et transférés sont pris en compte dans la limite de 2 000.
- Les utilisateurs qui ne sont pas propriétaires de compte, mais qui ont été ajoutés à un compte d’inscription par le biais d’un RBAC Azure, ne peuvent pas créer d’abonnements dans le portail Azure.
- Vous ne pouvez pas sélectionner le locataire où l’abonnement doit être créé. L’abonnement est toujours créé dans le locataire de base du propriétaire du compte. Pour déplacer l’abonnement vers un autre locataire, consultez [Changer le locataire d’abonnement](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="next-steps"></a>Étapes suivantes

* Maintenant que vous avez créé un abonnement, vous pouvez accorder cette capacité à d’autres utilisateurs et principaux de service. Pour plus d’informations, consultez [Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)](grant-access-to-create-subscription.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../governance/management-groups/overview.md).
* Pour changer le groupe d’administration d’un abonnement, consultez [Déplacer des abonnements](../../governance/management-groups/manage.md#move-subscriptions).
