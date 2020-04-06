---
title: Intégration du contrôle de code source dans Azure Automation
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132928"
---
# <a name="source-control-integration-in-azure-automation"></a>Intégration du contrôle de code source dans Azure Automation

 L’intégration du contrôle de code source à Azure Automation vous permet d’effectuer des synchronisations unidirectionnelles à partir de votre dépôt de contrôle de code source. Avec le contrôle de code source, vous avez la garantie que les runbooks de votre compte Automation sont à jour par rapport aux scripts de votre dépôt de contrôle de code source GitHub ou Azure Repos. Cette fonctionnalité simplifie la promotion du code qui a été testé dans votre environnement de développement dans votre compte Automation de production.
 
 L’intégration du contrôle de code source vous permet de facilement collaborer avec votre équipe, suivre les modifications et restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches dans le contrôle de code source avec vos comptes Automation de développement, de test et de production. 

>[!NOTE]
>Cet article a été mis à jour pour tenir compte de l’utilisation du nouveau module Az d’Azure PowerShell. Vous pouvez toujours utiliser le module AzureRM, qui continue à recevoir des correctifs de bogues jusqu’à au moins décembre 2020. Pour en savoir plus sur le nouveau module Az et la compatibilité avec AzureRM, consultez [Présentation du nouveau module Az d’Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pour obtenir des instructions relatives à l’installation du module Az sur votre Runbook Worker hybride, voir [Installer le module Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pour votre compte Automation, vous pouvez mettre à jour vos modules vers la dernière version en suivant les instructions du [Guide de mise à jour des modules Azure PowerShell dans Azure Automation](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Types de contrôle de code source

Azure Automation prend en charge trois types de contrôles de code source :

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>Prérequis

* Un dépôt de contrôle de code source (GitHub ou Azure Repos)
* Un [compte d’identification](manage-runas-account.md)
* Les [derniers modules Azure](automation-update-azure-modules.md) dans votre compte Automation, notamment le module `Az.Accounts` (module Az équivalant à `AzureRM.Profile`)

> [!NOTE]
> Les travaux de synchronisation du contrôle de code source sont exécutés sous le compte Automation de l’utilisateur et sont facturés au même tarif que les autres tâches Automation.

## <a name="configuring-source-control"></a>Configuration du contrôle de code source

Cette section explique comment configurer le contrôle de code source pour votre compte Automation. Vous pouvez utiliser le portail Azure ou PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Configurer le contrôle de code source dans le portail Azure

Utilisez cette procédure pour configurer le contrôle de code source à l’aide du portail Azure.

1. Dans votre compte Automation, sélectionnez **Contrôle de code source**, puis cliquez sur **Ajouter**.

    ![Sélection du contrôle de code source](./media/source-control-integration/select-source-control.png)

2. Choisissez **Type de contrôle de code source**, puis cliquez sur **Authentifier**. 

3. Une fenêtre de navigateur s’ouvre et vous invite à vous connecter. Suivez les invites pour effectuer l’authentification.

4. Dans la page Récapitulatif du contrôle de code source, utilisez les champs pour renseigner les propriétés du contrôle de code source définies ci-dessous. Lorsque vous avez terminé, cliquez sur **Enregistrer**. 

    |Propriété  |Description  |
    |---------|---------|
    |Nom du contrôle de code source     | Nom convivial du contrôle de code source. Ce nom ne doit contenir que des lettres et des chiffres.        |
    |Type de contrôle de code source     | Type de mécanisme de contrôle de code source. Options disponibles :</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Référentiel     | Nom du dépôt ou du projet. Les 200 premiers dépôts sont retournés. Pour rechercher un dépôt, tapez son nom dans le champ, puis cliquez sur **Rechercher sur GitHub**.|
    |Branche     | Branche à partir de laquelle extraire les fichiers sources. Le ciblage de branche n’est pas disponible pour le contrôle de code source de type TFVC.          |
    |Chemin d’accès du dossier     | Dossier qui contient les runbooks à synchroniser, par exemple, **/Runbooks**. Seuls les runbooks inclus dans le dossier spécifié sont synchronisés. La récursivité n’est pas prise en charge.        |
    |Synchronisation automatique<sup>1</sup>     | Paramètre qui active ou désactive la synchronisation automatique lorsqu’une validation est effectuée dans le dépôt de contrôle de code source.        |
    |Publier un runbook     | Paramètre activé si les runbooks sont automatiquement publiés après la synchronisation à partir du contrôle de code source, paramètre désactivé dans le cas contraire.           |
    |Description     | Texte spécifiant des détails supplémentaires sur le contrôle de code source.        |

    <sup>1</sup> Pour activer la synchronisation automatique lorsque vous configurez l’intégration du contrôle de code source dans les dépôts Azure, vous devez être l’administrateur du projet.

   ![Récapitulatif du contrôle de code source](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Les informations de connexion de votre dépôt de contrôle de code source peuvent être différentes de celles que vous utilisez pour le portail Azure. Lorsque vous configurez le contrôle de code source, vérifiez que vous êtes bien connecté au compte qui correspond à votre dépôt de contrôle de code source. En cas de doute, ouvrez un nouvel onglet dans votre navigateur, déconnectez-vous de **dev.azure.com**, **visualstudio.com** ou de **github.com** et essayez de vous reconnecter au contrôle de code source.

### <a name="configure-source-control-in-powershell"></a>Configurer le contrôle de code source dans PowerShell

Vous pouvez également utiliser PowerShell pour configurer le contrôle de code source dans Azure Automation. Pour utiliser des applets de commande PowerShell pour cette opération, vous avez besoin d’un jeton d’accès personnel (PAT). Pour créer la connexion de contrôle de code source, utilisez l’applet de commande [New-AzAutomationSourceControl](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
). Cette applet de commande exige une chaîne sécurisée pour le PAT. Pour savoir comment créer une chaîne sécurisée, consultez [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

Les sous-sections suivantes illustrent la création par PowerShell de la connexion de contrôle de code source pour GitHub, Azure Repos (Git) et Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Créer une connexion de contrôle de code source pour GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Créer une connexion de contrôle de code source pour Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) utilise une URL qui accède à  **dev.azure.com** au lieu de **visualstudio.com**, utilisé dans des formats antérieurs. L’ancien format d’URL `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` n’est plus utilisé, mais il demeure pris en charge. Le nouveau format est préféré.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Créer une connexion de contrôle de code source pour Azure Repos (TFVC)

> [!NOTE]
> Azure Repos (TFVC) utilise une URL qui accède à  **dev.azure.com** au lieu de **visualstudio.com**, utilisé dans des formats antérieurs. L’ancien format d’URL `https://<accountname>.visualstudio.com/<projectname>/_versionControl` n’est plus utilisé, mais il demeure pris en charge. Le nouveau format est préféré.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Autorisations de jeton d’accès personnel (PAT)

Le contrôle de code source exige des autorisations minimales pour les PAT. Les sous-sections suivantes indiquent les autorisations minimales nécessaires pour accéder à GitHub et à Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Autorisations PAT minimales pour GitHub

Le tableau suivant définit les autorisations PAT minimales nécessaires pour GitHub. Pour plus d’informations sur la création d’un PAT dans GitHub, consultez [Création d’un jeton d’accès personnel pour la ligne de commande](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Étendue  |Description  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | État de la validation d’accès         |
|`repo_deployment`      | État du déploiement d’accès         |
|`public_repo`     | Référentiels publics d’accès         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Écrire des hooks de référentiel         |
|`read:repo_hook`|Lire des hooks de référentiel|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Autorisations PAT minimales pour Azure Repos

La liste suivante définit les autorisations PAT minimales nécessaires pour Azure Repos. Pour plus d’informations sur la création d’un PAT dans Azure Repos, consultez [Authentifier l’accès à l’aide de jetons d’accès personnels](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Étendue  |  Type d’accès  |
|---------| ----------|
| `Code`      | Lire  |
| `Project and team` | Lire |
| `Identity` | Lire     |
| `User profile` | Lire     |
| `Work items` | Lire    |
| `Service connections` | Lire, interroger, gérer<sup>1</sup>    |

<sup>1</sup> L’autorisation `Service connections` est uniquement nécessaire si vous avez activé la synchronisation automatique.

## <a name="synchronizing"></a>Synchronisation

Suivez ces étapes pour effectuer une synchronisation avec le contrôle de code source. 

1. Sélectionnez la source dans le tableau de la page Contrôle de code source. 

2. Cliquez sur **Démarrer la synchronisation** pour initialiser le processus de synchronisation. 

3. Visualisez l’état du travail de synchronisation actuel ou des travaux précédents en cliquant sur l’onglet **Travaux de synchronisation**. 

4. Dans le menu déroulant **Contrôle de code source**, sélectionnez un mécanisme de contrôle de code source.

    ![État de synchronisation](./media/source-control-integration/sync-status.png)

5. Vous pouvez cliquer sur un travail afin de visualiser la sortie correspondante. L’exemple suivant présente la sortie d’un travail de synchronisation du contrôle de code source.

    ```output
    ===================================================================

    Azure Automation Source Control.
    Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

    Setting AzureRmEnvironment.

    Getting AzureRunAsConnection.

    Logging in to Azure...

    Source control information for syncing:

    [Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

    Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

    Connecting to VSTS...

    Source Control Sync Summary:

    2 files synced:
     - ExampleRunbook1.ps1
     - ExampleRunbook2.ps1

    ==================================================================

    ```

6. Vous pouvez disposer d’une journalisation supplémentaire en sélectionnant **Tous les journaux** dans la page Récapitulatif du travail de synchronisation du contrôle de code source. Ces entrées de journal supplémentaires peuvent vous aider à résoudre les problèmes qui peuvent survenir lorsque vous utilisez le contrôle de code source.

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter d’un dépôt de contrôle de code source :

1. Ouvrez **Contrôle de code source** sous **Paramètres du compte** dans votre compte Automation.

2. Sélectionnez le mécanisme de contrôle de code source à supprimer. 

3. Dans la page Récapitulatif du contrôle de code source, cliquez sur **Supprimer**.

## <a name="handling-encoding-issues"></a>Gestion des problèmes d’encodage

Si plusieurs personnes modifient des runbooks dans votre dépôt de contrôle de code source à l’aide de différents éditeurs, des problèmes d’encodage peuvent se produire. Pour plus d’informations sur cette situation, consultez [Causes courantes des problèmes d’encodage](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Mise à jour du PAT

Actuellement, vous ne pouvez pas utiliser le portail Azure pour mettre à jour le PAT dans le contrôle de code source. Lorsque votre PAT a expiré ou a été révoqué, vous pouvez mettre à jour le contrôle de code source avec un nouveau jeton d’accès de l’une des manières suivantes :

* Utilisez l’[API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Utilisez l’applet de commande [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur les types de runbooks, sur leurs avantages et sur leurs limites, voir [Types de runbooks Azure Automation](automation-runbook-types.md).
