---
title: Supervision des diagnostics Azure - Azure Attestation
description: Supervision des diagnostics Azure pour Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: d01e7817906927295591353b710afe2899aacdf1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101726476"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configuration des diagnostics avec un point de terminaison de Module de plateforme sécurisée (TPM) d’Azure Attestation

Les [journaux de plateforme](../azure-monitor/essentials/platform-logs-overview.md) dans Azure, y compris le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Les [métriques de plateforme](../azure-monitor/essentials/data-platform-metrics.md) sont collectées par défaut et généralement stockées dans la base de données de métriques Azure Monitor. Cet article fournit des détails sur la création et la configuration de paramètres de diagnostic pour envoyer les journaux de plateforme et les métriques de plateforme vers différentes destinations. 

Le service de point de terminaison TPM est activé avec le paramètre de diagnostic et peut être utilisé pour superviser l’activité. Pour configurer [Azure Monitor](../azure-monitor/overview.md) pour le point de terminaison du service TPM à l’aide de PowerShell, suivez les étapes ci-dessous. 

Configurez le service Azure Attestation. 

[Configurer Azure Attestation avec Azure PowerShell](./quickstart-powershell.md)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Les journaux d’activité se trouvent dans la section Conteneurs du compte de stockage. Pour plus d’informations, consultez [Collecter des journaux de ressources à partir d’une ressource Azure et les analyser avec Azure Monitor - Azure Monitor](../azure-monitor/essentials/tutorial-resource-logs.md)
