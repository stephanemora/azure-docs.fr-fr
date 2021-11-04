---
title: Intégration à Microsoft Defender pour le cloud avec PowerShell
description: Ce document vous guide tout au long du processus d’activation de Microsoft Defender pour le cloud avec les cmdlet PowerShell.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/20/2021
ms.author: memildin
ms.openlocfilehash: 5e2524121aea09142a04b06b5ee6736e977b2b94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452791"
---
# <a name="automate-onboarding-of-microsoft-defender-for-cloud-using-powershell"></a>Automatiser l’intégration de Microsoft Defender pour le cloud à l’aide de PowerShell

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Vous pouvez sécuriser vos charges de travail Azure par programme, à l’aide du module Microsoft Defender pour le cloud avec PowerShell.
PowerShell vous permet d’automatiser des tâches et d’éviter toute erreur humaine inhérente aux tâches manuelles. Cela est particulièrement utile dans le cadre de déploiements à grande échelle impliquant des dizaines d’abonnements avec des centaines de milliers de ressources qui doivent toutes être sécurisées dès le début.

L’intégration de Microsoft Defender pour le cloud à l’aide de PowerShell vous permet d’automatiser par programme l’intégration et la gestion de vos ressources Azure, ainsi que d’ajouter les contrôles de sécurité nécessaires.

Cet article fournit un exemple de script PowerShell que vous pouvez modifier et utiliser dans votre environnement pour déployer Defender pour le cloud sur tous vos abonnements. 

Dans cet exemple, nous allons activer Defender pour le cloud sur un abonnement dont l’ID est d07c0080-170c-4c24-861d-9c817742786c, puis appliquer les paramètres recommandés qui fournissent un niveau élevé de protection, en activant les fonctionnalités de sécurité renforcée de Defender pour le cloud, lequel fournit des fonctionnalités avancées de détection des menaces et de protection contre celles-ci :

1. Activez [la sécurité renforcée dans Microsoft Defender](enable-enhanced-security.md).
 
2. Définissez l’espace de travail Log Analytics auquel l’agent Log Analytics enverra les données collectées sur les machines virtuelles associées à l’abonnement. Dans cet exemple, il s’agit d’un espace de travail défini par l’utilisateur (myWorkspace).

3. Activez la fonctionnalité d’approvisionnement automatique d’agent de Defender pour le cloud qui [déploie l’agent Log Analytics](enable-data-collection.md#auto-provision-mma).

5. Définissez le [CISO de l’organisation en tant que contact de sécurité pour les événements notables et alertes Defender pour le cloud](configure-email-notifications.md).

6. Assignez les [stratégies de sécurité par défaut](tutorial-security-policy.md) de Defender pour le cloud.

## <a name="prerequisites"></a>Prérequis

Vous devez effectuer ces étapes avant d’exécuter les cmdlet Defender pour le cloud :

1. Exécutez PowerShell en tant qu’administrateur.

1. Exécutez les commandes suivantes dans PowerShell :
      
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy AllSigned
    ```

    ```powershell
    Install-Module -Name Az.Security -Force
    ```

## <a name="onboard-defender-for-cloud-using-powershell"></a>Intégrez Defender pour le cloud à l’aide de PowerShell

1. Inscrivez vos abonnements au fournisseur de ressources Defender pour le cloud :

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.Security'
    ```

1. Facultatif : définissez le niveau de couverture (activation/désactivation des fonctionnalités de sécurité renforcée de Microsoft Defender pour le cloud) des abonnements. S’il n’est pas défini, ces fonctionnalités sont désactivées :

    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```

    ```powershell
    Set-AzSecurityPricing -Name "default" -PricingTier "Standard"
    ```

1. Configurez un espace de travail Log Analytics auquel les agents rendront compte. Vous devez disposer d’un espace de travail Log Analytics que vous avez créé, auquel les machines virtuelles de l’abonnement rendront compte. Vous pouvez définir plusieurs abonnements rendant compte au même espace de travail. À défaut de définition, l’espace de travail par défaut est utilisé.

    ```powershell
    Set-AzSecurityWorkspaceSetting -Name "default" -Scope "/subscriptions/d07c0080-170c-4c24-861d-9c817742786c" -WorkspaceId"/subscriptions/d07c0080-170c-4c24-861d-9c817742786c/resourceGroups/myRg/providers/Microsoft.OperationalInsights/workspaces/myWorkspace"
    ```

1. Approvisionnez automatiquement l’installation de l’agent Log Analytics sur vos machines virtuelles Azure :
    
    ```powershell
    Set-AzContext -Subscription "d07c0080-170c-4c24-861d-9c817742786c"
    ```
    
    ```powershell
    Set-AzSecurityAutoProvisioningSetting -Name "default" -EnableAutoProvision
    ```

    > [!NOTE]
    > Nous vous recommandons d’activer l’approvisionnement automatique pour vous assurer que vos machines virtuelles Azure sont automatiquement protégées par Microsoft Defender pour le cloud.
    >

1. Facultatif : il est fortement recommandé de [définir les détails des contacts de sécurité](configure-email-notifications.md) pour les abonnements que vous intégrez, lesquels seront utilisés comme destinataires des alertes et des notifications générées par Defender pour le cloud :

    ```powershell
    Set-AzSecurityContact -Name "default1" -Email "CISO@my-org.com" -AlertAdmin -NotifyOnAlert
    ```

1. Assignez l’initiative de stratégie Defender pour le cloud par défaut :

    ```powershell
    Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
    ```

    ```powershell
    $Policy = Get-AzPolicySetDefinition | where {$_.Properties.displayName -EQ 'Azure Security Benchmark'} New-AzPolicyAssignment -Name 'ASC Default <d07c0080-170c-4c24-861d-9c817742786c>' -DisplayName 'Defender for Cloud Default <subscription ID>' -PolicySetDefinition $Policy -Scope '/subscriptions/d07c0080-170c-4c24-861d-9c817742786c'
    ```

Vous avez correctement intégré Microsoft Defender pour le cloud avec PowerShell.

Vous pouvez désormais utiliser ces cmdlets PowerShell avec des scripts d’automatisation pour itérer par programme dans les abonnements et ressources. Cela permet de gagner du temps et réduit la probabilité d’erreur humaine. Vous pouvez utiliser cette [exemple de script](https://github.com/Microsoft/Azure-Security-Center/blob/master/quickstarts/ASC-Samples.ps1) en tant que référence.




## <a name="see-also"></a>Voir aussi
Pour en savoir plus sur la façon d’utiliser PowerShell pour automatiser l’intégration de Defender pour le cloud, consultez l’article suivant :

* [Az.Security](/powershell/module/az.security)

Pour en savoir plus sur Defender pour le cloud, consultez les articles suivants :

* [Définition des stratégies de sécurité dans Microsoft Defender pour le cloud](tutorial-security-policy.md) : apprenez à configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
* [Gestion et résolution des alertes de sécurité dans Microsoft Defender pour le cloud](managing-and-responding-alerts.md) : apprenez à gérer et à résoudre les alertes de sécurité.
