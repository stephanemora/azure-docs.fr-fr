---
title: Mon premier runbook PowerShell Workflow dans Azure Automation
description: Ce didacticiel présente les procédures de création, de test et de publication d’un runbook textuel simple à l’aide de PowerShell Workflow.
keywords: powershell workflow, exemples de workflows powershell, workflows powershell
services: automation
ms.subservice: process-automation
ms.date: 09/24/2018
ms.topic: conceptual
ms.openlocfilehash: b96860afd649f33936ee8dd2954e6873f908a369
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605062"
---
# <a name="my-first-powershell-workflow-runbook"></a>Mon premier runbook PowerShell Workflow

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)

Ce didacticiel décrit la création d’un [Runbook PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) dans Azure Automation. Commencez avec un simple runbook que vous testez et publiez tout en apprenant comment suivre l’état du travail du runbook. Modifiez ensuite le runbook pour gérer les ressources Azure, comme l’illustre le démarrage d’une machine virtuelle Azure. Enfin, renforcez le runbook en ajoutant des paramètres de runbook.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Comme vous allez devoir l’arrêter et la démarrer, il ne doit pas s’agir d’une machine virtuelle de production.

## <a name="step-1---create-new-runbook"></a>Étape 1 - Créer un Runbook

Commencez par créer un runbook simple qui renvoie le texte « Hello World ».

1. Dans le portail Azure, ouvrez votre compte Automation.

   La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces ressources sont les modules automatiquement inclus dans un nouveau compte Automation. Vous devez également disposer de la ressource Informations d’identification associée à votre abonnement.
 
1. Sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
1. Créez un runbook en sélectionnant **+ Créer un runbook**.
1. Nommez le runbook « MyFirstRunbook-Workflow ».
1. Dans ce cas, vous allez créer un [runbook Workflow PowerShell](automation-runbook-types.md#powershell-workflow-runbooks). Par conséquent, sélectionnez **Workflow PowerShell** comme **Type de runbook**.
1. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur textuel.

## <a name="step-2---add-code-to-the-runbook"></a>Étape 2 - Ajouter du code au Runbook

Vous pouvez soit taper du code directement dans le runbook, soit sélectionner des cmdlets, des runbooks et des ressources à partir du contrôle Bibliothèque, puis les ajouter au runbook avec tous les paramètres associés. Pour cette procédure pas à pas, tapez le code directement dans le runbook.

1. Pour l’instant, votre runbook est vide, à l’exception du mot clé obligatoire **workflow**, du nom de votre runbook et des accolades qui entourent la totalité du workflow.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

1. Tapez `Write-Output "Hello World"` entre les accolades.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

1. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"></a>Étape 3 - Tester le runbook

Avant de publier le runbook pour le rendre disponible en production, vous devez le tester pour vous assurer qu’il fonctionne correctement. Le test d’un runbook exécute sa version Brouillon et vous permet d’afficher sa sortie de manière interactive.

1. Sélectionnez l’option **Volet de test**.
1. Cliquez sur **Démarrer** pour démarrer le test en testant la seule option activée.
1. Un [travail de runbook](automation-runbook-execution.md) est créé, dont l’état apparaît dans le volet.

   L’état initial du travail est Mis en file d’attente pour indiquer qu’il attend qu’un Runbook Worker devienne disponible dans le cloud. L’état passe ensuite à Démarrage quand un Worker revendique le travail. Enfin, l’état passe à Exécution lorsque le runbook commence à s’exécuter.

1. Une fois le travail du runbook terminé, le volet Test affiche sa sortie. En l’occurrence, vous voyez « Hello World ».

   ![Hello World](media/automation-first-runbook-textual/test-output-hello-world.png)

1. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 - Publier et démarrer le runbook

Le runbook que vous avez créé est toujours en mode brouillon. Vous devez le publier pour pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans ce cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Cliquez sur **Publier** pour publier le Runbook, puis sur **Oui** quand vous y êtes invité.
1. Faites défiler vers la gauche pour afficher le runbook dans le volet **Runbooks**, et notez que la valeur **État de création** indique Publié.
1. Faites défiler la page vers la droite pour afficher le volet MyFirstRunbook-Workflow.

   Les options en haut vous permettent de démarrer le runbook, de planifier une heure de démarrage ou de créer un [webhook](automation-webhooks.md) pour démarrer le runbook via un appel HTTP.

1. Cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité pour démarrer le runbook.

   ![Démarrer un Runbook](media/automation-first-runbook-textual/automation-runbook-controls-start.png)

1. Un volet Travail s’ouvre pour le travail du runbook que vous avez créé. Dans ce cas, laissez le volet ouvert pour pouvoir suivre la progression du travail.

1. Notez que l’état du travail est affiché dans **Résumé du travail**. Cet état correspond aux états que vous avez vus lors du test du runbook.

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-jobsummary.png)

1. Lorsque l’état du runbook est Terminé, cliquez sur **Sortie**. La page Sortie s’ouvre, affichant votre message « Hello World ».

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-outputtile.png)

1. Fermez la page Sortie.

