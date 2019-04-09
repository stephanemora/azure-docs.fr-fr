---
title: Résoudre les erreurs avec les runbooks Azure Automation
description: Découvrez comment résoudre les problèmes avec les runbooks Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: f93f6c8891ba9f7407310a8f09387e97f5c1f578
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59267342"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Résoudre les erreurs avec les runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erreurs d’authentification avec des runbooks Azure Automation

### <a name="sign-in-failed"></a>Scénario : La connexion au compte Azure a échoué

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Add-AzureAccount` ou `Connect-AzureRmAccount`.
:

```error
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Cause :

Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide. Elle peut également se produire si le nom d’utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

#### <a name="resolution"></a>Résolution :

Pour identifier le problème, effectuez les étapes suivantes :  

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **\@**, dans le nom de la ressource d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.  
2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe stockés dans les informations d’identification Azure Automation dans votre éditeur PowerShell ISE local. Vous pouvez vérifier si le nom d’utilisateur et le mot de passe sont corrects en exécutant les applets de commande suivantes dans PowerShell ISE :  

   ```powershell
   $Cred = Get-Credential  
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred  
   #Using Azure Resource Manager  
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Si l’authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Reportez-vous au billet de blog [Authenticating to Azure using Azure Active Directory (Authentification auprès d’Azure à l’aide d’Azure Active Directory)](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer correctement le compte Azure Active Directory.  

4. Si cette erreur semble temporaire (transient), essayez d’ajouter la logique de nouvelle tentative à votre routine d’authentification pour renforcer l’authentification.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
   $LogonAttempt++
   # Logging in to Azure...
   $connectionResult = Connect-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $servicePrincipalConnection.TenantId `
      -ApplicationId $servicePrincipalConnection.ApplicationId `
      -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

   Start-Sleep -Seconds 30
   }
   ```

### <a name="unable-to-find-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Select-AzureSubscription` ou `Select-AzureRmSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

Cette erreur peut se produire si :

* Le nom de l'abonnement n'est pas valide.

* L’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré comme administrateur de l’abonnement.

#### <a name="resolution"></a>Résolution :

Procédez comme suit pour déterminer si vous vous êtes correctement authentifié auprès d’Azure et si vous avez accès à l’abonnement que vous voulez sélectionner :  

1. Pour vérifier qu'il fonctionne de façon autonome, testez votre script en dehors d’Azure Automation.
2. Vérifiez que vous exécutez l’applet de commande `Add-AzureAccount` avant d’exécuter l’applet de commande `Select-AzureSubscription`. 
3. Ajoutez `Disable-AzureRmContextAutosave –Scope Process` au début de votre runbook. Cette applet de commande permet de s'assurer que les informations d’identification s’appliquent uniquement à l’exécution du runbook actuel.
4. Si ce message d’erreur persiste, modifiez votre code en ajoutant le paramètre **AzureRmContext** après l’applet de commande `Add-AzureAccount`, puis exécutez le code.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

### <a name="auth-failed-mfa"></a>Scénario : L’authentification auprès d’Azure a échoué, car l’authentification multifacteur est activée

#### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’authentification auprès d’Azure avec votre nom d’utilisateur et votre mot de passe Azure :

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Cause :

Si l’authentification multifacteur est configurée sur votre compte Azure, vous ne pouvez pas vous authentifier auprès d’Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

#### <a name="resolution"></a>Résolution :

Pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à [Création et ajout d’un certificat pour gérer des services Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec des applets de commande Azure Resource Manager, voir la rubrique sur la [création du principal du service à l’aide du portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) et l’[authentification d’un principal du service à l’aide d’Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="common-errors-when-working-with-runbooks"></a>Erreurs courantes avec des runbooks

### <a name="child-runbook-object"></a>Le runbook enfant renvoie une erreur lorsque le flux de sortie contient des objets plutôt que de simples types de données

#### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’appel d’un runbook enfant avec le `-Wait` le flux de sortie et de commutateur contient et de l’objet :

```error
Object reference not set to an instance of an object
```

#### <a name="cause"></a>Cause :

[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) peut ne pas gérer correctement le flux de sortie s’il contient des objets.

#### <a name="resolution"></a>Résolution :

Pour y remédier, il vous est conseillé de mettre en œuvre une logique d'interrogation et d'utiliser la cmdlet [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) pour récupérer la sortie. À titre d'exemple, cette logique est définie ci-dessous.

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while((IsJobTerminalState $job.Status) -eq $false -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzureRmAutomationJob
}

$jobResults | Get-AzureRmAutomationJobOutput | Get-AzureRmAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

### <a name="get-serializationsettings"></a>Scénario : Vous constatez une erreur dans votre flux de travail concernant la méthode get_SerializationSettings

#### <a name="issue"></a>Problème

Vous constatez une erreur dans votre flux de travail pour un runbook avec le message suivant :

```error
Connect-AzureRMAccount : Method 'get_SerializationSettings' in type 
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' 
does not have an implementation.
At line:16 char:1
+ Connect-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzureRmAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzureRmAccountCommand
```

#### <a name="cause"></a>Cause :

Cette erreur est due à l'utilisation des cmdlets AzureRM et Az dans un runbook. Elle se produit lorsque vous importez `Az` avant d’importer `AzureRM`.

#### <a name="resolution"></a>Résolution :

Les cmdlets AZ et AzureRM ne peut pas être importées et utilisées dans le même runbook. Pour plus d'informations sur la prise en charge de modules Az dans Azure Automation, consultez [Prise en charge de modules Az dans Azure Automation](../az-modules.md).

### <a name="task-was-cancelled"></a>Scénario : Le runbook échoue avec l’erreur : Une tâche a été annulée

#### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
Exception: A task was canceled.
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due à l’utilisation de modules Azure obsolètes.

#### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en mettant à jour vos modules Azure vers la dernière version.

Dans votre compte Automation, cliquez sur **Modules**, puis sur **Mettre à jour les modules Azure**. La mise à jour prend environ 15 minutes, après la réexécution du runbook qui a échoué. Pour en savoir plus sur la mise à jour de vos modules, consultez [Mettre à jour des modules Azure dans Azure Automation](../automation-update-azure-modules.md).

### <a name="runbook-auth-failure"></a>Scénario : Les runbooks échouent lors du traitement de plusieurs abonnements

#### <a name="issue"></a>Problème

Lors de l’exécution de runbooks avec `Start-AzureRmAutomationRunbook`, le runbook ne parvient pas à gérer les ressources Azure.

#### <a name="cause"></a>Cause :

Le runbook n’utilise pas le contexte approprié lors de l’exécution.

#### <a name="resolution"></a>Résolution :

Si vous utilisez plusieurs abonnements, le contexte de l’abonnement peut être perdu durant l’appel des runbooks. Pour s’assurer que le contexte de l’abonnement est passé aux runbooks, ajoutez le paramètre `AzureRmContext` à l’applet de commande et passez-lui le contexte. Il est également recommandé d’utiliser l’applet de commande `Disable-AzureRmContextAutosave` avec l’étendue **Processus** pour avoir la garantie que les informations d’identification dont vous vous servez sont uniquement utilisées pour le runbook actuel.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRmContext $AzureContext `
    –Parameters $params –wait
```

### <a name="not-recognized-as-cmdlet"></a>Scénario : Le runbook échoue en raison d’une cmdlet manquante

#### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Cause :

Cette erreur peut se produire pour l'une des raisons suivantes :

1. Le module contenant l’applet de commande n’est pas importé dans le compte Automation
2. Le module contenant l’applet de commande est importé, mais il est obsolète

#### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en effectuant une des tâches suivantes :

Si le module est un module Azure, consultez [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md) pour découvrir comment mettre à jour vos modules dans votre compte Automation.

S’il s’agit d’un module distinct, assurez-vous que le module est importé dans votre compte Automation.

### <a name="job-attempted-3-times"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais n’a pas pu démarrer

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```error
The job was tried three times but it failed
```

#### <a name="cause"></a>Cause :

Cette erreur se produit en raison d’un des problèmes suivants :

1. Limite de mémoire. Les limites documentées sur la quantité de mémoire qui est allouée à un bac à sable se trouvent à la section [Limites du service Automation](../../azure-subscription-service-limits.md#automation-limits). Un travail peut échouer s’il utilise plus de 400 Mo de mémoire.

2. Sockets réseau. Les bacs à sable Azure sont limités à 1 000 sockets réseau simultanés, comme décrit dans [Limites du service Automation](../../azure-subscription-service-limits.md#automation-limits).

3. Module incompatible. Cette erreur peut se produire si les dépendances de module ne sont pas correctes. Dans ce cas, votre runbook retourne généralement un message « Commande introuvable » ou « Impossible de lier le paramètre ».

4. Votre runbook a tenté d’appeler un fichier exécutable ou de sous-processus dans un runbook qui s’exécute dans un bac à sable Azure. Ce scénario n’est pas pris en charge dans les bacs à sable Azure.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Les méthodes recommandées pour travailler dans la limite de mémoire consistent à fractionner la charge de travail en plusieurs runbooks, à ne pas traiter trop de données en mémoire, à ne pas écrire de sortie inutile depuis vos runbooks ou à prendre en compte le nombre de points de contrôle que vous écrivez dans vos runbooks de flux de travail PowerShell. Vous pouvez utiliser la méthode clear, telle que `$myVar.clear()`, pour effacer la variable, ainsi que `[GC]::Collect()` pour exécuter immédiatement le nettoyage de la mémoire. Cela permet de réduire l’empreinte mémoire de votre runbook pendant l’exécution.

* Mettez à jour vos modules Azure en suivant les étapes de l’article [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).  

* Une autre solution consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par les limites de mémoire et réseau associées aux bacs à sable Azure.

* S'il vous faut appeler un processus (par exemple, .exe ou subprocess.call) dans un runbook, vous devez exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md).

### <a name="fails-deserialized-object"></a>Scénario : Le runbook échoue à cause d’un objet désérialisé

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

#### <a name="cause"></a>Cause :

Si votre runbook est un workflow PowerShell, il stocke les objets complexes dans un format désérialisé afin de conserver l’état du runbook si le workflow est suspendu.

#### <a name="resolution"></a>Résolution :

Chacune des trois solutions suivantes résout ce problème :

1. Si vous redirigez des objets complexes d’une applet de commande vers une autre, wrappez ces deux applets de commande dans un bloc InlineScript.
2. Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.
3. Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.

### <a name="runbook-fails"></a>Scénario : Mon runbook échoue, mais fonctionne lorsqu'il est exécuté localement.

#### <a name="issue"></a>Problème

Votre script échoue lorsqu'il est exécuté en tant que runbook, mais fonctionne lorsqu'il est exécuté localement.

#### <a name="cause"></a>Cause :

Votre script peut échouer lorsqu'il est exécuté en tant que runbook pour l’une des raisons suivantes :

1. Problèmes d’authentification
2. Des modules requis n'ont pas été importés ou sont obsolètes.
3. Votre script peut demander une interaction de l'utilisateur.
4. Certains modules font des hypothèses sur les bibliothèques présentes sur les ordinateurs Windows. Ces bibliothèques peuvent ne pas être présentes dans un bac à sable.
5. Certains modules s’appuient sur une version de .NET différente de la version disponible dans le bac à sable.

#### <a name="resolution"></a>Résolution :

Chacune des solutions suivantes peut résoudre ce problème :

1. Vérifiez que vous vous êtes correctement [authentifié auprès d’Azure](../manage-runas-account.md).
2. Assurez-vous que vos [modules Azure ont été importés et sont à jour](../automation-update-azure-modules.md).
3. Vérifiez qu’aucune de vos cmdlets ne demande d’informations. Ce comportement n’est pas pris en charge dans les runbooks.
4. Vérifiez si un élément de votre module dépend d'un élément non inclus dans ce module.
5. Les bacs à sable Azure utilisent .NET Framework 4.7.2, et dès lors, si un module utilise une version ultérieure, il ne fonctionnera pas. Si tel est le cas, utilisez un [Runbook Worker hybride](../automation-hybrid-runbook-worker.md)

Si aucune de ces solutions ne permet de résoudre le problème, consultez les [journaux relatifs de travail](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) afin de déterminer la raison pour laquelle votre runbook échoue.

### <a name="quota-exceeded"></a>Scénario : Le travail du Runbook a échoué, car le quota alloué a été dépassé

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Cause :

Cette erreur se produit quand l’exécution du travail dépasse le quota gratuit de 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks ou la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Résolution :

Si vous voulez utiliser plus de 500 minutes de traitement par mois, vous devez changer votre abonnement et le faire passer du niveau Gratuit au niveau De base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :  

1. Connectez-vous à votre abonnement Azure :  
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau  
3. Cliquez sur **Paramètres** > **Tarifs**.
4. Cliquez sur **Activer** en bas de page pour actualiser votre compte au niveau **De base**.

### <a name="cmdlet-not-recognized"></a>Scénario : La cmdlet n’est pas reconnue lors de l’exécution d’un runbook

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Cause :

Cette erreur survient quand le moteur PowerShell ne trouve pas la cmdlet que vous utilisez dans votre runbook. Cette erreur peut être dû au fait que le module contenant la cmdlet n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que la cmdlet existe déjà dans un autre module et Automation ne peut pas résoudre le nom.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :  

* Vérifiez que vous avez correctement entré le nom de la cmdlet.  
* Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande dans la bibliothèque ou exécutez `Get-Command <CommandName>`. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez l’applet de commande dans le canevas et veillez à utiliser un paramètre valide défini dans votre runbook.  
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **Nom_module\Nom_applet_de_commande**.  
* Si vous exécutez le runbook localement dans un groupe de Workers hybrides, assurez-vous que le module et l’applet de commande sont installés sur la machine qui héberge le Worker hybride.

### <a name="long-running-runbook"></a>Scénario : échec de l’exécution d’un long runbook

#### <a name="issue"></a>Problème

Votre runbook affiche l’état **Stopped** (Arrêté) au bout de trois heures d’exécution. Vous pouvez également recevoir l’erreur :

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Il s’agit du comportement par défaut dans les bacs à sable Azure en raison de la supervision de la répartition de charge équilibrée des processus au sein d’Azure Automation. S’il s’exécute pendant plus de trois heures, la répartition de charge équilibrée arrête automatiquement un runbook. L’état d’un runbook qui dépasse la limite de temps de la répartition de charge équilibrée diffère selon le type du runbook. Les runbooks PowerShell et Python sont mis à l’état **Stopped** (Arrêté). Les runbooks PowerShell Workflow sont mis à l’état **Failed** (Échec).

#### <a name="cause"></a>Cause :

Le runbook s’est exécuté au-delà de la limite de trois heures autorisée par la répartition de charge équilibrée dans un bac à sable Azure.

#### <a name="resolution"></a>Résolution :

Une solution recommandée consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md).

Les Workers hybrides ne sont pas restreints par la limite d’exécution de runbook de trois heures autorisée par la [répartition de charge équilibrée](../automation-runbook-execution.md#fair-share). Les runbooks s’exécutant sur eux doivent néanmoins être développés pour prendre en charge les comportements de redémarrage s’il y a un problème inattendu avec l’infrastructure locale.

Une autre option consiste à optimiser le runbook en créant des [runbooks enfants](../automation-child-runbooks.md). Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un runbook enfant. Chacun de ces runbooks enfants s’exécute en parallèle dans des processus distincts. Ce comportement réduit la quantité totale de temps pour l’exécution du runbook parent.

Applets de commande PowerShell prenant en charge le scénario avec des runbooks enfants :

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) : cette applet de commande vous permet de démarrer un runbook et de lui passer des paramètres.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) : si des opérations doivent être effectuées à la fin de l’exécution du runbook enfant, cette applet de commande vous permet de vérifier l’état du travail de chaque enfant.

### <a name="expired webhook"></a>Scénario : État de la version : 400 Requête incorrecte lors de l’appel d’un webhook

#### <a name="issue"></a>Problème

Lorsque vous essayez d’appeler un webhook pour un runbook Azure Automation, vous recevez l’erreur suivante :

```error
400 Bad Request : This webhook has expired or is disabled
```

#### <a name="cause"></a>Cause :

Le webhook que vous tentez d’appeler est désactivé ou a expiré.

#### <a name="resolution"></a>Résolution :

Si le webhook est désactivé, vous pouvez réactiver le webhook via le Portail Azure. Lorsque le webhook a expiré, il doit être supprimé et recréé. Vous pouvez uniquement [renouveler un webhook](../automation-webhooks.md#renew-webhook) s’il n’a pas déjà expiré.

### <a name="429"></a>Scénario : 429 : Le taux de requêtes est actuellement trop grand. Réessayez

#### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lors de l’exécution de l’applet de commande `Get-AzureRmAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

#### <a name="cause"></a>Cause :

Cette erreur peut se produire lors de la récupération d’une sortie de tâche à partir d’un runbook comportant de nombreux [flux détaillés](../automation-runbook-output-and-messages.md#verbose-stream).

#### <a name="resolution"></a>Résolution :

Il existe deux façons de résoudre cette erreur :

* Modifiez le runbook et réduisez le nombre de flux de tâches émis.
* Réduisez le nombre de flux à récupérer lors de l’exécution de la cmdlet. Pour suivre ce comportement, vous pouvez spécifier le paramètre `-Stream Output` sur l’applet de commande `Get-AzureRmAutomationJobOutput` afin de ne récupérer que les flux de sortie. 

### <a name="cannot-invoke-method"></a>Scénario : Tâche PowerShell échoue avec l’erreur : Impossible d’appeler la méthode

#### <a name="issue"></a>Problème

Quand vous démarrez un Job de PowerShell dans un runbook en cours d’exécution dans Azure, le message d’erreur suivant s’affiche :

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

#### <a name="cause"></a>Cause :

Cette erreur peut se produire lorsque vous démarrez un travail dans un runbook est exécuté dans Azure de PowerShell. Ce comportement peut se produire, car les procédures opérationnelles a été exécuté dans Azure bac à sable ne peut-être pas s’exécuter en le [mode langage complet](/powershell/module/microsoft.powershell.core/about/about_language_modes)).

#### <a name="resolution"></a>Résolution :

Il existe deux façons de résoudre cette erreur :

* Au lieu d’utiliser `Start-Job`, utilisez `Start-AzureRmAutomationRunbook` pour démarrer un runbook
* Si votre runbook possède ce message d’erreur, exécutez-le sur un Runbook Worker hybride

Pour en savoir plus sur ce comportement et d’autres comportements de Runbooks Azure Automation, consultez [Runbook comportement](../automation-runbook-execution.md#runbook-behavior).

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
