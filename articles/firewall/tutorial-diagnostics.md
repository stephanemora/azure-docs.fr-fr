---
title: Didacticiel - Surveiller les journaux d’activité de Pare-feu Azure et les métriques
description: Dans ce didacticiel, vous allez apprendre à activer et à gérer les journaux d’activité de Pare-feu Azure et les métriques.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: da46cf826da40658883d22692e5038b09d222907
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/24/2020
ms.locfileid: "75974530"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>Didacticiel : surveiller les journaux d’activité de Pare-feu Azure et les métriques

Vous pouvez surveiller le service Pare-feu Azure à l’aide des journaux d’activité de pare-feu. Vous pouvez également utiliser les journaux d’activité pour auditer les opérations sur les ressources de Pare-feu Azure. Grâce aux métriques, vous pouvez afficher des compteurs de performances dans le portail.

Vous pouvez accéder à certains de ces journaux d’activité via le portail. Les journaux d’activité peuvent être envoyés au service [Journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md), au stockage et aux hubs d’événements, puis analysés dans les journaux d’activité Azure Monitor ou par différents outils comme Excel et Power BI.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Dans ce tutoriel, vous allez apprendre à :

> [!div class="checklist"]
> * Activation de la journalisation avec le portail Azure
> * Activation de la journalisation avec PowerShell
> * Afficher et analyser le journal d’activité
> * Afficher et analyser les journaux d’activité de règles et d’application et de réseau
> * Afficher les mesures


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

Avant de commencer ce didacticiel, vous devez lire [Azure Firewall logs and metrics](logs-and-metrics.md) (Journaux d’activité de Pare-feu Azure et métriques) pour obtenir une vue d’ensemble des journaux de diagnostic et des métriques disponibles pour le Pare-feu Azure.


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Activer la journalisation des diagnostics via le portail Azure

L’affichage des données dans vos journaux d’activité peut prendre quelques minutes après que vous avez effectué cette procédure d’activation de la journalisation des diagnostics. Si aucune donnée n’apparaît dans un premier temps, patientez quelques minutes supplémentaires, puis vérifiez de nouveau.

1. Dans le portail Azure, ouvrez votre groupe de ressources de pare-feu, puis cliquez sur le pare-feu.
2. Sous **Supervision**, cliquez sur **Paramètres de diagnostic**.

   Pour le service Pare-feu Azure, les journaux d’activité propres à deux services sont disponibles :

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. Cliquez sur **Activer les diagnostics** pour démarrer la collecte de données.
4. La page **Paramètres de diagnostic** contient les paramètres des journaux de diagnostic.
5. Dans cet exemple, les journaux d’activité Azure Monitor stockent les journaux d’activité. Par conséquent, tapez **analytique des journaux d’activité de pare-feu** comme nom.
6. Cliquez sur **Envoyer à Log Analytics** pour configurer votre espace de travail. Vous pouvez également utiliser des concentrateurs d’événements et un compte de stockage pour enregistrer les journaux de diagnostic.
7. Sous **Log Analytics**, cliquez sur **Configurer**.
8. Sur la page Espaces de travail Log Analytics, cliquez sur **Créer un espace de travail**.
9. Sur la page **Espace de travail Log Analytics**, tapez **firewall-oms** comme nom du nouvel **Espace de travail Log Analytics**.
10. Sélectionnez votre abonnement, utilisez le groupe de ressources de pare-feu existant (**Test-FW-RG**), sélectionnez **USA Est** pour l’emplacement, puis le niveau tarifaire **Gratuit**.
11. Cliquez sur **OK**.
   ![Démarrage du processus de configuration][1] Les espaces de travail OMS sont désormais appelés espaces de travail Log Analytics.  
12. Sous **Journal**, cliquez sur **AzureFirewallApplicationRule** et **AzureFirewallNetworkRule** pour collecter des journaux d’activité pour les règles de réseau et d’application.
   ![Enregistrer les paramètres des diagnostics][2]
13. Cliquez sur **Enregistrer**.

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

La journalisation d’activité est automatiquement activée pour chaque ressource Resource Manager. Vous devez activer la journalisation des diagnostics pour commencer à collecter les données disponibles dans ces journaux d’activité.

Pour activer la journalisation des diagnostics, procédez comme suit :

1. Notez l’ID de ressource de votre compte de stockage, où les données de journalisation sont stockées. Cette valeur a le format suivant : */subscriptions/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Storage/storageAccounts/\<nom du compte de stockage\>* .

   Vous pouvez utiliser n’importe quel compte de stockage dans votre abonnement. Vous pouvez utiliser le portail Azure pour rechercher ces informations. Les informations se trouvent dans la page **Propriété** de la ressource.

2. Notez l’ID de ressource de votre pare-feu pour lequel la journalisation est activée. Cette valeur a le format suivant : */subscriptions/\<subscriptionId\>/resourceGroups/\<nom du groupe de ressources\>/providers/Microsoft.Network/azureFirewalls/\<nom du pare-feu\>* .

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

* **Outils Azure** : récupérez les informations à partir du journal d’activité au moyen d’Azure PowerShell, d’Azure CLI, de l’API REST Azure ou du Portail Azure. Des instructions pas à pas pour chaque méthode sont détaillées dans l’article [Opérations d’activité avec Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
* **Power BI** : si vous n’avez pas encore de compte [Power BI](https://powerbi.microsoft.com/pricing) , vous pouvez l’essayer gratuitement. À l’aide du [pack de contenus des journaux d’activité Azure pour Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), vous pouvez analyser vos données avec des tableaux de bord préconfigurés à utiliser en l’état ou à personnaliser.

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>Afficher et analyser les journaux d’activité de règles et d’application et de réseau

Les [journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md) collectent les compteurs et les fichiers journaux des événements. Il inclut des visualisations et des fonctionnalités puissantes de recherche pour analyser vos journaux d’activité.

Pour accéder aux exemples de requêtes d’analytique des journaux d’activité de Pare-feu Azure, consultez [Exemples d’analytique des journaux d’activité de pare-feu](log-analytics-samples.md).

Vous pouvez également vous connecter à votre compte de stockage et récupérer les entrées de journal d’activité JSON pour les journaux d’activité d’accès et des performances. Après avoir téléchargé les fichiers JSON, vous pouvez les convertir en CSV et les afficher dans Excel, PowerBI ou tout autre outil de visualisation de données.

> [!TIP]
> Si vous savez utiliser Visual Studio et les concepts de base de la modification des valeurs de constantes et variables en C#, vous pouvez utiliser les [outils de convertisseur de journaux](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponibles dans GitHub.

## <a name="view-metrics"></a>Afficher les mesures
Accédez à un Pare-feu Azure, sous **Supervision** cliquez sur **Métriques**. Pour afficher les valeurs disponibles, sélectionnez la liste déroulante **MÉTRIQUE**.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré votre pare-feu pour collecter des journaux d’activité, vous pouvez explorer les journaux d’activité Azure Monitor pour voir vos données.

> [!div class="nextstepaction"]
> [Solutions de supervision réseau dans les journaux d’activité Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
