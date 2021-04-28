---
title: Supervision des diagnostics Azure pour Azure Attestation
description: Supervision des diagnostics Azure pour Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b0cd0402348e4aa45b291f30b677fc9e4bbdb98
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833631"
---
# <a name="set-up-diagnostics-with-a-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configurer des diagnostics avec un point de terminaison de Module de plateforme sécurisée (TPM) d’Azure Attestation

Cet article vous aide dans la création et la configuration de paramètres de diagnostic pour envoyer les journaux de plateforme et les métriques de plateforme vers différentes destinations. Les [journaux de plateforme](/azure/azure-monitor/platform/platform-logs-overview) dans Azure, y compris le journal des activités Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Les [métriques de plateforme](/azure/azure-monitor/platform/data-platform-metrics) sont collectées par défaut et stockées dans la base de données Métriques Azure Monitor.

Avant de commencer, assurez-vous d’avoir [configuré Azure Attestation avec Azure PowerShell](quickstart-powershell.md).

Le service de point de terminaison TPM est activé dans les paramètres de diagnostic et peut être utilisé pour superviser l’activité. Configurez la [Supervision Azure](/azure/azure-monitor/overview) pour le point de terminaison du service TPM en utilisant le code suivant.

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```

Les journaux d’activité se trouvent dans la section **Conteneurs** du compte de stockage. Pour plus d’informations, consultez [Collecter et analyser des journaux de ressources à partir d’une ressource Azure](/azure/azure-monitor/learn/tutorial-resource-logs).
