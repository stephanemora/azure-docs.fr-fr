---
title: Découvrir le workflow PowerShell pour Azure Automation
description: Cet article est une rapide leçon expliquant aux auteurs familiarisés avec PowerShell les différences spécifiques entre PowerShell et un workflow PowerShell et les concepts applicables aux runbooks Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457533"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Découvrir les principaux concepts de workflow Windows PowerShell pour les runbooks Automation

Les Runbooks d'Azure Automation sont implémentés en tant que workflows Windows PowerShell.  Un workflow Windows PowerShell est similaire à un script Windows PowerShell, mais il présente des différences significatives qui peuvent être déconcertantes pour un nouvel utilisateur.  Bien que cet article soit destiné à vous aider à écrire des runbooks à l’aide de workflow PowerShell, nous vous recommandons d’écrire des runbooks à l’aide de PowerShell, sauf si vous avez besoin de points de contrôle.  Il existe plusieurs différences de syntaxe lors de la création de runbooks de workflow PowerShell et ces différences nécessitent un peu plus de travail pour l’écriture de workflows efficaces.

Un workflow est une séquence d’étapes programmées et connectées qui effectuent des tâches de longue durée ou requièrent la coordination de plusieurs étapes sur plusieurs appareils ou nœuds gérés. Les avantages d'un workflow par rapport à un script normal incluent la possibilité d'exécuter simultanément une action sur plusieurs appareils et de récupérer automatiquement après une défaillance. Un workflow Windows PowerShell est un script Windows PowerShell qui utilise Windows Workflow Foundation. Le workflow est écrit avec la syntaxe Windows PowerShell et lancé par Windows PowerShell, mais il est traité par Windows Workflow Foundation.

Plus d'informations sur les rubriques de cet article, consultez [Présentation du workflow Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="basic-structure-of-a-workflow"></a>Structure de base d'un workflow

La première étape de la conversion d'un script PowerShell en workflow PowerShell consiste à y intégrer le mot clé `Workflow`.  Un workflow commence par le mot clé `Workflow` suivi du corps du script entre accolades. Le nom du workflow suit le mot clé `Workflow`, comme illustré dans la syntaxe suivante :

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Le nom du workflow doit correspondre au nom du Runbook Automation. Si le Runbook est importé, le nom de fichier doit correspondre au nom du workflow et se terminer par *.ps1*.

Pour ajouter des paramètres au workflow, utilisez le mot clé `Param`, comme dans un script.

## <a name="code-changes"></a>Modifications du code

Le code d'un workflow PowerShell est quasiment identique au code d'un script PowerShell, à l'exception de quelques modifications importantes.  Les sections suivantes décrivent les modifications que vous devez apporter à un script PowerShell pour l’exécuter dans un workflow.

### <a name="activities"></a>Activités

