---
title: Configurer la surveillance et les alertes pour les tâches Azure Stream Analytics
description: Cet article explique comment utiliser le portail Azure pour configurer la surveillance, ainsi que des alertes pour les tâches Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/26/2017
ms.openlocfilehash: 4c676ab3039a02a4fda27ab00312133e5de8077a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "35771902"
---
# <a name="set-up-alerts-for-azure-stream-analytics-jobs"></a>Configuration d’alertes pour des tâches Azure Stream Analytics
Vous pouvez configurer des règles pour déclencher une alerte quand une mesure atteint une condition que vous spécifiez. Par exemple, vous pouvez configurer une alerte pour une condition comme suit :

`If there are zero input events in the last 5 minutes, send email notification to sa-admin@example.com`

Les règles peuvent être configurées sur des mesures par le biais du portail ou [par programmation](https://code.msdn.microsoft.com/windowsazure/Receive-Email-Notifications-199e2c9a) sur les données des journaux des opérations.

## <a name="set-up-alerts-in-the-azure-portal"></a>Configurer des alertes dans le portail Azure
1. Dans le portail Azure, ouvrez le travail Stream Analytics pour lequel vous souhaitez créer une alerte. 

2. Dans le panneau **Travail**, cliquez sur la section **Surveillance**.  

3. Dans le panneau **Métrique**, cliquez sur la commande **Ajouter une alerte**.

      ![Configuration du portail Azure](./media/stream-analytics-set-up-alerts/06-stream-analytics-set-up-alerts.png)  

4. Entrez un nom et une description.

5. Utilisez les sélecteurs pour définir la condition dans laquelle l’alerte est envoyée.

6. Indiquez des informations sur la destination de l’alerte.

      ![Configuration d’une alerte pour un travail Azure Stream Analytics](./media/stream-analytics-set-up-alerts/stream-analytics-add-alert.png)  

Pour plus d’informations sur la configuration d’alertes dans le portail Azure, consultez [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).  


## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l’échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

