---
title: Intégration du contrôle de code source dans Azure Automation
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/21/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3b2df5b24a12f3d2ea5d8a03721c08f8d2a742ad
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58539987"
---
# <a name="source-control-integration-in-azure-automation"></a>Intégration du contrôle de code source dans Azure Automation

Contrôle de code source vous permet de conserver vos runbooks dans votre Automation de compte sont à jour avec vos scripts dans votre référentiel de contrôle de source de GitHub ou de référentiels d’Azure. Le contrôle du code source vous permet de collaborer avec votre équipe en toute simplicité, de suivre les modifications et de restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches dans le contrôle de code source pour vos comptes Automation de développement, de test ou de production. Cela simplifie la promotion du code qui a été testé dans votre environnement de développement dans votre compte Automation de production. Intégration de contrôle de code source avec automation prend en charge la synchronisation de direction à sens unique à partir de votre référentiel de contrôle de code source.

Azure Automation prend en charge trois types de contrôle de code source :

* GitHub
* Référentiels Azure (Git)
* Référentiels Azure (TFVC)

## <a name="pre-requisites"></a>Conditions préalables

* Un référentiel de contrôle de code source (GitHub ou les référentiels Azure)
* A [compte Exécuter en tant que](manage-runas-account.md)
* Assurez-vous d’avoir le [derniers modules Azure](automation-update-azure-modules.md) dans votre compte Automation

> [!NOTE]
> Les travaux de synchronisation de contrôle de code source s’exécutent sous le compte Automation des utilisateurs et sont facturés au même tarif que les autres tâches Automation.

## <a name="configure-source-control---azure-portal"></a>Configurer le contrôle de code source - portail Azure

Au sein de votre compte Automation, sélectionnez **contrôle de code Source** et cliquez sur **+ ajouter**

![Sélection du contrôle de code source](./media/source-control-integration/select-source-control.png)

Choisissez **Type de contrôle de code source**, puis cliquez sur **Authentifier**. Une fenêtre de navigateur s’ouvre et vous invite à vous connecter, suivez les invites pour finaliser l’authentification.

Sur la page **Récapitulatif du contrôle de code source**, fournissez les informations requises, puis cliquez sur **Enregistrer**. Le tableau ci-après décrit les champs disponibles.

|Propriété  |Description  |
|---------|---------|
|Nom du contrôle de code source     | Nom convivial du contrôle de code source.        |
|Type de contrôle de code source     | Type de source de contrôle de code source. Options disponibles :</br> GitHub</br>Référentiels Azure (Git)</br> Référentiels Azure (TFVC)        |
|Référentiel     | Nom du référentiel ou du projet. Les 200 premiers référentiels sont retournés. Pour rechercher un référentiel, tapez le nom dans le champ, puis cliquez sur **recherche sur GitHub**.|
|Branche     | Branche de laquelle extraire les fichiers sources. Ciblage de branche n’est pas disponible pour le type de contrôle de code source TFVC.          |
|Chemin d’accès du dossier     | Dossier contenant les runbooks à synchroniser. Exemple : /Runbooks </br>*Runbooks uniquement dans le dossier spécifié sont synchronisées. La récursivité n’est pas pris en charge.*        |
|Synchronisation automatique     | Active ou désactive la synchronisation automatique lorsqu’une validation est effectuée dans le référentiel de contrôle de code source.         |
|Publier un runbook     | Si la valeur **sur**, une fois que les runbooks sont synchronisés avec contrôle de code source allez soient publiées automatiquement.         |
|Description     | Champ de texte permettant de fournir des détails supplémentaires.        |

![Récapitulatif du contrôle de code source](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Assurez-vous que vous êtes connecté avec le bon compte lors de la configuration du contrôle de code source. En cas de doute, ouvrez un nouvel onglet dans votre navigateur et déconnectez-vous de visualstudio.com ou de github.com et réessayez de vous connecter au contrôle de code source.

## <a name="configure-source-control---powershell"></a>Configurer le contrôle de code source - PowerShell

Vous pouvez également utiliser PowerShell pour configurer le contrôle de code source dans Azure Automation. Pour configurer le contrôle de code source avec les applets de commande PowerShell, un jeton d’accès personnel (PAT) est nécessaire. Vous utilisez le [New-AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) pour créer la connexion de contrôle source. L’applet de commande nécessite une chaîne sécurisée du jeton d’accès personnel, pour apprendre à créer une chaîne sécurisée, consultez [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Référentiels Azure (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Référentiels Azure (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Autorisations de jeton d’accès personnel

Le contrôle de code source nécessite des autorisations minimales pour les jetons d’accès personnel. Les tableaux suivants contiennent les autorisations minimales requises pour GitHub et les dépôts de Azure.

#### <a name="github"></a>GitHub

Pour plus d’informations sur la création d’un jeton d’accès personnel dans GitHub, visitez [création d’un jeton d’accès personnel pour la ligne de commande](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

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

Pour plus d’informations sur la création d’un jeton d’accès personnel dans les référentiels Azure, visitez [authentifier l’accès avec des jetons d’accès personnels](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Étendue  |
|---------|
|Code (lire)     |
|Projet et équipe (lire)|
|Identité (lire)      |
|Profil utilisateur (lire)     |
|Éléments de travail (lire)    |
|Connexions de service (lire, interroger et gérer)<sup>1</sup>    |

<sup>1</sup> les connexions de Service l’autorisation sont uniquement nécessaire si vous avez activé la synchronisation automatique.

## <a name="syncing"></a>Synchronisation

Sélectionnez la source à partir de la table sur le **contrôle de code Source** page. Cliquez sur **Démarrer la synchronisation** pour initialiser le processus de synchronisation.

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

Journalisation supplémentaire est disponible en sélectionnant **tous les journaux** sur le **résumé de tâche de synchronisation de contrôle Source** page. Ces entrées de journal supplémentaires peuvent vous aider à résoudre les problèmes qui peuvent survenir lors de l’utilisation du contrôle de code source.

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter d’un référentiel de contrôle de code source, ouvrez **contrôle de code Source** sous **comptable** dans votre compte Automation.

Sélectionnez le contrôle de code source que vous souhaitez supprimer. Sur la page **Récapitulatif du contrôle de code source**, cliquez sur **Supprimer**.

## <a name="encoding"></a>Encodage

Si plusieurs personnes sont modifier des runbooks dans votre référentiel de contrôle de code source avec différents éditeurs, il est une occasion de rencontrer des problèmes de codage. Cette situation peut entraîner des caractères incorrects dans votre runbook. Pour plus d’informations, consultez [causes courantes des problèmes de codage](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)
