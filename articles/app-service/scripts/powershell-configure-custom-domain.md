---
title: Exemple de script Azure PowerShell - Affecter un domaine personnalisé à une application web | Microsoft Docs
description: Exemple de script Azure PowerShell - Affecter un domaine personnalisé à une application web
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b78955336226276b7a2cf7e4867be51267e2527f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112340"
---
# <a name="assign-a-custom-domain-to-a-web-app-using-powershell"></a>Affecter un domaine personnalisé à une application web à l'aide de PowerShell

Cet exemple de script crée une application web dans App Service avec ses ressources associées, puis la mappe à `www.<yourdomain>`. 

Si nécessaire, installez Azure PowerShell à l’aide des instructions figurant dans le [Guide Azure PowerShell](/powershell/azure/overview), puis exécutez `Connect-AzAccount` pour créer une connexion avec Azure. Vous devez également avoir accès à la page de configuration DNS du bureau d’enregistrement de votre domaine.

## <a name="sample-script"></a>Exemple de script

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

## <a name="clean-up-deployment"></a>Nettoyer le déploiement 

Une fois l’exemple de script exécuté, la commande suivante permet de supprimer le groupe de ressources, l’application web et toutes les ressources associées.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explication du script

Ce script utilise les commandes suivantes. Chaque commande du tableau renvoie à une documentation spécifique.

| Commande | Notes |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crée un groupe de ressources dans lequel toutes les ressources sont stockées. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Crée un plan App Service. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crée une application web. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modifie le niveau tarifaire d’un plan App Service. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifie la configuration d’une application web. |

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur le module Azure PowerShell, consultez la [Documentation Azure PowerShell](/powershell/azure/overview).

Vous trouverez des exemples supplémentaires de scripts Azure PowerShell pour Azure App Service Web Apps sur la page [Azure PowerShell Samples](../samples-powershell.md) (Exemples Azure PowerShell).
