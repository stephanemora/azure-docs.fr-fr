---
title: Intégrer Azure Automation au contrôle de code source Azure DevOps Services
description: Ce scénario vous guide tout au long de la configuration de l’intégration d’un compte Azure Automation au contrôle de code source Azure DevOps Services.
services: automation
author: eamonoreilly
ms.author: eamono
keywords: azure powershell, Azure DevOps Services, contrôle de code source, automation
ms.service: automation
ms.component: process-automation
ms.topic: conceptual
ms.date: 09/26/2018
ms.openlocfilehash: b06e315cc12856976dce87791b423d10f002c6df
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801435"
---
# <a name="azure-automation-scenario---automation-source-control-integration-with-azure-devops"></a>Scénario Azure Automation - Intégration du contrôle de code source à Azure DevOps

> [!NOTE]
> Il existe une nouvelle expérience de contrôle de code source. Pour plus d’informations sur cette nouvelle expérience, consultez l’article concernant le [contrôle de code source (préversion)](source-control-integration.md).

Dans ce scénario, vous disposez d’un projet Azure DevOps que vous utilisez pour gérer des runbooks Azure Automation ou des configurations DSC avec un contrôle de code source.

Cet article décrit comment intégrer Azure DevOps à votre environnement Azure Automation afin d’assurer une intégration continue pour chaque archivage.

## <a name="getting-the-scenario"></a>Obtention du scénario

