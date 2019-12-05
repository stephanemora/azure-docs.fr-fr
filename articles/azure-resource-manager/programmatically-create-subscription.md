---
title: Créer des abonnements Azure par programmation
description: Découvrez comment créer des abonnements Azure supplémentaires par programmation.
author: amberb
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: banders
ms.openlocfilehash: 757a542c8583f6a2b3f73e8144b6281438d75ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273600"
---
# <a name="programmatically-create-azure-subscriptions-preview"></a>Créer des abonnements Azure par programmation (préversion)

Les clients Azure qui ont un compte de facturation associé à un [Contrat Entreprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), un [Contrat Client Microsoft (MCA)](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/) ou un [Contrat Partenaire Microsoft (MPA)](https://www.microsoft.com/licensing/news/introducing-microsoft-partner-agreement) peuvent créer des abonnements par programmation. Dans cet article, vous apprenez à créer des abonnements par programmation en utilisant Azure Resource Manager.

Quand vous créez un abonnement Azure par programmation, cet abonnement est régi par le contrat applicable à votre achat de services Azure auprès de Microsoft ou d’un revendeur agréé. Pour en savoir plus, consultez [Informations Juridiques Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]


## <a name="create-subscriptions-for-an-ea-billing-account"></a>Créer des abonnements pour un compte de facturation EA

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire sur un compte d’inscription pour créer un abonnement. Vous pouvez obtenir ce rôle de deux manières :

* L’administrateur d’entreprise de votre inscription peut [vous rendre propriétaire d’un compte](https://ea.azure.com/helpdocs/addNewAccount) (connexion obligatoire), ce qui vous rend propriétaire du compte d’inscription.

* Un propriétaire existant du compte d’inscription peut [vous accorder l’accès](grant-access-to-create-subscription.md). De la même façon, si vous souhaitez utiliser un principal de service pour créer un abonnement EA, vous devez [autoriser ce principal de service à créer des abonnements](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Rechercher les comptes auxquels vous avez accès

Une fois que vous avez été ajouté à un compte d’inscription associé à un propriétaire de compte, Azure utilise la relation entre le compte et l’inscription pour déterminer où facturer les frais d’abonnement. Tous les abonnements créés sous le compte sont facturés à l’inscription EA à laquelle appartient ce compte. Pour créer des abonnements, vous devez transmettre les valeurs sur le compte d’inscription et les principaux d’utilisateur pour qu’ils obtiennent la propriété de l’abonnement. 

Pour exécuter les commandes suivantes, vous devez être connecté au *répertoire de base* du propriétaire de compte, qui est le répertoire dans lequel les abonnements sont créés par défaut.

### <a name="resttabrest"></a>[REST](#tab/rest)

Demande pour obtenir la liste de tous les comptes d’inscription auxquels vous avez accès :

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

En réponse, l’API retourne la liste de tous les comptes d’inscription auxquels vous avez accès :

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

Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copiez l’élément `name` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le compte d’inscription SignUpEngineering@contoso.com, vous allez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cet identificateur est l’ID d’objet du compte d’inscription. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Ouvrez [Azure Cloud Shell](https://shell.azure.com/) et sélectionnez PowerShell.

Utilisez l’applet de commande [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure renvoie la liste de tous les comptes d’inscription auxquels vous avez accès :

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copiez l’élément `ObjectId` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le compte d’inscription SignUpEngineering@contoso.com, vous allez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Collez cet ID d’objet quelque part, de façon à pouvoir l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Utilisez la commande [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) pour lister tous les comptes d’inscription auxquels vous avez accès.

```azurecli-interactive 
az billing enrollment-account list
```

Azure renvoie la liste de tous les comptes d’inscription auxquels vous avez accès :

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

Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copiez l’élément `name` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le compte d’inscription SignUpEngineering@contoso.com, vous allez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Cet identificateur est l’ID d’objet du compte d’inscription. Collez cette valeur quelque part, de façon à pouvoir l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Créer des abonnements sous un compte d’inscription spécifique

L’exemple suivant crée un abonnement nommé *Dev Team Subscription* dans le compte d’inscription sélectionné à l’étape précédente. L’offre d’abonnement est *MS-AZR - 0017p* (Accord Entreprise Microsoft standard). L’exemple ajoute également deux utilisateurs en tant que propriétaires RBAC pour l’abonnement.

### <a name="resttabrest"></a>[REST](#tab/rest)

Envoyez la requête suivante, en remplaçant `<enrollmentAccountObjectId>` par l’élément `name` copié lors de la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, découvrez [comment obtenir les ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

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

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offerType`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |

Dans la réponse, vous obtenez un objet `subscriptionOperation` pour le monitoring. Quand la création de l’abonnement est terminée, l’objet `subscriptionOperation` retourne un objet `subscriptionLink`, qui a l’ID d’abonnement.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, installez ce module en préversion en exécutant `Install-Module Az.Subscription -AllowPrerelease`. Pour vous assurer que `-AllowPrerelease` fonctionne, installez une version récente de PowerShellGet à partir de la page [Obtenir le module PowerShellGet](/powershell/scripting/gallery/installing-psget).

Exécutez la commande [New-AzSubscription](/powershell/module/az.subscription) ci-dessous, en remplaçant `<enrollmentAccountObjectId>` par l’élément `ObjectId` collecté dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, découvrez [comment obtenir les ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `OfferType`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | OUI       | Chaîne | ID d’objet du compte d’inscription sous lequel l’abonnement est créé et facturé. Il s’agit d’une valeur GUID que vous obtenez à partir de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `OwnerSignInName`    | Non       | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`.|
| `OwnerApplicationId` | Non       | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`. Lorsque vous utilisez ce paramètre, le principal de service doit avoir un [accès en lecture au répertoire](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Pour obtenir la liste complète de tous les paramètres, consultez [New-AzSubscription](/powershell/module/az.subscription).

### <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Commencez par installer cette extension en préversion en exécutant `az extension add --name subscription`.

Exécutez la commande [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) ci-dessous en remplaçant `<enrollmentAccountObjectId>` par l’élément `name` que vous avez copié dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, découvrez [comment obtenir les ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offer-type`   | OUI      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | OUI       | Chaîne | ID d’objet du compte d’inscription sous lequel l’abonnement est créé et facturé. Il s’agit d’une valeur GUID que vous obtenez à partir de `az billing enrollment-account list`. |
| `owner-object-id`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `owner-upn`    | Non       | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`.|
| `owner-spn` | Non       | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`. Lorsque vous utilisez ce paramètre, le principal de service doit avoir un [accès en lecture au répertoire](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Pour obtenir la liste complète de tous les paramètres, consultez [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitations de l’API de création d’abonnement Azure Enterprise

- Seuls des abonnements Azure Enterprise peuvent être créés à l’aide de cette API.
- Il existe une limite de 200 abonnements par compte d’inscription. Au-delà de cette limite, les abonnements supplémentaires pour le compte peuvent être créés uniquement à partir du portail Azure. Si vous souhaitez créer des abonnements supplémentaires via l’API, créez un autre compte d’inscription.
- Les utilisateurs qui ne sont pas propriétaires de compte, mais qui ont été ajoutés à un compte d’inscription par le biais de RBAC, ne peuvent pas créer d’abonnements dans le portail Azure.
- Vous ne pouvez pas sélectionner le locataire où l’abonnement doit être créé. L’abonnement est toujours créé dans le locataire de base du propriétaire du compte. Pour déplacer l’abonnement vers un autre locataire, consultez [Changer le locataire d’abonnement](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).


## <a name="create-subscriptions-for-an-mca-account"></a>Créer des abonnements pour un compte MCA

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Propriétaire, Contributeur ou Créateur de l’abonnement Azure sur une section de facture ou bien un rôle Propriétaire ou Contributeur sur un profil de facturation ou un compte de facturation pour créer des abonnements. Pour plus d’informations, consultez [Rôles et tâches liés à la facturation des abonnements](../billing/billing-understand-mca-roles.md#subscription-billing-roles-and-tasks).

L’exemple ci-dessous utilise des API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès 

Exécutez la requête suivante pour lister tous les comptes de facturation.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
En réponse, l’API retourne la liste de tous les comptes de facturation auxquels vous avez accès.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Assurez-vous que le compte affiche *MicrosoftCustomerAgreement* comme agreeementType. Copiez la valeur `name` du compte.  Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Rechercher les sections de facture pour créer des abonnements

Les frais de votre abonnement sont indiqués dans une section de la facture d’un profil de facturation. Utilisez l’API suivante pour obtenir la liste des sections de facture et des profils de facturation sur lesquels vous avez l’autorisation de créer des abonnements Azure.

Envoyez la requête suivante, en remplaçant `<billingAccountName>` par l’élément `name` copié lors de la première étape (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```
En réponse, l’API retourne la liste de toutes les sections de facture et des profils de facturation associés auxquels vous avez accès pour créer des abonnements :

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}
    
```

Utilisez la propriété `invoiceSectionDisplayName` afin d’identifier la section de facture pour laquelle vous souhaitez créer des abonnements. Copiez la valeur `invoiceSectionId`, la valeur `billingProfileId` et l’une des valeurs `skuId` pour la section de facture. Par exemple, si vous souhaitez créer un abonnement de type `Microsoft Azure plan` pour la section de facture `Development`, copiez `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` et `0001`. Collez ces valeurs quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="create-a-subscription-for-an-invoice-section"></a>Créer un abonnement pour une section de facture

L’exemple suivant crée un abonnement nommé *Dev Team subscription* de type *Microsoft Azure Plan* pour la section de facture *Development*. Cet abonnement sera facturé sur le profil de facturation de *Contoso finance* et apparaîtra dans la section *Development* de la facture correspondante. 

Exécutez la requête suivante, en remplaçant `<invoiceSectionId>` par la valeur `invoiceSectionId` que vous avez copiée à la deuxième étape (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```). Vous devez passer les valeurs `billingProfileId` et `skuId`, copiées à la deuxième étape, dans les paramètres de requête de l’API. Si vous souhaitez spécifier des propriétaires, découvrez [comment obtenir les ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | OUI      | Chaîne | Nom d’affichage de l’abonnement.|
| `billingProfileId`   | OUI      | Chaîne | ID du profil de facturation sur lequel les frais de l’abonnement seront facturés.  |
| `skuId` | OUI      | Chaîne | Référence SKU qui détermine le type de plan Azure. |
| `owners`      | Non       | Chaîne | ID d’objet du principal de service ou de l’utilisateur que vous souhaitez ajouter comme propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `costCenter` | Non      | Chaîne | Centre de coûts associé à l’abonnement. Il est indiqué dans le fichier CSV de l’utilisation. |
| `managementGroupId` | Non      | Chaîne | ID du groupe d’administration auquel l’abonnement sera ajouté. Pour obtenir la liste des groupes d’administration, consultez [Groupes d’administration - Lister les API](https://docs.microsoft.com/rest/api/resources/managementgroups/list). Utilisez l’ID d’un groupe d’administration fourni par l’API. |

Dans la réponse, vous obtenez un objet `subscriptionCreationResult` pour le monitoring. Quand la création de l’abonnement est terminée, l’objet `subscriptionCreationResult` retourne un objet `subscriptionLink`, qui a l’ID d’abonnement.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Créer des abonnements pour un compte de facturation MPA

### <a name="prerequisites"></a>Prérequis

Vous devez avoir un rôle Administrateur général ou Agent d’administration sur le compte du fournisseur de solutions Cloud dans votre organisation pour créer un abonnement associé à votre compte de facturation. Pour plus d’informations, consultez [Espace partenaires - Affecter des rôles et des autorisations aux utilisateurs](https://docs.microsoft.com/partner-center/permissions-overview).

L’exemple ci-dessous utilise des API REST. Actuellement, PowerShell et Azure CLI ne sont pas pris en charge.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>Rechercher les comptes de facturation auxquels vous avez accès 

Exécutez la requête suivante pour lister tous les comptes de facturation auxquels vous avez accès.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
En réponse, l’API retourne la liste des comptes de facturation.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Utilisez la propriété `displayName` afin d’identifier le compte de facturation pour lequel vous souhaitez créer des abonnements. Assurez-vous que le compte affiche *MicrosoftPartnerAgreement* comme agreeementType. Copiez la valeur `name` du compte. Par exemple, si vous souhaitez créer un abonnement pour le compte de facturation `Contoso`, copiez `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx`. Collez cette valeur quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="find-customers-that-have-azure-plans"></a>Rechercher les clients qui ont des plans Azure

Exécutez la requête suivante, en remplaçant `<billingAccountName>` par la valeur `name` que vous avez copiée à la première étape (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) pour lister tous les clients associés au compte de facturation pour lequel vous pouvez créer des abonnements Azure. 

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
En réponse, l’API retourne la liste des clients du compte de facturation qui ont des plans Azure. Vous pouvez créer des abonnements pour ces clients.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}
    
```

Utilisez la propriété `displayName` afin d’identifier le client pour lequel vous souhaitez créer des abonnements. Copiez la valeur `id` du client. Par exemple, si vous souhaitez créer un abonnement pour `Fabrikam toys`, copiez `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. Collez cette valeur quelque part, car vous en aurez besoin dans les étapes suivantes.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Facultatif pour les fournisseurs indirects : Obtenir les revendeurs d’un client

Si vous avez été ajouté comme fournisseur indirect dans le modèle CSP de niveau deux, vous pouvez spécifier un revendeur au moment de la création des abonnements pour les clients. 

Exécutez la requête suivante, en remplaçant `<customerId>` par la valeur `id` que vous avez copiée à la deuxième étape (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) pour lister tous les revendeurs disponibles pour un client.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
En réponse, l’API retourne la liste des revendeurs pour le client :

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```
Utilisez la propriété `description` afin d’identifier le revendeur qui sera associé à l’abonnement. Copiez la valeur `resellerId` du revendeur. Par exemple, si vous souhaitez associer `Wingtip`, copiez `3xxxxx`. Collez cette valeur quelque part, car vous en aurez besoin à l’étape suivante.

### <a name="create-a-subscription-for-a-customer"></a>Créer un abonnement pour un client

L’exemple suivant crée un abonnement nommé *Dev Team subscription* pour *Fabrikam toys* et associe le revendeur *Wingtip* à l’abonnement. T

Exécutez la requête suivante, en remplaçant `<customerId>` par la valeur `id` que vous avez copiée à la deuxième étape (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Passez la valeur *resellerId* facultative, que vous avez copiée à la deuxième étape, dans les paramètres de requête de l’API. 

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | OUI      | Chaîne | Nom d’affichage de l’abonnement.|
| `skuId` | OUI      | Chaîne | Référence SKU du plan Azure. Utilisez *0001* pour les abonnements de type Plan Microsoft Azure |
| `resellerId`      | Non       | Chaîne | ID MPN du revendeur qui sera associé à l’abonnement.  |

Dans la réponse, vous obtenez un objet `subscriptionCreationResult` pour le monitoring. Quand la création de l’abonnement est terminée, l’objet `subscriptionCreationResult` retourne un objet `subscriptionLink`, qui a l’ID d’abonnement.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple de création d’un abonnement Contrat Entreprise (EA) à l’aide de .NET, consultez l’[exemple de code sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Maintenant que vous avez créé un abonnement, vous pouvez accorder cette capacité à d’autres utilisateurs et principaux de service. Pour plus d’informations, consultez [Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)](grant-access-to-create-subscription.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).
