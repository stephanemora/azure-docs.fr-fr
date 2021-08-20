---
title: Créer un runbook graphique dans Azure Automation
description: Cet article vous apprend à créer, tester et publier un runbook graphique simple dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 07/16/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e782fd046af23beb5c84c8e419cd5925db7230ce
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449044"
---
# <a name="tutorial-create-a-graphical-runbook"></a>Tutoriel : Créer un runbook graphique

Ce didacticiel vous familiarise avec la création d’un [Runbook graphique](../automation-runbook-types.md#graphical-runbooks) dans Azure Automation. Vous pouvez créer et modifier des runbooks graphiques de workflow PowerShell avec l’éditeur graphique du Portail Azure.

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Créer un runbook graphique simple
> * Tester et publier le runbook
> * Exécuter le travail du runbook et en suivre l’état
> * Mettre à jour le runbook pour démarrer une machine virtuelle Azure avec des paramètres de runbook et des liens conditionnels

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis

* [Compte Automation](../index.yml) avec un [Compte d’identification Azure](../create-run-as-account.md) pour conserver le runbook et authentifier les ressources Azure. Ce compte doit avoir l’autorisation de démarrer et d’arrêter la machine virtuelle.
* Modules PowerShell **Az.accounts** et **Az.Compute** pour le compte Automation. Pour plus d’informations, consultez [Gestion des modules dans Azure Automation](../shared-resources/modules.md).
* Une [machine virtuelle Azure](../../virtual-machines/windows/quick-create-portal.md) (VM). Comme vous allez devoir l’arrêter et la démarrer, il ne doit pas s’agir d’une machine virtuelle de production. Commencez par **arrêter** la machine virtuelle.

## <a name="create-runbook"></a>Créer un runbook

Commencez par créer un runbook simple qui renvoie le texte `Hello World`.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).

1. Sur le Portail Azure, accédez à votre compte Automation.

1. Sous **Automation du processus**, cliquez sur **Runbooks** pour ouvrir la page **Runbooks**.

1. Sélectionnez **Créer un runbook** pour ouvrir la page **Créer un runbook**.

1. Nommez le runbook `MyFirstRunbook-Graphical`.

1. Dans le menu déroulant **Type de runbook**, sélectionnez **Graphique**.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/create-graphical-runbook.png" alt-text="Créez une page d’entrée du runbook.":::

1. Sélectionnez **Créer** pour créer le runbook et ouvrez l’éditeur graphique, la page **Modifier un runbook graphique**.

## <a name="add-activities"></a>Ajouter des activités

