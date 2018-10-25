---
title: Résoudre les erreurs avec les runbooks Azure Automation
description: Découvrez comment résoudre les problèmes avec les runbooks Azure Automation
services: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/17/2018
ms.topic: conceptual
ms.service: automation
manager: carmonm
ms.openlocfilehash: 33c2bd48084c3d0e73fe2f4a1ce922e7a66b944f
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955414"
---
# <a name="troubleshoot-errors-with-runbooks"></a>Résoudre les erreurs avec les runbooks

## <a name="authentication-errors-when-working-with-azure-automation-runbooks"></a>Erreurs d’authentification avec des runbooks Azure Automation

### <a name="sign-in-failed"></a>Scénario : La connexion au compte Azure a échoué

#### <a name="issue"></a>Problème

L’erreur suivante s’affiche quand vous utilisez les applets de commande `Add-AzureAccount` ou `Connect-AzureRmAccount`.
:

```
Unknown_user_type: Unknown User Type
```

#### <a name="cause"></a>Cause :

Cette erreur se produit si le nom de la ressource d’informations d’identification n’est pas valide, ou si le nom d’utilisateur et le mot de passe que vous avez utilisés pour configurer la ressource d’informations d’identification Automation ne sont pas valides.

#### <a name="resolution"></a>Résolution :

Pour identifier le problème, effectuez les étapes suivantes :  

1. Assurez-vous qu’il n’existe aucun caractère spécial, notamment le caractère **@**, dans le nom de la ressource d’informations d’identification Automation que vous utilisez pour vous connecter à Azure.  
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

```
The subscription named <subscription name> cannot be found.
```

#### <a name="error"></a>Error

Cette erreur se produit si le nom de l’abonnement n’est pas valide ou si l’utilisateur Azure Active Directory qui essaie d’obtenir les détails de l’abonnement n’est pas configuré comme administrateur de l’abonnement.

#### <a name="resolution"></a>Résolution :

Pour déterminer si vous vous êtes correctement authentifié auprès d’Azure et si vous avez accès à l’abonnement que vous voulez sélectionner, effectuez les étapes suivantes :  

1. Assurez-vous d’exécuter la cmdlet **Add-AzureAccount** avant d’exécuter la cmdlet **Select-AzureSubscription**.  
2. Si ce message d’erreur persiste, modifiez votre code en ajoutant le paramètre **-AzureRmContext** après la cmdlet **Add-AzureAccount**, puis exécutez le code.

   ```powershell
   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
-ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzureRmContext

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $context
   ```

### <a name="auth-failed-mfa"></a>Scénario : L’authentification auprès d’Azure a échoué, car l’authentification multifacteur est activée

#### <a name="issue"></a>Problème

Vous recevez l’erreur suivante lors de l’authentification auprès d’Azure avec votre nom d’utilisateur et votre mot de passe Azure :

```
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

#### <a name="cause"></a>Cause :

Si l’authentification multifacteur est configurée sur votre compte Azure, vous ne pouvez pas vous authentifier auprès d’Azure avec un utilisateur Azure Active Directory. Au lieu de cela, vous devez utiliser un certificat ou un principal de service pour l’authentification dans Azure.

#### <a name="resolution"></a>Résolution :

Pour utiliser un certificat avec les applets de commande de modèle de déploiement Azure Classic, reportez-vous à [Création et ajout d’un certificat pour gérer des services Azure.](http://blogs.technet.com/b/orchestrator/archive/2014/04/11/managing-azure-services-with-the-microsoft-azure-automation-preview-service.aspx) Pour utiliser un principal du service avec des applets de commande Azure Resource Manager, voir la rubrique sur la [création du principal du service à l’aide du portail Azure](../../active-directory/develop/howto-create-service-principal-portal.md) et l’[authentification d’un principal du service à l’aide d’Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

## <a name="common-errors-when-working-with-runbooks"></a>Erreurs courantes avec des runbooks

### <a name="task-was-cancelled"></a>Scénario : Le runbook échoue avec l’erreur : Une tâche a été annulée

#### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```
Exception: A task was canceled.
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due à l’utilisation de modules Azure obsolètes.

#### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en mettant à jour vos modules Azure vers la dernière version.

Dans votre compte Automation, cliquez sur **Modules**, puis sur **Mettre à jour les modules Azure**. La mise à jour prend environ 15 minutes, après la réexécution du runbook qui a échoué. Pour en savoir plus sur la mise à jour de vos modules, consultez [Mettre à jour des modules Azure dans Azure Automation](../automation-update-azure-modules.md).

### <a name="child-runbook-auth-failure"></a>Scénario : le runbook enfant échoue lors du traitement de plusieurs abonnements

#### <a name="issue"></a>Problème

Lors de l’exécution de runbooks enfants avec `Start-AzureRmRunbook`, le runbook enfant ne parvient pas à gérer les ressources Azure.

#### <a name="cause"></a>Cause :

Le runbook enfant n’utilise pas le contexte approprié lors de l’exécution.

#### <a name="resolution"></a>Résolution :

Si vous utilisez plusieurs abonnements, le contexte de l’abonnement peut être perdu durant l’appel des runbooks enfants. Pour que le contexte de l’abonnement soit passé aux runbooks enfants, ajoutez le paramètre `AzureRmContext` à l’applet de commande et passez-lui le contexte.

```azurepowershell-interactive
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

### <a name="not-recognized-as-cmdlet"></a>Scénario : Le runbook échoue en raison d’une applet de commande manquante

#### <a name="issue"></a>Problème

Votre runbook échoue avec une erreur similaire à l’exemple suivant :

```
The term 'Connect-AzureRmAccount' is not recognized as the name of a cmdlet, function, script file, or operable program.  Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due aux raisons suivantes :

1. Le module contenant l’applet de commande n’est pas importé dans le compte Automation
2. Le module contenant l’applet de commande est importé, mais il est obsolète

#### <a name="resolution"></a>Résolution :

Cette erreur peut être résolue en effectuant une des tâches suivantes :

Si le module est un module Azure, consultez [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md) pour découvrir comment mettre à jour vos modules dans votre compte Automation.

S’il s’agit d’un module distinct, assurez-vous que le module est importé dans votre compte Automation.

### <a name="job-attempted-3-times"></a>Scénario : Le travail du runbook a tenté de démarrer à trois reprises, mais n’a pas pu démarrer

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```
The job was tried three times but it failed
```

#### <a name="cause"></a>Cause :

Cette erreur peut être due aux raisons suivantes :

