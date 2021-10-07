---
title: Surveillance d’Azure Cache pour Redis
description: Découvrez comment surveiller l’état et les performances de vos instances de cache Azure pour Redis
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 64234292051c5f780c33fd55fabf3305f45b756d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538404"
---
# <a name="monitor-azure-cache-for-redis"></a>Surveillance d’Azure Cache pour Redis

Le cache Azure pour Redis utilise [Azure Monitor](../azure-monitor/index.yml) afin d’offrir plusieurs possibilités de surveillance de vos instances de cache. Ces outils vous permettent de surveiller l’intégrité de vos instances de cache Azure pour Redis et vous aident à gérer vos applications de mise en cache.

Utilisez Azure Monitor pour :

- afficher les métriques
- épingler les graphiques de métriques au tableau d’accueil
- personnaliser la plage de dates et d’heures des graphiques de surveillance
- ajouter et supprimer des métriques dans les graphiques
- et définir des alertes lorsque certaines conditions sont remplies

Les métriques pour les instances Azure Cache pour Redis sont collectées à l’aide de la commande Redis [INFO](https://redis.io/commands/info). Les métriques sont collectées environ deux fois par minute et stockées automatiquement pendant 30 jours afin de pouvoir être affichées dans les graphiques des métriques et évaluées par les règles d’alerte.

Pour configurer une autre stratégie de conservation, consultez [Exporter les métriques du cache](#export-cache-metrics).  

Pour plus d’informations sur les différentes valeurs INFO utilisées pour chaque mesure de cache, consultez la section [Mesures disponibles et intervalles de création des rapports](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Pour voir les mesures de cache, [accédez](cache-configure.md#configure-azure-cache-for-redis-settings) à votre instance de cache dans le [portail Azure](https://portal.azure.com).  Azure Cache pour Redis fournit des graphiques intégrés sur la gauche à l’aide de **Vue d’ensemble** et de **Métriques Redis**. Chaque graphique peut être personnalisé en ajoutant ou en supprimant des mesures et en modifiant l’intervalle de création des rapports.

![Six graphiques sont affichés. L’un d’eux est Accès au cache et absences dans le cache durant la dernière heure.](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Afficher des graphiques de mesures préconfigurés

Sur la gauche, la **vue d’ensemble** contient les graphiques de surveillance préconfigurés suivants.

- [Graphiques de surveillance](#monitoring-charts)
- [Graphiques d’utilisation](#usage-charts)

### <a name="monitoring-charts"></a>Graphiques de surveillance

La section **Surveillance** (dans **Vue d’ensemble** sur la gauche) contient les graphiques **Présences et absences**, **Obtentions et définitions**, **Connexions** et **Total des commandes**.

![Graphiques de surveillance](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Graphiques d’utilisation

La section **Utilisation** (dans **Vue d’ensemble** sur la gauche) contient les graphiques **Charge du serveur Redis**, **Utilisation de la mémoire**, **Bande passante réseau** et **Utilisation du processeur** et affiche également le **Niveau tarifaire** de l’instance de cache.

![Graphiques d’utilisation](./media/cache-how-to-monitor/redis-cache-usage-part.png)

**Niveau tarifaire** affiche le niveau tarifaire du cache et peut être utilisé pour la [mise à l’échelle](cache-how-to-scale.md) du cache à un autre niveau tarifaire.

## <a name="view-metrics-charts-for-all-your-caches-with-azure-monitor-for-azure-cache-for-redis"></a>Afficher les graphiques de métriques de tous vos caches avec Azure Monitor pour Azure Cache pour Redis

Utilisez [Azure Monitor pour Azure Cache pour Redis](../azure-monitor/insights/redis-cache-insights-overview.md) (préversion) pour visualiser les performances globales, les défaillances, la capacité et l’intégrité opérationnelle de toutes vos ressources Azure Cache pour Redis. Affichez les métriques dans une expérience personnalisable, unifiée et interactive qui vous permet d’examiner des détails pour les ressources individuelles. Azure Monitor pour Azure Cache pour Redis est basé sur la [fonctionnalité des classeurs d’Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) qui fournit des visualisations enrichies pour les métriques et d’autres données. Plus d’informations, consultez l’article [Explorer Azure Monitor pour Azure Cache pour Redis](../azure-monitor/insights/redis-cache-insights-overview.md).

## <a name="view-metrics-with-azure-monitor-metrics-explorer"></a>Afficher les métriques avec l’Explorateur de métriques Azure Monitor

Dans les scénarios où vous n’avez pas besoin de la flexibilité totale d’Azure Monitor pour Azure Cache pour Redis, vous pouvez à la place afficher les métriques et créer des graphiques personnalisés à l’aide de l’Explorateur de métriques Azure Monitor. Sélectionnez **Métriques** dans le **menu Ressource** et personnalisez votre graphique selon vos préférences de métriques, d’intervalle de consignation, de type de graphique, etc.

![Dans le volet de navigation de gauche de contoso55, Mesures est une option sous Supervision, et elle est mise en surbrillance. La zone Mesures affiche une liste de mesures. Les options Accès au cache et Absences dans le cache sont sélectionnées.](./media/cache-how-to-monitor/redis-cache-monitor.png)

Pour plus d’informations sur l’utilisation des mesures à l’aide d’Azure Monitor, consultez [Vue d’ensemble des mesures dans Microsoft Azure](../azure-monitor/data-platform.md).

<a name="enable-cache-diagnostics"></a>

## <a name="export-cache-metrics"></a>Exporter les mesures de cache

Par défaut, les mesures de cache dans Azure Monitor sont [stockées pendant 30 jours](../azure-monitor/essentials/data-platform-metrics.md), puis supprimées. Pour conserver vos mesures de cache pendant plus de 30 jours, vous pouvez [désigner un compte de stockage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) et spécifiez une stratégie de **Rétention (jours)** pour vos mesures de cache.

Pour configurer un compte de stockage pour vos mesures de cache :

1. Dans la page **Azure Cache for Redis**, sous l’en-tête **Supervision**, sélectionnez **Diagnostics**.
1. Sélectionnez **+ Ajouter le paramètre de diagnostic**.
1. Nommez les paramètres.
1. Cochez **Archive vers un compte de stockage**. Des frais de données normaux vous sont facturés pour le stockage et les transactions lorsque vous envoyez des diagnostics à un compte de stockage.
1. Sélectionnez **Configurer** pour choisir le compte de stockage dans lequel stocker les métriques de cache.
1. Sous l’en-tête de table **métrique**, cochez la case en regard des éléments de ligne que vous souhaitez stocker, par exemple **AllMetrics**. Spécifiez une stratégie de **Rétention (jours)** . Le durée maximale de rétention que vous pouvez spécifier est **365 jours**. Toutefois, si vous souhaitez conserver les données de métriques à jamais, définissez la valeur **Rétention (jours)** sur **0**.
1. Sélectionnez **Enregistrer**.

![Diagnostics Redis](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>En plus d’archiver vos mesures de cache vers le stockage, vous pouvez également [les diffuser vers un Hub d’événements ou les envoyer vers les journaux d’activité Azure Monitor](../azure-monitor/essentials/rest-api-walkthrough.md#retrieve-metric-values).
>

Pour accéder à vos métriques, vous pouvez les afficher dans le portail Azure, comme décrit précédemment dans cet article. Vous pouvez également y accéder à l’aide de l’[API REST Azure Monitor Metrics](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Si vous changez de compte de stockage, les données du compte de stockage configuré précédemment restent disponibles en téléchargement, mais elles ne sont pas affichées dans le portail Azure.  
>

## <a name="available-metrics-and-reporting-intervals"></a>Mesures disponibles et intervalles de création des rapports

Les mesures de cache font l’objet de rapports à différents intervalles : **Dernière heure**, **Aujourd’hui**, **Semaine dernière** et **Personnalisé**. Sur la gauche, la sélection **Métrique** de chaque graphique de métrique affiche les valeurs moyenne, minimale et maximale de chaque métrique du graphique et certaines métriques affichent un total pour l’intervalle de consignation.

Chaque mesure inclut deux versions. Une première mesure évalue les performances de la totalité du cache et, pour les caches qui utilisent le [clustering](cache-how-to-premium-clustering.md), une deuxième version de la mesure dont le nom inclut `(Shard 0-9)` évalue les performances d’une seule partition d’un cache. Par exemple, si un cache comporte quatre partitions, `Cache Hits` indique le nombre total d’accès pour le cache entier, et `Cache Hits (Shard 3)` simplement le nombre d’accès à cette partition du cache.

> [!NOTE]
> Lorsque vous voyez le type d’agrégation :
>
> - « Nombre » (Count) indique 2, à savoir que la métrique a reçu 2 points de données pour votre précision temporelle (1 minute).
> - « Max » indique la valeur maximale d’un point de données dans la précision temporelle.
> - « Min » indique la valeur minimale d’un point de données dans la précision temporelle.
> - « Moyenne » (Average) indique la valeur moyenne de tous les points de données dans la précision temporelle.
> - « Somme » (Sum) indique la somme de tous les points de données dans la précision temporelle et peut être trompeuse en fonction de la métrique spécifique.
> Dans des conditions normales, les types « Moyenne » et « Max » sont très similaires, car un seul nœud émet ces métriques (le nœud principal). Dans un scénario où le nombre de clients connectés change rapidement, les types « Max », « Moyenne » et « Min » affichent des valeurs très différentes, ce qui correspond également au comportement attendu.
>
> En règle générale, le type « Moyenne » vous montre un graphique lisse de la métrique que vous souhaitez et réagit correctement aux modifications de précision temporelle. Les types « Max » et « Min » peuvent masquer des modifications importantes dans la métrique si la précision temporelle est importante, mais peuvent être utilisés avec une petite précision temporelle pour identifier les moments précis où des modifications importantes se produisent dans la métrique.
>
> Les types « Nombre » et « Somme » peuvent être trompeurs pour certaines métriques (clients connectés inclus).
>
> Par conséquent, nous vous suggérons de consulter les métriques de type Moyenne et non pas celles de type Somme.

> [!NOTE]
> Même lorsque le cache est inactif sans applications clientes actives connectées, vous pouvez constater une certaine activité du cache, par exemple les opérations en cours d’exécution, l’utilisation de la mémoire et les clients connectés. Cette activité est normale dans le cadre du fonctionnement d’une instance de cache Azure pour Redis.
>
>

| Métrique | Description |
| --- | --- |
| Présences dans le cache |Nombre de recherches clés réussies au cours de l’intervalle de création des rapports. Ce nombre mappe à [ à partir de la commande Redis ](https://redis.io/commands/info)INFO`keyspace_hits`. |
| Latence du cache (préversion) | Latence du cache calculée à partir de la latence entre les nœuds du cache. Exprimée en microsecondes, cette métrique a trois dimensions : `Avg`, `Min` et `Max`. Ces dimensions représentent les latences moyenne, minimale et maximale du cache pendant l’intervalle de consignation spécifié. |
| Absences dans le cache |Nombre de recherches clés non réussies au cours de l’intervalle de création des rapports. Ce nombre mappe à `keyspace_misses` à partir de la commande Redis INFO. Les absences dans le cache ne signifient pas nécessairement qu’il y a un problème dans le cache. Par exemple, en cas d’utilisation du mode de programmation de type cache-aside, une application recherche d’abord l’élément dans le cache. Si cet élément ne s’y trouve pas (absence dans le cache), il est récupéré à partir de la base de données et ajouté au cache pour la prochaine fois. Les absences dans le cache sont un comportement normal pour le mode de programmation de type cache-aside. Si le nombre d’absences dans le cache est plus élevé que prévu, examinez la logique d’application qui remplit le cache et y lit les informations. Si des éléments sont supprimés du cache en raison d’une trop grande sollicitation de la mémoire, il peut y avoir des absences dans le cache, mais `Used Memory` ou `Evicted Keys` seraient de meilleures métriques pour superviser la pression sur la mémoire. |
| Lecture du cache |Quantité de données lues dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. **Cette valeur correspond à la bande passante réseau utilisée par ce cache. Si vous souhaitez configurer des alertes pour des limites de bande passante réseau côté serveur, vous pouvez les créer à l’aide de ce compteur `Cache Read`. Pour connaître les limites de bande passante observées pour les différents niveaux de tarification et tailles de cache, voir [ce tableau](./cache-planning-faq.yml#azure-cache-for-redis-performance).** |
| Cache d’écriture |Quantité de données écrites dans le cache en mégaoctets par seconde (Mo/s) au cours de l’intervalle de création des rapports. Cette valeur est dérivée des cartes réseau qui prennent en charge la machine virtuelle qui héberge le cache. Elle n’est pas spécifique de Redis. Cette valeur correspond à la bande passante réseau des données envoyées au cache depuis le client. |
| Clients connectés |Nombre de connexions client au cache au cours de l’intervalle de création des rapports spécifié. Ce nombre mappe à `connected_clients` à partir de la commande Redis INFO. Une fois la [limite de connexions](cache-configure.md#default-redis-server-configuration) atteinte, les tentatives de connexion ultérieures au cache échouent. Même s’il n’y a aucune application cliente active, il peut rester quelques instances de clients connectés en raison de connexions et processus internes. |
| UC |Utilisation du processeur du serveur de cache Azure pour Redis (sous forme de pourcentage) au cours de l’intervalle spécifié pour la création des rapports. Cette valeur correspond au compteur de performances `\Processor(_Total)\% Processor Time` du système d’exploitation. |
| Erreurs | Défaillances et problèmes de performances spécifiques que le cache pourrait rencontrer pendant un intervalle de rapport spécifié. Cette mesure a huit dimensions représentant différents types d’erreurs, mais d’autres pourraient y être ajoutées à l’avenir. Les types d’erreurs actuellement représentés sont les suivants : <br/><ul><li>**Failover** : quand un cache bascule (nœud subordonné promu nœud principal)</li><li>**Dataloss** : quand une perte de données se produit sur le cache</li><li>**UnresponsiveClients**: quand les clients ne lisent pas les données du serveur assez rapidement</li><li>**AOF** : quand il y a un problème lié à la persistance d’AOF</li><li>**RDB** : quand il y a un problème lié à la persistance RDB</li><li>**Import** : quand il y a un problème lié à l’importation RDB</li><li>**Export** : quand il y a un problème lié à l’exportation RDB</li></ul> |
| Clés exclues |Nombre d’éléments supprimés du cache au cours de l’intervalle de consignation spécifié en raison de la limite `maxmemory`. Ce nombre mappe à `evicted_keys` à partir de la commande Redis INFO. |
| Clés expirées |Nombre d’éléments expirés dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur correspond à la commande Redis INFO `expired_keys` .|
| Gets |Nombre d’opérations get dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` et `cmdstat_getrange`. Elle est équivalente à la somme du nombre de présences et d’absences au cours de l’intervalle de création du rapport. |
| Opérations par seconde | Nombre total de commandes traitées par second par le serveur de cache au cours de l’intervalle spécifié.  Cette valeur correspond à celle « instantaneous_ops_per_sec » obtenue à l’aide de la commande Redis INFO. |
| Charge du serveur Redis |Pourcentage de cycles dans lesquels le serveur Redis est occupé par le traitement et n’est pas inactif, en attente de messages. Si ce compteur atteint 100, c’est que le serveur Redis a atteint un plafond de performances et que le processeur ne peut pas fonctionner plus rapidement. Si vous voyez une forte charge de serveur Redis, vous voyez des exceptions d’expiration dans le client. Dans ce cas, vous devez envisager d’effectuer une mise à l’échelle ou de partitionner vos données dans plusieurs caches. |
| Jeux |Nombre d’opérations set dans le cache au cours de l’intervalle de création des rapports spécifié. Cette valeur est la somme des valeurs suivantes obtenues de toutes les commandes Redis INFO : `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` et `cmdstat_setnx`. |
| Nombre total de clés  | Le nombre maximal de clés dans le cache pendant la dernière période de rapport. Ce nombre mappe à `keyspace` à partir de la commande Redis INFO. En raison d’une limitation du système de métriques sous-jacent pour les caches avec le clustering activé, Nombre total de clés retourne le nombre maximal de clés de la partition qui a eu le nombre maximal de clés au cours de l’intervalle de consignation.  |
| Total des opérations |Nombre total de commandes traitées par le serveur de cache au cours de l’intervalle spécifié. Cette valeur correspond à la commande Redis INFO `total_commands_processed` . Lorsque Azure Cache for Redis est utilisé uniquement pour des opérations de publication/abonnement, il n’y a aucune métrique pour `Cache Hits`, `Cache Misses`, `Gets` ou `Sets`, mais bien des métriques `Total Operations` reflétant l’utilisation du cache pour ces opérations. |
| Mémoire utilisée |Quantité de mémoire cache, exprimée en Mo, utilisée pour les paires clé/valeur dans le cache au cours de l’intervalle de consignation. Cette valeur correspond à la commande Redis INFO `used_memory` . Elle n’inclut pas les métadonnées ni la fragmentation. |
| Pourcentage de mémoire utilisé | Pourcentage de la mémoire totale utilisé au cours de l’intervalle de rapport spécifié.  Cette valeur fait référence à la valeur `used_memory` obtenue à l’aide de la commande Redis INFO pour calculer le pourcentage. |
| Taille de la mémoire résidente utilisée |Quantité de mémoire cache utilisée (exprimée en Mo) au cours de l’intervalle de création des rapports, fragmentation et métadonnées comprises. Cette valeur correspond à la commande Redis INFO `used_memory_rss` . |

<a name="operations-and-alerts"></a>

## <a name="alerts"></a>Alertes

Vous pouvez configurer les paramètres pour recevoir des alertes en fonction des mesures et des journaux d’activité. Azure Monitor vous permet de configurer une alerte pour effectuer les opérations suivantes lors de son déclenchement :

- Envoyer un e-mail de notification
- Appeler un webhook
- Appeler une application logique Azure

Pour configurer les règles d’alerte de votre cache, sélectionnez **Règles d’alerte** depuis le **menu Ressource**.

![Surveillance](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Pour plus d’informations sur la configuration et l’utilisation des Alertes, consultez [Vue d’ensemble des Alertes](../azure-monitor/alerts/alerts-classic-portal.md).

<!-- 
## Activity Logs

Activity logs provide insight into the operations that completed on your Azure Cache for Redis instances. It was previously known as "audit logs" or "operational logs". Using activity logs, you can determine the "what, who, and when" for any write operations (PUT, POST, DELETE) taken on your Azure Cache for Redis instances.

> [!NOTE]
> Activity logs do not include read (GET) operations.
> 

To view activity logs for your cache, select **Activity logs** from the **Resource menu**.

For more information about Activity logs, see [Overview of the Azure Activity Log](../azure-monitor/essentials/platform-logs-overview.md).
-->
