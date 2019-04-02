---
title: Exemple de script Azure PowerShell - Ajouter une règle de groupe de sécurité réseau | Microsoft Docs
description: Exemple de script Azure PowerShell - Ajouter une règle de groupe de sécurité réseau pour autoriser le trafic entrant sur un port spécifique.
services: service-fabric
documentationcenter: ''
author: aljo-microsoft
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.devlang: na
ms.topic: sample
ms.date: 11/28/2017
ms.author: aljo
ms.custom: mvc
ms.openlocfilehash: fead6aa19775eab6cc3a1014e9f52b30cfa2cefa
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58498313"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Ajouter une règle de groupe de sécurité réseau de trafic entrant

Cet exemple de script crée une règle de groupe de sécurité réseau visant à autoriser le trafic entrant sur le port 8081.  Le script obtient la ressource `Microsoft.Network/networkSecurityGroups` où se trouve le cluster, crée une règle de configuration de sécurité réseau et met à jour le groupe de sécurité réseau. Personnalisez les paramètres selon vos besoins.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview). 

## <a name="sample-script"></a>Exemple de script

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Obtient la ressource `Microsoft.Network/networkSecurityGroups`. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Obtient le groupe de sécurité réseau à partir du nom.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Ajoute une configuration de règle de sécurité réseau à un groupe de sécurité réseau. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Définit l’état de l’objectif d’un groupe de sécurité réseau.|

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez [Documentation Azure PowerShell](/powershell/azure/overview).