Une activité est une tâche spécifique dans un workflow. Tout comme un script se compose d'une ou de plusieurs commandes, un workflow se compose d'une ou de plusieurs activités exécutées en séquence. Le workflow Windows PowerShell convertit automatiquement la plupart des applets de commande Windows PowerShell en activités lors de son exécution. Lorsque vous spécifiez une de ces applets de commande dans votre Runbook, l’activité correspondante est exécutée par Windows Workflow Foundation. Pour ces applets de commande sans activité correspondante, le workflow Windows PowerShell exécute automatiquement l'applet de commande au sein d'une activité [InlineScript](#inlinescript) . Il existe un ensemble d'applets de commande qui sont exclues et ne peuvent pas être utilisées dans un workflow, à moins que vous ne les incluiez explicitement dans un bloc InlineScript. Pour plus d'informations sur ces concepts, consultez [Utilisation des activités dans les workflows de script](https://technet.microsoft.com/library/jj574194.aspx).

Les activités de workflow partagent un ensemble de paramètres communs pour configurer leur opération. Pour plus d’informations sur les paramètres communs de flux de travail, consultez [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Paramètres positionnels

Vous ne pouvez pas utiliser les paramètres positionnels avec les activités et les applets de commande dans un workflow.  Cela signifie que vous devez utiliser des noms de paramètres.

Par exemple, utilisez le code suivant pour afficher tous les services en cours d'exécution.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Si vous essayez d’exécuter ce même code dans un workflow, vous recevez un message de type « le jeu de paramètres est introuvable avec les paramètres nommés spécifiés ».  Pour corriger ce problème, entrez le nom du paramètre comme dans l’exemple suivant.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Objets désérialisés

Dans les workflows, les objets sont désérialisés.  Cela signifie que leurs propriétés restent disponibles, mais pas leurs méthodes.  Par exemple, utilisez le code PowerShell suivant qui arrête un service à l'aide de la méthode Stop de l'objet Service.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Si vous essayez d’exécuter ce code dans un workflow, vous obtenez une erreur indiquant que « l’appel de méthode n’est pas pris en charge dans un workflow Windows PowerShell ».

Une option consiste à intégrer ces deux lignes de code dans un bloc [InlineScript](#inlinescript), où $Service représente un objet de service au sein du bloc.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Une autre option consiste à utiliser une autre applet de commande qui exécute les mêmes fonctionnalités que la méthode, si celle-ci est disponible.  Dans notre exemple, l’applet de commande Stop-Service fournit les mêmes fonctionnalités que la méthode Stop, et vous pouvez utiliser les éléments suivants pour un workflow.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

L'activité `InlineScript` est utile lorsque vous devez exécuter une ou plusieurs commandes en tant que script PowerShell standard plutôt que workflow PowerShell.  Alors que les commandes d'un workflow sont envoyées à Windows Workflow Foundation pour être traitées, les commandes d'un bloc InlineScript sont traitées par Windows PowerShell.

InlineScript utilise la syntaxe illustrée ci-dessous.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Vous pouvez renvoyer la sortie d'un bloc InlineScript en l'affectant à une variable. L'exemple suivant arrête un service puis renvoie le nom du service.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Vous pouvez passer des valeurs dans un bloc InlineScript, mais vous devez utiliser le modificateur de portée **$Using** .  L'exemple suivant est identique à l'exemple précédent, sauf que le nom du service est fourni par une variable.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Même si les activités InlineScript peuvent être critiques dans certains workflows, elles ne prennent pas en charge les constructions de workflow et doivent être utilisées uniquement lorsque cela est nécessaire pour les raisons suivantes :

* Vous ne pouvez pas utiliser de [points de contrôle](#checkpoints) à l’intérieur d’un bloc InlineScript. Si une défaillance se produit dans le bloc, l'exécution doit reprendre depuis le début du bloc.
* Vous ne pouvez pas effectuer [d’exécution en parallèle](#parallel-processing) à l’intérieur d’un bloc InlineScriptBlock.
* InlineScript affecte l'extensibilité du workflow puisque l'activité maintient la session Windows PowerShell pendant toute la durée du bloc InlineScript.

Pour plus d’informations sur l’utilisation d’InlineScript, consultez [Exécution des commandes Windows PowerShell dans un workflow](https://technet.microsoft.com/library/jj574197.aspx) et [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Traitement parallèle

L'un des avantages des workflows Windows PowerShell est la possibilité d'exécuter un ensemble de commandes en parallèle, et non séquentiellement comme avec un script classique.

Vous pouvez utiliser le mot clé `Parallel` pour créer un bloc de script avec plusieurs commandes qui s'exécutent simultanément. Pour ce faire, utilisez la syntaxe illustrée ci-dessous. Dans ce cas, Activity1 et Activity2 démarrent en même temps. Activity3 démarre uniquement quand Activity1 et Activity2 sont toutes deux terminées.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Par exemple, considérez les commandes PowerShell suivantes qui copier plusieurs fichiers vers une destination sur le réseau.  Ces commandes sont exécutées séquentiellement afin que le fichier termine la copie avant de démarrer la suivante.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Le workflow suivant exécute ces commandes en parallèle afin qu'elles commencent toutes la copie en même temps.  Le message confirmant la fin de l’opération apparaît uniquement une fois toutes les copies effectuées.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Vous pouvez utiliser la construction `ForEach -Parallel` pour traiter simultanément les commandes de chaque élément d'une collection. Les éléments de la collection sont traités en parallèle, tandis que les commandes du bloc de script sont exécutées séquentiellement. Pour ce faire, utilisez la syntaxe illustrée ci-dessous. Dans ce cas, Activity1 démarre en même temps pour tous les éléments de la collection. Pour chaque élément, Activity2 démarre une fois Activity1 terminée. Activity3 démarre uniquement quand Activity1 et Activity2 sont toutes deux terminées pour tous les éléments. Nous utilisons le paramètre `ThrottleLimit` pour limiter le parallélisme. Une valeur `ThrottleLimit` trop élevée peut entraîner des problèmes. La valeur idéale pour le paramètre `ThrottleLimit` dépend de nombreux facteurs dans votre environnement. Vous devez commencer avec une valeur faible, puis essayer différentes valeurs croissantes jusqu’à ce que vous en trouviez une qui fonctionne pour votre situation spécifique.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

L'exemple suivant est similaire à l'exemple précédent concernant la copie de fichiers en parallèle.  Dans ce cas, un message s'affiche pour chaque fichier après la copie.  Le message confirmant la fin de l'opération apparaît uniquement une fois toutes les copies effectuées.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Nous vous déconseillons d'exécuter des Runbooks enfants en parallèle car les résultats obtenus ne sont pas fiables. Parfois, la sortie du runbook enfant n’apparaît pas, et les paramètres d’un runbook enfant peuvent affecter les autres runbooks enfants parallèles. Les variables comme $VerbosePreference, $WarningPreference et d’autres, ne peuvent pas être propagées dans les runbooks enfants. De plus, si le runbook enfant modifie ces valeurs, celles-ci peuvent ne pas être correctement restaurées après l’appel.

## <a name="checkpoints"></a>Points de contrôle

Un *point de contrôle* est un instantané de l'état actuel du workflow qui inclut la valeur actuelle des variables et toute sortie générée à ce stade. Si un flux de travail se termine par erreur ou est suspendu, il démarrera à la prochaine exécution à partir de son dernier point de contrôle et non depuis le début du worfklow.  Vous pouvez définir un point de contrôle dans un workflow avec l'activité `Checkpoint-Workflow`. Azure Automation dispose d’une fonctionnalité appelée [répartition équilibrée](automation-runbook-execution.md#fair-share), grâce à laquelle tout runbook s’exécutant depuis plus de 3 heures est déchargé pour permettre l’exécution des autres runbooks. Ensuite, le runbook déchargé est rechargé, puis reprend son exécution à partir du dernier point de contrôle enregistré dans le runbook. Pour que le runbook s’exécute jusqu’au bout, vous devez ajouter des points de contrôle à des intervalles inférieurs à 3 heures. Si un point de contrôle est ajouté lors de chaque exécution, et si le runbook est évincé au bout de 3 heures en raison d’une erreur, le runbook sera exécuté indéfiniment.

Dans l'exemple de code suivant, une exception se produit après qu'Activity2 a provoqué l'arrêt du workflow. Lorsque le workflow est réexécuté, il commence par exécuter Activity2, juste après le dernier point de contrôle défini.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Vous devez définir les points de contrôle d'un workflow après les activités qui peuvent être sujettes à une exception et qui ne doivent pas être réexécutées à la reprise du workflow. Par exemple, votre workflow peut créer une machine virtuelle. Vous pouvez définir un point de contrôle avant et après les commandes de création de la machine virtuelle. En cas d'échec de la création, les commandes doivent être répétées si le workflow est redémarré. Si le workflow échoue après que la création a réussi, la machine virtuelle n’est pas recréée à la reprise du workflow.

L'exemple suivant copie plusieurs fichiers vers un emplacement réseau et définit un point de contrôle après chaque fichier.  Si l’emplacement réseau est perdu, le workflow s’arrête en générant une erreur.  Lorsqu’il est relancé, il reprend au dernier point de contrôle, ce qui signifie que seuls les fichiers qui ont déjà été copiés sont ignorés.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Comme les informations d'identification du nom d'utilisateur ne sont pas conservées après l'appel de l'activité [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) ou après le dernier point de contrôle, vous devez définir les informations d'identification comme « null », puis les récupérer à nouveau à partir du magasin de ressources après l'activité `Suspend-Workflow` ou après l'appel du point de contrôle.  Sinon, le message d’erreur suivant peut s'afficher : `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Le même code ci-dessous montre comment traiter ce problème dans vos Runbooks de workflow PowerShell.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Pour les runbooks PowerShell non graphiques, `Add-AzAccount` et `Add-AzureRMAccount` sont des alias de [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Vous pouvez utiliser ces cmdlets ou [mettre à jour vos modules](automation-update-azure-modules.md) dans votre compte Automation vers les dernières versions. Il est possible que vous deviez mettre à jour vos modules, même si vous venez de créer un compte Automation.


Cette procédure n’est pas nécessaire si vous vous authentifiez à l’aide d’un compte d’identification configuré avec un service principal.

Pour plus d'informations sur les points de contrôle, consultez [Ajout de points de contrôle à un workflow de script](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des Runbooks de workflow PowerShell, consultez [Mon premier Runbook PowerShell Workflow](automation-first-runbook-textual.md)

