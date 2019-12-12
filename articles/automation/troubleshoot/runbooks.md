---
title: Résoudre les erreurs avec les runbooks Azure Automation
description: Découvrez comment résoudre les problèmes avec les runbooks Azure Automation
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 84a17cb4468f60abf2463e6aa3ca331466aad247
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850140"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Résoudre les erreurs avec les runbooks

Lorsque des erreurs surviennent lors de l’exécution de runbooks dans Azure Automation, vous pouvez utiliser les étapes suivantes pour diagnostiquer le problème.

1. **Veillez à ce que le script de votre runbook s’exécute avec succès sur votre ordinateur local :**  Reportez-vous à la [documentation de PowerShell](/powershell/scripting/overview) ou à la [documentation de Python](https://docs.python.org/3/) pour obtenir des modules de référence et d’entraînement du langage.

   L’exécution en local de votre script peut détecter et résoudre les erreurs courantes, telles que :

   - **Modules manquants**
   - **Erreurs de syntaxe**
   - **Erreurs logiques**

2. **Examinez les [flux d’erreur](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) du runbook** pour voir s’ils contiennent des messages spécifiques et comparez-les aux erreurs ci-dessous.

3. **Veillez à ce que vos nœuds et votre espace de travail Automation disposent des modules nécessaires :** Si votre runbook importe des modules, assurez-vous qu’ils sont disponibles dans votre compte Automation à l’aide des étapes répertoriées dans [Importer des modules](../shared-resources/modules.md#import-modules). Mettez à jour vos modules vers la dernière version disponible en suivant les instructions fournies dans la section [Mise à jour des modules Azure dans Azure Automation](..//automation-update-azure-modules.md). Pour plus d'informations sur la résolution des problèmes, consultez [Résoudre les problèmes liés aux modules](shared-resources.md#modules).

En cas d’échec inattendu ou d’interruption du runbook :

* [Vérifier les états du travail](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) définit les états du runbook et certaines causes possibles.
* [Ajoutez une sortie supplémentaire](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) au runbook pour identifier ce qui se passe avant l'interruption du runbook.
* [Gérez les exceptions](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) qui sont levées par votre travail.

## <a name="login-azurerm"></a>Scénario : Exécuter Login-AzureRmAccount pour se connecter

### <a name="issue"></a>Problème

Lors de l’exécution d’un runbook, vous recevez l’erreur suivante :

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Cause :

Cette erreur peut se produire lorsque vous n’utilisez pas un compte d’identification ou quand le compte d’identification a expiré. Consultez [Gérer les comptes d'identification Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Cette erreur a deux causes principales :

* Différentes versions des modules AzureRM.
* Vous essayez d’accéder à des ressources dans un abonnement distinct.

### <a name="resolution"></a>Résolution :

Si vous recevez cette erreur après avoir mis à jour un module AzureRM, vous devez mettre à jour tous vos modules AzureRM avec la même version.

Si vous essayez d’accéder à des ressources dans un autre abonnement, vous pouvez suivre les étapes ci-dessous pour configurer les autorisations.

1. Accédez au compte Automation exécuté en tant que compte, puis copiez l’ID d’application et l’empreinte.
  ![Copier l’ID d’application et l’empreinte](../media/troubleshoot-runbooks/collect-app-id.png)
1. Accédez du contrôle d’accès de l’abonnement où le compte Automation n’est pas hébergé, puis ajoutez une nouvelle attribution de rôle.
  ![Contrôle d’accès](../media/troubleshoot-runbooks/access-control.png)
1. Ajoutez l’ID d’application que vous avez collecté à l’étape précédente. Sélectionnez des autorisations Contributeur.
   ![Ajouter une attribution de rôle](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copiez le nom de l’abonnement que vous allez utiliser à la prochaine étape.
1. Vous pouvez maintenant utiliser le code de runbook suivant pour tester les autorisations de votre compte Automation sur l’autre abonnement.

    Remplacez \<CertificateThumbprint\> par la valeur que vous avez copiée à l’étape 1, et \<SubscriptionName\> par la valeur que vous avez copiée à l’étape 4.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzureRmSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzureRmSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzureRmContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="unable-to-find-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Select-AzureSubscription` ou `Select-AzureRmSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

Cette erreur peut se produire si :

* Le nom de l'abonnement n'est pas valide.

* L’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré comme administrateur de l’abonnement.

### <a name="resolution"></a>Résolution :

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

## <a name="auth-failed-mfa"></a>Scénario : L’authentification auprès d’Azure a échoué, car l’authentification multifacteur est activée

### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’authentification auprès d’Azure avec votre nom d’utilisateur et votre mot de passe Azure :

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Cause :

Si l’authentification multifacteur est configurée sur votre compte Azure, vous ne pouvez pas vous authentifier auprès d’Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

### <a name="resolution"></a>Résolution :

Pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à [Création et ajout d’un certificat pour gérer des services Azure.](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec des applets de commande Azure Resource Manager, voir la rubrique sur la [création du principal du service à l’aide du portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) et l’[authentification d’un principal du service à l’aide d’Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="get-serializationsettings"></a>Scénario : Vous constatez une erreur dans votre flux de travail concernant la méthode get_SerializationSettings

### <a name="issue"></a>Problème

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

### <a name="cause"></a>Cause :

Cette erreur est due à l'utilisation des cmdlets AzureRM et Az dans un runbook. Elle se produit lorsque vous importez `Az` avant d’importer `AzureRM`.

### <a name="resolution"></a>Résolution :

Les cmdlets AZ et AzureRM ne peut pas être importées et utilisées dans le même runbook. Pour plus d'informations sur la prise en charge de modules Az dans Azure Automation, consultez [Prise en charge de modules Az dans Azure Automation](../az-modules.md).

## <a name="task-was-cancelled"></a>Scénario : Le runbook échoue avec l’erreur : Une tâche a été annulée

### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Cause :

Cette erreur peut être due à l’utilisation de modules Azure obsolètes.

### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en mettant à jour vos modules Azure vers la dernière version.

Dans votre compte Automation, cliquez sur **Modules**, puis sur **Mettre à jour les modules Azure**. La mise à jour prend environ 15 minutes, après la réexécution du runbook qui a échoué. Pour en savoir plus sur la mise à jour de vos modules, consultez [Mettre à jour des modules Azure dans Azure Automation](../automation-update-azure-modules.md).

## <a name="runbook-auth-failure"></a>Scénario : Les runbooks échouent lors du traitement de plusieurs abonnements

### <a name="issue"></a>Problème

Lors de l’exécution de runbooks, le runbook ne parvient pas à gérer les ressources Azure.

### <a name="cause"></a>Cause :

Le runbook n’utilise pas le contexte approprié lors de l’exécution.

### <a name="resolution"></a>Résolution :

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

Pour plus d’informations, consultez [Utilisation de plusieurs abonnements](../automation-runbook-execution.md#working-with-multiple-subscriptions).

## <a name="not-recognized-as-cmdlet"></a>Scénario : Terme non reconnu comme nom d’une applet de commande, d’une fonction, d’un script

### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Cause :

Cette erreur peut se produire pour l'une des raisons suivantes :

* Le module contenant l’applet de commande n’est pas importé dans le compte Automation
* Le module contenant l’applet de commande est importé, mais il est obsolète

### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en effectuant une des tâches suivantes :

Si le module est un module Azure, consultez [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md) pour découvrir comment mettre à jour vos modules dans votre compte Automation.

S’il s’agit d’un module distinct, assurez-vous que le module est importé dans votre compte Automation.

## <a name="job-attempted-3-times"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais n’a pas pu démarrer

### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Cause :

Cette erreur se produit en raison d’un des problèmes suivants :

* Limite de mémoire. Les limites documentées sur la quantité de mémoire qui est allouée à un bac à sable se trouvent à la section [Limites du service Automation](../../azure-subscription-service-limits.md#automation-limits). Un travail peut échouer s’il utilise plus de 400 Mo de mémoire.

* Sockets réseau. Les bacs à sable Azure sont limités à 1 000 sockets réseau simultanés, comme décrit dans [Limites du service Automation](../../azure-subscription-service-limits.md#automation-limits).

* Module incompatible. Cette erreur peut se produire si les dépendances de module ne sont pas correctes. Dans ce cas, votre runbook retourne généralement un message « Commande introuvable » ou « Impossible de lier le paramètre ».

* Votre runbook a tenté d’appeler un fichier exécutable ou un sous-processus dans un runbook qui s’exécute dans un bac à sable Azure. Ce scénario n’est pas pris en charge dans les bacs à sable Azure.

* Votre runbook a tenté d’écrire trop de données d’exception dans le flux de sortie.

### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Les méthodes recommandées pour travailler dans la limite de mémoire consistent à fractionner la charge de travail en plusieurs runbooks, à ne pas traiter trop de données en mémoire, à ne pas écrire de sortie inutile depuis vos runbooks ou à prendre en compte le nombre de points de contrôle que vous écrivez dans vos runbooks de flux de travail PowerShell. Vous pouvez utiliser la méthode clear, telle que `$myVar.clear()`, pour effacer la variable, ainsi que `[GC]::Collect()` pour exécuter immédiatement le nettoyage de la mémoire. Cela permet de réduire l’empreinte mémoire de votre runbook pendant l’exécution.

* Mettez à jour vos modules Azure en suivant les étapes de l’article [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

* Une autre solution consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par les limites de mémoire et réseau associées aux bacs à sable Azure.

* S'il vous faut appeler un processus (par exemple, .exe ou subprocess.call) dans un runbook, vous devez exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md).

* Il existe une limite de 1 Mo sur le flux de sortie de travail. Assurez-vous que vous placez les appels à un fichier exécutable ou à un sous-processus dans un bloc try/catch. En cas d’exception, écrivez le message de cette exception dans une variable Automation. Cela l’empêchera d’être écrite dans le flux de sortie de travail.

## <a name="sign-in-failed"></a>Scénario : La connexion au compte Azure a échoué

### <a name="issue"></a>Problème

L’une des erreurs suivantes s’affiche quand vous utilisez les applets de commande `Add-AzureAccount` ou `Connect-AzureRmAccount` :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Cause :

Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide. Elle peut également se produire si le nom d’utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

### <a name="resolution"></a>Résolution :

Pour identifier le problème, effectuez les étapes suivantes :

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **\@** , dans le nom de la ressource d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.
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
       #Logging in to Azure...
       $connectionResult = Connect-AzureRmAccount `
                              -ServicePrincipal `
                              -TenantId $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="child-runbook-object"></a>La référence d’objet n’est pas définie sur une instance d’objet

### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lorsque vous appelez un runbook enfant avec le commutateur `-Wait` et que le flux de sortie contient un objet :

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Cause :

[Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) peut ne pas gérer correctement le flux de sortie s’il contient des objets.

### <a name="resolution"></a>Résolution :

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

## <a name="fails-deserialized-object"></a>Scénario : Le runbook échoue à cause d’un objet désérialisé

### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Cause :

Si votre runbook est un workflow PowerShell, il stocke les objets complexes dans un format désérialisé afin de conserver l’état du runbook si le workflow est suspendu.

### <a name="resolution"></a>Résolution :

Chacune des trois solutions suivantes résout ce problème :

* Si vous redirigez des objets complexes d’une applet de commande vers une autre, wrappez ces deux applets de commande dans un bloc InlineScript.
* Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.
* Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.

## <a name="quota-exceeded"></a>Scénario : Le travail du Runbook a échoué, car le quota alloué a été dépassé

### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Cause :

Cette erreur se produit quand l’exécution du travail dépasse le quota gratuit de 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks ou la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Résolution :

Si vous voulez utiliser plus de 500 minutes de traitement par mois, vous devez changer votre abonnement et le faire passer du niveau Gratuit au niveau De base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :

1. Connectez-vous à votre abonnement Azure :
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau
3. Cliquez sur **Paramètres** > **Tarifs**.
4. Cliquez sur **Activer** en bas de page pour actualiser votre compte au niveau **De base**.

## <a name="cmdlet-not-recognized"></a>Scénario : La cmdlet n’est pas reconnue lors de l’exécution d’un runbook

### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Cause :

Cette erreur survient quand le moteur PowerShell ne trouve pas la cmdlet que vous utilisez dans votre runbook. Cette erreur peut être dû au fait que le module contenant la cmdlet n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que la cmdlet existe déjà dans un autre module et Automation ne peut pas résoudre le nom.

### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Vérifiez que vous avez correctement entré le nom de la cmdlet.
* Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande dans la bibliothèque ou exécutez `Get-Command <CommandName>`. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez l’applet de commande dans le canevas et veillez à utiliser un paramètre valide défini dans votre runbook.
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **Nom_module\Nom_applet_de_commande**.
* Si vous exécutez le runbook localement dans un groupe de Workers hybrides, assurez-vous que le module et l’applet de commande sont installés sur la machine qui héberge le Worker hybride.

## <a name="long-running-runbook"></a>Scénario : échec de l’exécution d’un long runbook

### <a name="issue"></a>Problème

Votre runbook affiche l’état **Stopped** (Arrêté) au bout de trois heures d’exécution. Vous pouvez également recevoir l’erreur :

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Il s’agit du comportement par défaut dans les bacs à sable Azure en raison de la supervision de la répartition de charge équilibrée des processus au sein d’Azure Automation. S’il s’exécute pendant plus de trois heures, la répartition de charge équilibrée arrête automatiquement un runbook. L’état d’un runbook qui dépasse la limite de temps de la répartition de charge équilibrée diffère selon le type du runbook. Les runbooks PowerShell et Python sont mis à l’état **Stopped** (Arrêté). Les runbooks PowerShell Workflow sont mis à l’état **Failed** (Échec).

### <a name="cause"></a>Cause :

Le runbook s’est exécuté au-delà de la limite de trois heures autorisée par la répartition de charge équilibrée dans un bac à sable Azure.

### <a name="resolution"></a>Résolution :

Une solution recommandée consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md).

Les Workers hybrides ne sont pas restreints par la limite d’exécution de runbook de trois heures autorisée par la [répartition de charge équilibrée](../automation-runbook-execution.md#fair-share). Les runbooks s’exécutant sur eux doivent néanmoins être développés pour prendre en charge les comportements de redémarrage s’il y a un problème inattendu avec l’infrastructure locale.

Une autre option consiste à optimiser le runbook en créant des [runbooks enfants](../automation-child-runbooks.md). Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un runbook enfant. Chacun de ces runbooks enfants s’exécute en parallèle dans des processus distincts. Ce comportement réduit la quantité totale de temps pour l’exécution du runbook parent.

Applets de commande PowerShell prenant en charge le scénario avec des runbooks enfants :

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) : cette applet de commande vous permet de démarrer un runbook et de lui passer des paramètres.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) : si des opérations doivent être effectuées à la fin de l’exécution du runbook enfant, cette applet de commande vous permet de vérifier l’état du travail de chaque enfant.

## <a name="expired webhook"></a>Scénario : État de la version : 400 Requête incorrecte lors de l’appel d’un webhook

### <a name="issue"></a>Problème

Lorsque vous essayez d’appeler un webhook pour un runbook Azure Automation, vous recevez l’erreur suivante :

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Cause :

Le webhook que vous tentez d’appeler est désactivé ou a expiré.

### <a name="resolution"></a>Résolution :

Si le webhook est désactivé, vous pouvez réactiver le webhook via le Portail Azure. Lorsque le webhook a expiré, il doit être supprimé et recréé. Vous pouvez uniquement [renouveler un webhook](../automation-webhooks.md#renew-webhook) s’il n’a pas déjà expiré.

## <a name="429"></a>Scénario : 429 : Le taux de requêtes est actuellement trop grand. Réessayez

### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lors de l’exécution de l’applet de commande `Get-AzureRmAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Cause :

Cette erreur peut se produire lors de la récupération d’une sortie de tâche à partir d’un runbook comportant de nombreux [flux détaillés](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Résolution :

Il existe deux façons de résoudre cette erreur :

* Modifiez le runbook et réduisez le nombre de flux de tâches émis.
* Réduisez le nombre de flux à récupérer lors de l’exécution de la cmdlet. Pour suivre ce comportement, vous pouvez spécifier le paramètre `-Stream Output` sur l’applet de commande `Get-AzureRmAutomationJobOutput` afin de ne récupérer que les flux de sortie. 

## <a name="cannot-invoke-method"></a>Scénario : Le travail PowerShell échoue avec l’erreur : Impossible d’appeler la méthode

### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lorsque vous démarrez un travail PowerShell dans un runbook exécuté dans Azure :

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Cause :

Cette erreur peut se produire lorsque vous démarrez un travail PowerShell dans un runbook exécuté dans Azure. Ce comportement peut s’expliquer par le fait que les runbooks exécutés dans un bac à sable Azure peuvent ne pas s’exécuter en [mode langage complet](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Résolution :

Il existe deux façons de résoudre cette erreur :

* Au lieu d’utiliser `Start-Job`, utilisez `Start-AzureRmAutomationRunbook` pour démarrer un runbook.
* Si votre runbook rencontre ce message d’erreur, exécutez-le sur un runbook Worker hybride.

Pour en savoir plus sur ce comportement et sur d’autres comportements des runbooks Azure Automation, consultez [Comportement des runbooks](../automation-runbook-execution.md#runbook-behavior).

## <a name="other"></a>Mon problème n’est pas listé ci-dessus

Les sections ci-dessous répertorient d'autres erreurs courantes en plus de la documentation de prise en charge pour vous aider à résoudre votre problème.

## <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Le runbook Worker hybride n’exécute pas de travaux ou ne répond pas

Si vous exécutez des travaux à l'aide d'un Worker hybride plutôt qu'avec Azure Automation, vous devrez peut-être [résoudre les problèmes du Worker hybride lui-même](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

## <a name="runbook-fails-with-no-permission-or-some-variation"></a>Échec du runbook avec l’erreur « Aucune autorisation » ou une variante

Les comptes d’identification n’ont peut-être pas les mêmes autorisations sur les ressources Azure que votre compte actuel. Vérifiez que votre compte d’identification [a les autorisations nécessaires pour accéder aux ressources](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilisées dans votre script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Les runbooks fonctionnaient, mais se sont soudainement arrêtés

* Si les runbooks étaient précédemment exécutés et qu'ils se sont arrêtés, [vérifiez que le compte d'identification n'a pas expiré](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Si vous utilisez des webhooks pour démarrer des runbooks, [vérifiez que le webhook n’a pas expiré](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="issues-passing-parameters-into-webhooks"></a>Problèmes liés au passage de paramètres dans des webhooks

Pour obtenir de l'aide sur le passage des paramètres dans des webhooks, consultez [Démarrer un runbook à partir d'un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="issues-using-az-modules"></a>Problèmes liés à l'utilisation de modules Az

L’utilisation de modules Az et AzureRM dans le même compte Automation n’est pas prise en charge. Pour plus d'informations, consultez [Modules Az dans les runbooks](https://docs.microsoft.com/azure/automation/az-modules).

## <a name="inconsistent-behavior-in-runbooks"></a>Comportement incohérent dans des runbooks

Suivez les instructions de [Exécution de runbooks](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior) afin d’éviter les problèmes liés aux travaux simultanés, à des ressources créées plusieurs fois ou à une logique liée à la chronologie dans les runbooks.

## <a name="runbook-fails-with-the-errors-no-permission-forbidden-403-or-some-variation"></a>Échec du runbook avec les erreurs : Aucune autorisation, Interdit, 403 ou toute autre variante

Les comptes d’identification n’ont peut-être pas les mêmes autorisations sur les ressources Azure que votre compte actuel. Vérifiez que votre compte d'identification dispose des [autorisations nécessaires pour accéder aux ressources](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilisées dans votre script.

## <a name="runbooks-were-working-but-suddenly-stopped"></a>Les runbooks fonctionnaient, mais se sont soudainement arrêtés

* Si les runbooks étaient précédemment exécutés et qu'ils se sont arrêtés, vérifiez que le compte d'identification [n'a pas expiré](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Si vous utilisez des webhooks pour démarrer les runbooks, vérifiez que le webhook [n'a pas expiré](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook).

## <a name="passing-parameters-into-webhooks"></a>Passage de paramètres dans des webhooks

Pour obtenir de l'aide sur le passage des paramètres dans des webhooks, consultez [Démarrer un runbook à partir d'un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters).

## <a name="using-az-modules"></a>Utilisation de modules Az

L’utilisation de modules Az et AzureRM dans le même compte Automation n’est pas prise en charge. Pour plus d'informations, consultez [Modules Az dans les runbooks](https://docs.microsoft.com/azure/automation/az-modules).

## <a name="using-self-signed-certificates"></a>Utilisation de certificats autosignés

Pour utiliser des certificats autosignés, vous devez suivre le guide relatif à la [création d'un certificat](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

## <a name="recommended-documents"></a>Documents recommandés

* [Démarrage d’un Runbook dans Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Exécution d’un Runbook dans Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
