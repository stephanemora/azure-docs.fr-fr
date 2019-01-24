---
title: Intégration du contrôle de code source dans Azure Automation
description: Cet article décrit l’intégration du contrôle de code source avec GitHub dans Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 400cf53172fbd1ce5803cf3de298749afbf45cd4
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430192"
---
# <a name="source-control-integration-in-azure-automation"></a>Intégration du contrôle de code source dans Azure Automation

Le contrôle de code source vous permet de vous assurer que les runbooks dans votre compte Automation restent à jour par rapport à vos scripts dans le référentiel de contrôle de code source GitHub ou Azure DevOps. Le contrôle du code source vous permet de collaborer avec votre équipe en toute simplicité, de suivre les modifications et de restaurer des versions antérieures de vos runbooks. Par exemple, le contrôle de code source vous permet de synchroniser différentes branches dans le contrôle de code source pour vos comptes Automation de développement, de test ou de production. Cela simplifie la promotion du code qui a été testé dans votre environnement de développement dans votre compte Automation de production.

Azure Automation prend en charge 3 types de contrôles de code source :

* GitHub
* Azure DevOps (Git)
* Azure DevOps (TFVC)

## <a name="pre-requisites"></a>Conditions préalables

* Un référentiel de contrôle de code source (GitHub ou Azure DevOps)
* Les [autorisations](#personal-access-token-permissions) appropriées dans le référentiel du contrôle de code source
* [Un compte d’identification et une connexion](manage-runas-account.md)

> [!NOTE]
> Les travaux de synchronisation de contrôle de code source s’exécutent sous le compte Automation des utilisateurs et sont facturés au même tarif que les autres tâches Automation.

## <a name="configure-source-control"></a>Configuration du contrôle de code source

Dans votre compte Automation, sélectionnez **Contrôle de code source (préversion)**, puis cliquez sur **+ Ajouter**.

![Sélection du contrôle de code source](./media/source-control-integration/select-source-control.png)

Choisissez **Type de contrôle de code source**, puis cliquez sur **Authentifier**.

Examinez la page des autorisations de demande d’application, puis cliquez sur **Accepter**.

Sur la page **Récapitulatif du contrôle de code source**, fournissez les informations requises, puis cliquez sur **Enregistrer**. Le tableau ci-après décrit les champs disponibles.

|Propriété  |Description  |
|---------|---------|
|Nom du contrôle de code source     | Nom convivial du contrôle de code source.        |
|Type de contrôle de code source     | Type de source de contrôle de code source. Options disponibles :</br> GitHub</br>Azure DevOps (Git)</br> Azure DevOps (TFVC)        |
|Référentiel     | Nom du référentiel ou du projet. Cette valeur est extraite du référentiel du contrôle de code source. Exemple : $/ContosoFinanceTFVCExample         |
|Branche     | Branche de laquelle extraire les fichiers sources. Le ciblage de branche n’est pas disponible pour le type de contrôle de code source TFVC.          |
|Chemin d’accès du dossier     | Dossier contenant les runbooks à synchroniser. Exemple : /Runbooks         |
|Synchronisation automatique     | Active ou désactive la synchronisation automatique lorsqu’une validation est effectuée dans le référentiel de contrôle de code source.         |
|Publier un runbook     | Si ce champ est défini sur **Actif**, les runbooks sont automatiquement publiés une fois qu’ils ont été synchronisés à partir du contrôle de code source.         |
|Description     | Champ de texte permettant de fournir des détails supplémentaires.        |

![Récapitulatif du contrôle de code source](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Assurez-vous que vous êtes connecté avec le bon compte lors de la configuration du contrôle de code source. En cas de doute, ouvrez un nouvel onglet dans votre navigateur et déconnectez-vous de visualstudio.com ou de github.com et réessayez de vous connecter au contrôle de code source.

## <a name="syncing"></a>Synchronisation

En configurant la synchronisation automatique lors de la configuration de l’intégration du contrôle de code source, la synchronisation initiale démarre automatiquement. Si la synchronisation automatique n’a pas été définie, sélectionnez la source dans le tableau de la page **Contrôle de code source (préversion)**. Cliquez sur **Démarrer la synchronisation** pour initialiser le processus de synchronisation.

Vous pouvez visualiser l’état du travail de synchronisation actuel ou des travaux précédents en cliquant sur l’onglet **Travaux de synchronisation**. Dans la liste déroulante **Contrôle de code source**, sélectionnez un contrôle de code source.

![État de synchronisation](./media/source-control-integration/sync-status.png)

Vous pouvez cliquer sur un travail afin de visualiser la sortie correspondante. L’exemple suivant présente la sortie d’un travail de synchronisation du contrôle de code source.

```output
========================================================================================================

Azure Automation Source Control Public Preview.
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

## <a name="personal-access-token-permissions"></a>Autorisations de jeton d’accès personnel

Le contrôle de code source nécessite des autorisations minimales pour les jetons d’accès personnel. Les tableaux suivants indiquent les autorisations minimales requises pour GitHub et Azure DevOps.

### <a name="github"></a>GitHub

|Étendue  |Description  |
|---------|---------|
|**référentiel**     |         |
|repo:status     | État de la validation d’accès         |
|repo_deployment      | État du déploiement d’accès         |
|public_repo     | Référentiels publics d’accès         |
|**admin:repo_hook**     |         |
|write:repo_hook     | Écrire des hooks de référentiel         |
|read:repo_hook|Lire des hooks de référentiel|

### <a name="azure-devops"></a>Azure DevOps

|Étendue  |
|---------|
|Code (lire)     |
|Projet et équipe (lire)|
|Identité (lire)      |
|Profil utilisateur (lire)     |
|Éléments de travail (lire)    |
|Connexions de service (lire, interroger et gérer)<sup>1</sup>    |

<sup>1</sup>l’autorisation de connexions de service est uniquement nécessaire si vous avez activé la synchronisation automatique.

## <a name="disconnecting-source-control"></a>Déconnexion du contrôle de code source

Pour vous déconnecter d’un référentiel de contrôle de code source, ouvrez **Contrôle de code source (préversion)** sous **Paramètres du compte** dans votre compte Automation.

Sélectionnez le contrôle de code source que vous souhaitez supprimer. Sur la page **Récapitulatif du contrôle de code source**, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur les types de Runbook, leurs avantages et leurs limites, consultez [Types de Runbooks Azure Automation](automation-runbook-types.md)

