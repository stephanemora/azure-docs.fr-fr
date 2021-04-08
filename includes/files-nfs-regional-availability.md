---
title: Fichier include
description: inclure fichier
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f5ebd05177abf5fb3468b3ce935b7d004b414424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100552289"
---
NFS est pris en charge dans **TOUTES** les plus de 30 régions où le stockage de fichiers Premium est disponible.

Nous ajoutons continuellement des régions. Pour obtenir la liste la plus récente, utilisez l’exemple ci-dessous afin d’interroger la liste des régions avec prise en charge NFS. Vous pouvez également vérifier la prise en charge de votre région sur la [page des produits Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all) sous **Stockage des fichiers Premium**.

```azurepowershell-interactive
# Log in first with Connect-AzAccount if not using Cloud Shell

$azContext = Get-AzContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Provide specific subscription id if you want  list for a different subscription
$subscription = $azContext.Subscription.Id

# Invoke the REST API
$restUri = "https://management.azure.com/subscriptions/$subscription/providers/Microsoft.Storage/skus?api-version=2019-06-01"
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader

# List of all regions that has NFS support.
$response.value| Where-Object -FilterScript {$_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'}}| Select-Object locations, kind, name

# List of regions that support NFS Zonal redundancy.
$response.value| Where-Object -FilterScript {($_.name -EQ 'Premium_ZRS') -and ($_.capabilities| Where-Object { $_.name -eq 'supportsNfsShare' -and $_.value -eq 'true'})}| Select-Object locations
```

Exemple de réponse
```
List of regions that support NFS Zonal redundancy
locations
---------
{eastus}
{eastus2}
{westeurope}
{southeastasia}
{japaneast}
{northeurope}
{australiaeast}
{westus2}
{uksouth}
{eastus2euap}
{francecentral}
```
