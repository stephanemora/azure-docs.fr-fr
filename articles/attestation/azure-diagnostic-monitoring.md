---
title: Supervision des diagnostics Azure - Azure Attestation
description: Supervision des diagnostics Azure pour Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 40bc76f839cf6757b8f874112504249e611c3e1a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605914"
---
# <a name="setting-up-diagnostics-with-trusted-platform-module-tpm-endpoint-of-azure-attestation"></a>Configuration des diagnostics avec un point de terminaison de Module de plateforme sécurisée (TPM) d’Azure Attestation

Les [journaux de plateforme](/azure/azure-monitor/platform/platform-logs-overview) dans Azure, y compris le journal d’activité Azure et les journaux de ressources, fournissent des informations de diagnostic et d’audit détaillées pour les ressources Azure et la plateforme Azure dont elles dépendent. Les [métriques de plateforme](/azure/azure-monitor/platform/data-platform-metrics) sont collectées par défaut et généralement stockées dans la base de données de métriques Azure Monitor. Cet article fournit des détails sur la création et la configuration de paramètres de diagnostic pour envoyer les journaux de plateforme et les métriques de plateforme vers différentes destinations. 

Le service de point de terminaison TPM est activé avec le paramètre de diagnostic et peut être utilisé pour superviser l’activité. Pour configurer [Azure Monitor](/azure/azure-monitor/overview) pour le point de terminaison du service TPM à l’aide de PowerShell, suivez les étapes ci-dessous. 

Configurez le service Azure Attestation. 

[Configurer Azure Attestation avec Azure PowerShell](/azure/attestation/quickstart-powershell#:~:text=%20Quickstart%3A%20Set%20up%20Azure%20Attestation%20with%20Azure,Register%20Microsoft.Attestation%20resource%20provider.%20Register%20the...%20More%20)

```powershell

 Connect-AzAccount 

 Set-AzContext -Subscription <Subscription id> 

 $attestationProviderName=<Name of the attestation provider> 

 $attestationResourceGroup=<Name of the resource Group> 

 $attestationProvider=Get-AzAttestation -Name $attestationProviderName -ResourceGroupName $attestationResourceGroup 

 $storageAccount=New-AzStorageAccount -ResourceGroupName $attestationProvider.ResourceGroupName -Name <Storage Account Name> -SkuName Standard_LRS -Location <Location> 

 Set-AzDiagnosticSetting -ResourceId $ attestationProvider.Id -StorageAccountId $ storageAccount.Id -Enabled $true 

```
Les journaux d’activité se trouvent dans la section Conteneurs du compte de stockage. Pour plus d’informations, consultez [Collecter des journaux de ressources à partir d’une ressource Azure et les analyser avec Azure Monitor - Azure Monitor](/azure/azure-monitor/learn/tutorial-resource-logs)
