---
title: Créer des abonnements Azure Enterprise par programmation| Microsoft Docs
description: Découvrez comment créer des abonnements Azure Enterprise ou Enterprise Dev/Test supplémentaires par programmation.
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
ms.date: 04/10/2019
ms.author: jureid
ms.openlocfilehash: 7985451eb2bb5e9fd4fbcfb3d2fcf35149122c15
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65796078"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Créer des abonnements Azure Enterprise par programmation (préversion)

En tant que client Azure bénéficiant d’un [Contrat Entreprise](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA, Enterprise Agreement), vous pouvez créer des abonnements EA (MS-AZR-0017P) et EA Dev/Test (MS-AZR-0148P) par programmation. Dans cet article, vous apprenez à créer des abonnements par programmation en utilisant Azure Resource Manager.

Lorsque vous créez un abonnement Azure par le biais de cette API, cet abonnement est régi par le contrat aux termes duquel vous avez obtenu des services Microsoft Azure auprès de Microsoft ou d’un revendeur agréé. Pour en savoir plus, consultez [Informations Juridiques Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

Vous devez disposer d’un rôle de propriétaire sur le compte d’inscription que vous souhaitez créer des abonnements sous. Vous pouvez obtenir ces rôles de deux manières :

* Votre administrateur d’inscription peut [vous rendre propriétaire d’un compte](https://ea.azure.com/helpdocs/addNewAccount) (connexion obligatoire) qui vous rend un propriétaire du compte d’inscription. Suivez les instructions de l’e-mail d’invitation que vous recevez pour créer manuellement un premier abonnement. Confirmez la propriété du compte et créez manuellement un premier abonnement EA avant de passer à l’étape suivante. Ajouter simplement le compte à l’inscription ne suffit pas.

* Un propriétaire existant du compte d’inscription peut [vous accorder l’accès](grant-access-to-create-subscription.md). Par ailleurs, si vous souhaitez utiliser un principal de service pour créer l’abonnement EA, vous devez [accorder à ce principal de service la capacité à créer des abonnements](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Rechercher les comptes auxquels vous avez accès

Une fois que vous êtes ajouté à une inscription de Contrat Entreprise Azure en tant propriétaire de compte, Azure utilise la relation entre le compte et l’inscription pour déterminer où facturer les frais d’abonnement. Tous les abonnements créés sous le compte sont facturés à l’inscription EA à laquelle appartient celui-ci. Pour créer des abonnements, vous devez transmettre les valeurs sur le compte d’inscription et les principaux d’utilisateur pour qu’ils obtiennent la propriété de l’abonnement. 

Pour exécuter les commandes suivantes, vous devez être connecté au *répertoire de base* du propriétaire de compte, qui est le répertoire dans lequel les abonnements sont créés par défaut.

## <a name="resttabrest"></a>[REST](#tab/rest)

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

Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copie le `name` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Il s’agit de l’ID d’objet du compte d’inscription. Collez cette valeur quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

## <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

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
Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copie le `ObjectId` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Collez cet ID d’objet quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que le `enrollmentAccountObjectId`.

## <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

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

Utilisez la propriété `principalName` pour identifier le compte auquel vous souhaitez que soient facturés les abonnements. Copie le `name` de ce compte. Par exemple, si vous souhaitez créer des abonnements dans le SignUpEngineering@contoso.com compte d’inscription, vous voulez copier ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Il s’agit de l’ID d’objet du compte d’inscription. Collez cette valeur quelque part afin que vous pouvez l’utiliser à l’étape suivante en tant que `enrollmentAccountObjectId`.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Créer des abonnements sous un compte d’inscription spécifique

L’exemple suivant crée un abonnement nommé *Dev Team Subscription* dans le compte d’inscription sélectionné à l’étape précédente. L’offre d’abonnement est *MS-AZR - 0017p* (régulière Microsoft Enterprise Agreement). L’exemple ajoute également deux utilisateurs en tant que propriétaires RBAC pour l’abonnement.

# <a name="resttabrest"></a>[REST](#tab/rest)

Vérifiez les éléments suivants demande, en remplaçant `<enrollmentAccountObjectId>` avec la `name` copiés à partir de la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, Découvrez [comment obtenir l’ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

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

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                                |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offerType`   | Oui      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |

Dans la réponse, vous obtenez un objet `subscriptionOperation` pour le monitoring. Quand la création de l’abonnement est terminée, l’objet `subscriptionOperation` retourne un objet `subscriptionLink`, qui a l’ID d’abonnement.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Tout d’abord, installez ce module de la version préliminaire en exécutant `Install-Module Az.Subscription -AllowPrerelease`. Pour vous assurer que `-AllowPrerelease` fonctionne, installez une version récente de PowerShellGet à partir de la page [Obtenir le module PowerShellGet](/powershell/gallery/installing-psget).

Exécutez le [New-AzSubscription](/powershell/module/az.subscription) commande ci-dessous, en remplaçant `<enrollmentAccountObjectId>` avec la `ObjectId` collectées dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, Découvrez [comment obtenir l’ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                                |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `OfferType`   | Oui      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Oui       | Chaîne | ID d’objet du compte d’inscription sous lequel l’abonnement est créé et facturé. Il s’agit d’une valeur GUID que vous obtenez à partir de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `OwnerSignInName`    | Non       | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`.|
| `OwnerApplicationId` | Non       | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `OwnerObjectId`. Lorsque vous utilisez ce paramètre, le principal de service doit avoir un [accès en lecture au répertoire](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Pour obtenir la liste complète de tous les paramètres, consultez [New-AzSubscription](/powershell/module/az.subscription.preview).

# <a name="azure-clitabazure-cli"></a>[Interface de ligne de commande Azure](#tab/azure-cli)

Tout d’abord, installez cette extension de la version préliminaire en exécutant `az extension add --name subscription`.

Exécutez le [az compte créer](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) commande ci-dessous, en remplaçant `<enrollmentAccountObjectId>` avec la `name` vous avez copié dans la première étape (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Si vous souhaitez spécifier des propriétaires, Découvrez [comment obtenir l’ID d’objet utilisateur](grant-access-to-create-subscription.md#userObjectId).

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nom de l’élément  | Obligatoire | Type   | Description                                                                                                |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Non      | Chaîne | Nom d’affichage de l’abonnement. Si cet élément n’est pas spécifié, il est défini sur le nom de l’offre, tel que « Microsoft Azure Enterprise ».                                 |
| `offer-type`   | Oui      | Chaîne | Offre de l’abonnement. Les deux options pour EA sont [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (utilisation en production) et [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (développement/test, à [activer à l’aide du portail EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Oui       | Chaîne | ID d’objet du compte d’inscription sous lequel l’abonnement est créé et facturé. Il s’agit d’une valeur GUID que vous obtenez à partir de `az billing enrollment-account list`. |
| `owner-object-id`      | Non       | Chaîne | ID d’objet de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création.  |
| `owner-upn`    | Non       | Chaîne | Adresse e-mail de tout utilisateur que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`.|
| `owner-spn` | Non       | Chaîne | ID d’application de tout principal de service que vous souhaitez ajouter en tant que propriétaire RBAC sur l’abonnement au moment de sa création. Vous pouvez utiliser ce paramètre au lieu de `owner-object-id`. Lorsque vous utilisez ce paramètre, le principal de service doit avoir un [accès en lecture au répertoire](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Pour obtenir la liste complète de tous les paramètres, consultez [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitations de l’API de création d’abonnement Azure Enterprise

- Seuls des abonnements Azure Enterprise peuvent être créés à l’aide de cette API.
- Il existe une limite de 50 abonnements par compte de l’inscription initiale, mais vous pouvez [créer une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) pour augmenter la limite à 200. Après cela, les abonnements peuvent uniquement être créées via le centre des comptes.
- Il doit y avoir au moins un abonnement EA ou EA Dev/Test sous le compte, ce qui signifie que le propriétaire du compte a effectué une inscription manuelle au moins une fois.
- Les utilisateurs qui ne sont pas propriétaires de compte, mais qui ont été ajoutés à un compte d’inscription par le biais de RBAC, ne peuvent pas créer d’abonnements à l’aide du Centre de gestion.
- Vous ne pouvez pas sélectionner le locataire où l’abonnement doit être créé. L’abonnement est toujours créé dans le locataire de base du propriétaire du compte. Pour déplacer l’abonnement vers un autre locataire, consultez [Changer le locataire d’abonnement](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple de création d’abonnements à l’aide de .NET, consultez [l’exemple de code sur GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Maintenant que vous avez créé un abonnement, vous pouvez accorder cette capacité à d’autres utilisateurs et principaux de service. Pour plus d’informations, consultez [Accorder l’accès pour créer des abonnements Azure Enterprise (préversion)](grant-access-to-create-subscription.md).
* Pour plus d’informations sur la gestion d’un grand nombre d’abonnements à l’aide de groupes d’administration, consultez [Organiser vos ressources avec des groupes d’administration Azure](management-groups-overview.md).
