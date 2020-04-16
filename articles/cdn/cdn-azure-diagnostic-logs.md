---
title: Journaux de diagnostic Azure | Microsoft Docs
description: Le client peut activer l’analyse des journaux pour Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 35d028a38e6ac19f270abcc8708a532b3749eb39
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "81254799"
---
# <a name="azure-diagnostic-logs"></a>Journaux de diagnostic Azure

Les journaux de diagnostic Azure permettent d’afficher l’analytique principale et de l’enregistrer dans une ou plusieurs destinations, dont les suivantes :

 - Compte de Stockage Azure
 - Hubs d'événements Azure
 - [Espace de travail Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Cette fonctionnalité est disponible sur les points de terminaison de CDN à tous les niveaux tarifaires. 

Les journaux de diagnostic Azure vous permettent d’exporter des métriques d’utilisation de base de votre point de terminaison CDN vers diverses sources afin de pouvoir les utiliser de façon personnalisée. Vous pouvez par exemple exécuter les types suivants d’exportation de données :

- Exporter des données vers un stockage d’objets blob, exporter au format CSV et générer des graphiques dans Excel.
- Exporter des données vers des hubs d’événements et mettre les données en corrélation avec d’autres services Azure.
- Exporter des données vers des journaux Azure Monitor et afficher les données dans votre propre espace de travail Log Analytics

Le diagramme suivant montre une vue des données au moyen de l’analytique principale CDN classique.

![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Figure 1 : Vue avec l’analytique principale CDN*

Pour plus d’informations sur les journaux de diagnostic, consultez [Journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Activation de la journalisation avec le portail Azure

Suivez les étapes suivantes pour activer la journalisation avec l’analytique principale CDN :

Connectez-vous au [portail Azure](https://portal.azure.com). Si vous n’avez pas déjà activé CDN pour votre flux de travail, [créez un point de terminaison et un profil de réseau de distribution de contenu Azure](cdn-create-new-endpoint.md) avant de continuer.

1. Dans le portail Azure, accédez au **profil CDN**.

2. Dans le portail Azure, recherchez le profil CDN ou sélectionnez-en un dans votre tableau de bord. Choisissez ensuite le point de terminaison CDN pour lequel vous souhaitez activer les journaux de diagnostic.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Dans la section SURVEILLANCE, sélectionnez **Journaux de diagnostic**.

   La page **Journaux de diagnostic** s’affiche.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Activation de la journalisation avec Stockage Azure

Pour utiliser un compte de stockage afin de stocker les journaux d’activité, effectuez les étapes suivantes :
    
1. Dans le champ **Nom**, entrez un nom pour les paramètres du journal de diagnostic.
 
2. Sélectionnez **Archiver dans un compte de stockage**, puis **CoreAnalytics**. 

2. Dans le champ **Rétention (jours)** , choisissez le nombre de jours de rétention. Si la valeur zéro est appliquée à la rétention, les journaux d’activité sont stockés pour une durée indéfinie. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Sélectionnez **Compte de stockage**.

    La page **Sélectionner un compte de stockage** apparaît.

4. Choisissez un compte de stockage dans la liste déroulante, puis sélectionnez **OK**.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Après avoir défini les paramètres de votre journal de diagnostic, sélectionnez **Enregistrer**.

### <a name="logging-with-azure-monitor"></a>Journalisation avec Azure Monitor

Pour stocker les journaux à l’aide d’Azure Monitor, effectuez les étapes suivantes :

1. Dans la page **Journaux de diagnostic**, sélectionnez **Envoyer à Log Analytics**. 

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Sélectionnez **Configurer** pour configurer la journalisation Azure Monitor. 

   La page **Espace de travail Log Analytics** s’affiche.

    >[!NOTE] 
    >Les espaces de travail OMS sont désormais appelés « espaces de travail Log Analytics ».

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Sélectionnez **Créer un espace de travail**.

    La page **Espace de travail Log Analytics** s’affiche.

    >[!NOTE] 
    >Les espaces de travail OMS sont désormais appelés « espaces de travail Log Analytics ».

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/07_Create-new.png)

4. Pour **Espace de travail Log Analytics**, entrez un nom d’espace de travail Log Analytics. Les noms d’espace de travail Log Analytics doivent être uniques et contenir uniquement des lettres, des chiffres et des traits d’union. Les espaces et les traits de soulignement ne sont pas autorisés. 

5. Dans le champ **Abonnement**, sélectionnez un abonnement existant dans la liste déroulante. 

6. Dans le champ **Groupe de ressources**, créez un groupe de ressources Azure ou sélectionnez un groupe existant.

7. Dans le champ **Emplacement**, sélectionnez un emplacement dans la liste.

8. Sélectionnez **Épingler au tableau de bord** si vous souhaitez enregistrer la configuration du journal dans votre tableau de bord. 

9. Sélectionnez **OK** pour achever la configuration.

10. Une fois votre espace de travail créé, la page **Journaux de diagnostic** s’affiche de nouveau. Confirmez le nom de votre nouvel espace de travail Log Analytics.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Sélectionnez **CoreAnalytics**, puis **Enregistrer**.

12. Pour afficher le nouvel espace de travail Log Analytics, sélectionnez **Analytique principale** dans la page de votre point de terminaison CDN.

    ![Portail - Journaux de diagnostics](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Votre espace de travail Log Analytics est maintenant prêt à enregistrer des données. Pour utiliser ces données, vous devez recourir à une [solution de journaux Azure Monitor](#consuming-diagnostics-logs-from-a-log-analytics-workspace), comme indiqué plus loin dans cet article.

Pour plus d’informations sur les retards des données de journal, consultez [Retards des données de journal](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Activation de la journalisation avec PowerShell

L’exemple suivant montre comment activer les journaux de diagnostic via les applets de commande Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Activation des journaux de diagnostic dans un compte de stockage

1. Connectez-vous et sélectionnez un abonnement :

    Connect-AzAccount 

    Select-AzureSubscription -SubscriptionId 

2. Pour activer les journaux de diagnostic dans un compte de stockage, entrez cette commande :

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Pour activer les journaux de diagnostic dans un espace de travail Log Analytics, entrez cette commande :

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Utilisation des journaux de diagnostic à partir de Stockage Azure
Cette section décrit le schéma de l’analytique principale CDN et leur organisation à l’intérieur d’un compte de stockage Azure, et fournit un exemple de code pour télécharger les journaux d’activité dans un fichier CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Utilisation de l’explorateur de stockage Microsoft Azure
Pour pouvoir accéder aux données d’analyse de base à partir d’un compte de stockage Azure, vous avez d’abord besoin d’un outil pour accéder au contenu dans un compte de stockage. Si plusieurs outils sont disponibles sur le marché, celui que nous recommandons est l’Explorateur de stockage Microsoft Azure. Pour télécharger l’outil, consultez [Explorateur de stockage Azure](https://storageexplorer.com/). Après le téléchargement et l’installation du logiciel, configurez-le pour utiliser le même compte de stockage Azure que celui qui a été configuré en tant que destination pour les journaux de diagnostic CDN.

1.  Ouvrez **l’explorateur de stockage Microsoft Azure**
2.  Localiser le compte de stockage
3.  Développez le nœud **Conteneurs d’objets Blob** sous ce compte de stockage.
4.  Sélectionnez le conteneur nommé *journaux-insights-coreanalytics*.
5.  Les résultats s’affichent dans le volet droit, en commençant par le premier niveau, soir *resourceId=* . Continuez à sélectionner chaque niveau jusqu’à atteindre le fichier *PT1H.json*. Pour obtenir une explication sur le chemin, consultez [Format du chemin des objets blob](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Chaque fichier d’objet blob *PT1H.json* représente les journaux d’activité d’analyse pendant une heure pour un point de terminaison CDN spécifique ou son domaine personnalisé.
7.  Le schéma du contenu de ce fichier JSON est décrit dans la section Schéma des journaux d’activité Core Analytics.


#### <a name="blob-path-format"></a>Format du chemin des objets blob

Les journaux d’activité Core Analytics sont générés toutes les heures et les données sont collectées et stockées dans un objet blob Azure unique sous forme de charge utile JSON. Étant donné que l’outil Explorateur Stockage interprète « / » comme un séparateur de répertoires et affiche la hiérarchie, le chemin d’accès à l’objet blob Azure apparaît sous forme de structure hiérarchique et représente le nom de l’objet blob. Ce nom d’objet blob suit la convention d’affectation de noms suivante :   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Description des champs :**

|Valeur|Description|
|-------|---------|
|Identifiant d’abonnement    |ID de l’abonnement Azure au format Guid.|
|Nom du groupe de ressources |Nom du groupe de ressources auquel les ressources CDN appartiennent.|
|Nom de profil |Nom du profil CDN|
|Nom du point de terminaison |Nom du point de terminaison CDN|
|Year|  Représentation à quatre chiffres de l’année, par exemple, 2017|
|Month| Représentation à deux chiffres du mois de l’année. 01 = Janvier ... 12 = Décembre|
|jour|   Représentation à deux chiffres du jour du mois|
|PT1H.json| Fichier JSON où sont effectivement stockées les données d’analyse|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Exportation des données de Core Analytics vers un fichier CSV

Pour faciliter l’accès à Core Analytics, nous fournissons un exemple de code pour un outil. Cet outil permet de télécharger les fichiers JSON dans un format de fichier plat avec séparation par virgules, qui peut être utilisé pour créer des graphiques ou autres agrégations.

Voici comment vous pouvez utiliser l’outil :

1.  Consultez le lien GitHub : [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Téléchargez le code.
3.  Suivez les instructions pour la compilation et la configuration.
4.  Exécutez l’outil.
5.  Le fichier CSV résultant présente les données d’analyse dans une hiérarchie plate simple.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Utilisation des journaux de diagnostic à partir d’un espace de travail Log Analytics
Azure Monitor est un service qui supervise vos environnements cloud et locaux, afin de préserver leur disponibilité et leurs performances. Il collecte les données générées par les ressources de votre cloud et de vos environnements locaux et d’autres outils d’analyse pour fournir une analyse sur plusieurs sources. 

Pour utiliser Azure Monitor, vous devez [activer la journalisation](#enable-logging-with-azure-storage) dans l’espace de travail Azure Log Analytics, dont il est question plus haut dans cet article.

### <a name="using-the-log-analytics-workspace"></a>Utilisation de l’espace de travail Log Analytics

 Le diagramme suivant illustre l’architecture des entrées et sorties du référentiel :

![Espace de travail Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figure 3 : référentiel OMS Log Analytics*

Vous pouvez afficher les données de plusieurs façons à l’aide des solutions de gestion. Vous pouvez obtenir des solutions de gestion à partir de la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Vous pouvez installer des solutions de supervision à partir de la Place de marché Azure en sélectionnant le lien **Obtenir maintenant** au bas de chaque solution.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Ajouter une solution de supervision CDN Azure Monitor

Suivez les étapes ci-après pour ajouter une solution de supervision Azure Monitor :

1.   Connectez-vous au portail Azure à l’aide de votre abonnement Azure et accédez à votre tableau de bord.
    ![Tableau de bord Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Dans la page **Nouveau** sous **Marketplace**, sélectionnez **Surveillance + gestion**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Dans la page **Surveillance + gestion**, cliquez sur **Afficher tout**.

    ![Afficher tout](./media/cdn-diagnostics-log/15_See-all.png)

4. Recherchez CDN dans la zone de recherche.

    ![Afficher tout](./media/cdn-diagnostics-log/16_Search-for.png)

5. Sélectionnez **Analytique principale Azure CDN**. 

    ![Afficher tout](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Après avoir sélectionné **Créer**, vous êtes invité à créer un espace de travail Log Analytics ou à en utiliser un existant. 

    ![Afficher tout](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Sélectionnez l’espace de travail que vous avez créé. Vous devez ensuite ajouter un compte Automation.

    ![Afficher tout](./media/cdn-diagnostics-log/19_Add-automation.png)

8. L’écran suivant montre le formulaire de compte Automation que vous devez remplir. 

    ![Afficher tout](./media/cdn-diagnostics-log/20_Automation.png)

9. Après avoir créé le compte Automation, vous êtes prêt à ajouter votre solution. Cliquez sur le bouton **Créer**.

    ![Afficher tout](./media/cdn-diagnostics-log/21_Ready.png)

10. Votre solution a maintenant été ajoutée à votre espace de travail. Retournez à votre tableau de bord du portail Azure.

    ![Afficher tout](./media/cdn-diagnostics-log/22_Dashboard.png)

    Sélectionnez l’espace de travail Log Analytics que vous avez créé pour accéder à votre espace de travail. 

11. Sélectionnez la mosaïque **Portail OMS** pour afficher votre nouvelle solution.

    ![Afficher tout](./media/cdn-diagnostics-log/23_workspace.png)

12. Votre portail doit maintenant ressembler à l’écran suivant :

    ![Afficher tout](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Sélectionnez une des vignettes pour afficher plusieurs vues de vos données.

    ![Afficher tout](./media/cdn-diagnostics-log/25_Interior-view.png)

    Vous pouvez faire défiler l’écran vers la gauche ou la droite pour voir d’autres vignettes représentant des vues des données. 

    Sélectionnez une des vignettes pour obtenir des détails supplémentaires sur vos données.

     ![Afficher tout](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Offres et niveaux tarifaires

Vous pouvez afficher des offres et des niveaux tarifaires pour les solutions de gestion [ici](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions).

### <a name="customizing-views"></a>Personnalisation des vues

Vous pouvez personnaliser la vue de vos données à l’aide du **Concepteur de vues**. Pour commencer la conception, accédez à votre espace de travail Log Analytics, puis sélectionnez la mosaïque **Concepteur de vues**.

![Concepteur de vue](./media/cdn-diagnostics-log/27_Designer.png)

Faites glisser et déplacez les types de graphiques et renseignez le détail des données que vous souhaitez analyser.

![Concepteur de vue](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Retards des données de journal

Le tableau suivant présente les retards des données de journal pour **CDN Azure Standard fourni par Microsoft**, **CDN Azure Standard fourni par Akamai** et **CDN Azure Standard/Premium fourni par Verizon**.

Retards des données de journal Microsoft | Retards des données de journal Verizon | Retards des données de journal Akamai
--- | --- | ---
Retard de 1 heure. | Retard de 1 heure et possibilité de nécessiter jusqu’à 2 heures pour commencer à apparaître après l’achèvement de la propagation du point de terminaison. | Retard de 24 heures. Si la création remonte à plus de 24 heures, il faut attendre jusqu’à 2 heures avant leur apparition. Si elle a été effectuée récemment, il peut falloir jusqu’à 25 heures pour que les journaux d’activité commencent à apparaître.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Types de journaux de diagnostic pour l’analytique principale CDN

Microsoft propose uniquement les journaux d’activité de l’analytique principale, qui contiennent les métriques affichant les statistiques de réponse HTTP et de sortie, comme dans les points de présence/périmètres CDN.

### <a name="core-analytics-metrics-details"></a>Détails des métriques de Core Analytics
Le tableau suivant présente la liste des métriques disponibles dans les journaux d’activité d’analyse principale pour **CDN Azure Standard fourni par Microsoft**, **CDN Azure Standard fourni par Akamai** et **CDN Azure Standard/Premium fourni par Verizon**. Toutes les métriques ne sont pas disponibles auprès tous les fournisseurs, même si ces différences sont minimes. Ce tableau indique également si une métrique donnée est disponible à partir d’un fournisseur particulier. Les métriques sont disponibles uniquement pour les points de terminaison CDN recevant du trafic.


|Métrique                     | Description | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Nombre total d’occurrences de requêtes pendant cette période. | Oui | Oui |Oui |
| RequestCountHttpStatus2xx | Nombre total des requêtes ayant abouti à un code HTTP 2xx (par exemple, 200, 202). | Oui | Oui |Oui |
| RequestCountHttpStatus3xx | Nombre total des requêtes ayant abouti à un code HTTP 3xx (par exemple, 300, 302). | Oui | Oui |Oui |
| RequestCountHttpStatus4xx | Nombre total des requêtes ayant abouti à un code HTTP 4xx (par exemple, 400, 404). | Oui | Oui |Oui |
| RequestCountHttpStatus5xx | Nombre total des requêtes ayant abouti à un code HTTP 5xx (par exemple, 500, 504). | Oui | Oui |Oui |
| RequestCountHttpStatusOthers | Nombre total des autres codes HTTP (en dehors de 2xx-5xx). | Oui | Oui |Oui |
| RequestCountHttpStatus200 | Nombre total des requêtes ayant abouti à un code HTTP 200. | Oui | Non  |Oui |
| RequestCountHttpStatus206 | Nombre total des requêtes ayant abouti à un code HTTP 206. | Oui | Non  |Oui |
| RequestCountHttpStatus302 | Nombre total des requêtes ayant abouti à un code HTTP 302. | Oui | Non  |Oui |
| RequestCountHttpStatus304 | Nombre total des requêtes ayant abouti à un code HTTP 304. | Oui | Non  |Oui |
| RequestCountHttpStatus404 | Nombre total des requêtes ayant abouti à un code HTTP 404. | Oui | Non  |Oui |
| RequestCountCacheHit | Nombre de toutes les requêtes qui ont abouti à un accès au cache. La ressource a été traitée directement du point de présence vers le client. | Oui | Oui | Non  |
| RequestCountCacheMiss | Nombre de toutes les requêtes qui ont abouti à un échec de cache. Un échec de cache signifie que la ressource est introuvable sur le point de présence le plus proche du client et a donc été récupérée à l’origine. | Oui | Oui | Non |
| RequestCountCacheNoCache | Nombre de requêtes vers une ressource empêchées d’être mises en cache en raison d’une configuration de l’utilisateur sur le périmètre. | Oui | Oui | Non |
| RequestCountCacheUncacheable | Nombre total des requêtes de ressources que les en-têtes Cache-Control et Expires empêchent de mettre en cache, et qui indiquent qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP. | Oui | Oui | Non |
| RequestCountCacheOthers | Nombre de toutes les requêtes avec un état du cache non traité ci-dessus. | Non | Oui | Non  |
| EgressTotal | Transfert de données sortantes en Go | Oui |Oui |Oui |
| EgressHttpStatus2xx | Transfert de données sortantes* pour les réponses avec des codes d’état HTTP 2xx en Go. | Oui | Oui | Non  |
| EgressHttpStatus3xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 3xx en Go. | Oui | Oui | Non  |
| EgressHttpStatus4xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 4xx en Go. | Oui | Oui | Non  |
| EgressHttpStatus5xx | Transfert de données sortantes pour les réponses avec des codes d’état HTTP 5xx en Go. | Oui | Oui | Non |
| EgressHttpStatusOthers | Transfert de données sortantes pour les réponses avec d’autres codes d’état HTTP en Go. | Oui | Oui | Non  |
| EgressCacheHit | Transfert de données sortantes pour les réponses qui ont été livrées directement à partir du cache CDN sur les points de présence/périmètres CDN. | Oui | Oui | Non |
| EgressCacheMiss. | Transfert des données sortantes pour les réponses introuvables sur le serveur POP le plus proche et récupérées à partir du serveur d’origine. | Oui | Oui | Non |
| EgressCacheNoCache | Transferts de données sortants pour les ressources empêchés d’être mis en cache en raison d’une configuration de l’utilisateur sur le périmètre. | Oui | Oui | Non |
| EgressCacheUncacheable | Transfert de données sortantes pour les ressources empêchées d’être mises en cache par les en-têtes Cache-Control et/ou Expires. Cela indique qu’elles ne doivent pas être mises en cache sur un point de présence ou par le client HTTP. | Oui | Oui | Non |
| EgressCacheOthers | Transfère les données sortantes pour d’autres scénarios de cache. | Non | Oui | Non |

\* Le transfert de données sortantes fait référence au trafic produit des serveurs POP CDN vers le client.


### <a name="schema-of-the-core-analytics-logs"></a>Schéma des journaux d’activité Core Analytics 

Tous les journaux d’activité sont stockés au format JSON, et chaque entrée comprend des champs de chaîne conformément au schéma suivant :

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Où *time* représente l’heure de début des limites d’heure pour lesquelles les statistiques sont rapportées. Quand une métrique n’est pas prise en charge par un fournisseur CDN, il y a une valeur null au lieu d’un entier ou d’un double. Cette valeur null indique l’absence de métrique, et diffère de la valeur 0. Il y a un ensemble de ces métriques par domaine configuré sur le point de terminaison.

Exemple de propriétés :

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Ressources supplémentaires

* [Journaux de diagnostic Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Core Analytics via le portail supplémentaire Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Journaux d’activité Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [API REST Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







