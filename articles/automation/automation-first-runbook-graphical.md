---
title: Mon premier runbook graphique dans Azure Automation
description: Ce didacticiel vous familiarise avec la création, le test et la publication d'un Runbook graphique simple.
keywords: runbook, modèle de runbook, automatisation des runbooks, runbook azure
services: automation
ms.subservice: process-automation
ms.date: 04/13/2018
ms.topic: conceptual
ms.openlocfilehash: b891c8a7bbb33e3a3f18adbbc723d4bc9aa99a3a
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246459"
---
# <a name="my-first-graphical-runbook"></a>Mon premier Runbook graphique

> [!div class="op_single_selector"]
> * [Graphique](automation-first-runbook-graphical.md)
> * [PowerShell](automation-first-runbook-textual-powershell.md)
> * [Workflow PowerShell](automation-first-runbook-textual.md)
> * [Python](automation-first-runbook-textual-python2.md)
> 

Ce didacticiel vous familiarise avec la création d’un [Runbook graphique](automation-runbook-types.md#graphical-runbooks) dans Azure Automation. Commencez avec un simple runbook qui teste et publie tout en expliquant comment suivre l’état du travail du runbook. Modifiez ensuite le runbook pour gérer les ressources Azure, en démarrant dans ce cas une machine virtuelle Azure. Terminez le didacticiel en renforçant le runbook par l’ajout de paramètres de runbook et de liens conditionnels.

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Prérequis

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Abonnement Azure. Si vous n’avez pas encore d’abonnement, vous pouvez [activer vos avantages abonnés MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou créer [un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [compte Automation](automation-offering-get-started.md) pour le stockage du Runbook et l’authentification auprès des ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Une machine virtuelle Azure. Comme vous allez devoir l’arrêter et la démarrer, il ne doit pas s’agir d’une machine virtuelle de production.

## <a name="step-1---create-runbook"></a>Étape 1 - Création d’un Runbook

Commencez par créer un runbook simple qui renvoie le texte « Hello World ».

1. Dans le portail Azure, ouvrez votre compte Automation. 

    La page du compte Automation vous offre un aperçu rapide des ressources de ce compte. Vous devriez déjà posséder certains éléments. La plupart de ces ressources sont les modules automatiquement inclus dans un nouveau compte Automation. Vous devez également disposer de la ressource Informations d’identification associée à votre abonnement.
2. Sélectionnez **Runbooks** sous **Automatisation de processus** pour ouvrir la liste des runbooks.
3. Créez un runbook en sélectionnant **Créer un runbook**.
4. Nommez le Runbook **MyFirstRunbook-Graphical**.
5. Dans ce cas, vous allez créer un [graphique runbook](automation-graphical-authoring-intro.md). Pour **Type de Runbook**, sélectionnez **Graphique**.<br> ![Nouveau Runbook](media/automation-first-runbook-graphical/create-new-runbook.png)<br>
6. Cliquez sur **Créer** pour créer le Runbook et ouvrez l’éditeur graphique.

## <a name="step-2---add-activities"></a>Étape 2 - Ajouter des activités

Le contrôle Bibliothèque à gauche de l’éditeur vous permet de sélectionner des activités à ajouter à votre Runbook. Nous allons ajouter une cmdlet **Write-Output** pour extraire du texte du runbook.

1. Dans le contrôle Bibliothèque, cliquez dans la zone de recherche, puis saisissez **write-output**. Les résultats de la recherche sont présentés dans l’image suivante. <br> ![Microsoft.PowerShell.Utility](media/automation-first-runbook-graphical/search-powershell-cmdlet-writeoutput.png)
1. Faites défiler jusqu'au bas de la liste. Cliquez avec le bouton droit sur **Write-Output**, puis sélectionnez **Ajouter au canevas**. Vous pouvez également cliquer sur les points de suspension (...) en regard du nom de la cmdlet, puis sélectionner **Ajouter au canevas**.
1. Cliquez sur l’activité **Write-Output** sur le canevas. La page du contrôle Configuration qui vous permet de configurer l’activité s’ouvre.
1. Par défaut, le champ **Étiquette** affiche le nom de la cmdlet, mais vous pouvez le modifier pour le rendre plus convivial. Remplacez-le par **Write Hello World to output**.
1. Cliquez sur **Paramètres** pour renseigner les paramètres de l’applet de commande.

   Certaines cmdlets comportent plusieurs jeux de paramètres, et vous devez choisir lequel utiliser. Dans ce cas, **Write-Output** ne possède qu’un seul jeu de paramètres.

1. Sélectionnez le paramètre *InputObject* . Il s’agit du paramètre utilisé pour spécifier le texte à envoyer au flux de sortie.
1. Le menu déroulant **Source de données** affiche des sources que vous utilisez pour renseigner un paramètre. Dans ce menu, sélectionnez **Expression PowerShell**. 

   Vous pouvez utiliser une sortie de ces sources, par exemple, une autre activité, une ressource Automation ou une expression PowerShell. Dans ce cas, la sortie est simplement **Hello World**. Vous pouvez utiliser une expression PowerShell et spécifier une chaîne.<br>

1. Dans le champ **Expression**, saisissez **Hello World**, puis cliquez deux fois sur **OK** pour revenir au canevas.
1. Enregistrez le Runbook en cliquant sur **Enregistrer**.

## <a name="step-3---test-the-runbook"></a>Étape 3 : Test du Runbook

Avant de publier le runbook pour le rendre disponible en production, vous devez le tester pour vous assurer qu’il fonctionne correctement. Le test d’un runbook exécute sa version Brouillon et vous permet d’afficher sa sortie de manière interactive.

1. Sélectionnez l’option **Volet de test**.
1. Cliquez sur **Démarrer** pour démarrer le test. Ce doit être la seule option activée.
1. Un [travail de runbook](automation-runbook-execution.md) est créé, dont l’état apparaît dans le volet.

   L’état initial du travail est **Mis en file d’attente** pour indiquer qu’il attend qu’un Runbook Worker devienne disponible dans le cloud. L’état passe ensuite à **Démarrage** quand un Worker revendique le travail. Enfin, l’état passe à **Exécution** lorsque le runbook commence à s’exécuter.

1. Une fois le travail du runbook terminé, la page Test affiche sa sortie. Dans ce cas, vous voyez **Hello World**.<br> ![Hello World](media/automation-first-runbook-graphical/runbook-test-results.png)
1. Fermez le volet de test pour revenir au canevas.

## <a name="step-4---publish-and-start-the-runbook"></a>Étape 4 : Publication et démarrage du Runbook

Le runbook que vous avez créé est toujours en mode Brouillon. Il doit être publié avant de pouvoir l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon. Dans ce cas, vous n’avez pas encore de version publiée car vous venez de créer le runbook.

1. Sélectionnez l’option **Publier** pour publier le runbook, puis cliquez sur **Oui** quand vous y êtes invité.
1. Faites défiler la page vers la gauche pour afficher le runbook sur la page Runbooks, et notez que la valeur **État de création** indique **Publié**.
1. Faites défiler la page vers la droite pour visualiser la page **MyFirstRunbook-Graphical**.

   Les options en haut vous permettent de démarrer le runbook, de planifier une heure de démarrage ou de créer un [webhook](automation-webhooks.md) pour démarrer le runbook via un appel HTTP.

1. Cliquez sur **Démarrer**, puis sur **Oui** quand vous y êtes invité pour démarrer le runbook.
1. Un volet Travail s’ouvre pour le travail du runbook que vous avez créé. Vérifiez que le champ **État du travail** indique **Terminé**.
1. Cliquez sur **Sortie** pour ouvrir la page Sortie qui affiche **Hello World**.
1. Fermez la page Sortie.
1. Cliquez sur **Tous les journaux d’activité** pour ouvrir le volet Flux de la tâche du Runbook. Vous ne devriez voir que **Hello World** dans le flux de sortie. 

    Notez que le volet Flux peut afficher d’autres flux pour une tâche de runbook, tels que des flux de détails et d’erreurs, si le runbook écrit dans ceux-ci.
1. Fermez les volets Flux et Travail pour revenir à la page **MyFirstRunbook-Graphical**.
1. Pour afficher toutes les tâches du runbook, sélectionnez **Travaux** sous **Ressources**. La page Travaux répertorie toutes les tâches créées par votre runbook. Vous ne devriez voir qu’une seule tâche, car vous n’avez exécuté le travail qu’une seule fois.
1. Cliquez sur le nom du travail pour ouvrir le volet Travail que vous avez vu au démarrage du runbook. Utilisez ce volet pour voir les détails de tout travail créé pour le runbook.

## <a name="step-5---create-variable-assets"></a>Étape 5 - Créer des ressources de variables

Vous avez testé et publié votre runbook mais, jusqu’à présent, il ne fait rien d’utile pour gérer des ressources Azure. Avant de configurer le runbook pour l’authentification, vous devez créer une variable destinée à contenir l’ID d’abonnement, configurer une activité à authentifier, puis référencer la variable. L’ajout d’une référence au contexte de l’abonnement vous permet d’utiliser facilement plusieurs abonnements.

1. Copiez votre ID d’abonnement à partir de l’option **Abonnements** du panneau de navigation.
1. Dans la page Comptes Automation, sélectionnez **Variables** sous **Ressources partagées**.
1. Sélectionnez **Ajouter une variable**.
1. Dans les champs de la page Nouvelle variable, définissez les paramètres suivants.

    * **Nom** : entrez **AzureSubscriptionId**.
    * **Valeur** : entrez votre ID d’abonnement. 
    * **Type** : conservez la chaîne sélectionnée.
    * **Chiffrement** : utilisez la valeur par défaut.
1. Cliquez sur **Créer** pour créer la variable.

## <a name="step-6---add-authentication"></a>Étape 6 - Ajouter une authentification

Maintenant que vous disposez d’une variable pouvant contenir l’ID d’abonnement, vous pouvez configurer le runbook pour l’authentification à l’aide des informations d’identification Exécuter en tant que de votre abonnement. Pour ce faire, ajoutez la connexion Azure Exécuter en tant que comme ressource. Vous devez également ajouter les cmdlets [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/Connect-AzAccount?view=azps-3.5.0) et la cmdlet [Set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/Set-AzContext?view=azps-3.5.0) au canevas.

>[!NOTE]
>Pour les runbooks PowerShell, **Add-AzAccount** et **Add-AzureRMAccount** sont des alias pour **Connect-AzAccount**. Notez que ces alias ne sont pas disponibles pour vos runbooks graphiques. Un runbook graphique peut uniquement utiliser **Connect-AzAccount**.

1. Accédez à votre runbook, puis sélectionnez **Modifier** dans la page **MyFirstRunbook-Graphical**.
1. Vous n’avez plus besoin de l’entrée **Write Hello World to output**. Il vous suffit de cliquer sur les points de suspension, puis de sélectionner **Supprimer**.
1. Dans le contrôle Bibliothèque, développez **RESSOURCES**, puis **Connexions**. Ajoutez **AzureRunAsConnection** au canevas en sélectionnant **Ajouter au canevas**.
1. Dans la zone de recherche du contrôle Bibliothèque, saisissez **Connect-AzAccount**.
1. Ajoutez **Connect-AzAccount** au canevas.
1. Pointez sur **Get Run As Connection** (Obtenir une connexion d’identification) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Connect-AzAccount** pour former un lien. Le runbook démarre avec **Obtenir une connexion d’identification**, puis exécute la cmdlet **Connect-AzAccount**.<br> ![Créer un lien entre des activités](media/automation-first-runbook-graphical/runbook-link-auth-activities.png)
1. Sur le canevas, sélectionnez **Connect-AzAccount**. Dans le volet Contrôle de configuration, saisissez **Login to Azure** dans le champ **Étiquette**.
1. Cliquez sur **Paramètres** pour afficher la page Configuration des paramètres d’activité.
1. La cmdlet **Connect-AzAccount** prend plusieurs jeux de paramètres. Vous devez en sélectionner un avant de fournir des valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **ServicePrincipalCertificate**.
1. Les paramètres de ce jeu de paramètres apparaissent sur la page Configuration des paramètres d’activité. Cliquez sur **APPLICATIONID**.<br> ![Ajouter des paramètres de compte Azure](media/automation-first-runbook-graphical/Add-AzureRmAccount-params.png)
1. Dans la page Valeur du paramètre, définissez les paramètres suivants, puis cliquez sur **OK**.

   * **Source de données** : sélectionnez **Sortie d’activité**.
   * Liste des sources de données : sélectionnez **Obtenir une connexion d’identification**.
   * **Chemin du champ** : saisissez **ApplicationId**. Vous spécifiez le nom de la propriété du chemin du champ parce que l’activité génère un objet doté de plusieurs propriétés.
1. Cliquez sur **CERTIFICATETHUMBPRINT**, puis, dans la page Valeur du paramètre, définissez les paramètres suivants et cliquez sur **OK**.

    * **Source de données** : sélectionnez **Sortie d’activité**.
    * Liste des sources de données : sélectionnez **Obtenir une connexion d’identification**.
    * **Chemin du champ** : saisissez **CertificateThumbprint**.
1. Cliquez sur **SERVICEPRINCIPAL** puis, dans la page Valeur du paramètre, sélectionnez **ConstantValue** comme **Source de données**, cliquez sur l’option **True**, puis sur **OK**.
1. Cliquez sur **TENANTID**, puis définissez les paramètres suivants sur la page Valeur du paramètre. Lorsque vous avez terminé, cliquez sur **OK** à deux reprises.

    * **Source de données** : sélectionnez **Sortie d’activité**. 
    * Liste des sources de données : sélectionnez **Obtenir une connexion d’identification**.
    * **Chemin du champ** : saisissez **TenantId**. 
1. Dans la zone de recherche du contrôle Bibliothèque, saisissez **Set-AzContext**.
1. Ajoutez **Set-AzContext** au canevas.
1. Sélectionnez **Set-AzContext** sur le canevas. Dans le volet Contrôle de configuration, saisissez **Specify Subscription Id** dans le champ **Étiquette**.
1. Cliquez sur **Paramètres** pour afficher la page Configuration des paramètres d’activité.
1. La cmdlet **Set-AzContext** prend plusieurs jeux de paramètres. Vous devez en sélectionner un avant de fournir des valeurs de paramètre. Cliquez sur **Jeu de paramètres**, puis sélectionnez le jeu de paramètres **SubscriptionId**.
1. Les paramètres de ce jeu de paramètres apparaissent sur la page Configuration des paramètres d’activité. Cliquez sur **SubscriptionID**.
1. Dans la page Valeur du paramètre, sélectionnez **Ressource de variable** comme **Source de données** et sélectionnez **AzureSubscriptionId** dans la liste de sources. Lorsque vous avez terminé, cliquez sur **OK** à deux reprises.
1. Pointez sur **Login to Azure** (Connexion à Azure) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Specify Subscription Id**(Spécifier un ID d’abonnement). 

À ce stade, votre Runbook doit ressembler à ce qui suit : <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-auth-config.png)

## <a name="step-7---add-activity-to-start-a-virtual-machine"></a>Étape 7 - Ajouter une activité pour démarrer une machine virtuelle

Vous devez maintenant ajouter une activité **Start-AzVM** pour démarrer une machine virtuelle. Vous pouvez choisir tout machine virtuelle dans votre abonnement Azure. Pour l’instant, vous allez coder son nom en dur dans la cmdlet [Start-AzVM](https://docs.microsoft.com/powershell/module/az.compute/start-azvm?view=azps-3.5.0).

1. Dans la zone de recherche du contrôle Bibliothèque, tapez **Start-Az**.
2. Ajoutez **Start-AzVM** au canevas, puis faites-le glisser sous **Specify Subscription Id** (Spécifier un ID d’abonnement).
1. Pointez sur **Specify Subscription Id** (Spécifier un ID d’abonnement) jusqu’à ce qu’un cercle apparaisse au bas de la forme. Cliquez sur le cercle et faites glisser la flèche vers **Start-AzVM**.
1. Sélectionnez **Start-AzVM**. Cliquez sur **Paramètres**, puis sur **Jeu de paramètres** afin d’afficher les jeux de paramètres disponibles pour l’activité.
1. Sélectionnez le jeu de paramètres **ResourceGroupNameParameterSetName** . Les champs **ResourceGroupName** et **Name** contiennent des points d’exclamation pour indiquer qu’il s’agit de paramètres obligatoires. Notez que les deux champs attendent des valeurs de chaîne.
1. Sélectionnez **Name**. Pour le champ **Source de données**, choisissez **Expression PowerShell**. Pour la machine virtuelle que vous utilisez pour démarrer ce runbook, tapez le nom de la machine entouré de guillemets doubles. Cliquez sur **OK**.
1. Sélectionnez **ResourceGroupName**. Pour le champ **Source de données**, utilisez la valeur **Expression PowerShell**, puis saisissez le nom du groupe de ressources entre guillemets. Cliquez sur **OK**.
1. Cliquez sur le volet de **Test** afin de tester le runbook.
1. Cliquez sur **Démarrer** pour commencer le test. Une fois le test terminé, assurez-vous que la machine virtuelle a démarré. 

À ce stade, votre Runbook doit ressembler à ce qui suit : <br>![Configuration de l’authentification de runbook](media/automation-first-runbook-graphical/runbook-startvm.png)

## <a name="step-8---add-additional-input-parameters"></a>Étape 8 - Ajouter des paramètres d’entrée supplémentaires

Votre runbook démarre actuellement la machine virtuelle dans le groupe de ressources que vous avez spécifié pour la cmdlet **Start-AzVM**. Le runbook est plus utile si vous spécifiez le nom et le groupe de ressources lors du démarrage du runbook. Ajoutons des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Ouvrez l’éditeur graphique en cliquant sur **Modifier** dans le volet **MyFirstRunbook-Graphical**.
1. Cliquez sur **Entrée et sortie**, puis sur **Ajouter une entrée** pour ouvrir le volet Paramètre d’entrée de Runbook.
1. Définissez les paramètres suivants dans les champs fournis, puis cliquez sur **OK**.
   * **Nom** : spécifiez **VMName**.
   * **Type** : conservez le paramètre de chaîne.
   * **Obligatoire** : remplacez la valeur par **Oui**.
1. Créez un second paramètre d’entrée obligatoire appelé *ResourceGroupName*, puis cliquez sur **OK** pour fermer le panneau Entrée et sortie.<br> ![Paramètres d'entrée de Runbook](media/automation-first-runbook-graphical/start-azurermvm-params-outputs.png)
1. Sélectionnez l’activité **Start-AzVM**, puis cliquez sur **Paramètres**.
1. Modifiez le champ **Source de données** pour **Nom** en **Entrée de Runbook**. Sélectionnez ensuite **VMName**.
1. Modifiez le champ **Source de données** de **ResourceGroupName** en **Entrée de Runbook**, puis sélectionnez **ResourceGroupName**.<br> ![Paramètres Start-AzVM](media/automation-first-runbook-graphical/start-azurermvm-params-runbookinput.png)
1. Enregistrez le Runbook et ouvrez le volet de test. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée que vous utilisez dans le test.
1. Fermez le volet de test.
1. Cliquez sur **Publier** pour publier la nouvelle version du Runbook.
1. Arrêtez la machine virtuelle que vous avez démarrée précédemment.
1. Cliquez sur **Démarrer** pour démarrer le Runbook. Tapez les valeurs de **VMName** et **ResourceGroupName** pour la machine virtuelle que vous allez démarrer.
1. Une fois l’exécution du runbook terminée, assurez-vous que la machine virtuelle a été démarrée.

## <a name="step-9---create-a-conditional-link"></a>Étape 9 - Création d’un lien conditionnel

Vous pouvez maintenant modifier le runbook afin qu’il tente de démarrer la machine virtuelle uniquement si elle n’est pas déjà démarrée. Pour ce faire, ajoutez une cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/Az.Compute/Get-AzVM?view=azps-3.5.0) qui récupère l’état de niveau d’instance de la machine virtuelle. Ensuite, vous pouvez ajouter un module de code Workflow PowerShell appelé **Obtenir l’état** avec un extrait de code PowerShell pour déterminer si l’état de la machine virtuelle est en cours d’exécution ou arrêté. Un lien conditionnel à partir du module **Obtenir l’état** exécute **Start-AzVM** uniquement si l’état d’exécution en cours est arrêté. À la fin de cette procédure, votre runbook utilise la cmdlet **Write-Output** pour générer un message vous indiquant si la machine virtuelle a été démarrée avec succès.

1. Ouvrez **MyFirstRunbook-Graphical** dans l’éditeur graphique.
1. Supprimez le lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Start-AzVM** en cliquant dessus, puis en appuyant sur **Supprimer**.
1. Dans la zone de recherche du contrôle Bibliothèque, saisissez **Get-Az**.
1. Ajoutez **Get-AzVM** au canevas.
1. Sélectionnez **Get-AzVM** puis **Jeu de paramètres** afin d’afficher les jeux pour la cmdlet. 
1. Sélectionnez le jeu de paramètres **GetVirtualMachineInResourceGroupNameParamSet** . Les champs **ResourceGroupName** et **Name** sont assortis de points d’exclamation pour indiquer qu’il s’agit de paramètres obligatoires. Notez que les deux champs attendent des valeurs de chaîne.
1. Pour le champ **Name** sous **Source de données**, sélectionnez **Entrée du Runbook** puis **VMName**. Cliquez sur **OK**.
1. Pour le champ **ResourceGroupName** sous **Source de données**, sélectionnez **Entrée de Runbook**, puis **ResourceGroupName**. Cliquez sur **OK**.
1. Pour le paramètre **Status** sous **Source de données**, sélectionnez **Valeur constante**, puis **True**. Cliquez sur **OK**.
1. Créez un lien entre **Specify Subscription Id** (Spécifier un ID d’abonnement) et **Get-AzVM**.
1. Dans le contrôle Bibliothèque, développez **Contrôle de Runbook**, puis ajoutez **Code** au canevas.  
1. Créez un lien entre **Get-AzVM** et **Code**.  
1. Cliquez sur **Code**, puis, dans le panneau Configuration, changez l’étiquette en **Obtenir l’état**.
1. Sélectionnez **Code** pour afficher la page Éditeur de code.  
1. Collez l’extrait de code suivant dans la page de l’éditeur.

    ```powershell-interactive
     $StatusesJson = $ActivityOutput['Get-AzVM'].StatusesText
     $Statuses = ConvertFrom-Json $StatusesJson
     $StatusOut =""
     foreach ($Status in $Statuses){
     if($Status.Code -eq "Powerstate/running"){$StatusOut = "running"}
     elseif ($Status.Code -eq "Powerstate/deallocated") {$StatusOut = "stopped"}
     }
     $StatusOut
     ```

1. Créez un lien entre **Obtenir l’état** et **Start-AzVM**.<br> ![Runbook avec module de code](media/automation-first-runbook-graphical/runbook-startvm-get-status.png)  
1. Sélectionnez le lien puis, dans le volet Configuration, changez **Appliquer la condition** en **Oui**. Notez que le lien se transforme en ligne pointillée, indiquant que l’activité cible s’exécute uniquement si la condition produit la valeur true.  
1. Pour **Expression de condition**, tapez `$ActivityOutput['Get Status'] -eq "Stopped"`. **Start-AzVM** ne s’exécute que si la machine virtuelle est arrêtée.
1. Dans le contrôle Bibliothèque, développez **Applets de commande**, puis **Microsoft.PowerShell.Utility**.
1. Ajoutez **Write-Output** au canevas à deux reprises.
1. Pour le premier contrôle **Write-Output**, cliquez sur **Paramètres**, puis changez la valeur de **Étiquette** en **Notify VM Started**.
1. Pour **InputObject**, définissez le champ **Source de données** sur **Expression PowerShell**, puis saisissez l’expression **$VMName successfully started**.
1. Sur le second contrôle **Write-Output**, cliquez sur **Paramètres**, puis changez la valeur de **Étiquette** en **Notify VM Start Failed**.
1. Pour **InputObject**, changez **Source de données** en **Expression PowerShell** puis saisissez l’expression **$VMName could not start**.
1. Créez des liens entre **Start-AzVM**, **Notify VM Started** et **Notify VM Start Failed**.
1. Sélectionnez le lien vers **Notify VM Started**, puis changez **Appliquer la condition** en True.
1. Pour **Expression de condition**, tapez `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Ce contrôle **Write-Output** s’exécute désormais uniquement si la machine virtuelle démarre correctement.
1. Sélectionnez le lien vers **Notify VM Start Failed** et changez **Appliquer la condition** en True.
1. Pour le champ **Expression de condition**, tapez `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Ce contrôle **Write-Output** s’exécute désormais uniquement si la machine virtuelle n’a pas démarré correctement. À ce stade, votre runbook doit ressembler à l’image suivante : <br> ![Runbook avec Write-Output](media/automation-first-runbook-graphical/runbook-startazurermvm-complete.png)
1. Enregistrez le Runbook et ouvrez le volet de test.
1. Démarrez le runbook avec la machine virtuelle arrêtée. La machine doit démarrer.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création graphique, consultez [Création de graphiques dans Azure Automation](automation-graphical-authoring-intro.md).
* Pour une prise en main des Runbooks PowerShell, consultez [Mon premier Runbook PowerShell](automation-first-runbook-textual-powershell.md).
* Pour une prise en main des runbooks PowerShell Workflow, consultez [Mon premier runbook PowerShell Workflow](automation-first-runbook-textual.md).