1. Limite de mémoire. Il existe des [limites du service Automation](../../azure-subscription-service-limits.md#automation-limits) documentées sur la quantité de mémoire allouée à un bac à sable. Un travail peut donc échouer s’il utilise plus de 400 Mo de mémoire.

1. Sockets réseau. Les bacs à sable Azure sont limités à 1 000 sockets réseau simultanés, comme décrit dans [Limites du service Automation](../../azure-subscription-service-limits.md#automation-limits).

1. Module incompatible. Cette erreur peut se produire si les dépendances de module ne sont pas correctes. Dans ce cas, votre runbook retourne généralement un message « Commande introuvable » ou « Impossible de lier le paramètre ».

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Les méthodes recommandées pour travailler dans la limite de mémoire consistent à fractionner la charge de travail en plusieurs runbooks, à ne pas traiter trop de données en mémoire, à ne pas écrire de sortie inutile depuis vos runbooks ou à prendre en compte le nombre de points de contrôle que vous écrivez dans vos runbooks de flux de travail PowerShell. Vous pouvez utiliser la méthode clear, telle que `$myVar.clear()`, pour effacer la variable, ainsi que `[GC]::Collect()` pour exécuter immédiatement le nettoyage de la mémoire, ce qui permet de réduire l’empreinte mémoire de votre runbook pendant l’exécution.

* Mettez à jour vos modules Azure en suivant les étapes de l’article [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](../automation-update-azure-modules.md).  

* Une autre solution consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md). Les Workers hybrides ne sont pas restreints par les limites de mémoire et réseau associées aux bacs à sable Azure.

### <a name="fails-deserialized-object"></a>Scénario : Le runbook échoue à cause d’un objet désérialisé

#### <a name="issue"></a>Problème

Votre runbook échoue avec l’erreur :

```
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

### <a name="quota-exceeded"></a>Scénario : Le travail du Runbook a échoué, car le quota alloué a été dépassé

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```
The quota for the monthly total job run time has been reached for this subscription
```

#### <a name="cause"></a>Cause :

Cette erreur se produit quand l’exécution du travail dépasse le quota gratuit de 500 minutes pour votre compte. Ce quota s’applique à tous les types de tâches d’exécution de travail, notamment un le test d’un travail, le démarrage d’un travail à partir du portail, l’exécution d’un travail à l’aide de webhooks et la planification d’un travail à exécuter en utilisant le portail Azure ou dans votre centre de données. Pour en savoir plus sur la tarification d’Automation, consultez [Tarification d’Automation](https://azure.microsoft.com/pricing/details/automation/).

#### <a name="resolution"></a>Résolution :

Si vous voulez utiliser plus de 500 minutes de traitement par mois, vous devez changer votre abonnement et le faire passer du niveau Gratuit au niveau De base. Une mise à niveau vers le niveau de base s’effectue de la façon suivante :  

1. Connectez-vous à votre abonnement Azure :  
2. Sélectionnez le compte Automation que vous souhaitez mettre à niveau  
3. Cliquez sur **Paramètres** > **Tarifs**.
4. Cliquez sur **Activer** en bas de page pour actualiser votre compte au niveau **De base**.

### <a name="cmdlet-not-recognized"></a>Scénario : L’applet de commande n’est pas reconnue lors de l’exécution d’un runbook

#### <a name="issue"></a>Problème

Le travail de votre runbook échoue avec l’erreur :

```
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

#### <a name="cause"></a>Cause :

Cette erreur survient quand le moteur PowerShell ne trouve pas la cmdlet que vous utilisez dans votre runbook. Cela peut être dû au fait que le module contenant la cmdlet n’est pas présent dans le compte, qu’il existe un conflit de nom avec un nom de runbook ou que la cmdlet existe déjà dans un autre module et Automation ne peut pas résoudre le nom.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :  

* Vérifiez que vous avez correctement entré le nom de la cmdlet.  
* Assurez-vous que l’applet de commande existe dans votre compte Automation et qu’il n’y a aucun conflit. Pour vérifier si l’applet de commande est présente, ouvrez un runbook en mode édition et recherchez l’applet de commande dans la bibliothèque ou exécutez `Get-Command <CommandName>`. Une fois que vous avez vérifié que l’applet de commande est disponible pour le compte, et qu’il n’existe aucun conflit de nom avec d’autres applets de commande ou runbooks, ajoutez l’applet de commande dans le canevas et veillez à utiliser un paramètre valide défini dans votre runbook.  
* Si vous rencontrez un conflit de noms et si l’applet de commande est disponible dans deux modules différents, vous pouvez résoudre ce problème en utilisant le nom qualifié complet de l’applet de commande. Vous pouvez par exemple utiliser **Nom_module\Nom_applet_de_commande**.  
* Si vous exécutez le runbook localement dans un groupe de Workers hybrides, assurez-vous que le module/l’applet de commande est installé sur la machine qui héberge le worker hybride.

### <a name="long-running-runbook"></a>Scénario : échec de l’exécution d’un long runbook

#### <a name="issue"></a>Problème

Votre runbook affiche l’état **Stopped** (Arrêté) au bout de trois heures d’exécution. Vous pouvez également recevoir l’erreur :

```
The job was evicted and subsequently reached a Stopped state. The job cannot continue running
```

Il s’agit du comportement par défaut dans les bacs à sable Azure en raison de la supervision de la répartition de charge équilibrée des processus au sein d’Azure Automation, qui arrête automatiquement un runbook dont l’exécution a commencé depuis plus de trois heures. L’état d’un runbook qui dépasse la limite de temps de la répartition de charge équilibrée diffère selon le type du runbook. Les runbooks PowerShell et Python sont mis à l’état **Stopped** (Arrêté). Les runbooks PowerShell Workflow sont mis à l’état **Failed** (Échec).

#### <a name="cause"></a>Cause :

Le runbook s’est exécuté au-delà de la limite de trois heures autorisée par la répartition de charge équilibrée dans un bac à sable Azure.

#### <a name="resolution"></a>Résolution :

Une solution recommandée consiste à exécuter le runbook sur un [Runbook Worker hybride](../automation-hrw-run-runbooks.md).

Les Workers hybrides ne sont pas restreints par la limite d’exécution de runbook de trois heures autorisée par la [répartition de charge équilibrée](../automation-runbook-execution.md#fair-share). Les Runbooks Workers hybrides ne sont pas limités par la répartition de charge équilibrée de trois heures, mais les runbooks s’exécutant sur eux doivent néanmoins être développés pour prendre en charge les comportements de redémarrage après un problème inattendu avec l’infrastructure locale.

Une autre option consiste à optimiser le runbook en créant des [runbooks enfants](../automation-child-runbooks.md). Si votre runbook exécute une boucle via la même fonction sur plusieurs ressources, comme une opération de base de données sur diverses bases de données, vous pouvez déplacer cette fonction vers un runbook enfant. Chacun de ces runbooks enfants s’exécute en parallèle dans des processus distincts, diminuant ainsi le temps total d’exécution du runbook parent.

Applets de commande PowerShell prenant en charge le scénario avec des runbooks enfants :

[Start-AzureRMAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) : cette applet de commande vous permet de démarrer un runbook et de lui passer des paramètres.

[Get-AzureRmAutomationJob](/powershell/module/azurerm.automation/get-azurermautomationjob) : cette applet de commande vous permet de vérifier l’état du travail de chaque enfant et déterminer si des opérations doivent être effectuées à la fin de l’exécution du runbook enfant.

## <a name="common-errors-when-importing-modules"></a>Erreurs courantes survenant lors de l’importation de modules

### <a name="module-fails-to-import"></a>Scénario : Le module ne parvient pas à terminer l’importation ou il est impossible d’exécuter des applets de commande après l’importation

#### <a name="issue"></a>Problème

Un module ne parvient pas à importer ou réussit l’importation, mais aucune applet de commande n’est extraite.

#### <a name="cause"></a>Cause :

Voici quelques raisons courantes pour lesquelles l’importation d’un module dans Azure Automation peut échouer :

* La structure ne correspond pas à la structure dont Automation a besoin.
* Le module dépend d’un autre module qui n’a pas été déployé sur votre compte Automation.
* Le module n’a pas de dépendances dans le dossier.
* L’applet de commande `New-AzureRmAutomationModule` est utilisée pour charger le module, et vous n’avez pas spécifié le chemin de stockage complet ou vous n’avez pas chargé le module en utilisant une URL accessible publiquement.

#### <a name="resolution"></a>Résolution :

Une des solutions suivantes corrige ce problème :

* Assurez-vous que le module suit le format suivant : NomModule.zip **->** NomModule ou Numéro de version **->** (ModuleName.psm1, ModuleName.psd1)
* Ouvrez le fichier .psd1 et regardez si le module possède des dépendances. Si c’est le cas, téléchargez ces modules dans le compte Automation.
* Assurez-vous que les fichiers .dll référencés sont présents dans le dossier de module.

## <a name="next-steps"></a>Étapes suivantes

Si votre problème ne figure pas dans cet article ou si vous ne parvenez pas à le résoudre, utilisez un des canaux suivants pour obtenir de l’aide :

* Obtenez des réponses de la part d’experts Azure via les [Forums Windows](https://azure.microsoft.com/support/forums/)
* Connectez-vous avec [@AzureSupport](https://twitter.com/azuresupport), qui est le compte Microsoft Azure officiel pour améliorer l’expérience client en connectant la communauté Azure aux ressources appropriées : réponses, support technique et experts.
* Si vous avez besoin de plus d’aide, vous pouvez signaler un incident au support Azure. Accédez au [site du support Azure](https://azure.microsoft.com/support/options/) , puis cliquez sur **Obtenir un support**.