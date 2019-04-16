---
title: Mon premier runbook PowerShell Workflow dans Azure Automation
description: Ce didacticiel présente les procédures de création, de test et de publication d’un runbook textuel simple à l’aide de PowerShell Workflow.
keywords: powershell workflow, exemples de workflows powershell, workflows powershell
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/24/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 1697f479cf013f2ef94dd5a8a2fc637d72e6e18a
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549238"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mon premier runbook PowerShell Workflow

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) dans Azure Automation. Vous commencez avec un simple runbook que vous testez et publiez tout en expliquant comment suivre l’état du travail du runbook. Vous modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Enfin, vous le rendrez plus robuste en lui ajoutant des paramètres.

## <a name="prerequisites"></a>Conditions préalables

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure.  Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. vous arrêtez et démarrez cette machine afin qu’il ne doit pas être une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Création d’un Runbook

Commençons par créer un runbook simple qui retourne le texte *Hello World*.

1. Dans le portail Azure, ouvrez votre compte Automation.

   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces éléments représentent les modules automatiquement inclus dans un nouveau compte Automation. Vous devriez également disposer de la ressource d’informations d’identification mentionnée dans les [composants requis](#prerequisites).

1. Cliquez sur **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
1. Créez un runbook en cliquant sur le bouton **+ Ajouter un Runbook**, puis sur **Créer un Runbook**.
1. Nommez le runbook *MyFirstRunbook-Workflow*.
1. Dans ce cas précis, vous allez créer un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks). Par conséquent, sélectionnez **Powershell Workflow** dans le champ **Type de Runbook**.
1. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le Runbook, soit sélectionner des applets de commande, des Runbooks et des ressources à partir du contrôle Bibliothèque et les ajouter au Runbook avec tous les paramètres associés. Pour cette procédure pas à pas, vous tapez directement le code dans le runbook.

1. Pour l’instant, votre runbook est vide, à l’exception du mot-clé requis *workflow*, du nom de votre runbook et des accolades qui entourent la totalité du workflow.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Tapez *Write-Output « Hello World. »*  entre les accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook

Avant de publier le runbook pour le rendre disponible en production, vous voulez le tester pour vous assurer qu’il fonctionne correctement. Lorsque vous testez un Runbook, vous exécutez sa version **Brouillon** et affichez sa sortie de manière interactive.

1. Cliquez sur **Volet de test** pour ouvrir le volet de test.
1. Cliquez sur **Démarrer** pour démarrer le test. Cette option doit être la seule option est activée.
1. Une [tâche de Runbook](automation-runbook-execution.md) est créée et son état apparaît.

   L’état du travail démarrera en tant que *en file d’attente* indiquant qu’il attend d’un runbook worker dans le cloud devienne disponible. Il passe à *En cours de démarrage* lorsqu’un Worker sélectionne la tâche, puis à *En cours d’exécution* lorsque le runbook se lance.  

1. Lorsque la tâche du Runbook est terminée, sa sortie s'affiche. Dans votre cas, *Hello World* devrait apparaître.

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook

Le runbook que vous avez créé est toujours en mode brouillon. Vous devez le publier avant de pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans votre cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
1. Si vous faites maintenant défiler la page vers la gauche pour visualiser le runbook sur la page **Runbooks**, celle-ci affiche **l’État de création** **Publié**.
1. Faites défiler la page vers la droite pour visualiser le volet **MyFirstRunbook-Workflow**.  
   Les options de la partie supérieure nous permettent de démarrer le Runbook, de planifier son démarrage à un moment ultérieur ou de créer un [Webhook](automation-webhooks.md) afin de le démarrer par le biais d’un appel HTTP.
1. Comme vous voulez simplement démarrer le runbook, cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité.

   ![Démarrer un Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Un volet s’ouvre pour la tâche du runbook qui vient d’être créée. Vous pouvez le fermer, mais, dans ce cas précis, laissez-le ouvert pour pouvoir suivre la progression du travail.
1. L’état de la tâche, indiqué dans le champ **Résumé de la tâche**, correspond aux états constatés lors du test du runbook.

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Lorsque le Runbook prend l’état *Terminé*, cliquez sur **Sortie**. Le volet Sortie s’ouvre, affichant *Hello World*.

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)  

