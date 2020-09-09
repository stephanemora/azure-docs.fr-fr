---
title: Superviser les journaux d’activité et les métriques du Pare-feu Azure
description: Dans cet article, vous allez apprendre à activer et gérer les journaux d'activité et les métriques du Pare-feu Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 09/02/2020
ms.author: victorh
ms.openlocfilehash: 92fc4252dd52236e2cc4e8fdfdd2afa32059a721
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89376942"
---
# <a name="monitor-azure-firewall-logs-and-metrics"></a>Superviser les journaux d’activité et les métriques du Pare-feu Azure

Vous pouvez surveiller le service Pare-feu Azure à l’aide des journaux d’activité de pare-feu. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure. Grâce aux métriques, vous pouvez afficher des compteurs de performances dans le portail.

Vous pouvez accéder à certains de ces journaux d’activité via le portail. Les journaux d’activité peuvent être envoyés au service [Journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), au stockage et aux hubs d’événements, puis analysés dans les journaux d’activité Azure Monitor ou par différents outils comme Excel et Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer, consultez [Journaux d'activité et métriques du Pare-feu Azure](logs-and-metrics.md) pour obtenir une vue d'ensemble des journaux de diagnostic et des métriques disponibles pour le Pare-feu Azure.

## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Activer la journalisation des diagnostics via le portail Azure

L’affichage des données dans vos journaux d’activité peut prendre quelques minutes après que vous avez effectué cette procédure d’activation de la journalisation des diagnostics. Si aucune donnée n’apparaît dans un premier temps, patientez quelques minutes supplémentaires, puis vérifiez de nouveau.

1. Sur le portail Azure, ouvrez votre groupe de ressources de pare-feu et sélectionnez le pare-feu.
2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

   Pour le service Pare-feu Azure, les journaux d’activité propres à deux services sont disponibles :

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Sélectionnez **Ajouter le paramètre de diagnostic**. La page **Paramètres de diagnostic** contient les paramètres des journaux de diagnostic.
5. Dans cet exemple, les journaux d’activité Azure Monitor stockent les journaux d’activité. Par conséquent, tapez **analytique des journaux d’activité de pare-feu** comme nom.
6. Sous **Journal**, sélectionnez **AzureFirewallApplicationRule** et **AzureFirewallNetworkRule** afin de collecter des journaux d'activité pour les règles de réseau et d'application.
7. Sélectionnez **Envoyer à Log Analytics** pour configurer votre espace de travail.
8. Sélectionnez votre abonnement.
9. Sélectionnez **Enregistrer**.

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation des diagnostics pour commencer à collecter les données disponibles dans ces journaux d’activité.

Pour activer la journalisation des diagnostics, procédez comme suit :

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Cette valeur se présente sous la forme : */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>* .

   Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser le portail Azure pour rechercher ces informations. Les informations se trouvent dans la page **Propriété** de la ressource.

2. Notez l’ID de ressource de votre pare-feu pour lequel la journalisation est activée. Cette valeur se présente sous la forme : */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/azureFirewalls/\<Firewall name\>* .

   Vous pouvez utiliser le portail pour rechercher ces informations.

3. Activez la journalisation des diagnostics à l’aide de l’applet de commande PowerShell suivante :

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```

> [!TIP]
>Les journaux de diagnostic ne nécessitent pas de compte de stockage distinct. L’utilisation du stockage pour la journalisation de l’accès et des performances occasionne des frais de service.

## <a name="view-and-analyze-the-activity-log"></a>Afficher et analyser le journal d’activité

Vous pouvez afficher et analyser les données du journal d’activité en utilisant l’une des méthodes suivantes :

* **Outils Azure** : récupérez les informations du journal d’activité en utilisant Azure PowerShell, Azure CLI, l’API REST Azure ou le portail Azure. Des instructions pas à pas pour chaque méthode sont détaillées dans l’article [Opérations d’activité avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI** : si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing), vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité Azure pour Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.
* **Azure Sentinel** : Vous pouvez connecter des journaux Pare-feu Azure à Azure Sentinel, ce qui vous permet d’afficher les données des journaux dans des classeurs, de les utiliser pour créer des alertes personnalisées et de les incorporer pour améliorer votre investigation. Le connecteur de données Pare-feu Azure dans Azure Sentinel est en préversion publique. Pour plus d’informations, consultez [Connecter des données à partir du Pare-feu Azure](../sentinel/connect-azure-firewall.md).

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Afficher et analyser les journaux d’activité de règles et d’application et de réseau

Les [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) collectent les compteurs et les fichiers journaux des événements. Il inclut des visualisations et des fonctionnalités puissantes de recherche pour analyser vos journaux d’activité.

Pour accéder aux exemples de requêtes d’analytique des journaux d’activité de Pare-feu Azure, consultez [Exemples d’analytique des journaux d’activité de pare-feu](log-analytics-samples.md).

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal d’activité JSON pour les journaux d’activité d’accès et des performances. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.

## <a name="view-metrics"></a>Afficher les mesures
Accédez à un Pare-feu Azure. Sous **Supervision**, sélectionnez **Métriques**. Pour afficher les valeurs disponibles, sélectionnez la liste déroulante **MÉTRIQUE**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré votre pare-feu pour collecter des journaux d’activité, vous pouvez explorer les journaux d’activité Azure Monitor pour voir vos données.

[Solutions de supervision réseau dans les journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)
