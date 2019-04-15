---
title: Diffuser en continu le journal des activités Azure sur les Event Hubs
description: Apprenez comment diffuser en continu le journal d’activité Azure sur les Event Hubs.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: 3d4c1029315e754410f31b13042d1d6acb105da1
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2019
ms.locfileid: "57309687"
---
# <a name="stream-the-azure-activity-log-to-event-hubs"></a>Diffuser en continu le journal des activités Azure sur les Event Hubs
Vous pouvez diffuser en continu le [journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md) en temps quasi réel vers n’importe quelle application :

* À l’aide de la fonction **Exportation** intégrée dans le portail
* En activant l’ID de règle Azure Service Bus dans un profil de journal via les applets de commande Azure PowerShell ou CLI d’Azure

## <a name="what-you-can-do-with-the-activity-log-and-event-hubs"></a>Ce que vous pouvez faire avec le journal d’activité et Event Hubs
Voici deux façons d’utiliser la fonctionnalité de diffusion en continu pour le journal d’activité :

* **Diffuser en continu sur des systèmes de journalisation et de télémétrie tiers** : Au fil du temps, la diffusion en continu sur Azure Event Hubs deviendra le mécanisme de diffusion de votre journal d’activité vers les solutions tierces SIEM et Log Analytics.
* **Créer une plateforme de journalisation et de télémétrie personnalisée** : Si vous disposez déjà d’une plate-forme de télémétrie personnalisée, ou si vous envisagez d’en créer une, la nature hautement évolutive de publication et d’abonnement d’Event Hubs vous permet d’intégrer avec souplesse le journal d’activité. Pour plus d’informations, regardez la [vidéo de Dan Rosanova sur l’utilisation d’Event Hubs dans une plateforme de télémétrie à l’échelle mondiale](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-the-activity-log"></a>Activer la diffusion en continu du journal d’activité
Vous pouvez activer la diffusion en continu du journal d’activité soit par programmation, soit via le portail. Dans tous les cas, vous pouvez choisir un espace de noms Event Hubs et une stratégie d’accès partagé pour cet espace de noms. Un Hub d’événements appelé insights-logs-operationallogs est créé dans cet espace de noms lorsque le premier nouvel événement de journal d’activité se produit. 

Si vous n’avez pas d’espace de noms Event Hubs, vous devez d’abord en créer un. Si vous avez précédemment diffusé en continu des événements du journal d’activité vers cet espace de noms Event Hubs, le Hub d’événements créé précédemment sera réutilisé. 

La stratégie d’accès partagé définit les autorisations dont dispose le mécanisme de diffusion en continu. À l’heure actuelle, la diffusion vers Event Hubs requiert des autorisations de **gestion**, d’**envoi** et d’**écoute**. Vous pouvez créer ou modifier des stratégies d’accès partagé de l’espace de noms Event Hubs dans le portail Azure, sous l’onglet **Configurer** de votre espace de noms Event Hubs. 

Pour mettre à jour le profil de journal d’activité afin d’inclure la diffusion en continu, l’utilisateur qui apporte la modification doit avoir l’autorisation ListKey sur la règle d’autorisation Event Hubs. Il n’est pas nécessaire que l’espace de noms Event Hubs se trouve dans le même abonnement que l’abonnement qui génère des journaux, à condition que l’utilisateur configurant le paramètre dispose d’un accès RBAC aux deux abonnements, et que ces derniers se trouvent dans le même locataire AAD.

### <a name="via-the-azure-portal"></a>Via le portail azure
1. Accédez au panneau **Journal d’activité** à l’aide de la recherche **Tous les services** sur le côté gauche du portail.
   
   ![Sélection du journal d’activité dans la liste des services du portail](./media/activity-logs-stream-event-hubs/activity-logs-portal-navigate-v2.png)
2. Sélectionnez le bouton **Exporter vers Event Hub** en haut du journal.
   
   ![Bouton Exporter dans le portail](./media/activity-logs-stream-event-hubs/activity-logs-portal-export-v2.png)

   Notez que les paramètres de filtre que vous aviez appliqué lors de l’affichage du journal d’activité dans la vue précédente n’ont aucun impact sur vos paramètres d’exportation. Ils concernent uniquement le filtrage que vous voyez lorsque vous parcourez votre journal d’activité dans le portail.
3. Dans la section qui s’affiche, sélectionnez **Toutes les régions**. Ne sélectionnez pas de régions particulières.
   
   ![Section Exportation](./media/activity-logs-stream-event-hubs/export-audit.png)

   > [!WARNING]  
   > Si vous sélectionnez autre chose que **Toutes les régions**, vous manquerez des événements clés que vous attendez de recevoir. Le journal d’activité étant un journal global (non régional), aucune région n’est associée à la plupart des événements. 
   >

4. Cliquez sur l’option **Azure Event Hubs**, sélectionnez l’espace de noms Event Hubs vers lequel les journaux doivent être envoyés, puis cliquez sur **OK**.
5. Sélectionnez **Enregistrer** pour enregistrer ces paramètres. Les paramètres sont appliqués immédiatement à votre abonnement.
6. Si vous avez plusieurs abonnements, répétez cette action et envoyez toutes les données au même Hub d’événements.

### <a name="via-powershell-cmdlets"></a>Via les applets de commande PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si un profil de journal existe déjà, vous devez tout d’abord le supprimer, puis créer un nouveau profil de journal.

1. Utilisez `Get-AzLogProfile` pour déterminer s’il existe un profil de journal.  S’il n’existe aucun profil de journal, recherchez la propriété *name*.
2. Utilisez `Remove-AzLogProfile` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```
3. Utilisez `Add-AzLogProfile` pour créer un profil de journal :

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```

### <a name="via-azure-cli"></a>Via l’interface de ligne de commande Azure
Si un profil de journal existe déjà, vous devez tout d’abord le supprimer, puis créer un nouveau profil de journal.

1. Utilisez `az monitor log-profiles list` pour déterminer s’il existe un profil de journal.
2. Utilisez `az monitor log-profiles delete --name "<log profile name>` pour supprimer le profil de journal à l’aide de la valeur provenant de la propriété *name*.
3. Utilisez `az monitor log-profiles create` pour créer un profil de journal :

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

## <a name="consume-the-log-data-from-event-hubs"></a>Utilisez les données de journal d’Event Hubs
Le schéma pour le journal d’activité est disponible dans [Surveiller l’activité d’abonnement avec le journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md). Chaque événement est dans un tableau d’objets blob JSON appelé *enregistrements*.

## <a name="next-steps"></a>Étapes suivantes
* [Archiver le journal d’activité dans un compte de stockage](../../azure-monitor/platform/archive-activity-log.md)
* [Lire la présentation du journal d’activité Azure](../../azure-monitor/platform/activity-logs-overview.md)
* [Définir une alerte basée sur un événement de journal d’activité](../../azure-monitor/platform/alerts-log-webhook.md)


