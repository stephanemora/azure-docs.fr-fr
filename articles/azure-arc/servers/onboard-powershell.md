---
title: Connecter des machines hybrides à Azure à l’aide de PowerShell
description: Cet article explique comment installer l’agent et connecter une machine à Azure à l’aide de serveurs compatibles avec Azure Arc en utilisant PowerShell.
ms.date: 10/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0755846ef02377edade98b69e478908a111ab247
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92901537"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Connecter des machines hybrides à Azure à l’aide de PowerShell

Vous pouvez activer des serveurs compatibles avec Azure Arc pour au moins une machine Windows ou Linux dans votre environnement en effectuant une série d’étapes manuelles. Vous pouvez également utiliser la cmdlet PowerShell [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) pour télécharger l’agent Connected Machine, installer l’agent et inscrire l’ordinateur auprès d’Azure Arc. La cmdlet télécharge le package Windows Installer de l’agent Windows à partir du centre de téléchargement de Microsoft et le package de l’agent Linux à partir du référentiel de packages de Microsoft.

Cette méthode nécessite que vous disposiez d’autorisations d’administrateur sur la machine pour installer et configurer l’agent. Sur Linux, vous utilisez le compte root et, sur Windows, vous êtes membre du groupe Administrateurs local. Vous pouvez effectuer ce processus de manière interactive ou à distance sur un serveur Windows à l’aide de la [communication à distance PowerShell](/powershell/scripting/learn/ps101/08-powershell-remoting).

Avant de commencer, veillez à consulter les [conditions préalables](agent-overview.md#prerequisites) et vérifiez que votre abonnement et vos ressources répondent aux exigences. Pour plus d’informations sur les régions prises en charge et d’autres considérations liées, consultez [Régions Azure prises en charge](overview.md#supported-regions).

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

- Machine avec Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/powershell/azure/).

Avant d’utiliser Azure PowerShell pour gérer les extensions de machine virtuelle sur votre serveur hybride géré par des serveurs Arc, vous devez installer le module `Az.ConnectedMachine`. Exécutez la commande suivante sur votre serveur Arc :

```powershell
Install-Module -Name Az.ConnectedMachine
```

Une fois l’installation terminée, le message suivant est retourné :

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installer l’agent et le connecter à Azure

1. Ouvrez une console PowerShell avec des privilèges élevés.

2. Connectez-vous à Azure en exécutant la commande `Connect-AzAccount`.

3. Pour installer l’agent Connected Machine, utilisez `Connect-AzConnectedMachine` avec les paramètres `-Name`, `-ResourceGroupName`et `-Location`. Utilisez le paramètre `-SubscriptionId` pour remplacer l’abonnement par défaut à la suite du contexte Azure créé après la connexion. Exécutez l’une des commandes suivantes :

    * Pour installer l’agent Connected Machine sur la machine cible pouvant directement communiquer avec Azure, exécutez :

        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
        ```
    
    * Pour installer l’agent Connected Machine sur la machine cible communiquant directement via un serveur proxy, exécutez :
        
        ```azurepowershell
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
        ```

Si le démarrage de l’agent échoue une fois l’installation terminée, recherchez des informations détaillées sur l’erreur dans les journaux. Sous Windows, à l’emplacement *%ProgramData%\AzureConnectedMachineAgent\Log\himds.log* et sous Linux, à l’emplacement */var/opt/azcmagent/log/himds.log*.

## <a name="install-and-connect-using-powershell-remoting"></a>Installer et se connecter à l’aide de la communication à distance PowerShell

Procédez comme suit pour configurer au moins un serveur Windows avec des serveurs compatibles avec Azure Arc. La communication à distance PowerShell doit être activée sur chaque machine distante. Utilisez l’applet de commande `Enable-PSRemoting` pour activer la communication à distance PowerShell.

1. Ouvrez une console PowerShell en tant qu’administrateur.

2. Connectez-vous à Azure en exécutant la commande `Connect-AzAccount`.

3. Pour installer l’agent Connected Machine, utilisez `Connect-AzConnectedMachine` avec les paramètres `-Name`, `-ResourceGroupName`et `-Location`. Utilisez le paramètre `-SubscriptionId` pour remplacer l’abonnement par défaut à la suite du contexte Azure créé après la connexion.

    * Pour installer l’agent Connected Machine sur la machine cible pouvant directement communiquer avec Azure, exécutez la commande suivante :
    
        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```
    
    * Pour installer l’agent Connected Machine sur plusieurs ordinateurs distants en même temps, ajoutez une liste de noms d’ordinateurs distants séparés par une virgule.

        ```azurepowershell
        $session = Connect-PSSession -ComputerName myMachineName1, myMachineName2, myMachineName3
        Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
        ```

    L’exemple suivant illustre les résultats de la commande ciblant un seul ordinateur :
    
    ```azurepowershell
    time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
    time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
    time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
    time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941
    
    Name           Location OSName   Status     ProvisioningState
    ----           -------- ------   ------     -----------------
    myMachineName  eastus   windows  Connected  Succeeded
    ```

## <a name="verify-the-connection-with-azure-arc"></a>Vérifier la connexion avec Azure Arc

Une fois que vous avez installé l’agent et que vous l’avez configuré pour qu’il se connecte à Azure Arc enabled servers, accédez au portail Azure pour vérifier que le serveur s’est correctement connecté. Affichez vos machines dans le [portail Azure](https://portal.azure.com).

![Connexion au serveur réussie](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur la résolution des problèmes, consultez le [guide Résoudre les problèmes de l’agent Connected Machine](troubleshoot-agent-onboard.md).

* Apprenez à gérer votre machine à l’aide d’[Azure Policy](../../governance/policy/overview.md), par exemple pour la [configuration invité](../../governance/policy/concepts/guest-configuration.md) des machines virtuelles, pour vérifier que l’ordinateur crée des rapports sur l’espace de travail Log Analytics prévu, pour activer l’analyse d’[Azure Monitor sur des machines virtuelles](../../azure-monitor/insights/vminsights-enable-policy.md) et bien plus encore.

* En savoir plus sur [l’agent Log Analytics](../../azure-monitor/platform/log-analytics-agent.md). L’agent Log Analytics pour Windows et Linux est nécessaire quand vous souhaitez collecter des données de supervision du système d’exploitation et de la charge de travail, gérer les ressources à l’aide de runbooks Automation ou de fonctionnalités comme Update Management, ou utiliser d’autres services Azure comme [Azure Security Center](../../security-center/security-center-introduction.md).