Le côté gauche de l’éditeur est le **contrôle des bibliothèques**. Le centre est les **Canevas**. La partie droite est le **Contrôle de configuration**. Le **Contrôle des bibliothèques** vous permet de sélectionner les activités à ajouter à votre runbook. Nous allons ajouter une cmdlet `Write-Output` pour extraire du texte du runbook.

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Write-Output`.

    ![Microsoft.PowerShell.Utility](../media/automation-tutorial-runbook-graphical/search-powershell-cmdlet-writeoutput.png)

1. Faites défiler jusqu'au bas de la liste. Cliquez avec le bouton droit sur **Write-Output**, puis sélectionnez **Ajouter au canevas**. Vous pouvez également sélectionner les points de suspension (...) en regard du nom de la cmdlet, puis sélectionner **Ajouter au canevas**.

1. Dans **Canevas**, sélectionnez l’activité **Écrire-Sortie**. Cette action permet d’alimenter la page **Contrôle de la configuration** pour configurer l’activité.

1. Dans **Contrôle de la configuration**, le champs par défaut **Étiquette** affiche le nom de la cmdlet, mais vous pouvez le modifier pour le rendre plus convivial. Remplacez-le par `Write Hello World to output`.

1. Sélectionnez **Paramètres** pour renseigner les valeurs des paramètres de la cmdlet.

   Certaines cmdlets comportent plusieurs jeux de paramètres, et vous devez choisir lequel utiliser. Dans ce cas, `Write-Output` ne possède qu'un seul jeu de paramètres.

1. Da,s la page **Configuration des paramètres de l’activité**, sélectionnez le paramètre `INPUTOBJECT` pour ouvrir la page **Valeur du paramètre**. Vous utilisez ce paramètre pour spécifier le texte à envoyer au flux de sortie.

1. Le menu déroulant **Source de données** affiche des sources que vous utilisez pour renseigner une valeur de paramètre. Dans ce menu, sélectionnez **Expression PowerShell**.

   Vous pouvez utiliser une sortie de ces sources, par exemple, une autre activité, une ressource Automation ou une expression PowerShell. Dans ce cas, la sortie est simplement `Hello World`. Vous pouvez utiliser une expression PowerShell et spécifier une chaîne.

1. Dans la zone de texte **Expression**, entrez `"Hello World"` puis sélectionnez deux fois **OK** pour revenir à l’éditeur graphique.

1. Sélectionnez **Enregistrer** pour enregistrer le runbook.

## <a name="test-the-runbook"></a>Tester le Runbook

Avant de publier le runbook pour le rendre disponible en production, vous devez le tester pour vous assurer qu’il fonctionne correctement. Le test d’un runbook exécute sa version Brouillon et vous permet d’afficher sa sortie de manière interactive.

1. Dans l’éditeur graphique, sélectionnez **Volet de test** pour ouvrir le volet **Test**.

1. Sélectionnez **Démarrer** pour démarrer le test.

   Une [tâche de runbook](../automation-runbook-execution.md) est créée et son état est affiché dans le volet. L’état du travail commence par `Queued`, ce qui indique qu’il attend qu’un Runbook Worker soit disponible dans le cloud. L’état passe ensuite à `Starting` lorsqu’un Worker revendique le travail. Enfin, l’état passe à `Running` lorsque le runbook commence à s’exécuter.

   Une fois le travail du runbook terminé, le volet Test affiche sa sortie. Dans ce cas, vous voyez `Hello World`.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-test-results.png" alt-text="Sortie de runbook Hello World.":::

1. Sélectionnez **X** dans l’angle supérieur droit pour fermer le volet **Test** et revenir à l’éditeur graphique.

## <a name="publish-and-start-the-runbook"></a>Publier et démarrer le Runbook

Le runbook que vous avez créé est toujours en mode Brouillon et doit être publié avant que vous puissiez l’exécuter en production. Lorsque vous publiez un Runbook, vous écrasez la version publiée existante par la version brouillon.

1. Dans la fenêtre graphique, sélectionnez l’option **Publier** pour publier le runbook, puis cliquez sur **Oui** quand vous y êtes invité. Vous êtes redirigé vers la page de présentation **Runbook**.

1. Dans la page de présentation **Runbook**, la valeur **État** est **publiée**.

1. Sélectionnez **Démarrer**, puis **Oui** quand vous y êtes invité pour démarrer le runbook et ouvrir la page **Tâche**.

   Les options en haut vous permettent de : démarrer le runbook, planifier une heure de démarrage prochain ou créer un [webhook](../automation-webhooks.md) pour démarrer le runbook via un appel HTTP.

   :::image type="content" source="../media/automation-tutorial-runbook-graphical/published-status.png" alt-text="Page de présentation et état publié.":::

1. Dans la page **Tâche**, vérifiez que le champ **État** affiche **Terminé**.

1. Sélectionnez **Sortie** pour afficher `Hello World`.

1. Sélectionnez **Tous les journaux** pour afficher les flux de la tâche runbook et sélectionnez la seule entrée pour ouvrir la page **Détails du flux de travail**. Vous ne devriez voir que `Hello World`.

    **Tous les journaux** peut afficher d’autres flux pour une tâche de runbook, tels que les flux Commentaires et Erreurs, si le runbook écrit des données dans ceux-ci.

1. Fermez la page **Détails du flux de travail**, puis la page **Tâche** pour revenir à la page de présentation **Runbook**.

1. Sous **Ressources**, sélectionnez **Tâches** pour afficher toutes les tâches du runbook. La page Travaux répertorie toutes les tâches créées par votre runbook. Vous ne devriez voir qu’une seule tâche, car vous n’avez exécuté le travail qu’une seule fois.

1. Sélectionnez la tâche pour ouvrir la même page **Tâche** que celle consultée au démarrage du runbook.

1. Fermez la page **Travail**, puis, dans le menu de gauche, sélectionnez **Vue d’ensemble**.

## <a name="create-variable-assets"></a>Créer des ressources de variables

Vous avez testé et publié votre runbook mais, jusqu’à présent, il ne fait rien d’utile pour gérer des ressources Azure. Avant de configurer le runbook pour l’authentification, vous devez créer une variable destinée à contenir l’ID d’abonnement, configurer une activité à authentifier, puis référencer la variable. L’ajout d’une référence au contexte de l’abonnement vous permet d’utiliser facilement plusieurs abonnements.

1. Dans **Vue d’ensemble**, sélectionnez l’icône **Copier dans le presse-papiers** pour l’**ID de l’abonnement**.

1. Fermez la page **Runbook** pour revenir à la page **Compte Automation**.

1. Sous **Ressources partagées**, sélectionnez **Variables**.

1. Sélectionnez **Ajouter une variable** pour ouvrir la page **Nouvelle variable**.

1. Dans la page **Nouvelle variable**, définissez les valeurs suivantes :

    | Champ| Valeur|
    |---|---|
    |Valeur|Appuyez sur <kbd>CTRL + V</kbd> pour coller votre ID d’abonnement.|
    |Nom |Entrez `AzureSubscriptionId`.|
    |Type|Conservez la valeur par défaut, **Chaîne**.|
    |Chiffré|Conservez la valeur par défaut, **Non**.|

1. Sélectionnez **Créer** pour créer la variable et revenir à la page **Variables**.

1. Sous **Automation de processus**, sélectionnez **Runbooks** , puis votre runbook, **MyFirstRunbook-Graphical**.

## <a name="add-authentication"></a>Ajouter une authentification

Maintenant que vous disposez d’une variable pouvant contenir l’ID d’abonnement, vous pouvez configurer le runbook pour l’authentification à l’aide des informations d’identification Exécuter en tant que de votre abonnement. Configurez l’authentification en ajoutant la Connexion d’identification Azure en tant que ressource. Vous devez également ajouter les cmdlets [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) et la cmdlet [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) au canevas.

> [!NOTE]
> Pour les runbooks PowerShell, `Add-AzAccount` et `Add-AzureRMAccount` sont des alias de `Connect-AzAccount`. Ces alias ne sont pas disponibles pour vos runbooks graphiques. Un runbook graphique peut uniquement utiliser `Connect-AzAccount`.

1. À partir de votre page **Runbook**, sélectionnez **Modifier** pour revenir à l’éditeur graphique.

1. Vous n’avez plus besoin de l'activité `Write Hello World to output`. Sélectionnez l’activité et les points de suspension s’affichent dans l’angle supérieur droit de l’activité. Les points de suspension peuvent être difficiles à voir. Sélectionnez les points de suspension, puis sélectionnez **Supprimer**.

1. Dans **Contrôle des bibliothèques**, accédez à **RESSOURCES** > **Connexions** > **AzureRunAsConnection**. Sélectionnez les points de suspension, puis sélectionnez **Ajouter au canevas**.

1. À partir du **Contrôle de la configuration**, remplacez la valeur de l'**étiquette**, `Get-AutomationConnection` par `Get Run As Connection`.

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Connect-AzAccount`.

