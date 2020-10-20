---
title: Créer des abonnements Azure programmatiquement avec les API les plus récentes
description: Découvrez comment créer des abonnements Azure supplémentaires programmatiquement avec des versions les plus récentes de l’API REST, d’Azure CLI et d’Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/12/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28397d36a611f26544fefb9f4fa7593a0fdf3f73
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014526"
---
# <a name="programmatically-create-azure-subscriptions-with-the-latest-apis"></a>Créer des abonnements Azure programmatiquement avec les API les plus récentes

Cet article explique comment créer des abonnements Azure programmatiquement à l’aide des versions les plus récentes des API. Si vous utilisez toujours la préversion, consultez [Créer des abonnements Azure programmatiquement avec des API en préversion](programmatically-create-subscription-preview.md). 

Les clients Azure disposant d’un compte de facturation pour les types de contrat suivants peuvent créer des abonnements programmatiquement :

- [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Contrat client Microsoft (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)
- [Contrat Partenaire Microsoft (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement)

Dans cet article, vous apprenez à créer des abonnements par programmation en utilisant Azure Resource Manager.

Quand vous créez un abonnement Azure par programmation, cet abonnement est régi par le contrat applicable à votre achat de services Azure auprès de Microsoft ou d’un revendeur agréé. Pour plus d’informations, consultez [Informations légales relatives à Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Créer des abonnements pour un compte de facturation EA

Consultez les sections suivantes pour savoir comment créer des abonnements EA.

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire sur un compte d’inscription pour créer un abonnement. Vous pouvez obtenir ce rôle de deux manières :

* L’administrateur d’entreprise de votre inscription peut [vous rendre propriétaire d’un compte](https://ea.azure.com/helpdocs/addNewAccount) (connexion obligatoire), ce qui vous rend propriétaire du compte d’inscription.
* Un propriétaire existant du compte d’inscription peut [vous accorder l’accès](grant-access-to-create-subscription.md). De la même façon, si vous souhaitez utiliser un principal de service pour créer un abonnement EA, vous devez [autoriser ce principal de service à créer des abonnements](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Rechercher les comptes auxquels vous avez accès

Une fois que vous avez été ajouté à un compte d’inscription associé à un propriétaire de compte, Azure utilise la relation entre le compte et l’inscription pour déterminer où facturer les frais d’abonnement. Tous les abonnements créés sous le compte sont facturés à l’inscription EA à laquelle appartient ce compte. Pour créer des abonnements, vous devez transmettre les valeurs sur le compte d’inscription et les principaux d’utilisateur pour qu’ils obtiennent la propriété de l’abonnement.

Pour exécuter les commandes suivantes, vous devez être connecté au *répertoire de base* du propriétaire de compte, qui est le répertoire dans lequel les abonnements sont créés par défaut.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

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

Notez l’`id` de l’un de vos `enrollmentAccounts`. Il s’agit de l’étendue de facturation dans laquelle une requête de création d’abonnement est lancée. 

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Créer des abonnements sous un compte d’inscription spécifique

L’exemple suivant crée un abonnement nommé *Dev Team Subscription* dans le compte d’inscription sélectionné à l’étape précédente. 

### <a name="rest"></a>[REST](#tab/rest-EA)

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Pour installer la version la plus récente du module contenant la cmdlet `New-AzSubscriptionAlias`, exécutez `Install-Module Az.Subscription`. Pour installer une version récente de PowerShellGet, consultez [Obtenir le module PowerShellGet](/powershell/scripting/gallery/installing-psget).

Exécutez la commande [New-AzSubscriptionAlias](/powershell/module/az.subscription/New-AzSubscriptionAlias) suivante à l’aide de l’étendue de facturation `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-EA)

Commencez par installer cette extension en exécutant `az extension add --name account` et `az extension add --name alias`.

Exécutez la commande [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) suivante, et fournissez le `billing-scope` et le `id` de l’une de vos `enrollmentAccounts`. 

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

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitations de l’API de création d’abonnement Azure Enterprise

- Seuls des abonnements Azure Enterprise peuvent être créés à l’aide de l’API.
- Il existe une limite de 2 000 abonnements par compte d’inscription. Au-delà de cette limite, les abonnements supplémentaires pour le compte peuvent être créés uniquement à partir du portail Azure. Pour créer des abonnements supplémentaires via l’API, créez un autre compte d’inscription.
- Les utilisateurs qui ne sont pas propriétaires de compte, mais qui ont été ajoutés à un compte d’inscription par le biais d’un RBAC Azure, ne peuvent pas créer d’abonnements dans le portail Azure.
- Vous ne pouvez pas sélectionner le locataire où l’abonnement doit être créé. L’abonnement est toujours créé dans le locataire de base du propriétaire du compte. Pour déplacer l’abonnement vers un autre locataire, consultez [Changer le locataire d’abonnement](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Créer des abonnements pour un compte MCA

Consultez les sections suivantes pour savoir comment créer des abonnements MCA.

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire, Contributeur ou Créateur de l’abonnement Azure sur une section de facture ou bien un rôle Propriétaire ou Contributeur sur un profil de facturation ou un compte de facturation pour créer des abonnements. Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Les exemples suivants utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès

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

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Rechercher les profils de facturation et les sections de facture pour créer des abonnements

Les frais de votre abonnement sont indiqués dans une section de la facture d’un profil de facturation. Utilisez l’API suivante pour obtenir la liste des profils de facturation et des sections de facture pour lesquels vous avez l’autorisation de créer des abonnements Azure.

Tout d’abord, vous pouvez obtenir la liste des profils de facturation dans le compte de facturation auquel vous avez accès.

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

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="create-a-subscription-for-an-invoice-section"></a>Créer un abonnement pour une section de facture

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

Exécutez la commande [New-AzSubscriptionAlias](/powershell/module/az.subscription/New-AzSubscriptionAlias) suivante et l’étendue de facturation `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

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

Exécutez la commande [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) suivante.

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

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Créer des abonnements pour un compte de facturation MPA

Utilisez les informations des sections suivantes pour savoir comment créer des abonnements MPA.

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Administrateur général ou Agent d’administration sur le compte du fournisseur de solutions Cloud dans votre organisation pour créer un abonnement associé à votre compte de facturation. Pour plus d’informations, consultez [Espace partenaires - Affecter des rôles et des autorisations aux utilisateurs](/partner-center/permissions-overview).

Les exemples suivants utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès

Exécutez la requête suivante pour lister tous les comptes de facturation auxquels vous avez accès.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

En réponse, l’API retourne la liste des comptes de facturation.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Vérifiez que le compte affiche *MicrosoftPartnerAgreement* comme agreementType. Copiez la valeur `name` du compte. Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts-MPA)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="find-customers-that-have-azure-plans"></a>Rechercher les clients qui ont des plans Azure

Exécutez la requête suivante avec la valeur `name`que vous avez copiée à la première étape (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) pour lister tous les clients associés au compte de facturation pour lequel vous pouvez créer des abonnements Azure.

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

En réponse, l’API retourne la liste des clients du compte de facturation qui ont des plans Azure. Vous pouvez créer des abonnements pour ces clients.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Utilisez la propriété `displayName` afin d’identifier le client pour lequel vous souhaitez créer des abonnements. Copiez la valeur `id` du client. Par exemple, si vous souhaitez créer un abonnement pour `Fabrikam toys`, copiez `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Collez cette valeur quelque part de façon à pouvoir l’utiliser dans les étapes suivantes.

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getCustomers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Facultatif pour les fournisseurs indirects : Obtenir les revendeurs d’un client

Si vous avez été ajouté comme fournisseur indirect dans le modèle CSP de niveau deux, vous pouvez spécifier un revendeur au moment de la création des abonnements pour les clients.

Exécutez la requête suivante avec la valeur `id` que vous avez copiée à la deuxième étape (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) afin de lister tous les revendeurs disponibles pour un client.

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
En réponse, l’API retourne la liste des revendeurs pour le client :

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Utilisez la propriété `description` afin d’identifier le revendeur qui est associé à l’abonnement. Copiez la valeur `resellerId` du revendeur. Par exemple, pour associer `Wingtip`, copiez `3xxxxx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getIndirectResellers)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="create-a-subscription-for-a-customer"></a>Créer un abonnement pour un client

L’exemple suivant crée un abonnement nommé *Dev Team subscription* pour *Fabrikam toys* et associe le revendeur *Wingtip* à l’abonnement. Utilisez l’étendue de facturation copiée à l’étape précédente : `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest-MPA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Corps de la demande

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
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

Passez la valeur *resellerId* facultative, que vous avez copiée à la deuxième étape dans le corps de requête de l’API.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MPA)

Pour installer la version la plus récente du module contenant la cmdlet `New-AzSubscriptionAlias`, exécutez `Install-Module Az.Subscription`. Pour installer une version récente de PowerShellGet, consultez [Obtenir le module PowerShellGet](/powershell/scripting/gallery/installing-psget).

Exécutez la commande [New-AzSubscriptionAlias](/powershell/module/az.subscription/New-AzSubscriptionAlias) suivante à l’aide de l’étendue de facturation `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
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

Passez la valeur *resellerId* facultative, que vous avez copiée à la deuxième étape dans l’appel `New-AzSubscriptionAlias`.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-MPA)

Commencez par installer cette extension en exécutant `az extension add --name account` et `az extension add --name alias`.

Exécutez la commande [az account alias create](/cli/azure/ext/account/account/alias?view=azure-cli-latest#ext_account_az_account_alias_create&preserve-view=true) suivante. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
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

Passez la valeur *resellerId* facultative, que vous avez copiée à la deuxième étape dans l’appel `az account alias create`.

---

## <a name="create-subscriptions-using-arm-templates"></a>Créer des abonnements à l’aide de modèles ARM

Vous pouvez créer des abonnements avec votre modèle Azure Resource Manager (ARM), ce qui vous permet d’automatiser vos processus de déploiement de production et de test. Dans l’exemple suivant, vous allez utiliser un modèle ARM pour créer un abonnement et un groupe de ressources Azure.

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire, Contributeur ou Créateur de l’abonnement Azure sur une section de facture ou bien un rôle Propriétaire ou Contributeur sur un profil de facturation ou un compte de facturation pour créer des abonnements. Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](understand-mca-roles.md#subscription-billing-roles-and-tasks).

En outre, étant donné que vous effectuez un déploiement de modèle ARM, vous devez disposer d’autorisations en écriture sur l’objet racine. Par conséquent, si vous créez le déploiement ARM dans un groupe d’administration, vous devez disposer d’autorisations en écriture sur le groupe d’administration. Notez que l’action consiste uniquement à créer un déploiement ARM. Si un abonnement est créé, il est créé uniquement dans le groupe d’administration qui est spécifié dans le modèle ARM.

Les exemples suivants utilisent les API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès

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

Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Vérifiez que le compte affiche *MicrosoftCustomerAgreement* comme agreementType. Copiez la valeur `name` du compte. Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Rechercher les profils de facturation et les sections de facture pour créer des abonnements

Les frais de votre abonnement sont indiqués dans une section de la facture d’un profil de facturation. Utilisez l’API suivante pour obtenir la liste des profils de facturation et des sections de facture pour lesquels vous avez l’autorisation de créer des abonnements Azure.

Tout d’abord, vous pouvez obtenir la liste des profils de facturation dans le compte de facturation auquel vous avez accès.

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

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

### <a name="create-a-subscription-and-resource-group-with-a-template"></a>Créer un abonnement et un groupe de ressources à l’aide d’un modèle

Le modèle ARM suivant crée un abonnement nommé *Dev Team subscription* pour la section de facture *Development*. Cet abonnement est facturé au profil de facturation *Contoso Billing Profile* et s’affiche dans la section *Development* de la facture correspondante. Utilisez l’étendue de facturation copiée à l’étape précédente : `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

#### <a name="request"></a>Requête

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

#### <a name="request-body"></a>Corps de la demande

```json
{
  "properties":
    {
        "location": "westus",
        "properties": {
            "template": {
                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                "contentVersion": "1.0.0.0",
                "parameters": {},
                "variables": {
                    "uniqueAliasName": "sampleAlias"
                },
                "resources": [
                    {
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "name": "sampleTemplate",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "template": {
                                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                "contentVersion": "1.0.0.0",
                                "variables": {
                                    "uniqueAliasName": "sampleAlias"
                                },
                                "resources": [
                                    {
                                        "name": "[variables('uniqueAliasName')]",
                                        "type": "Microsoft.Subscription/aliases",
                                        "apiVersion": "2020-09-01",
                                        "properties": {
                                            "workLoad": "Production",
                                            "displayName": "Dev Team subscription",
                                            "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                                        },
                                        "dependsOn": [],
                                        "tags": {}
                                    }
                                ],
                                "outputs": {
                                    "subscriptionId": {
                                        "type": "string",
                                        "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                                    }
                                }
                            }
                        }
                    },
                    {
                        "name": "sampleOuterResource",
                        "type": "Microsoft.Resources/deployments",
                        "apiVersion": "2019-10-01",
                        "location": "westus",
                        "properties": {
                            "expressionEvaluationOptions": {
                                "scope": "inner"
                            },
                            "mode": "Incremental",
                            "parameters": {
                                "subscriptionId": {
                                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                                }
                            },
                            "template": {
                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                            "contentVersion": "1.0.0.0",
                                "parameters": {
                                    "subscriptionId": {
                                        "type": "string"
                                    }
                                },
                                "variables": {},
                                "resources": [
                                    {
                                        "name": "sampleInnerResource",
                                        "type": "Microsoft.Resources/deployments",
                                        "subscriptionId": "[parameters('subscriptionId')]",
                                        "apiVersion": "2019-10-01",
                                        "location": "westus",
                                        "properties": {
                                            "expressionEvaluationOptions": {
                                                "scope": "inner"
                                            },
                                            "mode": "Incremental",
                                            "parameters": {},
                                            "template": {
                                                "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                                                "contentVersion": "1.0.0.0",
                                                "parameters": {},
                                                "variables": {},
                                                "resources": [
                                                    {
                                                        "type": "Microsoft.Resources/resourceGroups",
                                                        "apiVersion": "2020-05-01",
                                                        "location": "[deployment().location]",
                                                        "name": "sampleRG",
                                                        "properties": {},
                                                        "tags": {}
                                                    }
                                                ],
                                                "outputs": {}
                                            }
                                        }
                                    }
                                ],
                                "outputs": {}
                            }
                        }
                    }
                ],
                "outputs": {
                    "messageFromLinkedTemplate": {
                        "type": "string",
                        "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                    }
                }
            },
            "mode": "Incremental"
        }
    }
}
```

#### <a name="response"></a>response

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

Vous pouvez connaître l’état du déploiement pour en superviser la progression.

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

#### <a name="response"></a>response

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

Dans l’exemple précédent, vous pouvez voir que l’abonnement créé était `16edf959-11fd-48bb-9a46-85190963ead9` et que le groupe de ressources créé était `sampleRG`.

## <a name="next-steps"></a>Étapes suivantes

* Maintenant que vous avez créé un abonnement, vous pouvez accorder cette capacité à d’autres utilisateurs et principaux de service. Pour plus d’informations, consultez [Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)](grant-access-to-create-subscription.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](../../governance/management-groups/overview.md).
