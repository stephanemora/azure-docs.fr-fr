---
title: Résoudre les erreurs avec les runbooks Azure Automation
description: Découvrez comment détecter et résoudre les problèmes que vous pouvez rencontrer à l’aide de runbooks Azure Automation.
services: automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/24/2019
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 26c5c5b31d5f3f9e1a642c0bafb947190e479055
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632628"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Résoudre les erreurs avec les runbooks

Lorsque des erreurs surviennent pendant l’exécution de runbooks dans Azure Automation, vous pouvez utiliser les étapes suivantes pour diagnostiquer les problèmes.

1. **Vérifiez que le script de votre runbook s’exécute correctement sur votre ordinateur local.** 

    Reportez-vous à la [documentation de PowerShell](/powershell/scripting/overview) ou à la [documentation de Python](https://docs.python.org/3/) pour obtenir des modules de référence et d’entraînement du langage. L’exécution en local de votre script peut détecter et résoudre les erreurs courantes, telles que :

      * Modules manquants
      * Erreurs de syntaxe
      * Erreurs logiques

2. **Examinez les [flux d’erreurs](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#runbook-output) des runbooks.**

    Regardez si les flux contiennent des messages et comparez-les aux erreurs documentées dans cet article.

3. **Vérifiez que vos nœuds et votre espace de travail Automation disposent des modules nécessaires.** 

    Si votre runbook importe des modules, vérifiez qu’ils sont disponibles dans votre compte Automation à l’aide des étapes listées dans [Importer des modules](../shared-resources/modules.md#importing-modules). Mettez à jour vos modules vers la dernière version disponible en suivant les instructions fournies dans [Mettre à jour des modules Azure dans Azure Automation](..//automation-update-azure-modules.md). Pour plus d’informations sur la résolution des problèmes, consultez [Résoudre les problèmes liés aux modules](shared-resources.md#modules).

4. **À faire en cas d’échec inattendu ou d’interruption du runbook.**

    * [Vérifiez l’état des travaux](https://docs.microsoft.com/azure/automation/automation-runbook-execution#job-statuses) pour voir l’état des runbooks et les causes possibles de leur état.
    * [Ajoutez une sortie supplémentaire](https://docs.microsoft.com/azure/automation/automation-runbook-output-and-messages#message-streams) au runbook pour identifier ce qui se passe avant l'interruption du runbook.
    * [Gérez les exceptions](https://docs.microsoft.com/azure/automation/automation-runbook-execution#handling-exceptions) qui sont levées par votre travail.

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scénario : Exécuter Login-AzureRMAccount pour se connecter

### <a name="issue"></a>Problème

Lors de l’exécution d’un runbook, vous recevez l’erreur suivante :

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Cause

Cette erreur peut se produire lorsque vous n’utilisez pas un compte d’identification ou quand le compte d’identification a expiré. Consultez [Gérer les comptes d’identification Azure Automation](https://docs.microsoft.com/azure/automation/manage-runas-account).

Cette erreur a deux causes principales :

* Il existe plusieurs versions du module AzureRM ou Az.
* Vous essayez d’accéder à des ressources dans un abonnement distinct.

### <a name="resolution"></a>Résolution

Si vous recevez cette erreur après avoir mis à jour un module AzureRM ou Az, vous devez mettre à jour tous vos modules vers la même version.

Si vous essayez d’accéder à des ressources dans un autre abonnement, vous pouvez suivre les étapes ci-dessous pour configurer les autorisations.

1. Accédez au compte d’identification Automation, puis copiez l’ID d’application et l’empreinte.
  ![Copier l’ID d’application et l’empreinte](../media/troubleshoot-runbooks/collect-app-id.png)
1. Accédez au service Access Control de l’abonnement où le compte Automation n’est PAS hébergé, puis ajoutez une nouvelle attribution de rôle.
  ![Contrôle d’accès](../media/troubleshoot-runbooks/access-control.png)
1. Ajoutez l’ID d’application collecté précédemment. Sélectionnez des autorisations Contributeur.
   ![Ajouter une attribution de rôle](../media/troubleshoot-runbooks/add-role-assignment.png)
1. Copiez le nom de l’abonnement.
1. Vous pouvez maintenant utiliser le code de runbook suivant pour tester les autorisations de votre compte Automation sur l’autre abonnement. Remplacez `"\<CertificateThumbprint\>"` par la valeur que vous avez copiée à l’étape 1. Remplacez `"\<SubscriptionName\>"` par la valeur que vous avez copiée à l’étape 4.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scénario : Impossible de trouver l’abonnement Azure

### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez l’applet de commande `Select-AzureSubscription` ou `Select-AzureRmSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Error

Cette erreur peut se produire si :

* Le nom de l’abonnement n’est pas valide.
* L’utilisateur Azure Active Directory qui tente d’obtenir les détails de l’abonnement n’est pas configuré comme l’administrateur de l’abonnement.

### <a name="resolution"></a>Résolution

Effectuez les étapes ci-dessous pour déterminer si vous vous êtes correctement authentifié auprès d’Azure et si vous avez accès à l’abonnement que vous voulez sélectionner.

1. Pour vérifier qu’il fonctionne de façon autonome, testez votre script en dehors d’Azure Automation.
2. Vérifiez que votre script exécute l’applet de commande `Add-AzureAccount` avant d’exécuter l’applet de commande `Select-AzureSubscription`.
3. Ajoutez `Disable-AzureRmContextAutosave –Scope Process` au début de votre runbook. L’appel de cette applet de commande garantit que les informations d’identification s’appliqueront uniquement à l’exécution du runbook actuel.
4. Si ce message d’erreur persiste, modifiez votre code en ajoutant le paramètre `AzureRmContext` après l’applet de commande `Add-AzureAccount`, puis exécutez le code.

   ```powershell
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
    ```

## <a name="scenario-authentication-to-azure-failed-because-multi-factor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scénario : L’authentification auprès d’Azure a échoué, car l’authentification multifacteur est activée

### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’authentification auprès d’Azure avec votre nom d’utilisateur et votre mot de passe Azure :

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Cause

Si l’authentification multifacteur est configurée sur votre compte Azure, vous ne pouvez pas vous authentifier auprès d’Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification.

### <a name="resolution"></a>Résolution

Pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à [Création et ajout d’un certificat pour gérer des services Azure](https://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx). Pour utiliser un principal de service avec des applets de commande Azure Resource Manager, reportez-vous à [Création du principal du service à l’aide du portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) et [Authentification d’un principal du service à l’aide d’Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="scenario-you-see-an-error-in-your-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scénario : Vous constatez une erreur dans votre flux de travail concernant la méthode get_SerializationSettings

### <a name="issue"></a>Problème

Vous voyez l’erreur suivante dans votre flux de travail concernant un runbook :

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

### <a name="cause"></a>Cause

Cette erreur est due à l’utilisation des applets de commande des modules AzureRM et Az dans un runbook. Elle se produit lorsque vous importez le module Az avant d’importer le module AzureRM.

### <a name="resolution"></a>Résolution

Les applets de commande AZ et AzureRM ne peuvent pas être importées ni utilisées dans le même runbook. Pour en savoir plus sur les applets de commande AZ dans Azure Automation, consultez [Prise en charge du module Az dans Azure Automation](../az-modules.md).

## <a name="scenario-the-runbook-fails-with-the-error-a-task-was-canceled"></a><a name="task-was-cancelled"></a>Scénario : Le runbook échoue avec l’erreur : Une tâche a été annulée

### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Cause

Cette erreur peut être due à l’utilisation de modules Azure obsolètes.

### <a name="resolution"></a>Résolution

Vous pouvez résoudre cette erreur en mettant à jour vos modules Azure vers la dernière version. 

1. Dans votre compte Automation, cliquez sur **Modules**, puis sur **Mettre à jour les modules Azure**. 
2. La mise à jour prend environ 15 minutes. Une fois l’opération terminée, réexécutez le runbook qui a échoué.

Pour en savoir plus sur la mise à jour de vos modules, consultez [Mettre à jour des modules Azure dans Azure Automation](../automation-update-azure-modules.md).

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scénario : Les runbooks échouent lors du traitement de plusieurs abonnements

### <a name="issue"></a>Problème

Lors de l’exécution de runbooks, le runbook ne parvient pas à gérer les ressources Azure.

### <a name="cause"></a>Cause

Le runbook n’utilise pas le contexte approprié lors de l’exécution.

### <a name="resolution"></a>Résolution

Le contexte de l’abonnement peut être perdu lorsqu’un runbook appelle plusieurs runbooks. Pour garantir que le contexte d’abonnement sera passé aux runbooks, indiquez au client runbook de passer le contexte à l’applet de commande `Start-AzureRmAutomationRunbook` dans le paramètre `AzureRmContext`. Utilisez l’applet de commande `Disable-AzureRmContextAutosave` avec le paramètre `Scope` défini sur `Process` pour faire en sorte que les informations d’identification spécifiées soient utilisées uniquement pour le runbook actuel. Pour plus d’informations, consultez [Utilisation de plusieurs abonnements](../automation-runbook-execution.md#working-with-multiple-subscriptions).

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with Run As account
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

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-script"></a><a name="not-recognized-as-cmdlet"></a>Scénario : Terme non reconnu comme nom d’une applet de commande, d’une fonction, d’un script

### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```error
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Cause

Cette erreur peut se produire pour les raisons suivantes :

* Le module contenant la cmdlet n’est pas importé dans le compte Automation.
* Le module contenant l’applet de commande est importé, mais il est obsolète.

### <a name="resolution"></a>Résolution

Effectuez l’une des tâches suivantes pour résoudre cette erreur. 

* Pour un module Azure, consultez [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md) afin de savoir comment mettre à jour vos modules dans votre compte Automation.

* Pour un module autre qu’Azure, vérifiez que le module a été importé dans votre compte Automation.

## <a name="scenario-the-runbook-job-start-was-attempted-three-times-but-failed-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais il n’a pas pu démarrer.

### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur suivante :

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Cause

Cette erreur se produit en raison de l’un des problèmes suivants :

* Limite de mémoire. Un travail peut échouer s’il utilise plus de 400 Mo de mémoire. Les limites documentées sur la mémoire allouée à un bac à sable sont indiquées dans [Limites du service Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* Sockets réseau. Les bacs à sable Azure sont limités à 1 000 sockets réseau simultanés. Consultez [Limites du service Automation](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* Module incompatible. Les dépendances du module sont peut-être incorrectes. Dans ce cas, votre runbook retourne généralement un message `Command not found` ou `Cannot bind parameter`.

* Aucune authentification auprès d’Active Directory pour le bac à sable. Votre runbook a tenté d’appeler un fichier exécutable ou un sous-processus qui s’exécute dans un bac à sable Azure. La configuration de runbooks pour s’authentifier auprès d’Azure AD avec la Bibliothèque d’authentification Azure Active Directory (ADAL) n’est pas prise en charge.

* Trop de données d’exception. Votre runbook a tenté d’écrire trop de données d’exception dans le flux de sortie.

### <a name="resolution"></a>Résolution

* Limite de mémoire, sockets réseau. Les méthodes suggérées pour respecter les limites de mémoire consistent à diviser la charge de travail entre plusieurs runbooks, à traiter moins de données en mémoire, à éviter d’écrire une sortie inutile depuis vos runbooks et à prendre en compte le nombre de points de contrôle écrits dans vos runbooks de workflow PowerShell. Utilisez la méthode de vidage, telle que `$myVar.clear`, pour effacer les variables et utilisez `[GC]::Collect` pour exécuter immédiatement un nettoyage de la mémoire. Cela permet de réduire l’empreinte mémoire de votre runbook pendant l’exécution.

* Module incompatible. Mettez à jour vos modules Azure en suivant les étapes décrites dans [Guide pratique pour mettre à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).

* Aucune authentification auprès d’Active Directory pour le bac à sable. Lorsque vous vous authentifiez auprès d’Azure AD avec un runbook, vérifiez que le module Azure AD est disponible dans votre compte Automation. Veillez à accorder au compte d’identification les autorisations nécessaires pour effectuer les tâches que le runbook automatise.

  Si votre runbook ne peut pas appeler un exécutable ou un sous-processus s’exécutant dans un bac à sable Azure, utilisez le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par les limites de mémoire et réseau associées aux bacs à sable Azure.

* Trop de données d’exception. Il existe une limite de 1 Mo sur le flux de sortie de travail. Vérifiez que votre runbook renferme les appels à un fichier exécutable ou à un sous-processus à l’aide de blocs `try` et `catch`. Si les opérations lèvent une exception, faites en sorte que le code écrive le message de l’exception dans une variable Automation. Cette technique empêche l’écriture du message dans le flux de sortie du travail.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scénario : Échec de la connexion au compte Azure

### <a name="issue"></a>Problème

L’une des erreurs suivantes s’affiche quand vous utilisez l’applet de commande `Add-AzureAccount` ou `Connect-AzureRmAccount` :

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Cause

Ces erreurs se produisent quand le nom de la ressource d’informations d’identification n’est pas valide. Elles peuvent également se produire si le nom d’utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

### <a name="resolution"></a>Résolution

Pour identifier le problème, effectuez les étapes suivantes :

1. Vérifiez qu’il n’existe aucun caractère spécial (notamment le caractère `\@`) dans le nom de la ressource d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.
2. Vérifiez que vous pouvez utiliser le nom d’utilisateur et le mot de passe stockés dans les informations d’identification Azure Automation dans votre éditeur PowerShell ISE local. Exécutez les applets de commande suivantes dans PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzureRmAccount –Credential $Cred
   ```

3. Si l’authentification échoue localement, cela signifie que vous n’avez pas correctement configuré vos informations d’identification Azure Active Directory. Reportez-vous au billet de blog [Authenticating to Azure using Azure Active Directory](https://azure.microsoft.com/blog/azure-automation-authenticating-to-azure-using-azure-active-directory/) pour configurer correctement le compte Azure Active Directory.

4. Si cette erreur semble temporaire, essayez d’ajouter la logique de nouvelle tentative à votre routine d’authentification pour renforcer l’authentification.

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

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scénario : La référence d’objet n’a pas la valeur d’une instance d’un objet

### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lorsque vous appelez un runbook enfant avec le paramètre `Wait` et que le flux de sortie contient un objet :

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Cause

`Start-AzureRmAutomationRunbook` ne gère pas correctement le flux de sortie si celui-ci contient des objets.

### <a name="resolution"></a>Résolution

Il vous est conseillé d’implémenter une logique d’interrogation et d’utiliser l’applet de commande [Get-AzureRmAutomationJobOutput](/powershell/module/azurerm.automation/get-azurermautomationjoboutput) pour récupérer la sortie. Un exemple de cette logique est défini ci-dessous.

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scénario : Le runbook échoue à cause d’un objet désérialisé

### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Cause

Si votre runbook est un workflow PowerShell, il stocke les objets complexes dans un format désérialisé afin de conserver l’état du runbook si le workflow est suspendu.

### <a name="resolution"></a>Résolution

Utilisez l’une des solutions suivantes pour résoudre ce problème.

* Si vous redirigez des objets complexes d’une applet de commande vers une autre, wrappez ces deux applets de commande dans une activité `InlineScript`.
* Transmettez le nom ou la valeur dont vous avez besoin depuis l’objet complexe au lieu de transmettre la totalité de l’objet.
* Utilisez un runbook PowerShell au lieu d’un runbook Workflow PowerShell.

## <a name="scenario-runbook-job-fails-because-allocated-quota-exceeded"></a><a name="quota-exceeded"></a>Scénario : Le travail du runbook échoue en raison du dépassement du quota alloué

### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Cause

Cette erreur se produit quand l’exécution du travail dépasse le quota gratuit de 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks ou la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

### <a name="resolution"></a>Résolution

Si vous voulez utiliser plus de 500 minutes de traitement par mois, vous devez passer d’un abonnement de niveau Gratuit à un abonnement de niveau De base.

1. Connectez-vous à votre abonnement Azure.
2. Sélectionnez le compte Automation à mettre à niveau.
3. Cliquez sur **Paramètres**, puis sur **Tarifs**.
4. Cliquez sur **Activer** au bas de la page pour mettre à niveau votre compte vers le niveau De base.

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scénario : La cmdlet n’est pas reconnue lors de l’exécution d’un runbook

### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Cause

Cette erreur survient quand le moteur PowerShell ne trouve pas la cmdlet que vous utilisez dans votre runbook. Il est possible que le module contenant l’applet de commande ne soit pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que l’applet de commande existe déjà dans un autre module et qu’Automation ne puisse pas résoudre le nom.

### <a name="resolution"></a>Résolution

Utilisez l’une des solutions suivantes pour résoudre ce problème.

* Vérifiez que vous avez correctement entré le nom de l’applet de commande.
* Vérifiez que l’applet de commande existe dans votre compte Automation et qu’il n’existe aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande dans la bibliothèque, ou exécutez `Get-Command <CommandName>`. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez l’applet de commande dans le canevas et veillez à utiliser un paramètre valide défini dans votre runbook.
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom complet de l’applet de commande. Par exemple, vous pouvez utiliser `ModuleName\CmdletName`.
* Si vous exécutez le runbook localement dans un groupe de workers hybrides, vérifiez que le module et l’applet de commande sont installés sur la machine qui héberge le worker hybride.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scénario : Échec de l’exécution d’un long runbook

### <a name="issue"></a>Problème

Votre runbook affiche l’état Stopped (Arrêté) au bout de trois heures d’exécution. Il est également possible que vous obteniez cette erreur :

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Il s’agit du comportement par défaut dans les bacs à sable Azure en raison de la supervision de la [répartition de charge équilibrée](../automation-runbook-execution.md#fair-share) des processus au sein d’Azure Automation. Si un processus s’exécute pendant plus de trois heures, la répartition de charge équilibrée arrête automatiquement un runbook. L’état d’un runbook qui dépasse la limite de temps de la répartition de charge équilibrée diffère selon le type de runbook. Les runbooks PowerShell et Python sont mis à l’état Stopped (Arrêté). Les runbooks PowerShell Workflow sont mis à l’état Failed (Échec).

### <a name="cause"></a>Cause

Le runbook s’est exécuté au-delà de la limite de 3 heures autorisée par la répartition de charge équilibrée dans un bac à sable Azure.

### <a name="resolution"></a>Résolution

Une solution recommandée consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par la limite d’exécution de runbook de 3 heures applicable aux bacs à sable Azure. Les runbooks s’exécutant sur eux doivent néanmoins être développés pour prendre en charge les comportements de redémarrage s’il y a un problème inattendu avec l’infrastructure locale.

Une autre solution consiste à optimiser le runbook en créant des [runbooks enfants](../automation-child-runbooks.md). Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un runbook enfant. Chaque runbook enfant s’exécute en parallèle dans un processus distinct. Ce comportement réduit la quantité totale de temps pour l’exécution du runbook parent.

Applets de commande PowerShell prenant en charge le scénario avec des runbooks enfants :

* [Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook). Cette applet de commande vous permet de démarrer un runbook et de lui transmettre des paramètres.

* [Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob). Si des opérations doivent être effectuées à la fin de l’exécution du runbook enfant, cette applet de commande vous permet de vérifier l’état du travail de chaque enfant.

## <a name="scenario-status-400-bad-request-when-calling-a-webhook"></a><a name="expired webhook"></a>Scénario : État : 400 Requête incorrecte lors de l’appel d’un webhook

### <a name="issue"></a>Problème

Lorsque vous essayez d’appeler un webhook pour un runbook Azure Automation, vous recevez l’erreur suivante :

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Cause

Le webhook que vous tentez d’appeler est désactivé ou a expiré.

### <a name="resolution"></a>Résolution

Si le webhook est désactivé, vous pouvez réactiver le webhook via le Portail Azure. Si le webhook a expiré, vous devez le supprimer puis le recréer. Vous pouvez uniquement [renouveler un webhook](../automation-webhooks.md#renew-webhook) s’il n’a pas déjà expiré.

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scénario : 429 : Le taux de requêtes est actuellement trop grand...

### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lors de l’exécution de l’applet de commande `Get-AzureRmAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Cause

Cette erreur peut se produire lors de la récupération d’une sortie de tâche à partir d’un runbook comportant de nombreux [flux détaillés](../automation-runbook-output-and-messages.md#verbose-stream).

### <a name="resolution"></a>Résolution

Effectuez l’une des tâches suivantes pour résoudre cette erreur.

* Modifiez le runbook et réduisez le nombre de flux de tâches émis.

* Réduisez le nombre de flux à récupérer lors de l’exécution de la cmdlet. Pour cela, vous pouvez définir la valeur du paramètre `Stream` de l’applet de commande `Get-AzureRmAutomationJobOutput` de manière à récupérer uniquement les flux de sortie. 

## <a name="scenario-powershell-job-fails-with-error-cannot-invoke-method"></a><a name="cannot-invoke-method"></a>Scénario : Le travail PowerShell échoue avec l’erreur : Impossible d’appeler la méthode

### <a name="issue"></a>Problème

Vous recevez le message d’erreur suivant lorsque vous démarrez un travail PowerShell dans un runbook exécuté dans Azure :

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Cause

Cette erreur peut indiquer que l’exécution de runbooks dans un bac à sable (sandbox) Azure n’est pas possible en [mode Langage complet](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Résolution

Il existe deux façons de résoudre cette erreur.

* Au lieu d’utiliser `Start-Job`, utilisez `Start-AzureRmAutomationRunbook` pour démarrer le runbook.
* Exécutez le runbook sur un Runbook Worker hybride.

Pour plus d’informations sur ce comportement et sur d’autres comportements des runbooks Azure Automation, consultez [Comportement des runbooks](../automation-runbook-execution.md#runbook-behavior).

## <a name="scenario-linux-hybrid-runbook-worker-receives-a-prompt-for-a-password-when-signing-a-runbook"></a>Scénario : Le Runbook Worker hybride Linux reçoit une invite de mot de passe lors de la signature d’un runbook

### <a name="issue"></a>Problème

L’exécution de la commande `sudo` pour un Runbook Worker hybride Linux permet de récupérer une invite inattendue de mot de passe.

### <a name="cause"></a>Cause

Le compte **nxautomationuser** de l’agent Log Analytics pour Linux n’est pas correctement configuré dans le fichier **sudoers**. Le Runbook Worker hybride a besoin de la configuration appropriée des autorisations de compte et d’autres données afin de pouvoir signer des runbooks sur le Runbook Worker Linux.

### <a name="resolution"></a>Résolution

* Vérifiez que le Runbook Worker hybride a l’exécutable GnuPG (GPG) sur la machine.

* Vérifiez la configuration du compte **nxautomationuser** dans le fichier **sudoers**. Consultez [Exécution de runbooks sur un Runbook Worker hybride](../automation-hrw-run-runbooks.md).

## <a name="scenario-cmdlet-failing-in-pnp-powershell-runbook-on-azure-automation"></a>Scénario : Échec de la cmdlet dans le runbook PowerShell PnP sur Azure Automation

### <a name="issue"></a>Problème

Lorsqu’un runbook écrit directement un objet généré par PowerShell PnP dans la sortie Azure Automation, la sortie de la cmdlet ne peut pas être renvoyée à Automation.

### <a name="cause"></a>Cause

Ce problème est généralement dû au fait qu’Azure Automation traite les runbooks qui appellent des applets de commande PowerShell PnP, par exemple `add-pnplistitem`, sans intercepter les objets de retour.

### <a name="resolution"></a>Résolution

Modifiez vos scripts pour attribuer des valeurs renvoyées à des variables afin que les cmdlets n’essaient pas d’écrire des objets entiers dans la sortie standard. Un script peut rediriger le flux de sortie vers une cmdlet, comme indiqué ci-dessous.

```azurecli
  $null = add-pnplistitem
```

Si votre script analyse la sortie de la cmdlet, le script doit stocker la sortie dans une variable et manipuler la variable au lieu de simplement diffuser la sortie.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="my-problem-isnt-listed-above"></a><a name="other"></a>Mon problème n’est pas listé ci-dessus

Les sections ci-dessous listent d’autres erreurs courantes en plus de la documentation de prise en charge pour vous aider à résoudre votre problème.

### <a name="hybrid-runbook-worker-doesnt-run-jobs-or-isnt-responding"></a>Le runbook Worker hybride n’exécute pas de travaux ou ne répond pas

Si vous exécutez des travaux à l’aide d’un Runbook Worker hybride plutôt qu’avec Azure Automation, vous devrez peut-être [résoudre les problèmes du worker hybride](https://docs.microsoft.com/azure/automation/troubleshoot/hybrid-runbook-worker).

### <a name="runbook-fails-with-no-permission-or-some-variation"></a>Échec du runbook avec l’erreur « Aucune autorisation » ou une variante

Les comptes d’identification n’ont peut-être pas les mêmes autorisations sur les ressources Azure que votre compte actuel. Vérifiez que votre compte d’identification dispose des [autorisations nécessaires pour accéder aux ressources](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilisées dans votre script.

### <a name="issues-passing-parameters-into-webhooks"></a>Problèmes liés au passage de paramètres dans des webhooks

Pour obtenir de l’aide sur le passage des paramètres dans des webhooks, consultez [Démarrer un runbook à partir d’un webhook](../automation-webhooks.md#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="issues-using-az-modules"></a>Problèmes liés à l’utilisation de modules Az

L’utilisation de modules Az et AzureRM dans le même compte Automation n’est pas prise en charge. Pour plus d’informations, consultez [Modules Az dans les runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="inconsistent-behavior-in-runbooks"></a>Comportement incohérent dans des runbooks

Pour éviter les problèmes liés aux travaux simultanés, à des ressources créées plusieurs fois ou à une logique liée à la chronologie dans les runbooks, suivez les instructions fournies dans [Exécution de runbooks](https://docs.microsoft.com/azure/automation/automation-runbook-execution#runbook-behavior).

### <a name="runbook-fails-with-the-error-no-permission-forbidden-403-or-some-variation"></a>Échec du runbook avec l’erreur Aucune autorisation, Interdit (403) ou toute autre variante

Les comptes d’identification n’ont peut-être pas les mêmes autorisations sur les ressources Azure que votre compte actuel. Vérifiez que votre compte d’identification dispose des [autorisations nécessaires pour accéder aux ressources](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal) utilisées dans votre script.

### <a name="runbooks-were-working-but-suddenly-stopped"></a>Les runbooks fonctionnaient, mais se sont soudainement arrêtés

* Vérifiez que le compte d’identification n’a pas expiré. Consultez [Renouvellement de certification](https://docs.microsoft.com/azure/automation/manage-runas-account#cert-renewal).
* Si vous utilisez un [webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#renew-webhook) pour démarrer un runbook, vérifiez que le webhook n’a pas expiré.

### <a name="passing-parameters-into-webhooks"></a>Passage de paramètres dans des webhooks

Pour obtenir de l’aide sur le passage des paramètres dans des webhooks, consultez [Démarrer un runbook à partir d’un webhook](https://docs.microsoft.com/azure/automation/automation-webhooks#parameters-used-when-the-webhook-starts-a-runbook).

### <a name="using-az-modules"></a>Utilisation de modules Az

L’utilisation de modules Az et AzureRM dans le même compte Automation n’est pas prise en charge. Consultez [Modules Az dans les runbooks](https://docs.microsoft.com/azure/automation/az-modules).

### <a name="using-self-signed-certificates"></a>Utilisation de certificats auto-signés

Pour utiliser des certificats auto-signés, consultez [Création d’un certificat](https://docs.microsoft.com/azure/automation/shared-resources/certificates#creating-a-new-certificate).

### <a name="access-denied-when-using-azure-sandbox-for-a-runbook"></a>Accès refusé lors de l’utilisation d’un sandbox Azure pour un runbook

Le bac à sable (sandbox) Azure empêche l’accès à tous les serveurs COM hors processus. Par exemple, une application sandbox ou un runbook ne peut pas appeler Windows Management Instrumentation (WMI) ou le service Windows Installer (msiserver.exe). Pour plus d’informations sur l’utilisation du sandbox, consultez [Exécution d’un runbook dans Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution).

## <a name="recommended-documents"></a>Documents recommandés

* [Démarrage d’un Runbook dans Azure Automation](https://docs.microsoft.com/azure/automation/automation-starting-a-runbook)
* [Exécution d’un runbook dans Azure Automation](https://docs.microsoft.com/azure/automation/automation-runbook-execution)

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Azure](https://azure.microsoft.com/support/forums/).
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.