1. Fermez le volet Sortie.
1. Cliquez sur **Tous les journaux d’activité** pour ouvrir le volet Flux de la tâche du Runbook. Vous devez voir *Hello World* dans la sortie stream, mais cette vue peut afficher les autres flux pour un travail de runbook telles que le mode détaillé et erreur si le runbook consigne ces.

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Fermez la page de flux de données et de la page tâche pour revenir à la page MyFirstRunbook.
1. Cliquez sur **travaux** pour ouvrir la page des tâches pour ce runbook. Cette page liste toutes les tâches créées par ce runbook. Vous devez voir un seul travail, car vous n’avez exécuté le travail qu’une seule fois.

   ![Tâches](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Vous pouvez cliquer sur cette tâche pour ouvrir la page de travail que vous avez consulté au démarrage du runbook. Cette action permet de revenir en arrière et d’afficher les détails d’une tâche créée pour un runbook donné.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 : Ajout d’une authentification pour gérer les ressources Azure

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Il ne le sauront pas cependant, sauf si vous avez été authentifié à l’aide des informations d’identification mentionnées dans le [conditions préalables](#prerequisites). Vous utilisez pour cela l’applet de commande **Connect-AzureRmAccount**.

1. Ouvrez l’éditeur textuel en cliquant sur **Modifier** dans le volet MyFirstRunbook-Workflow.
2. La ligne **Write-Output** ne vous est plus utile. Vous pouvez donc la supprimer.
3. Positionnez le curseur sur une ligne vide entre les accolades.
4. Tapez ou copiez-collez le code suivant qui gérera l’authentification avec votre compte d’authentification Automation :

   ```powershell-interactive
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $Conn.SubscriptionID
   ```

   > [!IMPORTANT]
   > **Add-AzureRmAccount** et **Login-AzureRmAccount** sont désormais des alias pour **Connect-AzureRMAccount**. Si l’applet de commande **Connect-AzureRMAccount** n’existe pas, vous pouvez utiliser **Add-AzureRmAccount** ou **Login-AzureRmAccount**, ou encore [mettre à jour vos modules](automation-update-azure-modules.md) dans votre compte Automation avec les dernières versions.

> [!NOTE]
> Il est possible que vous deviez [mettre à jour vos modules](automation-update-azure-modules.md), même si vous venez de créer un compte Automation.

1. Cliquez sur le volet de **Test** afin de tester le runbook.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois terminé, la sortie générée semblable à celle illustrée ci-dessous devrait afficher les informations de base sur votre compte. Cette action confirme que les informations d’identification sont valide.

   ![Authentifier](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle

À présent que votre runbook s’authentifie auprès de votre abonnement Azure, vous pouvez gérer les ressources. Vous ajoutez une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle dans votre abonnement Azure, et pour l’instant, vous êtes coder ce nom dans le runbook. Si vous gérez des ressources entre plusieurs abonnements, vous devez utiliser le **- AzureRmContext** paramètre avec [Get-AzureRmContext](/powershell/module/azurerm.profile/get-azurermcontext).

1. Après *Connect-AzureRmAccount*, tapez *Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'NameofResourceGroup'* en fournissant le nom et le nom de groupe de ressources de la machine virtuelle à démarrer.  

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzureRmSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzureRmVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzureRmContext $AzureContext
   }
   ```

1. Enregistrez le runbook, puis cliquez sur le volet de **Test** pour le tester.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 : Ajout d’un paramètre d'entrée au Runbook

Pour l’instant, votre runbook démarre la machine virtuelle que vous avez codée en dur dans le runbook, mais ce dernier serait plus utile si vous pouviez spécifier la machine virtuelle lorsque le runbook est démarré. Vous ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ajoutez des paramètres au Runbook pour *VMName* et *ResourceGroupName* et utilisez ces variables avec l’applet de commande **Start-AzureRmVM** comme dans l’exemple ci-dessous.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )  
   # Ensures you do not inherit an AzureRMContext in your runbook
   Disable-AzureRmContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzureRmVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée se trouvant dans le test.
3. Fermez le volet de test.
4. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5. Arrêtez la machine virtuelle que vous avez démarrée à l'étape précédente.
6. Cliquez sur **Démarrer** pour démarrer le Runbook. Tapez les valeurs **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.

   ![Démarrer le Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

7. Une fois le Runbook terminé, vérifiez que la machine virtuelle a démarré.  

## <a name="next-steps"></a>Étapes suivantes

* Pour une prise en main des Runbooks graphiques, consultez [Mon premier Runbook graphique](automation-first-runbook-graphical.md)
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md)
* Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
* Pour plus d’informations sur la fonctionnalité de prise en charge de script PowerShell, consultez le billet [Native PowerShell script support in Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
