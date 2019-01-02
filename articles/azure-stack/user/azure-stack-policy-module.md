---
title: Utiliser le module de stratégie Azure Stack | Microsoft Docs
description: Découvrez comment contraindre un abonnement Azure à se comporter comme un abonnement Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2018
ms.author: sethm
ms.openlocfilehash: b0236a790200feec7f1d16724f351882056b2cd5
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/30/2018
ms.locfileid: "52678523"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Gérer la stratégie Azure à l’aide du module de stratégie Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Le module de stratégie Azure Stack vous permet de configurer un abonnement Azure avec la même gestion des versions et la même disponibilité de service qu’Azure Stack. Le module utilise l’applet de commande [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) pour créer une stratégie Azure qui limite les types de ressources et les services disponibles dans un abonnement. Ensuite, vous créez une attribution de stratégie dans l’étendue appropriée à l’aide de l’applet de commande [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Une fois la stratégie configurée, vous pouvez utiliser votre abonnement Azure pour développer des applications ciblées pour Azure Stack.

## <a name="install-the-module"></a>Installer le module

1. Installez la version requise du module AzureRM PowerShell, comme décrit à l’étape 1 de [Installer PowerShell pour Azure Stack](azure-stack-powershell-install.md).
2. [Téléchargez les outils Azure Stack à partir de GitHub](azure-stack-powershell-download.md).
3. [Configurez PowerShell pour une utilisation avec Azure Stack](azure-stack-powershell-configure-user.md).
4. Importez le module AzureStack.Policy.psm1 :

    ```PowerShell
    Import-Module .\Policy\AzureStack.Policy.psm1
    ```

## <a name="apply-policy-to-azure-subscription"></a>Appliquer la stratégie à l’abonnement Azure

Vous pouvez utiliser la commande suivante pour appliquer une stratégie Azure Stack par défaut à votre abonnement Azure. Avant d’exécuter cette commande, remplacez `Azure Subscription Name` par le nom de votre abonnement Azure.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Appliquer la stratégie à un groupe de ressources

Vous souhaiterez peut-être appliquer des stratégies plus granulaires. Par exemple, vous pourriez avoir d’autres ressources en cours d’exécution dans le même abonnement. Vous pouvez limiter l’application de la stratégie à un groupe de ressources spécifique, ce qui vous permet de tester vos applications pour Azure Stack à l’aide de ressources Azure. Avant d’exécuter la commande suivante, remplacez `Azure Subscription Name` par le nom de votre abonnement Azure.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure Subscription Name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Stratégie en action

Une fois que vous avez déployé la stratégie Azure, vous recevez une erreur quand vous essayez de déployer une ressource interdite par la stratégie.

![Échec de déploiement de ressources en raison de la contrainte de stratégie](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Étapes suivantes

* [Déployer des modèles avec PowerShell](azure-stack-deploy-template-powershell.md)
* [Déployer des modèles avec l’interface de ligne de commande Azure](azure-stack-deploy-template-command-line.md)
* [Déployer des modèles avec Visual Studio](azure-stack-deploy-template-visual-studio.md)