1. Ajoutez `Connect-AzAccount` au canevas, puis faites glisser l’activité ci-dessous `Get Run As Connection`.

1. Pointez sur `Get Run As Connection` jusqu'à ce qu'un cercle apparaisse au bas de la forme. Sélectionnez le cercle et maintenez-le enfoncé. Une flèche s’affiche. Faites glisser la flèche vers `Connect-AzAccount` pour former un lien. Le runbook commence par `Get Run As Connection`, puis exécute `Connect-AzAccount`.

    ![Créer un lien entre des activités](../media/automation-tutorial-runbook-graphical/runbook-link-auth-activities.png)

1. Sur **Canevas**, sélectionnez `Connect-AzAccount`.

1. À partir du **Contrôle de la configuration**, remplacez la valeur de l'**étiquette**, `Connect-AzAccount` par `Login to Azure`.

1. Sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.

1. La cmdlet `Connect-AzAccount` possède plusieurs jeux de paramètres. Vous devez en sélectionner un avant de fournir des valeurs de paramètre. Sélectionnez **Jeu de paramètres**, puis sélectionnez **ServicePrincipalCertificateWithSubscriptionId**. Veillez à ne pas sélectionner **ServicePrincipalCertificateFileWithSubscriptionId**, car les noms sont similaires

   Les paramètres de ce jeu de paramètres apparaissent sur la page **Configuration des paramètres d’activité**.

    ![Ajouter des paramètres de compte Azure](../media/automation-tutorial-runbook-graphical/Add-AzureRmAccount-params.png)

