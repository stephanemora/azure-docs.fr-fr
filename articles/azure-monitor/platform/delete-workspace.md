---
title: Supprimer et restaurer un espace de travail Azure Log Analytics | Microsoft Docs
description: Découvrez comment supprimer votre espace de travail Log Analytics si vous en avez créé un dans votre abonnement personnel ou restructurer votre modèle d’espace de travail.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: magoedte
ms.openlocfilehash: fb6714a52a65ef5efe4725b99acb30cb67af20c3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299281"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Supprimer et restaurer un espace de travail Azure Log Analytics

Cet article décrit le concept de suppression réversible d'un espace de travail Azure Log Analytics, et explique comment récupérer un espace de travail supprimé. 

## <a name="considerations-when-deleting-a-workspace"></a>Considérations relatives à la suppression d'un espace de travail

Lorsque vous supprimez un espace de travail Log Analytics, une opération de suppression réversible est lancée pour permettre la récupération de l'espace de travail, y compris de ses données et des agents connectés, dans un délai de 14 jours, que la suppression ait été accidentelle ou intentionnelle. À l'issue de cette période de suppression réversible, l'espace de travail et ses données deviennent irrécupérables et sont mis en file d'attente pour une suppression définitive dans un délai de 30 jours.

Soyez prudent lorsque vous supprimez un espace de travail, car celui-ci peut contenir une configuration et des données importantes dont la suppression peut avoir un impact négatif sur le fonctionnement de votre service. Examinez les agents, les solutions ainsi que les autres services et sources Azure qui stockent leurs données dans Log Analytics, comme :

* Liste des solutions de gestion
* Azure Automation
* Agents s’exécutant sur des machines virtuelles Windows et Linux
* Agents s’exécutant sur des ordinateurs Windows et Linux dans votre environnement
* System Center Operations Manager

L'opération de suppression réversible supprime la ressource de l'espace de travail, et l'autorisation des utilisateurs associés est rompue. Les utilisateurs associés à d'autres espaces de travail peuvent continuer à utiliser Log Analytics avec ceux-ci.

## <a name="soft-delete-behavior"></a>Comportement de la suppression réversible

L'opération de suppression de l'espace de travail supprime la ressource Resource Manager de l'espace de travail, mais sa configuration et ses données sont conservées pendant 14 jours, tout en donnant l'impression que l'espace de travail a été supprimé. Pendant la période de suppression réversible, tous les agents et groupes d'administration System Center Operations Manager configurés pour se rapporter à l'espace de travail conservent l'état d'orphelins. Le service fournit en outre un mécanisme de récupération de l'espace de travail supprimé (données et ressources connectées comprises) qui a essentiellement pour effet d'annuler la suppression.

> [!NOTE] 
> Les solutions installées et les services liés tels que votre compte Azure Automation sont définitivement supprimés de l'espace de travail au moment de la suppression. Leur récupération devient alors impossible. Ceux-ci doivent être reconfigurés après l’opération de récupération pour rétablir l’état précédemment configuré de l’espace de travail.

Vous pouvez supprimer un espace de travail à l'aide de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), de [l’API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) ou à partir du [portail Azure](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Supprimer un espace de travail à partir du portail Azure

1. Pour vous connecter, accédez au [portail Azure](https://portal.azure.com). 
2. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.
3. Dans la liste des espaces de travail Log Analytics, sélectionnez un espace de travail, puis cliquez sur **Supprimer** en haut du volet central.
   ![Option Supprimer dans le volet des propriétés de l’espace de travail](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quand la fenêtre de message de confirmation s’affiche vous invitant à confirmer la suppression de l’espace de travail, cliquez sur **Oui**.
   ![Confirmer la suppression de l’espace de travail](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Récupérer un espace de travail

Si vous disposez d'autorisations Contributeur pour l'abonnement et le groupe de ressources auxquels l'espace de travail était associé avant l'opération de suppression réversible, vous pouvez le récupérer (avec ses données, sa configuration et les agents connectés) pendant la période de suppression réversible. À l'issue de la période de suppression réversible, l'espace de travail devient irrécupérable et il est mis en file d'attente pour être définitivement supprimé. Les noms des espaces de travail supprimés sont conservés pendant toute la période de suppression réversible ; ils ne peuvent alors pas être utilisés pour tenter de créer un nouvel espace de travail.  

Vous pouvez récupérer un espace de travail en le recréant à l’aide des méthodes de création de l’espace de travail [PowerShell ](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) ou [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate), à condition que ces propriétés soient remplies avec les détails de l’espace de travail supprimé, notamment :

* Identifiant d’abonnement
* Nom du groupe ressources
* Nom de l’espace de travail
* Région

> [!NOTE]
> * La récupération de l’espace de travail n’est pas prise en charge dans le [portail Azure](https://portal.azure.com). 
> * La recréation d’un espace de travail pendant la période de suppression réversible indique que le nom de cet espace de travail est déjà utilisé. 
> 