1. Cliquez sur **Tous les journaux d’activité** pour ouvrir le volet Flux de la tâche du Runbook. Vous ne devriez voir que « Hello World » dans le flux de sortie. Notez que le volet Flux peut afficher d’autres flux pour une tâche de runbook, tels que des flux de détails et d’erreurs, si le runbook écrit dans ceux-ci.

   ![Résumé des tâches](media/automation-first-runbook-textual/job-pane-status-blade-alllogstile.png)

1. Fermez les volets du flux et de la tâche pour revenir à la page MyFirstRunbook.
1. Cliquez sur **Travaux** sous **Ressources** pour ouvrir la page Travaux de ce runbook. Cette page répertorie tous les travaux créés par votre runbook. Vous ne devriez en voir qu’un seul, car vous n’avez exécuté le travail qu’une seule fois.

   ![travaux](media/automation-first-runbook-textual/runbook-control-job-tile.png)

1. Cliquez sur le nom du travail pour ouvrir le volet Travail que vous avez vu au démarrage du runbook. Utilisez ce volet pour voir les détails de tout travail créé pour le runbook.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>Étape 5 - Ajouter une authentification pour gérer les ressources Azure

Vous avez testé et publié votre runbook, mais jusqu’à présent, il ne fait rien d’utile. Vous souhaitez qu’il gère les ressources Azure. Il ne peut le faire que s’il s’authentifie à l’aide des informations d’identification de l’abonnement. L’authentification utilise la cmdlet **Connect-AzAccount**.

>[!NOTE]
>Pour les runbooks PowerShell, **Add-AzAccount** et **Add-AzureRMAccount** sont des alias pour **Connect-AzAccount**. Vous pouvez utiliser ces cmdlets ou [mettre à jour vos modules](automation-update-azure-modules.md) dans votre compte Automation vers les dernières versions. Il est possible que vous deviez mettre à jour vos modules, même si vous venez de créer un compte Automation.

1. Accédez à la page MyFirstRunbook-Workflow et ouvrez l’éditeur de texte en cliquant sur **Modifier**.
2. Supprimez la ligne **Write-Output**.
3. Positionnez le curseur sur une ligne vide entre les accolades.
4. Tapez ou copiez-collez le code suivant qui gère l’authentification auprès de votre compte d’authentification Automation.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

1. Cliquez sur le volet de **Test** afin de tester le runbook.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois le test terminé, une sortie semblable à celle illustrée ci-dessous devrait afficher des informations de base sur votre compte. Cette action confirme la validité des informations d’identification.

   ![Authenticate](media/automation-first-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>Étape 6 - Ajouter du code pour démarrer une machine virtuelle

À présent que votre runbook s’authentifie auprès de votre abonnement Azure, vous pouvez gérer les ressources. Ajoutons une commande pour démarrer une machine virtuelle. Vous pouvez choisir n’importe quelle machine virtuelle de votre abonnement Azure. Pour l’instant, vous allez coder ce nom en dur dans le runbook. Si vous gérez des ressources associées à plusieurs abonnements, utilisez le paramètre *AzContext* avec la cmdlet [Get-AzContext](/powershell/module/az.accounts/get-azcontext).

1. Indiquez le nom et le nom du groupe de ressources de la machine virtuelle à démarrer en entrant un appel à la cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Start-AzVM?view=azps-3.5.0
) comme illustré ci-dessous. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

1. Enregistrez le runbook, puis cliquez sur le volet de **Test** pour le tester.
1. Cliquez sur **Démarrer** pour démarrer le test. Une fois l’opération terminée, vérifiez que la machine virtuelle a démarré.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>Étape 7 - Ajouter un paramètre d’entrée au runbook

Votre runbook démarre actuellement la machine virtuelle que vous avez codée en dur dans le runbook. Elle sera plus utile si vous pouvez la spécifier au démarrage du runbook. Ajoutez des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ajoutez au runbook des valeurs pour *VMName* et *ResourceGroupName*, et utilisez les variables associées avec la cmdlet **Start-AzVM** comme illustré ci-dessous.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée se trouvant dans le test.
3. Fermez le volet de test.
4. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
5. Arrêtez la machine virtuelle que vous avez démarrée.
6. Cliquez sur **Démarrer** pour démarrer le Runbook. 
7. Tapez les valeurs de **VMNAME** et **RESOURCEGROUPNAME** pour la machine virtuelle que vous allez démarrer.

   ![Démarrer le Runbook](media/automation-first-runbook-textual/automation-pass-params.png)

8. Une fois l’exécution du runbook terminée, vérifiez que la machine virtuelle a démarré.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur PowerShell, notamment le langage de référence et les modules d’apprentissage, consultez la [Documentation PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Pour une prise en main des runbooks graphiques, voir [Mon premier runbook graphique](automation-first-runbook-graphical.md).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour plus d’informations sur les types de runbooks, sur leurs avantages et sur leurs limites, voir [Types de runbooks Azure Automation](automation-runbook-types.md).
* Pour plus d’informations sur les fonctionnalités de prise en charge de script PowerShell, voir [Prise en charge de script PowerShell natif dans Azure Automation](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/).
