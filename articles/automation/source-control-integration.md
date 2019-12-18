---
title: Intégration du contrôle de code source dans Azure Automation
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/10/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4e5e0135e4ab56d319c78b7daeab9dd2e9d101f9
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74996508"
---
# <a name="source-control-integration-in-azure-automation"></a>Intégration du contrôle de code source dans Azure Automation

Avec le contrôle de code source, vous avez la garantie que les runbooks de votre compte Automation sont à jour par rapport aux scripts de votre dépôt de contrôle de code source GitHub ou Azure Repos. Le contrôle du code source vous permet de collaborer avec votre équipe en toute simplicité, de suivre les modifications et de restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches dans le contrôle de code source pour vos comptes Automation de développement, de test ou de production. Cela simplifie la promotion du code qui a été testé dans votre environnement de développement dans votre compte Automation de production. L’intégration du contrôle de code source à l’automation vous permet d’effectuer des synchronisations unidirectionnelles à partir de votre dépôt de contrôle de code source.

Azure Automation prend en charge trois types de contrôles de code source :

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Conditions préalables

* Un dépôt de contrôle de code source (GitHub ou Azure Repos)
* Un [compte d’identification](manage-runas-account.md)
* Vérifiez que vous disposez des [derniers modules Azure](automation-update-azure-modules.md) dans votre compte Automation, notamment le module **AzureRM.Profile**. 

> [!NOTE]
> Les travaux de synchronisation de contrôle de code source s’exécutent sous le compte Automation des utilisateurs et sont facturés au même tarif que les autres tâches Automation.

## <a name="configure-source-control---azure-portal"></a>Configurer le contrôle de code source - Portail Azure

Dans votre compte Automation, sélectionnez **Contrôle de code source**, puis cliquez sur **+ Ajouter**.

![Sélection du contrôle de code source](./media/source-control-integration/select-source-control.png)

Choisissez **Type de contrôle de code source**, puis cliquez sur **Authentifier**. Une fenêtre de navigateur s’ouvre et vous invite à vous connecter. Suivez les invites pour finaliser l’authentification.

Sur la page **Récapitulatif du contrôle de code source**, fournissez les informations requises, puis cliquez sur **Enregistrer**. Le tableau ci-après décrit les champs disponibles.

|Propriété  |Description  |
|---------|---------|
|Nom du contrôle de code source     | Nom convivial du contrôle de code source. *Ce nom ne doit contenir que des lettres et des chiffres.*        |
|Type de contrôle de code source     | Type de source de contrôle de code source. Options disponibles :</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|Référentiel     | Nom du référentiel ou du projet. Les 200 premiers dépôts sont retournés. Pour rechercher un dépôt, tapez son nom dans le champ, puis cliquez sur **Rechercher sur GitHub**.|
|Branche     | Branche de laquelle extraire les fichiers sources. Le ciblage de branche n’est pas disponible pour le contrôle de code source de type TFVC.          |
|Chemin d’accès du dossier     | Dossier contenant les runbooks à synchroniser. Exemple : /Runbooks </br>*Seuls les runbooks qui se trouvent dans le dossier spécifié sont synchronisés. La récursivité n’est pas prise en charge.*        |
|Synchronisation automatique<sup>1</sup>     | Active ou désactive la synchronisation automatique lorsqu’une validation est effectuée dans le référentiel de contrôle de code source.         |
|Publier un runbook     | Si ce champ est défini sur **Actif**, les runbooks sont automatiquement publiés une fois qu’ils ont été synchronisés à partir du contrôle de code source.         |
|Description     | Champ de texte permettant de fournir des détails supplémentaires.        |

<sup>1</sup> Pour activer la synchronisation automatique lorsque vous configurez l’intégration du contrôle de code source dans les dépôts Azure, vous devez être l’administrateur du projet.