1. Sélectionnez **CERTIFICATETHUMBPRINT** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **sortie de l’activité**.
    1. Dans **Sélectionner les données**, sélectionnez **Obtenir une connexion d’identification**.
    1. Dans la zone de texte **Chemin d’accès au champ**, entrez `CertificateThumbprint`.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **SERVICEPRINCIPAL** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Valeur des constantes**.
    1. Sélectionner l’option **True**.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **ABONNÉ** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **sortie de l’activité**.
    1. Dans **Sélectionner les données**, sélectionnez **Obtenir une connexion d’identification**.
    1. Dans la zone de texte **Chemin d’accès au champ**, entrez `TenantId`.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **APPLICATIONID** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **sortie de l’activité**.
    1. Dans **Sélectionner les données**, sélectionnez **Obtenir une connexion d’identification**.
    1. Dans la zone de texte **Chemin d’accès au champ**, entrez `ApplicationId`.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Set-AzContext`.

1. Ajoutez `Set-AzContext` au canevas, puis faites glisser l’activité ci-dessous `Login to Azure`.

1. À partir du **Contrôle de la configuration**, remplacez l'**étiquette**, `Set-AzContext` par `Specify Subscription Id`.

1. Sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.

1. La cmdlet `Set-AzContext` possède plusieurs jeux de paramètres. Vous devez en sélectionner un avant de fournir des valeurs de paramètre. Sélectionnez **Jeu de paramètres**, puis sélectionnez **Abonnement**. Les paramètres de ce jeu de paramètres apparaissent sur la page **Configuration des paramètres d’activité**.

1. Sélectionnez **ABONNEMENT** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Ressource des variables**.
    1. Dans la liste des variables, sélectionnez **AzureSubscriptionId**.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Former un lien entre `Login to Azure` et `Specify Subscription Id`. À ce stade, votre runbook doit être semblable au suivant.

    :::image type="content" source="../media/automation-tutorial-runbook-graphical/runbook-auth-config.png" alt-text="Capture d’écran du runbook après avoir fait glisser la flèche vers « Specify Subscription ID » (Spécifier un ID d’abonnement).":::

## <a name="add-activity-to-start-a-virtual-machine"></a>Ajouter une activité pour démarrer une machine virtuelle

Vous devez maintenant ajouter une activité `Start-AzVM` pour démarrer une machine virtuelle. Vous pouvez choisir toute machine virtuelle dans votre abonnement Azure. Pour l’instant, vous allez coder son nom en dur dans la cmdlet [Start-AzVM](/powershell/module/az.compute/start-azvm).

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Start-AzVM`.

1. Ajoutez `Start-AzVM` au canevas, puis faites glisser l’activité ci-dessous `Specify Subscription Id`.

1. Dans **Contrôle de la configuration**, sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.