Ce scénario se compose de deux runbooks PowerShell que vous pouvez importer directement depuis la [galerie de runbooks](automation-runbook-gallery.md) dans le portail Azure ou télécharger depuis [PowerShell Gallery](https://www.powershellgallery.com).

### <a name="runbooks"></a>Runbooks

Runbook | Description|
--------|------------|
Sync-VSTS | Importer des runbooks ou des configurations à partir du contrôle de code source Azure DevOps lorsqu’un archivage est effectué. En cas d’exécution manuelle, cette fonction importe et publie l’ensemble des runbooks ou des configurations dans le compte Automation.| 
Sync-VSTSGit | Importer des runbooks ou des configurations à partir du contrôle de code source Azure DevOps sous Git lorsqu’un archivage est effectué. En cas d’exécution manuelle, cette fonction importe et publie l’ensemble des runbooks ou des configurations dans le compte Automation.|

### <a name="variables"></a>variables

Variable | Description|
-----------|------------|
VSToken | Ressource de variable sécurisée créée par vos soins qui contient le jeton d’accès personnel Azure DevOps. Vous pouvez apprendre à créer un jeton d’accès personnel Azure DevOps sur la [page d’authentification Azure DevOps](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

## <a name="installing-and-configuring-this-scenario"></a>Installation et configuration de ce scénario

Créez un [jeton d’accès personnel](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate) dans Azure DevOps que vous utilisez pour synchroniser les runbooks ou les configurations dans votre compte Automation.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSPersonalToken.png) 

Créez une [variable sécurisée](automation-variables.md) dans votre compte Automation pour stocker le jeton d’accès personnel afin que le runbook puisse s’authentifier auprès d’Azure DevOps et synchroniser les runbooks ou les configurations dans le compte Automation. Vous pouvez la nommer VSToken.

![](media/automation-scenario-source-control-integration-with-VSTS/VSTSTokenVariable.png)

Importez le runbook qui synchronise vos runbooks ou configurations dans le compte Automation. Vous pouvez utiliser [l’exemple de runbook Azure DevOps](https://www.powershellgallery.com/packages/Sync-VSTS) ou [l’exemple de runbook Azure DevOps avec Git](https://www.powershellgallery.com/packages/Sync-VSTSGit) à partir de [PowerShell Gallery](https://www.powershellgallery.com), selon que vous utilisez le contrôle de code source Azure DevOps ou Azure DevOps avec Git pour effectuer un déploiement dans votre compte Automation.

![PowerShell Gallery](media/automation-scenario-source-control-integration-with-VSTS/VSTSPowerShellGallery.png)

Vous pouvez à présent [publier](automation-creating-importing-runbook.md#publishing-a-runbook) ce runbook pour créer un webhook.

![Publier un runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSPublishRunbook.png)

Créez un [webhook](automation-webhooks.md) pour ce runbook Sync-VSTS et renseigner les paramètres comme indiqué ci-dessous. Veillez à copier l’URL du webhook, car cette dernière est requise pour un crochet de service dans Azure DevOps. VSAccessTokenVariableName est le nom (VSToken) de la variable sécurisée que vous avez créée précédemment pour stocker le jeton d’accès personnel.

L’intégration à Azure DevOps (Sync-VSTS.ps1) utilise les paramètres suivants :

### <a name="sync-vsts-parameters"></a>Paramètres Sync-VSTS

Paramètre | Description|
--------|------------|
WebhookData | Ce paramètre contient les informations d’archivage envoyées à partir du crochet de service Azure DevOps. Laissez ce paramètre vide.| 
ResourceGroup | Nom du groupe de ressources dans lequel se trouve le compte Automation.|
AutomationAccountName | Nom du compte Automation qui se synchronise avec Azure DevOps.|
VSFolder | Nom du dossier dans Azure DevOps dans lequel se trouvent les runbooks et les configurations.|
VSAccount | Nom de l’organisation Azure DevOps.|
VSAccessTokenVariableName | Nom de la variable sécurisée (VSToken) qui contient le jeton d’accès personnel Azure DevOps.|

![webhook](media/automation-scenario-source-control-integration-with-VSTS/VSTSWebhook.png)

Si vous utilisez Azure DevOps avec GIT (Sync-VSTSGit.ps1), il a recours aux paramètres suivants.

Paramètre | Description|
--------|------------|
WebhookData | Ce paramètre contient les informations d’archivage envoyées à partir du crochet de service Azure DevOps. Laissez ce paramètre vide.|
ResourceGroup | Nom du groupe de ressources dans lequel se trouve le compte Automation.|
AutomationAccountName | Nom du compte Automation qui se synchronise avec Azure DevOps.|
VSAccount | Nom de l’organisation Azure DevOps.|
VSProject | Nom du projet dans Azure DevOps dans lequel se trouvent les runbooks et les configurations.|
GitRepo | Nom du référentiel Git.|
GitBranch | Nom de la branche du référentiel Git Azure DevOps.|
Dossier | Nom du dossier de la branche Git Azure DevOps.|
VSAccessTokenVariableName | Nom de la variable sécurisée (VSToken) qui contient le jeton d’accès personnel Azure DevOps.|

![Webhook GIT](media/automation-scenario-source-control-integration-with-VSTS/VSTSGitWebhook.png)

Créez un crochet de service dans Azure DevOps pour les archivages dans le dossier qui déclenche ce webhook lors de l’archivage de code. Sélectionnez **Raccordements web** comme service à intégrer quand vous créez un abonnement. Pour plus d’informations sur les crochets de service, consultez la [documentation sur les crochets de service Azure DevOps](https://www.visualstudio.com/docs/marketplace/integrate/service-hooks/get-started).
![Crochet de service](media/automation-scenario-source-control-integration-with-VSTS/VSTSServiceHook.png)

Vous devriez désormais être en mesure d’effectuer tous les archivages de vos runbooks et configurations dans Azure DevOps et de les synchroniser automatiquement dans votre compte Automation.

![Synchroniser une sortie de runbook](media/automation-scenario-source-control-integration-with-VSTS/VSTSSyncRunbookOutput.png)

Si vous exécutez ce runbook manuellement sans qu’il soit déclenché par Azure DevOps, vous pouvez laisser le paramètre webhookdata vide pour effectuer une synchronisation complète à partir du dossier Azure DevOps spécifié.

Si vous souhaitez désinstaller le scénario, supprimez le crochet de service d’Azure DevOps, puis supprimez le runbook et la variable VSToken.