![Récapitulatif du contrôle de code source](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Les informations de connexion pour votre dépôt de contrôle de code source peuvent être différentes de celles que vous utilisez pour le portail Azure. Lorsque vous configurez le contrôle de code source, vérifiez que vous êtes bien connecté au compte qui correspond à votre dépôt de contrôle de code source. En cas de doute, ouvrez un nouvel onglet dans votre navigateur et déconnectez-vous de visualstudio.com ou de github.com et réessayez de vous connecter au contrôle de code source.

## <a name="configure-source-control---powershell"></a>Configurer le contrôle de code source - PowerShell

Vous pouvez également utiliser PowerShell pour configurer le contrôle de code source dans Azure Automation. Pour configurer le contrôle de code source à l’aide des applets de commande PowerShell, vous avez besoin d’un jeton d’accès personnel (PAT). Pour créer la connexion de contrôle de code source, utilisez la commande [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl). L’applet de commande nécessite que le jeton d’accès personnel soit une chaîne sécurisée. Pour savoir comment créer une chaîne sécurisée, consultez [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Autorisations de jeton d’accès personnel

Le contrôle de code source nécessite des autorisations minimales pour les jetons d’accès personnel. Les tableaux suivants indiquent les autorisations minimales qui sont nécessaires pour accéder à GitHub et à Azure Repos.

#### <a name="github"></a>GitHub

Pour plus d’informations sur la création d’un jeton d’accès personnel dans GitHub, consultez [Creating a personal access token for the command line](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Étendue  |Description  |
|---------|---------|
|**référentiel**     |         |
|repo:status     | État de la validation d’accès         |
|repo_deployment      | État du déploiement d’accès         |
|public_repo     | Référentiels publics d’accès         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Écrire des hooks de référentiel         |
|read:repo_hook|Lire des hooks de référentiel|

#### <a name="azure-repos"></a>Azure Repos

Pour plus d’informations sur la création d’un jeton d’accès personnel dans Azure Repos, consultez [Authentifier l’accès à l’aide de jetons d’accès personnels](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Étendue  |
|---------|
|Code (lire)     |
|Projet et équipe (lire)|
|Identité (lire)      |
|Profil utilisateur (lire)     |
|Éléments de travail (lire)    |
|Connexions de service (lire, interroger et gérer)<sup>1</sup>    |

<sup>1</sup> L’autorisation de connexions de service est uniquement nécessaire si vous avez activé la synchronisation automatique.

## <a name="syncing"></a>Synchronisation

Sélectionnez la source dans le tableau de la page **Contrôle de code source**. Cliquez sur **Démarrer la synchronisation** pour initialiser le processus de synchronisation.

Vous pouvez visualiser l’état du travail de synchronisation actuel ou des travaux précédents en cliquant sur l’onglet **Travaux de synchronisation**. Dans la liste déroulante **Contrôle de code source**, sélectionnez un contrôle de code source.

![État de synchronisation](./media/source-control-integration/sync-status.png)

Vous pouvez cliquer sur un travail afin de visualiser la sortie correspondante. L’exemple suivant présente la sortie d’un travail de synchronisation du contrôle de code source.

```output
========================================================================================================

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



========================================================================================================
```

Vous pouvez disposer d’une journalisation supplémentaire en sélectionnant **Tous les journaux** dans la page **Récapitulatif du travail de synchronisation du contrôle de code source**. Ces entrées de journal supplémentaires peuvent vous aider à résoudre les problèmes qui peuvent survenir lorsque vous utilisez le contrôle de code source.

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter d’un dépôt de contrôle de code source, ouvrez **Contrôle de code source** sous **Paramètres du compte** dans votre compte Automation.

Sélectionnez le contrôle de code source que vous souhaitez supprimer. Sur la page **Récapitulatif du contrôle de code source**, cliquez sur **Supprimer**.

## <a name="encoding"></a>Encodage

Si plusieurs personnes modifient les runbooks qui se trouvent dans votre dépôt de contrôle de code source à l’aide de différents éditeurs, vous risquez de rencontrer des problèmes de codage. Cela peut entraîner l’affichage de caractères incorrects dans votre runbook. Pour plus d’informations, consultez [Causes courantes des problèmes d’encodage](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-access-token"></a>Mise à jour du jeton d’accès

Actuellement, il n’existe aucun moyen de mettre à jour le jeton d’accès dans le contrôle de code source à partir du portail. Une fois que votre jeton d’accès personnel a expiré ou a été révoqué, vous pouvez mettre à jour le contrôle de code source avec un nouveau jeton d’accès en procédant de l’une des manières suivantes :

* Via l’[API REST](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Via la cmdlet [Update-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
