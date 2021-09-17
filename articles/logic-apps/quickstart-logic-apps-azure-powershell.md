---
title: 'Démarrage rapide : création et gestion des workflows avec Azure PowerShell dans Azure Logic Apps multi-locataire'
description: À l’aide de PowerShell, créez et gérez les workflows d’applications Azure Logic Apps multi-locataire.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell, contperf-fy21q2
ms.date: 07/26/2021
ms.openlocfilehash: 42f6ac6ce06f1b852af2027e6ef79f347d8050a4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784878"
---
# <a name="quickstart-create-and-manage-workflows-using-azure-powershell-in-multi-tenant-azure-logic-apps"></a>Démarrage rapide : création et gestion des workflows avec Azure PowerShell dans Azure Logic Apps multi-locataire

Ce démarrage rapide montre comment créer et gérer des applications logiques à l’aide de [Azure PowerShell](/powershell/azure/install-az-ps). À partir de PowerShell, vous pouvez créer une application logique à l’aide du fichier JSON pour une définition de workflow d’application logique. Vous pouvez ensuite gérer votre application logique en exécutant les cmdlets dans le module [AZ.LogicApp](/powershell/module/az.logicapp/) PowerShell.

Si vous débutez avec Azure Logic Apps, vous pouvez aussi apprendre à créer vos premières applications logiques [par le biais du Portail Azure](quickstart-create-first-logic-app-workflow.md), [dans Visual Studio](quickstart-create-logic-apps-with-visual-studio.md) et [dans Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md).

## <a name="prerequisites"></a>Prérequis

