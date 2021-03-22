---
title: Créer des abonnements Azure pour un Contrat client Microsoft programmatiquement avec les API les plus récentes
description: Découvrez comment créer des abonnements Azure pour un Contrat client Microsoft programmatiquement en utilisant les versions les plus récentes de l’API REST, de l’interface Azure CLI et d’Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 61a658cc9654a93b4c92fda6cc1f38cd2e77dafa
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216086"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Créer des abonnements Azure pour un Contrat client Microsoft programmatiquement avec les API les plus récentes

Cet article vous aide à créer des abonnements Azure pour un Contrat client Microsoft programmatiquement à l’aide des versions les plus récentes des API. Si vous utilisez toujours la préversion, consultez [Créer des abonnements Azure programmatiquement avec des API en préversion](programmatically-create-subscription-preview.md). 

Dans cet article, vous apprenez à créer des abonnements par programmation en utilisant Azure Resource Manager.

Quand vous créez un abonnement Azure par programmation, cet abonnement est régi par le contrat applicable à votre achat de services Azure auprès de Microsoft ou d’un revendeur agréé. Pour plus d’informations, consultez [Informations légales relatives à Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire, Contributeur ou Créateur de l’abonnement Azure sur une section de facture ou bien un rôle Propriétaire ou Contributeur sur un profil de facturation ou un compte de facturation pour créer des abonnements. Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Si vous ne savez pas si vous avez accès à un compte Contrat client Microsoft, consultez [Vérifier l’accès à un Contrat client Microsoft](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

Les exemples suivants utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès

Exécutez la requête suivante pour lister tous les comptes de facturation.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
En réponse, l’API retourne la liste de tous les comptes de facturation auxquels vous avez accès.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Vérifiez que le compte affiche *MicrosoftCustomerAgreement* comme agreementType. Copiez la valeur `name` du compte.  Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-getBillingAccounts)

```azurepowershell-interactive
PS C:\WINDOWS\system32> Get-AzBillingAccount
```
Vous obtiendrez une liste de tous les comptes de facturation auxquels vous avez accès : 

```json
Name          : 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
DisplayName   : Contoso
AccountStatus : Active
AccountType   : Enterprise
AgreementType : MicrosoftCustomerAgreement
HasReadAccess : True
```
Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Vérifiez que le compte affiche *MicrosoftCustomerAgreement* comme agreementType. Copiez la valeur `name` du compte.  Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getBillingAccounts)
```azurecli
> az billing account list
```
Vous obtiendrez une liste de tous les comptes de facturation auxquels vous avez accès : 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Enterprise",
    "agreementType": "MicrosoftCustomerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Vérifiez que le compte affiche *MicrosoftCustomerAgreement* comme agreementType. Copiez la valeur `name` du compte.  Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Rechercher les profils de facturation et les sections de facture pour créer des abonnements

Les frais de votre abonnement sont indiqués dans une section de la facture d’un profil de facturation. Utilisez l’API suivante pour obtenir la liste des profils de facturation et des sections de facture pour lesquels vous avez l’autorisation de créer des abonnements Azure.

Vous obtenez d’abord la liste des profils de facturation sous le compte de facturation auquel vous avez accès (utilisez le `name` que vous avez obtenu à l’étape précédente).

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
En réponse, l’API retourne la liste de tous les profils de facturation auxquels vous avez accès pour créer des abonnements :

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Copiez l’`id` pour identifier les sections de facture sous le profil de facturation. Par exemple, copiez `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` et appelez l’API suivante.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>response

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Utilisez la propriété `id` afin d’identifier la section de facture pour laquelle vous souhaitez créer des abonnements. Copiez la chaîne entière. Par exemple, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-getBillingProfiles)

