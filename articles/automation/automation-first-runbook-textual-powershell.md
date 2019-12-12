---
title: Mon premier runbook PowerShell dans Azure Automation
description: Ce didacticiel vous familiarise avec la création, le test et la publication d’un Runbook PowerShell simple.
keywords: azure powershell, didacticiel sur le script powershell, automatisation powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 11/27/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f950a2fed2fbd355fc99453f09b655463e67102d
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850871"
---
# <a name="my-first-powershell-runbook"></a>Mon premier Runbook PowerShell

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ce didacticiel vous guide dans la création d’un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks) dans Azure Automation. Vous commencez avec un simple runbook que vous testez et publiez tout en découvrant comment suivre l’état de la tâche du runbook. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous le rendrez plus robuste en lui ajoutant des paramètres.

## <a name="prerequisites"></a>Prérequis

Pour effectuer ce didacticiel, vous avez besoin de ce qui suit :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-quickstart-create-account.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Comme elle sera arrêtée et relancée, ce ne doit pas être une machine virtuelle de production.
* Vous devrez peut-être [mettre à jour vos modules Azure](automation-update-azure-modules.md) en fonction des applets de commande que vous utilisez.

## <a name="create-new-runbook"></a>Créer un runbook

Commençons par créer un runbook simple qui retourne le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.
2. Cliquez sur **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
3. Créez un runbook en cliquant sur le bouton **Ajouter un Runbook**, puis sur **Créer un Runbook**.
4. Nommez le Runbook *MyFirstRunbook-PowerShell*.
5. Dans ce cas précis, nous allons créer un [Runbook PowerShell](automation-runbook-types.md#powershell-runbooks). Par conséquent, sélectionnez **Powershell** comme **Type de runbook**.
6. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="add-code-to-the-runbook"></a>Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, vous tapez directement le code dans le runbook.

1. Votre runbook est actuellement vide. Saisissez *Write-Output « Hello World ».* dans le corps du script.

   ![Hello World](media/automation-first-runbook-textual-powershell/automation-helloworld.png)

2. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"> </a> Tester le runbook

Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
2. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
3. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.

   Le statut initial de la tâche est *Mis en file d’attente*, ce qui indique qu’elle attend qu’un Runbook Worker du cloud devienne disponible. Il passe à *En cours de démarrage* lorsqu’un Worker sélectionne la tâche, puis à *En cours d’exécution* lorsque le runbook se lance.

4. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans votre cas, *Hello World* devrait apparaître.

   ![Sortie du volet de test](media/automation-first-runbook-textual-powershell/automation-testpane-output.png)

5. Fermez le volet de test pour revenir au canevas.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que vous avez créé est toujours en mode brouillon. Il doit être publié pour pouvoir être exécuté en production.  Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans votre cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
1. Si vous faites maintenant défiler la page vers la gauche pour visualiser le runbook sur la page **Runbooks**, celle-ci affiche **l’État de création** **Publié**.
1. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-PowerShell**.
   Les options de la partie supérieure nous permettent de démarrer le runbook, de l’afficher, de planifier son démarrage à un moment ultérieur ou de créer un [webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
1. L’objectif étant de démarrer le runbook, cliquez sur **Démarrer**, puis sur **Ok** à l’ouverture de la page Démarrer le runbook.
1. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Vous pouvez le fermer, mais, dans ce cas précis, laissez-le ouvert pour pouvoir suivre la progression de la tâche.
1. L’état de la tâche, indiqué dans le champ **Résumé de la tâche**, correspond aux états constatés lors du test du runbook.

   ![Résumé des tâches](media/automation-first-runbook-textual-powershell/job-pane-status-blade-jobsummary.png)

1. Lorsque le Runbook prend l’état *Terminé*, sous **Vue d’ensemble**, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.

   ![Sortie de la tâche](media/automation-first-runbook-textual-powershell/job-pane-status-blade-outputtile.png)

1. Fermez la page Sortie.
1. Cliquez sur **Tous les journaux d’activité** pour ouvrir le volet Flux de la tâche du Runbook. Seul le message *Hello World* devrait apparaître dans le flux de sortie, mais d’autres flux peuvent s’afficher pour une tâche de runbook, notamment Mode détaillé et Erreur si le runbook consigne ces informations.

   ![Tous les journaux d’activité](media/automation-first-runbook-textual-powershell/job-pane-status-blade-alllogstile.png)

1. Fermez les page du flux et de la tâche pour revenir à la page MyFirstRunbook-PowerShell.
1. Sous **Détails**, cliquez sur **Tâches** pour ouvrir le volet Tâches pour ce runbook. Cette page liste toutes les tâches créées par ce runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.

   ![Liste des tâches](media/automation-first-runbook-textual-powershell/runbook-control-job-tile.png)

1. Vous pouvez cliquer sur ce travail pour ouvrir le même volet du travail que vous avez consulté au démarrage du runbook. Cette action permet de revenir en arrière et d’afficher les détails d’une tâche créée pour un runbook donné.

## <a name="add-authentication-to-manage-azure-resources"></a>Ajouter une authentification pour gérer les ressources Azure

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Ce n’est possible que s’il s’authentifie à l’aide d’une connexion d’identification, créée automatiquement à la création du compte d’automatisation. La connexion d’identification s’établit avec la cmdlet **Connect-AzureRmAccount**. Si vous gérez des ressources associées à plusieurs abonnements, utilisez le paramètre **-AzureRmContext** avec [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   # Wrap authentication in retry logic for transient network failures
   $logonAttempt = 0
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $connection.SubscriptionID

   Get-AzureRmVM -ResourceGroupName myResourceGroup -AzureRmContext $AzureContext
   ```

1. Ouvrez l’éditeur de texte en cliquant sur **Modifier** dans la page MyFirstRunbook-PowerShell.
1. La ligne **Write-Output** ne vous est plus utile. Vous pouvez donc la supprimer.
1. Tapez ou copiez-collez le code suivant qui gère l’authentification avec votre compte d’authentification Automation :

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** et **Login-AzureRmAccount** sont désormais des alias pour **Connect-AzureRMAccount**. Si l’applet de commande **Connect-AzureRMAccount** n’existe pas, vous pouvez utiliser **Add-AzureRmAccount** ou **Login-AzureRmAccount**, ou encore mettre à jour vos modules dans votre compte Automation avec les dernières versions.

1. Cliquez sur le volet de **Test** afin de tester le runbook.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette sortie confirme que le compte d’identification est valide.

   ![Authentifier](media/automation-first-runbook-textual-powershell/runbook-auth-output.png)

## <a name="add-code-to-start-a-virtual-machine"></a>Ajouter du code pour démarrer une machine virtuelle

À présent que votre runbook s’authentifie auprès de votre abonnement Azure, vous pouvez gérer les ressources. Vous ajoutez une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, nous allons coder ce nom en dur dans le runbook.

1. Après *Connect-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.

   ```powershell
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName'
   ```

1. Enregistrez le runbook, puis cliquez sur le volet de **Test** pour le tester.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="add-an-input-parameter"></a>Ajouter un paramètre d’entrée

Pour l’instant, votre runbook démarre la machine virtuelle que vous avez codée en dur dans le runbook, mais ce dernier serait plus utile si vous pouviez spécifier la machine virtuelle lorsque le runbook est démarré. Vous ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

   ```powershell
   Param(
    [string]$VMName,
    [string]$ResourceGroupName
   )
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $connection = Get-AutomationConnection -Name AzureRunAsConnection
   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       # Logging in to Azure...
       $connectionResult =    Connect-AzureRmAccount `
                                  -ServicePrincipal `
                                  -Tenant $connection.TenantID `
                                  -ApplicationID $connection.ApplicationID `
                                  -CertificateThumbprint $connection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }

   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   ```

1. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée utilisées dans le test.
1. Fermez le volet de test.
1. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
1. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
1. Cliquez sur **OK** pour démarrer le Runbook. Tapez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.<br><br> ![Paramètre de réussite](media/automation-first-runbook-textual-powershell/automation-pass-params.png)<br>
1. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.

## <a name="differences-from-powershell-workflow"></a>Différences par rapport à PowerShell Workflow

Les runbooks PowerShell ont les mêmes cycle de vie, fonctionnalités et mode de gestion que ceux de PowerShell Workflow, avec toutefois quelques différences et limitations :

1. Les Runbooks PowerShell s’exécutent rapidement par rapport aux Runbooks PowerShell Workflow, car ils n’ont pas à subir l’étape de compilation.
2. Les runbooks PowerShell Workflow prennent en charge les points de contrôle, grâce auxquels ils peuvent reprendre à n’importe quel point. Les runbooks PowerShell ne peuvent reprendre que du début.
3. Les runbooks PowerShell Workflow prennent en charge l’exécution parallèle et l’exécution en série. Les runbooks PowerShell ne peuvent exécuter des commandes qu’en série.
4. Dans un runbook PowerShell Workflow, une activité, une commande ou un bloc de script peut avoir sa propre instance d’exécution. Dans un runbook PowerShell, tous les éléments d’un script s’exécutent au sein d’une même instance d’exécution. Il existe également certaines [différences syntaxiques](https://technet.microsoft.com/magazine/dn151046.aspx) entre un Runbook PowerShell natif et un Runbook PowerShell Workflow.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](/powershell/scripting/overview).
* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des runbooks de workflow PowerShell, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