1. Sélectionnez le **Jeu de paramètres** puis **ResourceGroupNameParameterSetName**. Les paramètres de ce jeu de paramètres apparaissent sur la page **Configuration des paramètres d’activité**. Les paramètres **RESOURCEGROUPNAME** et **NOM** contiennent des points d’exclamation pour indiquer qu’il s’agit de paramètres obligatoires. Les deux champs attendent des valeurs de chaîne.

1. Sélectionnez **RESOURCEGROUPNAME** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**.
    1. Dans la zone de texte **Expression**, entrez le nom de votre groupe de ressources entre guillemets.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **NOM** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**.
    1. Dans la zone de texte **Expression**, entrez le nom de votre machine virtuelle entre guillemets.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Formez un lien entre `Specify Subscription Id` et `Start-AzVM`. À ce stade, votre runbook doit être semblable au suivant.

    ![Runbook - Sortie de la commande Start-AzVM](../media/automation-tutorial-runbook-graphical/runbook-startvm.png)

1. Sélectionnez le **volet Test** afin de tester le runbook.

1. Sélectionnez **Démarrer** pour commencer le test.

1. Une fois le test terminé, assurez-vous que la machine virtuelle a démarré. Arrêtez ensuite la machine virtuelle pour les étapes ultérieures.

1. Revenez à l’éditeur graphique de votre runbook.

## <a name="add-input-parameters"></a>Ajouter des paramètres d'entrée

Votre runbook démarre actuellement la machine virtuelle dans le groupe de ressources que vous avez spécifié pour la cmdlet `Start-AzVM`. Le runbook est plus utile si vous spécifiez le nom et le groupe de ressources lors du démarrage du runbook. Ajoutons des paramètres d’entrée au runbook pour fournir cette fonctionnalité.

1. Dans la barre de menus supérieure de l’éditeur graphique, sélectionnez **Entrée et sortie**.

1. Sélectionnez **Entrée et sortie** pour ouvrir la page **Paramètre d’entrée du runbook**.

1. Sur la page **Paramètre d’entrée du runbook**, définissez les valeurs suivantes :

    | Champ| Valeur|
    |---|---|
    |Nom| Entrez `VMName`.|
    |Type|Conservez la valeur par défaut, **Chaîne**.|
    |Obligatoire|Remplacez la valeur par **Oui**.|

1. Sélectionnez **OK** pour revenir à la page **Entrée et sortie**

1. Sélectionnez **Ajouter une entrée** pour rouvrir la page **Paramètre d’entrée du runbook**.

1. Sur la page **Paramètre d’entrée du runbook**, définissez les valeurs suivantes :

    | Champ| Valeur|
    |---|---|
    |Nom| Entrez `ResourceGroupName`.|
    |Type|Conservez la valeur par défaut, **Chaîne**.|
    |Obligatoire|Remplacez la valeur par **Oui**.|

