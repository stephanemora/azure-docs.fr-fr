---
title: 'Didacticiel : Surveiller les journaux de Pare-feu Azure'
description: Dans ce didacticiel, vous allez apprendre à activer et à gérer les journaux de Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
ms.openlocfilehash: a4922fda80b957138a9929090f9d3c349348185d
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2018
ms.locfileid: "38991860"
---
# <a name="tutorial-monitor-azure-firewall-logs"></a>Didacticiel : Surveiller les journaux de Pare-feu Azure

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]

Les exemples indiqués dans les articles relatifs à Pare-feu Azure supposent que vous avez déjà activé la préversion publique de Pare-feu Azure. Pour plus d’informations, consultez [Activer la préversion publique de Pare-feu Azure](public-preview.md).

Vous pouvez surveiller le service Pare-feu Azure à l’aide des journaux de pare-feu. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure.

Vous pouvez accéder à certains de ces journaux via le portail. Les journaux peuvent être envoyés vers les services [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Stockage et Event Hubs, puis analysés dans Log Analytics ou par différents outils comme Excel et Power BI.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Activation de la journalisation avec le portail Azure
> * Activation de la journalisation avec PowerShell
> * Afficher et analyser le journal d’activité
> * Afficher et analyser les journaux de règles et d’application et de réseau

## <a name="diagnostic-logs"></a>Journaux de diagnostic

 Les journaux de diagnostic suivants sont disponibles pour Pare-feu Azure :

* **Journal de règles d’application**

   Le journal de règles d’application est enregistré dans un compte de stockage, transmis en continu au service Event Hubs et/ou envoyé vers Log Analytics uniquement si vous l’avez activé pour chaque Pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles d’application configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Journal de règles de réseau**

   Le journal de règles de réseau est enregistré dans un compte de stockage, transmis en continu au service Event Hubs et/ou envoyé vers Log Analytics uniquement si vous l’avez activé pour chaque Pare-feu Azure. Chaque nouvelle connexion qui correspond à l’une de vos règles de réseau configurées entraîne un journal pour la connexion acceptée/refusée. Les données sont consignées au format JSON, comme indiqué dans l’exemple suivant :

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

Pour stocker vos journaux, vous disposez de trois options :

* **Compte de stockage** : les comptes de stockage conviennent parfaitement aux journaux lorsqu’ils sont stockés pour une durée plus longue et consultés lorsque nécessaire.
* **Concentrateurs d’événements** : les concentrateurs d’événements constituent une excellente solution pour l’intégration avec d’autres outils SEIM (Security Information and Event Management) afin de recevoir des alertes sur vos ressources.
* **Log Analytics** : Log Analytics convient parfaitement pour la surveillance en temps réel générale de votre application ou la recherche de tendances.

## <a name="activity-logs"></a>Journaux d’activité

   Les entrées du journal d’activité sont recueillies par défaut et vous pouvez les afficher dans le Portail Azure.

   Vous pouvez utiliser les [journaux d’activité Azure](../azure-resource-manager/resource-group-audit.md) (anciennement journaux des opérations et journaux d’audit) pour afficher toutes les opérations soumises à votre abonnement Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Activer la journalisation des diagnostics via le portail Azure

L’affichage des données dans vos journaux peut prendre quelques minutes après que vous avez effectué cette procédure d’activation de la journalisation des diagnostics. Si aucune donnée n’apparaît dans un premier temps, patientez quelques minutes supplémentaires, puis vérifiez de nouveau.

1. Dans le portail Azure, ouvrez votre groupe de ressources de pare-feu, puis cliquez sur le pare-feu.
2. Sous **Supervision**, cliquez sur **Journaux de diagnostic**.

   Pour le service Pare-feu Azure, les journaux propres à deux services sont disponibles :

   * Journal de règles d’application
   * Journal de règles de réseau

3. Cliquez sur **Activer les diagnostics** pour démarrer la collecte de données.
4. La page **Paramètres de diagnostic** contient les paramètres des journaux de diagnostic. 
5. Dans cet exemple, Log Analytics stocke les journaux. Tapez le nom **Firewall log analytics**.
6. Cliquez sur **Envoyer à Log Analytics** pour configurer votre espace de travail. Vous pouvez également utiliser des concentrateurs d’événements et un compte de stockage pour enregistrer les journaux de diagnostic.
7. Sous **Log Analytics**, cliquez sur **Configurer**.
8. Dans la page Espaces de travail OMS, cliquez sur **Créer un espace de travail**.
9. Dans la page **Espace de travail Log Analytics**, tapez **firewall-oms** comme nom du nouvel **Espace de travail OMS**.
10. Sélectionnez votre abonnement, utilisez le groupe de ressources de pare-feu existant (**Test-FW-RG**), sélectionnez **USA Est** pour l’emplacement, puis le niveau tarifaire **Gratuit**.
11. Cliquez sur **OK**.
   ![Démarrage du processus de configuration][1]
12. Sous **Journal**, cliquez sur **AzureFirewallApplicationRule** et **AzureFirewallNetworkRule** pour collecter des journaux pour les règles de réseau et d’application.
   ![Enregistrer les paramètres des diagnostics][2]
13. Cliquez sur **Enregistrer**.

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation des diagnostics pour commencer à collecter les données disponibles dans ces journaux.

Pour activer la journalisation des diagnostics, procédez comme suit :

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Cette valeur a le format suivant : */subscriptions/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Storage/storageAccounts/\<nom du compte de stockage\>*.

   Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser le portail Azure pour rechercher ces informations. Les informations se trouvent dans la page **Propriété** de la ressource.

2. Notez l’ID de ressource de votre pare-feu pour lequel la journalisation est activée. Cette valeur a le format suivant : */subscriptions/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Network/azureFirewalls/\<nom du pare-feu\>*.

   Vous pouvez utiliser le portail pour rechercher ces informations.

3. Activez la journalisation des diagnostics à l’aide de l’applet de commande PowerShell suivante :

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Les journaux de diagnostic ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation de l’accès et des performances occasionne des frais de service.

## <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure** : récupérez les informations à partir du journal d’activité au moyen d’Azure PowerShell, d’Azure CLI, de l’API REST Azure ou du Portail Azure. Des instructions pas à pas pour chaque méthode sont détaillées dans l’article [Opérations d’activité avec Resource Manager](../azure-resource-manager/resource-group-audit.md).
* **Power BI** : si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité Azure pour Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Afficher et analyser les journaux de règles et d’application et de réseau

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) collecte les fichiers journaux des événements et des compteurs. Il inclut des visualisations et des fonctionnalités puissantes de recherche pour analyser vos journaux.

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal JSON pour les journaux d’accès et des performances. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.


## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré votre pare-feu pour collecter des journaux, vous pouvez explorer Log Analytics pour afficher vos données.

> [!div class="nextstepaction"]
> [Solutions d’analyse réseaux dans Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
