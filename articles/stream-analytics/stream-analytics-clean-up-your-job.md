---
title: Supprimer une tâche Azure Stream Analytics
description: Cet article montre différentes méthodes de suppression des tâches Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: 85db38fef5e69c4de855f8cb6d54151496faebbe
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090242"
---
# <a name="clean-up-your-azure-stream-analytics-job"></a>Supprimer une tâche Azure Stream Analytics

Vous pouvez facilement supprimer les tâches Azure Stream Analytics via le portail Azure, Azure PowerShell, Azure SDK pour .NET ou l’API REST.

>[!NOTE] 
>Quand vous arrêtez votre tâche Azure Stream Analytics, les données sont seulement conservées dans le stockage d’entrée et de sortie, comme Event Hubs ou Azure SQL Database. Si vous devez supprimer des données dans Azure, veillez à suivre le processus de suppression des ressources d’entrée et de sortie de votre tâche Azure Stream Analytics.

## <a name="stop-a-job-in-azure-portal"></a>Arrêter une tâche dans le portail Azure

1. Connectez-vous au [Portail Azure](https://portal.azure.com). 

2. Localisez votre tâche Stream Analytics en cours d’exécution et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sélectionnez **Arrêter** pour arrêter la tâche. 

   ![Arrêter une tâche Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/stop-stream-analytics-job.png)


## <a name="delete-a-job-in-azure-portal"></a>Supprimer une tâche dans le portail Azure

1. Connectez-vous au portail Azure. 

2. Localisez votre tâche Stream Analytics existante et sélectionnez-la.

3. Dans la page de la tâche Stream Analytics, sélectionnez **Supprimer** pour supprimer la tâche. 

   ![Supprimer une tâche Azure Stream Analytics](./media/stream-analytics-clean-up-your-job/delete-stream-analytics-job.png)


## <a name="stop-or-delete-a-job-using-powershell"></a>Arrêter ou supprimer une tâche avec PowerShell

Pour arrêter une tâche avec PowerShell, utilisez l’applet de commande [Stop-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/stop-azurermstreamanalyticsjob?view=azurermps-5.7.0). Pour supprimer une tâche avec PowerShell, utilisez l’applet de commande [Remove-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/Remove-AzureRmStreamAnalyticsJob?view=azurermps-5.7.0).

## <a name="stop-or-delete-a-job-using-azure-sdk-for-net"></a>Arrêter ou supprimer une tâche avec Azure SDK pour .NET

Pour arrêter un travail avec Azure SDK pour .NET, utilisez la méthode [StreamingJobsOperationsExtensions.BeginStop](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.beginstop?view=azure-dotnet). Pour supprimer un travail avec Azure SDK pour .NET, utilisez la méthode [StreamingJobsOperationsExtensions.BeginDelete](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.streamingjobsoperationsextensions.begindelete?view=azure-dotnet).

## <a name="stop-or-delete-a-job-using-rest-api"></a>Arrêter ou supprimer une tâche avec l’API REST

Pour arrêter une tâche avec l’API REST, référencez la méthode [Stop](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#stop). Pour supprimer une tâche avec l’API REST, référencez la méthode [Delete](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-job#delete).