```powershell-interactive
PS C:\WINDOWS\system32> Get-AzBillingProfile -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

Vous obtiendrez la liste des profils de facturation sous ce compte dans le cadre de la réponse.

```json
Name              : AW4F-xxxx-xxx-xxx
DisplayName       : Contoso Billing Profile
Currency          : USD
InvoiceDay        : 5
InvoiceEmailOptIn : True
SpendingLimit     : Off
Status            : Active
EnabledAzurePlans : {0002, 0001}
HasReadAccess     : True
BillTo            :
CompanyName       : Contoso
AddressLine1      : One Microsoft Way
AddressLine2      : 
City              : Redmond
Region            : WA
Country           : US
PostalCode        : 98052
```

Notez la valeur `name` du profil de facturation de la réponse ci-dessus. L’étape suivante consiste à obtenir la section des factures à laquelle vous avez accès sous ce profil de facturation. Vous aurez besoin de la valeur `name` du compte de facturation et du profil de facturation.

```powershell-interactive
PS C:\WINDOWS\system32> Get-AzInvoiceSection -BillingAccountName 5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx -BillingProfileName AW4F-xxxx-xxx-xxx
```

La section des factures vous sera renvoyée.

```json
Name        : SH3V-xxxx-xxx-xxx
DisplayName : Development
```

La valeur `name` ci-dessus correspond au nom de la section Facture dont vous avez besoin pour créer un abonnement. Construisez votre étendue de facturation en utilisant le format « /providers/Microsoft.Billing/billingAccounts/<BillingAccountName>/billingProfiles/<BillingProfileName>/invoiceSections/<InvoiceSectionName> ». Dans cet exemple, cela correspond à `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-getBillingProfiles)

```azurecli-interactive
> az billing profile list --account-name "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --expand "InvoiceSections"
```
Cette API renvoie la liste des profils de facturation et des sections de facture sous le compte de facturation fourni.

```json
[
  {
    "billTo": {
      "addressLine1": "One Microsoft Way",
      "addressLine2": "",
      "addressLine3": null,
      "city": "Redmond",
      "companyName": "Contoso",
      "country": "US",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": "98052",
      "region": "WA"
    },
    "billingRelationshipType": "Direct",
    "currency": "USD",
    "displayName": "Contoso Billing Profile",
    "enabledAzurePlans": [
      {
        "skuDescription": "Microsoft Azure Plan for DevTest",
        "skuId": "0002"
      },
      {
        "skuDescription": "Microsoft Azure Plan",
        "skuId": "0001"
      }
    ],
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
    "indirectRelationshipInfo": null,
    "invoiceDay": 5,
    "invoiceEmailOptIn": true,
    "invoiceSections": {
      "hasMoreResults": false,
      "value": [
        {
          "displayName": "Field_Led_Test_Ace",
          "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
          "labels": null,
          "name": "SH3V-xxxx-xxx-xxx",
          "state": "Active",
          "systemId": "SH3V-xxxx-xxx-xxx",
          "targetCloud": null,
          "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
        }
      ]
    },
    "name": "AW4F-xxxx-xxx-xxx",
    "poNumber": null,
    "spendingLimit": "Off",
    "status": "Warned",
    "statusReasonCode": "PastDue",
    "systemId": "AW4F-xxxx-xxx-xxx",
    "targetClouds": [],
    "type": "Microsoft.Billing/billingAccounts/billingProfiles"
  }
]
```
Utilisez la propriété id sous l’objet de section de facture afin d’identifier la section de facture pour laquelle vous souhaitez créer des abonnements. Copiez la chaîne entière. Par exemple, /providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx.

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Créer un abonnement pour une section de facture

L’exemple suivant crée un abonnement nommé *Dev Team subscription* pour la section de facture *Development*. Cet abonnement est facturé au profil de facturation *Contoso Billing Profile* et s’affiche dans la section *Development* de la facture correspondante. Vous utilisez l’étendue de facturation copiée à l’étape précédente : `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Corps de la demande

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```

### <a name="response"></a>response

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

Pour installer la version la plus récente du module contenant la cmdlet `New-AzSubscriptionAlias`, exécutez `Install-Module Az.Subscription`. Pour installer une version récente de PowerShellGet, consultez [Obtenir le module PowerShellGet](/powershell/scripting/gallery/installing-psget).

Exécutez la commande [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription) suivante et l’étendue de facturation `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
```

Vous recevez le subscriptionId dans la réponse à la commande.

```azurepowershell
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MCA)

Commencez par installer cette extension en exécutant `az extension add --name account` et `az extension add --name alias`.

Exécutez la commande [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create) suivante.

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="next-steps"></a>Étapes suivantes

* Maintenant que vous avez créé un abonnement, vous pouvez accorder cette capacité à d’autres utilisateurs et principaux de service. Pour plus d’informations, consultez [Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)](grant-access-to-create-subscription.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../governance/management-groups/overview.md).
