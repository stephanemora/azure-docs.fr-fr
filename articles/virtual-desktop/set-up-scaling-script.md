---
title: Procéder à la mise à l'échelle des hôtes de session à l'aide d'Azure Automation - Azure
description: Mise à l'échelle automatique des hôtes de session Windows Virtual Desktop à l'aide d'Azure Automation
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: helohr
ms.openlocfilehash: c201df03bb156bac3f63d03cc4ca35215792f65c
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061494"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Procéder à la mise à l'échelle des hôtes de session à l'aide d'Azure Automation

Vous pouvez réduire le coût total de déploiement de Windows Virtual Desktop en procédant à la mise à l'échelle de vos machines virtuelles. Cela implique l'arrêt et la libération des machines virtuelles hôtes de session aux heures creuses, puis leur réactivation et leur réallocation aux heures de pointe.

Cet article présente l'outil de mise à l'échelle créé avec Azure Automation et Azure Logic Apps pour procéder à la mise à l'échelle automatique des machines virtuelles hôtes de session dans votre environnement Windows Virtual Desktop. Pour en savoir plus sur l'utilisation de l'outil de mise à l'échelle, consultez la section [Conditions préalables](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Fonctionnement de l'outil de mise à l'échelle

L'outil de mise à l'échelle fournit une option d'automatisation à faible coût pour les clients qui souhaitent optimiser les coûts de leurs machines virtuelles hôtes de session.

Vous pouvez utiliser l'outil de mise à l'échelle pour :
 
- Planifier le démarrage et l'arrêt des machines virtuelles en fonction des heures de pointe et des heures creuses
- Monter les machines virtuelles en charge en fonction du nombre de sessions par cœur de processeur
- Diminuer la taille des instances de machines virtuelles pendant les heures creuses, en continuant à exécuter le nombre minimum de machines virtuelles hôtes de session

L'outil de mise à l'échelle fonctionne à l'aide de runbooks PowerShell Azure Automation, de webhooks et d'Azure Logic Apps. Lorsque l'outil s'exécute, Azure Logic Apps appelle un webhook pour lancer le runbook Azure Automation. Le runbook crée ensuite un travail.

Aux heures de pointe, le travail vérifie le nombre de sessions actives et la capacité en machines virtuelles de l'hôte de session en cours d'exécution pour chaque pool d'hôtes. Il utilise ces informations pour déterminer si les machines virtuelles hôtes de session en cours d'exécution sont en mesure de prendre en charge les sessions existantes en fonction du paramètre *SessionThresholdPerCPU* défini dans le fichier **createazurelogicapp.ps1**. Si les machines virtuelles hôtes de session ne peuvent pas prendre en charge les sessions existantes, le travail démarre des machines virtuelles hôtes de session supplémentaires dans le pool d'hôtes.

>[!NOTE]
>*SessionThresholdPerCPU* ne limite pas le nombre de sessions sur la machine virtuelle. Ce paramètre détermine uniquement quand de nouvelles machines virtuelles doivent être démarrées pour équilibrer la charge des connexions. Pour limiter le nombre de sessions, vous devez suivre les instructions [Set-RdsHostPool](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/set-rdshostpool) et configurer le paramètre *MaxSessionLimit* en conséquence.

Aux heures creuses, le travail détermine quelles machines virtuelles hôtes de session doivent être arrêtées, conformément au paramètre *MinimumNumberOfRDSH*. Le travail configure les machines virtuelles hôtes de session sur le mode de drainage pour empêcher les nouvelles sessions de se connecter aux hôtes. Si vous définissez le paramètre *LimitSecondsToForceLogOffUser* sur une valeur positive différente de zéro, le script envoie une notification aux utilisateurs actuellement connectés pour leur demander d'enregistrer leur travail, attend le laps de temps configuré, puis force les utilisateurs à se déconnecter. Une fois que toutes les sessions utilisateur ont été déconnectées sur la machine virtuelle hôte de session, le script arrête la machine virtuelle.

Si vous définissez le paramètre *LimitSecondsToForceLogOffUser* sur zéro, le travail autorise le paramètre de configuration de session des stratégies de groupe spécifiées à gérer la déconnexion des sessions utilisateur. Pour afficher ces stratégies de groupe, accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d'administration** > **Composants Windows** > **Services du terminal** > **Terminal Server** > **Délais d'expiration des sessions**. S'il reste des sessions en cours sur une machine virtuelle hôte de session, le travail laisse la machine virtuelle hôte de session s'exécuter. S'il ne reste aucune session en cours, le travail arrête la machine virtuelle hôte de session.

Le travail s'exécute périodiquement sur la base d'un intervalle de périodicité défini. Vous pouvez modifier cet intervalle en fonction de la taille de votre environnement Windows Virtual Desktop, mais n'oubliez pas que le démarrage et l'arrêt des machines virtuelles peuvent prendre un certain temps ; vous devez donc tenir compte de ce délai. Nous vous recommandons de définir l'intervalle de périodicité sur : Toutes les 15 minutes.

Cela dit, l'outil présente également les limitations suivantes :

- Cette solution s'applique uniquement aux machines virtuelles hôtes de session mises en pool.
- Cette solution permet de gérer les machines virtuelles de toutes les régions, mais ne peut être utilisée qu'au sein du même abonnement que votre compte Azure Automation et Azure Logic Apps.

>[!NOTE]
>L'outil de mise à l'échelle contrôle le mode d'équilibrage de charge du pool d'hôtes qu'il met à l'échelle. Il le définit sur l'équilibrage de charge à largeur prioritaire pour les heures de pointe et les heures creuses.

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer à configurer l'outil de mise à l'échelle, assurez-vous que vous disposez de ce qui suit :

- Un [locataire Windows Virtual Desktop et un pool d'hôtes](create-host-pools-arm-template.md)
- Machines virtuelles du pool d'hôtes de session configurées et inscrites auprès du service Windows Virtual Desktop
- Un utilisateur doté d'un [accès Contributeur](../role-based-access-control/role-assignments-portal.md) sur un abonnement Azure

L'ordinateur que vous utilisez pour déployer l'outil doit disposer de ce qui suit : 

- Windows PowerShell 5.1 ou version ultérieure
- Module Microsoft AZ PowerShell

Si tout est prêt, commençons.

## <a name="create-an-azure-automation-account"></a>Créer un compte Azure Automation

Tout d'abord, vous devez disposer d'un compte Azure Automation pour exécuter le runbook PowerShell. Pour configurer votre compte, procédez comme suit :

1. Ouvrez Windows PowerShell en tant qu’administrateur.
2. Exécutez la cmdlet suivante pour vous connecter à votre compte Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >Votre compte doit disposer de droits de contributeur sur l'abonnement Azure au sein duquel vous souhaitez déployer l'outil de mise à l'échelle.

3. Exécutez la cmdlet suivante pour télécharger le script de création du compte Azure Automation :

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1" -OutFile "your local machine path\ createazureautomationaccount.ps1"
     ```

4. Exécutez la cmdlet suivante pour exécuter le script et créer le compte Azure Automation :

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. La sortie de la cmdlet inclura un URI de webhook. Conservez un enregistrement de l'URI car vous l'utiliserez comme paramètre lors de la configuration du calendrier d'exécution pour les applications logiques Azure.

Après avoir configuré votre compte Azure Automation, connectez-vous à votre abonnement Azure et vérifiez que votre compte Azure Automation et le runbook correspondant apparaissent dans le groupe de ressources spécifié, comme illustré ci-dessous :

![Illustration de la page de présentation Azure affichant le compte d'automatisation et le runbook nouvellement créés.](media/automation-account.png)

Pour vérifier que votre webhook est bien à sa place, accédez à la liste des ressources sur le côté gauche de votre écran et sélectionnez **Webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Créer un compte d'identification Azure Automation

Maintenant que vous disposez d'un compte Azure Automation, vous devez également créer un compte d'identification Azure Automation pour accéder à vos ressources Azure.

Un [compte d'identification Azure Automation](../automation/manage-runas-account.md) fournit l'authentification nécessaire à la gestion des ressources Azure à l'aide de cmdlets Azure. Lorsque vous créez un compte d'identification, celui-ci crée un nouvel utilisateur de principal de service dans Azure Active Directory et attribue le rôle de contributeur à cet utilisateur au niveau de l'abonnement. Le compte d'identification Azure permet de s'authentifier en toute sécurité à l'aide de certificats et d'un nom de principal de service sans qu'il soit nécessaire de stocker un nom d'utilisateur et un mot de passe dans un objet d'informations d'identification. Pour en savoir plus sur l'authentification d'identification, consultez [Limiter les autorisations d'un compte d'identification](../automation/manage-runas-account.md#limiting-run-as-account-permissions).

Tout utilisateur membre du rôle Administrateurs des abonnements et coadministrateur de l'abonnement peut créer un compte d'identification en suivant les instructions de la section suivante.

Pour créer un compte d'identification sur votre compte Azure :

1. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste des ressources, entrez et sélectionnez **Comptes Automation**.

2. Sur la page **Comptes Automation**, sélectionnez le nom de votre compte Automation.

3. Dans le volet situé à gauche de la fenêtre, sélectionnez **Comptes d'identification** sous la section Paramètres du compte.

4. Sélectionnez **Compte d'identification Azure**. Lorsque le volet **Ajouter un compte d'identification Azure** apparaît, passez en revue les informations de présentation, puis sélectionnez **Créer** pour lancer le processus de création de compte.

5. Patientez quelques minutes, le temps qu'Azure crée le compte d'identification. Vous pouvez suivre la progression de la création dans le menu situé sous Notifications.

6. Au terme du processus, une ressource nommée AzureRunAsConnection est créée sur le compte Automation spécifié. La ressource de connexion contient l'ID de l'application, l'ID du locataire, l'ID de l'abonnement et l'empreinte du certificat. Mémorisez l'ID de l'application car vous en aurez besoin plus tard.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Créer une attribution de rôle dans Windows Virtual Desktop

Vous devez ensuite créer une attribution de rôle pour permettre à AzureRunAsConnection d'interagir avec Windows Virtual Desktop. Veillez à utiliser PowerShell afin de vous connecter avec un compte qui dispose des autorisations nécessaires pour créer des attributions de rôle.

Si ce n'est déjà fait, commencez par télécharger et importer le [module PowerShell Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) à utiliser dans votre session PowerShell. Exécutez les applets de commande PowerShell suivantes pour vous connecter à Windows Virtual Desktop et afficher vos locataires.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Lorsque vous trouvez le locataire contenant les pools d'hôtes que vous souhaitez mettre à l'échelle, suivez les instructions de la section [Créer un compte Azure Automation](#create-an-azure-automation-account) et utilisez le nom du locataire que vous avez obtenu à l'aide de la cmdlet précédente dans la cmdlet suivante pour créer l'attribution de rôle :

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Créer l'application logique Azure et le calendrier d'exécution

Enfin, vous devrez créer l'application logique Azure et configurer un calendrier d'exécution pour votre nouvel outil de mise à l'échelle.

1.  Ouvrez Windows PowerShell en tant qu'Administrateur.

2.  Exécutez la cmdlet suivante pour vous connecter à votre compte Azure.

     ```powershell
     Login-AzAccount
     ```

3. Exécutez la cmdlet suivante pour télécharger le fichier de script createazurelogicapp.ps1 sur votre ordinateur local.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Exécutez la cmdlet suivante pour vous connecter à Windows Virtual Desktop avec un compte disposant des autorisations de Propriétaire ou de Contributeur des Services Bureau à distance.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Exécutez le script PowerShell suivant pour créer l'application logique Azure et le calendrier d'exécution.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you’d like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you’d like the job to run in minutes, e.g. ‘15’"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don’t want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Une fois le script exécuté, l'application logique doit apparaître dans un groupe de ressources, comme illustré ci-dessous.

     ![Illustration de la page de présentation d'un exemple d'application logique Azure.](media/logic-app.png)

Pour apporter des modifications au calendrier d'exécution, telles que la modification de l'intervalle de périodicité ou du fuseau horaire, accédez au planificateur de mise à l'échelle automatique et sélectionnez **Modifier** pour accéder au concepteur d'applications logiques.

![Illustration du concepteur d'applications logiques. Les menus Périodicité et Webhook qui permettent à l'utilisateur de modifier la périodicité et le fichier webhook sont ouverts.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gérer votre outil de mise à l'échelle

Maintenant que vous avez créé votre outil de mise à l'échelle, vous pouvez accéder à sa sortie. Cette section décrit quelques fonctionnalités qui pourraient vous être utiles.

### <a name="view-job-status"></a>Afficher le statut de la tâche

Vous pouvez afficher un résumé de l'état de tous les travaux du runbook ou afficher un état plus approfondi d'un travail spécifique sur le portail Azure.

À droite du compte Automation sélectionné, sous « Statistiques des travaux », vous pouvez afficher la liste des résumés de tous les travaux du runbook. Ouvrez la page **Travaux** sur le côté gauche de la fenêtre pour afficher l'état actuel des travaux, ainsi que les heures de début et de fin de ceux-ci.

![Capture d'écran de la page d'état des travaux.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Afficher les journaux et la sortie de l'outil de mise à l'échelle

Vous pouvez consulter les journaux des opérations de « scale-out » et de « scale-in » en ouvrant votre runbook et en sélectionnant le nom de votre travail.

Accédez au runbook (le nom par défaut est WVDAutoScaleRunbook) du groupe de ressources qui héberge le compte Azure Automation et sélectionnez **Présentation**. Sur la page de présentation, sélectionnez un travail sous Travaux récents pour afficher la sortie de son outil de mise à l'échelle, comme illustré ci-dessous.

![Illustration de la fenêtre de sortie de l'outil de mise à l'échelle.](media/tool-output.png)
