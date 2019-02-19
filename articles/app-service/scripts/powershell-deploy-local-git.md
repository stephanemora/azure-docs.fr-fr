---
title: Exemple de script Azure PowerShell - Créer une application et déployer à partir d’un référentiel Git local | Microsoft Docs
description: Exemple de script Azure PowerShell - Créer une application web et déployer du code à partir d’un référentiel Git local
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 8103777b85d8e11416811c694103c58755f1a23a
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114431"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Créer une application web et déployer du code à partir d’un référentiel Git local

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis déploie votre code d’application web à partir d’un référentiel Git local.

Si nécessaire, mettez à jour vers la dernière version d’Azure PowerShell à l’aide des instructions figurant dans le [guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. En outre, votre code d’application doit être validé dans un dépôt Git local.

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application web avec le groupe de ressources et le groupe App Service nécessaires. Lorsque le répertoire actuel contient un référentiel Git, ajoutez aussi un élément `azure` à distance. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../samples-powershell.md) (Exemples Azure PowerShell).