1. Sélectionnez **OK** pour revenir à la page **Entrée et sortie**. La page peut ressembler à ce qui suit :

    ![Paramètres d'entrée de Runbook](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-outputs.png)

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Les nouvelles entrées peuvent ne pas être immédiatement disponibles. Sélectionnez **Enregistrer**, fermez l’éditeur graphique, puis rouvrez l’éditeur graphique. Les nouvelles entrées doivent maintenant être disponibles.

1. Sélectionnez l’activité `Start-AzVM`, puis **Paramètres** pour ouvrir la page **Configuration des paramètres de l’activité**.

1. Pour le paramètre précédemment configuré, **RESOURCEGROUPNAME**, modifiez la **Source de données** par **Entrée de runbook**, puis sélectionnez **ResourceGroupName**. Sélectionnez **OK**.

1. Pour le paramètre précédemment configuré, **NOM**, modifiez la **Source de données** par **Entrée de runbook**, puis sélectionnez **VMName**. Sélectionnez **OK**. La page peut ressembler à ce qui suit :

    ![Paramètres Start-AzVM](../media/automation-tutorial-runbook-graphical/start-azurermvm-params-runbookinput.png)

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Sélectionnez **Enregistrer**, puis **volet Test**. Vous pouvez désormais fournir des valeurs pour les deux variables d’entrée que vous avez créées.

1. Fermez la page **Test** pour revenir à l’éditeur graphique.

1. Sélectionnez **Publier**, puis **Oui** lorsque vous êtes invité à publier la nouvelle version du runbook. Vous êtes redirigé vers la page de présentation **Runbook**.

1. Sélectionnez **Démarrer** pour ouvrir la page **Démarrer le Runbook**.

1. Entrez les valeurs appropriées pour ces paramètres `VMNAME` et `RESOURCEGROUPNAME`. Sélectionnez ensuite **OK**. La page **Travail** s’ouvre alors.

1. Analysez le travail et vérifiez que la machine virtuelle a démarré une fois l'**État** sur **Terminé**. Arrêtez ensuite la machine virtuelle pour les étapes ultérieures.

1. Revenez à l’éditeur graphique de votre runbook.

## <a name="create-a-conditional-link"></a>Créer un lien conditionnel

Vous pouvez maintenant modifier le runbook afin qu’il tente de démarrer la machine virtuelle uniquement si elle n’est pas déjà démarrée. Pour ce faire, ajoutez une cmdlet [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) qui récupère l’état de niveau d’instance de la machine virtuelle. Vous pouvez ensuite ajouter un module de code Workflow PowerShell appelé `Get Status` avec un extrait de code PowerShell pour déterminer l'état de la machine virtuelle (En cours d'exécution ou Arrêté). Un lien conditionnel à partir du module `Get Status` exécute `Start-AzVM` uniquement si l'état d'exécution en cours est Arrêté. À la fin de cette procédure, votre runbook utilise la cmdlet `Write-Output` pour générer un message vous indiquant si la machine virtuelle a été démarrée avec succès.

