---
title: Déployer à grande échelle Azure Policy vers des abonnements délégués
description: Découvrez comment la gestion des ressources déléguées Azure vous permet de déployer une définition et une affectation de stratégie sur plusieurs locataires.
ms.date: 11/8/2019
ms.topic: conceptual
ms.openlocfilehash: 9015351c3fc8f374c5ce85712907fa05249cde11
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984570"
---
# <a name="deploy-azure-policy-to-delegated-subscriptions-at-scale"></a>Déployer à grande échelle Azure Policy vers des abonnements délégués

En tant que fournisseur de services, vous assurez peut-être la gestion des ressources déléguée Azure de plusieurs locataires clients. [Azure Lighthouse](../overview.md) permet aux fournisseurs de services d’effectuer des opérations à grande échelle sur plusieurs locataires à la fois, améliorant ainsi l'efficacité des tâches de gestion.

Cette rubrique montre comment utiliser [Azure Policy](../../governance/policy/index.yml) pour déployer une définition et une affectation de stratégie sur plusieurs locataires à l’aide de commandes PowerShell. Dans cet exemple, la définition de stratégie veille à ce que les comptes de stockage soient sécurisés en n'autorisant que le trafic HTTPS.

## <a name="use-azure-resource-graph-to-query-across-customer-tenants"></a>Utiliser Azure Resource Graph pour interroger plusieurs locataires clients

Vous pouvez utiliser [Azure Resource Graph](../../governance/resource-graph/index.yml) pour interroger tous les abonnements des locataires clients que vous gérez. Dans cet exemple, nous allons identifier tous les comptes de stockage de ces abonnements qui n'exigent pas encore le trafic HTTPS.  

```powershell
$MspTenant = "insert your managing tenantId here"

$subs = Get-AzSubscription

$ManagedSubscriptions = Search-AzGraph -Query "ResourceContainers | where type == 'microsoft.resources/subscriptions' | where tenantId != '$($mspTenant)' | project name, subscriptionId, tenantId" -subscription $subs.subscriptionId

Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | project name, location, subscriptionId, tenantId, properties.supportsHttpsTrafficOnly" -subscription $ManagedSubscriptions.subscriptionId | convertto-json
```

## <a name="deploy-a-policy-across-multiple-customer-tenants"></a>Déployer une stratégie sur plusieurs locataires clients

L’exemple ci-dessous illustre comment utiliser un [modèle Azure Resource Manager](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json) pour déployer une définition de stratégie et une affectation de stratégie sur des abonnements délégués dans plusieurs locataires clients. Cette définition de stratégie exige que tous les comptes de stockage utilisent du trafic HTTPS. Elle empêche la création de comptes de stockage non conformes et marquent comme non conformes les comptes de stockage existants qui n'utilisent pas ce paramètre.

```powershell
Write-Output "In total, there are $($ManagedSubscriptions.Count) delegated customer subscriptions to be managed"

foreach ($ManagedSub in $ManagedSubscriptions)
{
    Select-AzSubscription -SubscriptionId $ManagedSub.subscriptionId

    New-AzDeployment -Name mgmt `
                     -Location eastus `
                     -TemplateUri "https://raw.githubusercontent.com/Azure/Azure-Lighthouse-samples/master/templates/policy-enforce-https-storage/enforceHttpsStorage.json" `
                     -AsJob
}
```

## <a name="validate-the-policy-deployment"></a>Valider le déploiement de la stratégie

Une fois que vous avez déployé le modèle Azure Resource Manager, vous pouvez vérifier la réussite de l'application de la définition de stratégie en tentant de créer un compte de stockage avec **EnableHttpsTrafficOnly** défini sur **false** dans l’un de vos abonnements délégués. En raison de l’affectation de la stratégie, vous ne devriez pas pouvoir créer ce compte de stockage.  

```powershell
New-AzStorageAccount -ResourceGroupName (New-AzResourceGroup -name policy-test -Location eastus -Force).ResourceGroupName `
                     -Name (get-random) `
                     -Location eastus `
                     -EnableHttpsTrafficOnly $false `
                     -SkuName Standard_LRS `
                     -Verbose                  
```

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé, supprimez la définition et l’affectation de stratégie créées par le déploiement.

```powershell
foreach ($ManagedSub in $ManagedSubscriptions)
{
    select-azsubscription -subscriptionId $ManagedSub.subscriptionId

    Remove-AzDeployment -Name mgmt -AsJob

    $Assignment = Get-AzPolicyAssignment | where-object {$_.Name -like "enforce-https-storage-assignment"}

    if ([string]::IsNullOrEmpty($Assignment))
    {
        Write-Output "Nothing to clean up - we're done"
    }
    else
    {

    Remove-AzPolicyAssignment -Name 'enforce-https-storage-assignment' -Scope "/subscriptions/$($ManagedSub.subscriptionId)" -Verbose

    Write-Output "Deployment has been deleted - we're done"
    }
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Policy](../../governance/policy/index.yml).
- Découvrez les [Expériences de gestion inter-locataire](../concepts/cross-tenant-management-experience.md).
