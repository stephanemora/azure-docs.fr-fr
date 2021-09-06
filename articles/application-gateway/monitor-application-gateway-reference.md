---
title: Informations de référence sur les données de supervision d’Azure Application Gateway
description: Matériel de référence important nécessaire lorsque vous supervisez Application Gateway
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 06/10/2021
ms.openlocfilehash: dce23e60b3a3be5ba7647de1f45380d188c07094
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296000"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>Informations de référence sur les données de supervision d’Azure Application Gateway

Consultez [Supervision d’Azure Application Gateway](monitor-application-gateway.md) pour plus d’informations sur la collecte et l’analyse des données de surveillance d’Azure Application Gateway.

## <a name="metrics"></a>Mesures

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>Mesures d’Application Gateway v2

Fournisseur de ressources et type : [Microsoft.Compute/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>Métriques de minutage
Application Gateway fournit plusieurs métriques de minutage intégrées associées à la requête et à la réponse, qui sont toutes mesurées en millisecondes.

> [!NOTE]
>
> Si Application Gateway comprend plus d’un écouteur, filtrez toujours par la dimension *Écouteur* tout en comparant différentes mesures de latence pour obtenir une inférence plus significative.


| Métrique | Unité | Description|
|:-------|:-----|:------------|
|**Temps de connexion au principal**|millisecondes|Temps passé à établir une connexion avec l’application principale.<br><br>Cela comprend la latence du réseau ainsi que le temps pris par la pile TCP du serveur back-end pour établir de nouvelles connexions. Dans le cas de TLS, il comprend également le temps consacré à la négociation.|
|**Temps de réponse du premier octet du principal**|millisecondes|Intervalle de temps entre le début de l’établissement d’une connexion au serveur principal et la réception du premier octet de l’en-tête de la réponse.<br><br>Cela correspond approximativement à la somme du temps de connexion au principal, du temps pris par la demande pour atteindre le principal depuis Application Gateway, du temps pris par l’application principale pour répondre (le temps nécessaire au serveur pour générer du contenu, extraire potentiellement des requêtes de base de données) et du temps mis par le premier octet de la réponse à atteindre Application Gateway à partir du principal.|
|**Temps de réponse du dernier octet du principal**|millisecondes|Intervalle de temps entre le début de l’établissement d’une connexion au serveur principal et la réception du dernier octet du corps de la réponse.<br><br>Cela correspond approximativement à la somme du temps de réponse du premier octet principal et du temps de transfert des données. Ce chiffre peut varier en fonction de la taille des objets demandés et de la latence du réseau du serveur.|
|**Durée totale d’Application Gateway**|millisecondes|Temps moyen nécessaire pour la réception et le traitement d’une requête et l’envoi de la réponse.<br><br>Il s’agit de l’intervalle entre le moment où Application Gateway reçoit le premier octet de la requête HTTP et le moment où le dernier octet de la réponse a été envoyé au client. Cela comprend le temps de traitement pris par Application Gateway, le temps de réponse du dernier octet principal, le temps pris par Application Gateway pour envoyer toutes les réponses et le RTT client.|
|**RTT client**|millisecondes|Durée moyenne des allers-retours entre les clients et Application Gateway.|

Ces métriques peuvent être utilisées pour déterminer si le ralentissement observé est dû au réseau client, aux performances d’Application Gateway, à la saturation de la pile TCP du réseau principal et du serveur principal, aux performances de l’application principale ou à la taille volumineuse des fichiers.

Par exemple, s’il existe un pic dans la tendance *Temps de réponse du premier octet du principal*, mais que la tendance *Temps de connexion au principal* est stable, il peut être déduit que la latence de la passerelle d’application vers le principal et le temps nécessaire pour établir la connexion sont stables, et que le pic est dû à une augmentation du temps de réponse de l’application principale. En revanche, si le pic dans *Temps de réponse du premier octet du principal* est associé à un pic correspondant dans *Temps de connexion au principal*, il peut être déduit que le réseau entre Application Gateway et le serveur principal ou la pile TCP du serveur principal est saturé. 

Si vous remarquez un pic dans *Temps de réponse du dernier octet du principal*, mais que le *temps de réponse du premier octet du principal* est stable, il peut être déduit que le pic est dû à la demande d’un fichier plus volumineux.

De même, si la *durée totale de la passerelle d’application* présente un pic, mais que le *temps de réponse du dernier octet du principal* est stable, cela peut être le signe d’un goulot d’étranglement des performances au niveau d’Application Gateway ou d’un goulot d’étranglement dans le réseau entre le client et Application Gateway. En outre, si le *RTT client* présente également un pic correspondant, cela indique que la dégradation est due au réseau entre le client et le service Application Gateway.

#### <a name="application-gateway-metrics"></a>Mesures Application Gateway

| Métrique | Unité | Description|
|:-------|:-----|:------------|
|**Octets reçus**|octets|Nombre d’octets reçus par Application Gateway à partir des clients.|
|**Octets envoyés**|octets|Nombre d’octets envoyés par Application Gateway aux clients.|
|**Protocole TLS du client**|count|Nombre de demandes TLS et non-TLS initiées par le client qui a établi la connexion avec Application Gateway. Pour afficher la distribution du protocole TLS, filtrez par la dimension Protocole TLS.|
|**Unités de capacité actuelles**|count|Nombre d’unités de capacité consommées pour équilibrer la charge du trafic. Les unités de capacité incluent trois déterminants : l’unité Compute, les connexions persistantes et le débit. Chaque unité de capacité est composée au maximum de ce qui suit : une unité Compute ou 2500 connexions persistantes, ou 2,22 Mbits/s de débit.|
|**Unités de calcul actuelles**|count|Capacité processeur consommée. Les facteurs affectant l’unité Compute sont les connexions TLS/s, les calculs de réécriture d’URL et le traitement des règles WAF.|
|**Connexions en cours**|count|Nombre total de connexions simultanées actives de clients à Application Gateway.|
|**Unités de capacité facturées estimées**|count|Avec la référence (SKU) v2, le modèle de tarification est déterminé par la consommation. Les unités de capacité mesurent le coût basé sur la consommation qui est facturé en plus du coût fixe. Les *Unités de capacité facturées estimées* indiquent le nombre d’unités de capacité à l’aide desquelles la facturation est estimée. Cette valeur est calculée comme étant la plus grande valeur entre *Unités de capacité actuelles* (unités de capacité requises pour équilibrer la charge du trafic) et *Unités de capacité facturables fixes* (unités de capacité minimales approvisionnées conservées).|
|**Requêtes ayant échoué**|count|Nombre de requêtes traitées par Application Gateway avec des codes d'erreur serveur 5xx. Cela comprend les codes 5xx générés à partir d'Application Gateway, ainsi que les codes 5xx générés à partir du serveur principal. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.|
|**Unités de capacité facturables fixes**|count|Nombre minimal d’unités de capacité approvisionnées conservées conformément à la valeur du paramètre *Unités d’échelle minimales* (une instance se traduit par 10 unités de capacité) dans la configuration du service Application Gateway.|
|**Nouvelles connexions par seconde**|count|Nombre moyen de nouvelles connexions TCP par seconde à partir de clients au Service Application Gateway et à partir du service Application Gateway à des membres principaux.|
|**État de la réponse**|Code d’état|État de la réponse HTTP retourné par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.|
|**Débit**|octets/s|Nombre d’octets par seconde servis par Application Gateway.|
|**Total de requêtes**|count|Nombre de requêtes réussies servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.|

#### <a name="backend-metrics"></a>Métriques du principal

| Métrique | Unité | Description|
|:-------|:-----|:------------|
|**État de la réponse du principal**|count|Nombre de codes d’état de réponse HTTP retournés par les principaux. Cela n’inclut pas les codes de réponse générés par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.|
|**Nombre d’hôtes intègres**|count|Le nombre de serveurs principaux déterminés sains par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes sains dans un pool principal spécifique.|
|**Nombre d’hôtes défectueux**|count|Nombre de principaux déterminés défectueux par la sonde d’intégrité. Vous pouvez filtrer sur une base de pool principal pour afficher le nombre d’hôtes non sains dans un pool principal spécifique.|
|**Demandes par minute par hôte sain**|count|Nombre moyen de requêtes que chaque membre sain d’un pool principal reçoit par minute. Vous devez spécifier le pool principal à l’aide de la dimension *BackendPool HttpSettings*.|


### <a name="application-gateway-v1-metrics"></a>Mesures d’Application Gateway v1

#### <a name="application-gateway-metrics"></a>Mesures Application Gateway

| Métrique | Unité | Description|
|:-------|:-----|:------------|
|**Utilisation du processeur**|pour cent|Affiche l’utilisation des processeurs alloués à Application Gateway. Dans des conditions normales, l’utilisation du processeur ne doit pas dépasser 90 %, car cela peut entraîner une latence dans les sites Web hébergés derrière Application Gateway et perturber l’expérience du client. Vous pouvez indirectement contrôler ou améliorer l'utilisation du processeur en modifiant la configuration d'Application Gateway en augmentant le nombre d'instances ou en passant à une taille SKU plus grande, ou en faisant les deux.|
|**Connexions en cours**|count|Nombre de connexions en cours établies avec Application Gateway.|
|**Requêtes ayant échoué**|count|Nombre de demandes ayant échoué en raison de problèmes de connexion. Ce nombre comprend les requêtes qui ont échoué en raison du dépassement du paramètre HTTP *Délai d’expiration des demandes* ou de problèmes de connexion entre Application Gateway et le serveur principal. Ne sont pas comptabilisées les défaillances dues à l’absence de serveur principal sain disponible. Les réponses 4xx et 5xx du serveur principal ne sont pas non plus prises en compte dans le cadre de cette métrique.|
|**État de la réponse**|Code d’état|État de la réponse HTTP retourné par Application Gateway. La distribution du code d’état de la réponse peut être ultérieurement classée par catégorie afin d’afficher les réponses dans les catégories 2xx, 3xx, 4xx et 5xx.|
|**Débit**|octets/s|Nombre d’octets par seconde servis par Application Gateway.|
|**Total de requêtes**|count|Nombre de requêtes réussies servies par Application Gateway. Le nombre de demandes peut être filtré pour afficher le nombre d’affichages par combinaison de paramètres HTTP/pool principal spécifique.|
|**Nombre de demandes bloquées par le pare-feu d’applications web**|count|Nombre de requêtes bloquées par WAF.|
|**Distribution des règles de demandes bloquées par le pare-feu d’applications web**|count|Nombre de requêtes bloquées par WAF et filtrées pour afficher le nombre par groupe de règles WAF ou combinaison d’ID de règle WAF spécifique.|
|**Distribution totale des règles du pare-feu d’applications web**|count|Nombre de requêtes reçues par groupe de règles WAF ou combinaison d’ID de règle WAF spécifique.|


<!-- Keep this text as-is -->
Pour plus d’informations, consultez la liste de [toutes les métriques de plateforme prises en charge dans Azure Monitor](../azure-monitor/essentials/metrics-supported.md).



## <a name="metric-dimensions"></a>Dimensions de métrique

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

Pour plus d’informations sur les dimensions de métrique, consultez [Métriques multidimensionnelles](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure Application Gateway prend en charge des dimensions pour certaines métriques dans Azure Monitor. Chaque métrique comprend une description qui explique les dimensions disponibles spécifiquement pour cette métrique.


## <a name="resource-logs"></a>Journaux d’activité de ressources
<!-- REQUIRED. Please  keep headings in this order -->

Cette section répertorie les types de journaux de ressources que vous pouvez collecter pour Azure Application Gateway. 

<!-- List all the resource log types you can have and what they are for -->  

Pour référence, consultez la liste de [tous les types de catégories de journaux de ressources pris en charge dans Azure Monitor](../azure-monitor/essentials/resource-logs-schema.md).

> [!NOTE]
> Le journal des performances est uniquement disponible pour la référence SKU v1. S’il s’agit de la référence SKU v2, utilisez les [Métriques](#metrics) pour les données de performances.

Pour plus d’informations, consultez [Intégrité du back-end et journaux de diagnostic pour Application Gateway](application-gateway-diagnostics.md#access-log)

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>Application Gateway

Fournisseur de ressources et type : [Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| Category | Nom d’affichage | Informations|
|:---------|:-------------|------------------|
| **Activitylog**   | Journal d’activité | Les entrées du journal d’activité sont collectées par défaut. vous pouvez utiliser le [Journal d’activité Azure](../azure-resource-manager/management/view-activity-logs.md) (anciennement journaux d’activité des opérations et journaux d’audit) pour voir toutes les opérations soumises à votre abonnement Azure, ainsi que leur état. |
|**ApplicationGatewayAccessLog**|Journal d’accès| Vous pouvez utiliser ce journal pour voir les modèles d’accès Application Gateway et analyser les informations importantes. Ceci comprend l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour et les octets d’entrée et de sortie. Un journal d’accès est collecté toutes les 60 secondes. Ce journal contient un enregistrement par instance Application Gateway. L’instance de la passerelle Application Gateway est identifiée par la propriété instanceId.|
| **ApplicationGatewayPerformanceLog**|Journal des performances|vous pouvez utiliser ce journal pour afficher les performances des instances de la passerelle Application Gateway. Ce journal capture des informations sur les performances de chaque instance, notamment le nombre total de requêtes traitées, le débit en octets, le nombre total de requêtes présentées, le nombre de requêtes ayant échoué, le nombre d’instances du serveur principal intègres et défectueuses. Le journal des performances est collecté toutes les 60 secondes. Le journal des performances est uniquement disponible pour la référence SKU v1. S’il s’agit de la référence SKU v2, utilisez les [Métriques](#metrics) pour les données de performances.|
|**ApplicationGatewayFirewallLog**|Journal du pare-feu|vous pouvez utiliser ce journal pour afficher les requêtes consignées via le mode de détection ou de prévention d’une passerelle Application Gateway configuré avec un pare-feu d’applications web. Les journaux du pare-feu sont collectés toutes les 60 secondes.|


## <a name="azure-monitor-logs-tables"></a>Tables Azure Monitor Logs
<!-- REQUIRED. Please keep heading in this order -->

Cette section fait référence à toutes les tables Kusto de Journaux d’activité Azure Monitor qui sont pertinentes pour Azure Application Gateway et qui peuvent être interrogées par Log Analytics. 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|Type de ressource | Notes |
|-------|-----|
| [Application Gateway](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |Comprend AzureActivity, AzureDiagnostics et AzureMetrics |


Pour obtenir une référence de toutes les tables Azure Monitor Logs/Log Analytics, consultez la [référence relative aux tables Azure Monitor Logs](/azure/azure-monitor/reference/tables/tables-resourcetype).

### <a name="diagnostics-tables"></a>Tables de diagnostic
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure Application Gateway utilise la table [Diagnostics Azure](/azure/azure-monitor/reference/tables/azurediagnostics) pour stocker les informations des journaux de ressources. Les colonnes suivantes sont pertinentes.

**Diagnostics Azure**

| Propriété | Description |
|:--- |:---|
requestUri_s | URI de la requête du client.|
Message | messages d’information tels que « Attaque par Injection de code SQL »|
userAgent_s | Détails de l’agent utilisateur de la requête du client|
ruleName_s | Règle d’acheminement des requêtes utilisée pour traiter cette requête|
httpMethod_s | Méthode HTTP de la requête du client|
instanceId_s | Instance Appgw vers laquelle la requête du client est routée pour évaluation|
httpVersion_s | Version HTTP de la requête du client|
clientIP_s | Adresse IP à partir de laquelle la requête est effectuée|
host_s | En-tête de l’hôte de la requête du client|
requestQuery_s | Chaîne de requête dans le cadre de la requête du client|
sslEnabled_s | Si le protocole SSL est activé ou non pour la requête du client|


## <a name="see-also"></a>Voir aussi

<!-- replace below with the proper link to your main monitoring service article -->
- Voir [Supervision d’Azure Application Gateway](monitor-application-gateway.md) pour obtenir une description de la supervision d’Azure Application Gateway.
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md).