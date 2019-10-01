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
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: f8dcab1a7a46d518b752e48f9886b60a37d8ec4c
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299540"
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
> Les solutions installées et les services liés tels que le compte Automation sont définitivement supprimés de l'espace de travail au moment de la suppression. Leur récupération devient alors impossible. Ceux-ci doivent être reconfigurés après l'opération de récupération pour rétablir la fonctionnalité précédente de l'espace de travail. 

Vous pouvez supprimer un espace de travail à l'aide de [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), de l'[API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete), ou à partir du [portail Azure](https://portal.azure.com).

### <a name="delete-workspace-in-azure-portal"></a>Supprimer un espace de travail à partir du portail Azure
1. Pour vous connecter, accédez au [portail Azure](https://portal.azure.com). 
2. Dans le portail Azure, sélectionnez **Tous les services**. Dans la liste de ressources, saisissez **Log Analytics**. Au fur et à mesure de la saisie, la liste est filtrée. Sélectionnez **Espaces de travail Log Analytics**.
3. Dans la liste des espaces de travail Log Analytics, sélectionnez un espace de travail, puis cliquez sur **Supprimer** en haut du volet central.
   ![Option Supprimer dans le volet des propriétés de l’espace de travail](media/delete-workspace/log-analytics-delete-workspace.png)
4. Quand la fenêtre de message de confirmation s’affiche vous invitant à confirmer la suppression de l’espace de travail, cliquez sur **Oui**.
   ![Confirmer la suppression de l’espace de travail](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>Récupérer un espace de travail
Si vous disposez d'autorisations Contributeur pour l'abonnement et le groupe de ressources auxquels l'espace de travail était associé avant l'opération de suppression réversible, vous pouvez le récupérer (avec ses données, sa configuration et les agents connectés) pendant la période de suppression réversible. À l'issue de la période de suppression réversible, l'espace de travail devient irrécupérable et il est mis en file d'attente pour être définitivement supprimé.

Vous pouvez récupérer un espace de travail en le recréant à l'aide d'une des méthodes de création prises en charge : PowerShell, l'interface de ligne de commande Azure (Azure CLI), ou à partir du portail Azure tant que les propriétés suivantes sont renseignées avec les détails de l'espace de travail supprimé :
1.  Identifiant d’abonnement
2.  Nom du groupe ressources
3.  Nom de l’espace de travail
4.  Région

> [!NOTE]
> Les noms des espaces de travail supprimés sont conservés pendant toute la période de suppression réversible ; ils ne peuvent alors pas être utilisés pour créer un nouvel espace de travail. À l'issue de la période de suppression réversible, les noms des espaces de travail sont *libérés* et peuvent donc être utilisés pour créer un nouvel espace de travail.