* Compte Azure avec un abonnement actif. Si vous n’avez pas d’abonnement Azure, [créez un compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Le module [Az PowerShell](/powershell/azure/install-az-ps) installé sur votre ordinateur local.
* [Groupe de ressources Azure](#example---create-resource-group) dans lequel créer votre application logique.

### <a name="prerequisite-check"></a>Vérification du prérequis

Validez votre environnement avant de commencer :

* Connectez-vous au Portail Azure et vérifiez que votre abonnement est actif en exécutant [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount).
* Vérifiez votre version Azure PowerShell en exécutant `Get-InstalledModule -Name Az`. Pour obtenir la version la plus récente, consultez les [notes de publication les plus récentes](/powershell/azure/migrate-az-6.0.0).
  * Si vous ne disposez pas de la dernière version, mettez à jour votre installation en suivant [Mise à jour du module Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).

### <a name="example---create-resource-group"></a>Exemple – Créer un groupe de ressources

Si ne disposez pas encore d’un groupe de ressources pour votre application logique, créez-en un avec la cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Par exemple, la commande suivante crée un groupe de ressources nommé `testResourceGroup` à l’emplacement `westus`.

```azurepowershell-interactive
New-AzResourceGroup -Name testResourceGroup -Location westus
```

Une fois votre groupe de ressources créé, la sortie affiche l’état `ProvisioningState` `Succeeded` :

```Output
ResourceGroupName : testResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testResourceGroup
```

## <a name="workflow-definition"></a>Définition de flux de travail

Avant de [créer une nouvelle application logique](#create-logic-apps-from-powershell) ou de [mettre à jour une application logique existante](#update-logic-apps-from-powershell) à l’aide d’Azure PowerShell, vous avez besoin d’une définition de workflow pour votre application logique. Dans le portail Azure, vous pouvez afficher la définition de flux de travail sous-jacente de votre application logique au format JSON en basculant du **Mode Création** en **Mode Code**.

Lorsque vous exécutez les commandes pour créer ou mettre à jour votre application logique, votre définition de workflow est chargée en tant que paramètre obligatoire (`Definition`) (`DefinitionFilePath`) en fonction de l’ensemble de paramètres. Vous devez créer votre définition de flux de travail sous la forme d’un fichier JSON qui suit le [schéma de langage de définition de flux de travail](./logic-apps-workflow-definition-language.md).

## <a name="create-logic-apps-from-powershell"></a>Créer des applications logiques à partir de PowerShell

Vous pouvez créer un workflow d’applications logiques à partir d’Azure PowerShell à l’aide de la cmdlet [`New-AzLogicApp`](/powershell/module/az.logicapp/new-azlogicapp) avec un fichier JSON pour la définition.

### <a name="example---create-logic-app"></a>Exemple – Créer une application logique

Cet exemple permet de créer un workflow nommé `testLogicApp` dans le groupe de ressources `testResourceGroup` à l’emplacement `westus`. Le fichier JSON `testDefinition.json` contient la définition de flux de travail.

```azurepowershell-interactive
New-AzLogicApp -ResourceGroupName testResourceGroup -Location westus -Name testLogicApp -DefinitionFilePath .\testDefinition.json
```

Une fois votre workflow correctement créé, PowerShell votre nouvelle définition de workflow.

## <a name="update-logic-apps-from-powershell"></a>Mettre à jour des applications logiques à partir de PowerShell

Vous pouvez également mettre à jour le workflow d’une application logique à partir d’Azure PowerShell à l’aide de la cmdlet [`Set-AzLogicApp`](/powershell/module/az.logicapp/set-azlogicapp).

### <a name="example---update-logic-app"></a>Exemple – Mettre à jour une application logique

Cet exemple montre comment mettre à jour l'[échantillon de workflow créé dans la section précédente](#example---create-logic-app) à l’aide d’un fichier de définition JSON différent, `newTestDefinition.json`.

```azurepowershell-interactive
Set-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp -DefinitionFilePath .\newTestDefinition.json
```

Une fois votre workflow mis à jour, PowerShell affiche la définition de workflow mise à jour de votre application logique.

## <a name="delete-logic-apps-from-powershell"></a>Supprimer des applications logiques à partir de PowerShell

Vous pouvez également supprimer le workflow d’une application logique à partir d’Azure PowerShell à l’aide de la cmdlet [`Remove-AzLogicApp`](/powershell/module/az.logicapp/remove-azlogicapp).

### <a name="example---delete-logic-app"></a>Exemple – Supprimer une application logique

Cet exemple permet de supprimer l’[échantillon de workflow créé dans une section précédente](#example---create-logic-app).

```azurepowershell-interactive
Remove-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

Une fois que vous avez répondu à l’invite de confirmation en tapant `y`, l’application logique est supprimée.

### <a name="considerations---delete-logic-app"></a>Considérations liées à la suppression d’une application logique

Voici de quelles manières la suppression d’une application logique affecte les instances de workflow :

* Le service Logic Apps met tout en œuvre pour annuler les exécutions en cours et en attente.

  Même avec un gros volume ou un backlog important, la plupart des exécutions sont annulées avant qu’elles ne finissent ou ne démarrent. Toutefois, le processus d’annulation peut prendre du temps. Il peut arriver que certaines exécutions soient lancées dans l’intervalle.

* Le service Logic Apps ne crée pas ni n’exécute de nouvelles instances de workflow.

* Si, après avoir supprimé un workflow, vous recréez le même, les métadonnées de ce dernier sont différentes de celles du workflow supprimé. Vous devez enregistrer de nouveau les workflows qui ont appelé le workflow supprimé. L’appelant obtient ainsi les bonnes informations sur le workflow recréé. Dans le cas contraire, les appels au workflow recréé échouent avec une erreur `Unauthorized`. Ce comportement s’applique aussi aux workflows qui utilisent des artefacts dans les comptes d’intégration et aux workflows qui appellent des fonctions Azure.

## <a name="show-logic-apps-in-powershell"></a>Afficher des applications logiques dans PowerShell

Vous pouvez obtenir un flux de travail d’application logique spécifique à l’aide de la commande [`Get-AzLogicApp`](/powershell/module/az.logicapp/get-azlogicapp).

### <a name="example---get-logic-app"></a>Exemple – Obtenir une application logique

Cet exemple retourne l’application logique `testLogicApp` dans le groupe de ressources `testResourceGroup`.

```azurepowershell-interactive
Get-AzLogicApp -ResourceGroupName testResourceGroup -Name testLogicApp
```

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur Azure PowerShell, consultez la [documentation Azure PowerShell](/powershell/azure/).

Vous trouverez des échantillons de scripts supplémentaires Logic Apps dans le [navigateur d’exemples de code Microsoft](/samples/browse/?products=azure-logic-apps).