1. Dans l’éditeur graphique, cliquez avec le bouton de droite sur le lien entre `Specify Subscription Id` et `Start-AzVM` puis sélectionnez **Supprimer**.

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Get-AzVM`.

1. Ajoutez `Get-AzVM` au canevas, puis faites glisser l’activité ci-dessous `Specify Subscription Id`.

1. Dans **Contrôle de la configuration**, sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.

   Sélectionnez **Jeu de paramètres**, puis sélectionnez **GetVirtualMachineInResourceGroupParamSet**. Les paramètres de ce jeu de paramètres apparaissent sur la page **Configuration des paramètres d’activité**. Les paramètres **RESOURCEGROUPNAME** et **NOM** contiennent des points d’exclamation pour indiquer qu’il s’agit de paramètres obligatoires. Les deux champs attendent des valeurs de chaîne.

1. Sélectionnez **RESOURCEGROUPNAME** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Entrée du runbook**.
    1. Sélectionnez le paramètre **ResourceGroupName**.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **NOM** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Entrée du runbook**.
    1. Sélectionnez le paramètre **VMName**.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **ÉTAT** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Valeur des constantes**.
    1. Sélectionner l’option **True**.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Formez un lien entre `Specify Subscription Id` et `Get-AzVM`.

1. Effacez le champ de recherche **Contrôle des bibliothèques**, puis accédez à **CONTRÔLE DU RUNBOOK** > **Code**. Sélectionnez les points de suspension, puis **Ajouter au canevas**. Faites glisser l’activité ci-dessous `Get-AzVM`.

1. À partir du **Contrôle de la configuration**, remplacez l'**étiquette**, `Code` par `Get Status`.

1. Dans **Contrôle de la configuration**, sélectionnez **Code** pour ouvrir la page **Éditeur de code**.

1. Collez l’extrait de code suivant dans la zone de texte **Code PowerShell**.

    ```powershell
    $Statuses = $ActivityOutput['Get-AzVM'].Statuses
    $StatusOut = ""
    foreach ($Status in $Statuses) {
      if($Status.Code -eq "Powerstate/running")
        {$StatusOut = "running"}
      elseif ($Status.Code -eq "Powerstate/deallocated")
        {$StatusOut = "stopped"}
    }
    $StatusOut
    ```

1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Formez un lien entre `Get-AzVM` et `Get Status`.

1. Formez un lien entre `Get Status` et `Start-AzVM`. À ce stade, votre runbook doit être semblable au suivant.

    ![Runbook avec module de code](../media/automation-tutorial-runbook-graphical/runbook-startvm-get-status.png)  

1. Sélectionnez le lien entre `Get Status` et `Start-AzVM`.

1. Dans **Contrôle de la configuration**, passez **Appliquer la condition** sur **Oui**. Le lien se transforme en ligne pointillée, indiquant que l’activité cible s’exécute uniquement si la condition est résolue sur true.  

1. Pour **Expression de condition**, tapez `$ActivityOutput['Get Status'] -eq "Stopped"`. Désormais, `Start-AzVM` ne s'exécute que si la machine virtuelle est arrêtée.

1. Dans la zone de recherche **Contrôle des bibliothèques**, saisissez `Write-Output`.

1. Ajoutez `Write-Output` au canevas, puis faites glisser l’activité ci-dessous `Start-AzVM`.

1. Sélectionnez les points de suspension de l’activité et sélectionnez **Dupliquer**. Faites glisser l’activité dupliquée à droite de la première activité.

1. Sélectionnez la première activité `Write-Output`.
    1. À partir du **Contrôle de la configuration**, remplacez l’**étiquette**, `Write-Output` par `Notify VM Started`.
    1. Sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.
    1. Sélectionnez **INPUTOBJECT** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**.
    1. Dans la zone de texte **Expression**, entrez `"$VMName successfully started."`.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.
    1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Sélectionnez la première activité `Write-Output1`.
    1. À partir du **Contrôle de la configuration**, remplacez l’**étiquette**, `Write-Output1` par `Notify VM Start Failed`.
    1. Sélectionnez **Paramètres** pour ouvrir la page **Configuration des paramètres d’activité**.
    1. Sélectionnez **INPUTOBJECT** pour ouvrir la page **Valeur du paramètre**.
    1. Dans le menu déroulant **Source de données**, sélectionnez **Expression PowerShell**.
    1. Dans la zone de texte **Expression**, entrez `"$VMName could not start."`.
    1. Sélectionnez **OK** pour retourner à la page **Configuration des paramètres d’activité**.
    1. Sélectionnez **OK** pour revenir à l’éditeur graphique.

1. Formez un lien entre `Start-AzVM` et `Notify VM Started`.

1. Formez un lien entre `Start-AzVM` et `Notify VM Start Failed`.

1. Sélectionnez le lien vers `Notify VM Started`, puis remplacez **Appliquer la condition** par **Oui**.

1. Pour **Expression de condition**, tapez `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -eq $true`. Ce contrôle `Write-Output` ne s'exécute désormais que si la machine virtuelle démarre correctement.

1. Sélectionnez le lien sur `Notify VM Start Failed`.

1. Dans la **page Contrôle**, pour **Appliquer la condition**, sélectionnez **Oui**.

1. Dans la zone de texte **Expression des conditions**, entrez `$ActivityOutput['Start-AzVM'].IsSuccessStatusCode -ne $true`. Ce contrôle `Write-Output` ne s'exécute désormais que si la machine virtuelle ne démarre pas correctement. À ce stade, votre runbook doit être semblable à l'image suivante.

    ![Runbook avec Write-Output](../media/automation-tutorial-runbook-graphical/runbook-startazurermvm-complete.png)

1. Enregistrez le Runbook et ouvrez le volet de test.

1. Démarrez le runbook avec la machine virtuelle arrêtée. La machine doit démarrer.

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur la création graphique, consultez [Créer un runbook graphique dans Azure Automation](../automation-graphical-authoring-intro.md).
* Pour démarrer avec les runbooks PowerShell, consultez [Créer un runbook PowerShell](automation-tutorial-runbook-textual-powershell.md).
* Pour démarrer avec les runbooks de workflow PowerShell, consultez [Créer un runbook de workflow PowerShell](automation-tutorial-runbook-textual.md).
* Pour obtenir des informations de référence sur les applets de commande PowerShell, consultez [Az.Automation](/powershell/module/az.automation).
