---
title: Métriques personnalisées dans Azure Monitor (préversion)
description: Découvrez les métriques personnalisées dans Azure Monitor et leur modélisation.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 06/01/2021
ms.openlocfilehash: cacb1d9af577a90d8e9030cf00e51dda58763041
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130256626"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Métriques personnalisées dans Azure Monitor (préversion)

Quand vous déployez des ressources et des applications dans Azure, il est généralement utile de collecter des données de télémétrie pour obtenir des insights sur leurs performances et leur intégrité. Azure met à votre disposition des métriques prêtes à l’emploi. Il s’agit de métriques [standard ou de plateforme](./metrics-supported.md). Toutefois, ces métriques sont limitées. 

Vous pouvez avoir besoin de collecter certains indicateurs de performance personnalisés ou des métriques métier pour obtenir des insights plus approfondis. Ces métriques *personnalisées* peuvent être collectées par le biais de la télémétrie de votre application, d’un agent s’exécutant sur vos ressources Azure ou même d’un système de supervision externe. Elles peuvent ensuite être envoyées directement à Azure Monitor. Une fois les métriques personnalisées publiées dans Azure Monitor, vous pouvez parcourir les métriques personnalisées relatives à vos ressources et applications Azure, les interroger et créer des alertes à leur sujet parallèlement aux métriques Azure standard.

Les métriques personnalisées d’Azure Monitor sont actuellement en préversion publique. 

## <a name="methods-to-send-custom-metrics"></a>Méthodes d’envoi de métriques personnalisées

Les métriques personnalisées peuvent être envoyées à Azure Monitor à l’aide de plusieurs méthodes :
- Instrumenter votre application en utilisant le SDK Azure Application Insights et envoyer des données de télémétrie personnalisées à Azure Monitor 
- Installer l’agent Azure Monitor (préversion) sur votre [machine virtuelle Azure Windows ou Linux](../agents/azure-monitor-agent-overview.md) et utiliser une [règle de collecte de données](../agents/data-collection-rule-azure-monitor-agent.md) pour envoyer des compteurs de performances aux métriques Azure Monitor.
- Installer l’extension Diagnostics Azure sur votre [machine virtuelle Azure](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md), votre [groupe de machines virtuelles identiques](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md), votre [machine virtuelle classique](../essentials/collect-custom-metrics-guestos-vm-classic.md) ou votre [service cloud classique](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md) et envoyer des compteurs de performances à Azure Monitor. 
- Installer [l’agent InfluxData Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md) sur votre machine virtuelle Linux Azure et envoyer les métriques à l’aide du plug-in de sortie Azure Monitor.
- Envoyer des métriques personnalisées [directement à l’API REST Azure Monitor](./metrics-store-custom-rest-api.md) : `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`

## <a name="pricing-model-and-retention"></a>Modèle de tarification et rétention

Pour plus d’informations sur l’activation de la facturation pour les métriques personnalisées et les requêtes de métriques, consultez la [page de tarification Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). En résumé, l’ingestion de métriques standard (métriques de plateforme) dans un magasin de métriques Azure Monitor est gratuite, mais les métriques personnalisées engendreront des coûts lorsqu’elles seront mises en disponibilité générale. Les requêtes adressées à l’API de métriques engendrent des coûts.

Les métriques personnalisées sont conservées pendant la [même durée que les métriques de plateforme](../essentials/data-platform-metrics.md#retention-of-metrics). 

> [!NOTE]  
> Les métriques envoyées à Azure Monitor via le Kit de développement logiciel (SDK) Application Insights sont facturées en tant que données de journal ingérées. Elles entraînent des frais supplémentaires uniquement si la fonctionnalité [Activer les alertes sur les dimensions des métriques personnalisées](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) d’Application Insights a été sélectionnée. Cette case à cocher détermine l’envoi de données à la base de données de métriques d’Azure Monitor à l’aide de l’API de métriques personnalisées pour permettre la création d’alertes plus complexes.  En savoir plus sur le [modèle de tarification Application Insights](../app/pricing.md#pricing-model) et les [prix dans votre région](https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-to-send-custom-metrics"></a>Comment envoyer des métriques personnalisées

Lorsque vous envoyez des métriques personnalisées à Azure Monitor, chaque point de données ou valeur rapporté dans les métriques doit inclure les informations suivantes.

### <a name="authentication"></a>Authentification
Pour soumettre des métriques personnalisées à Azure Monitor, l’entité qui soumet la métrique doit disposer d’un jeton Azure Active Directory (Azure AD) valide, figurant dans l’en-tête **porteur** de la requête. Les moyens d’acquérir un jeton du porteur valide incluent :

- [Identités managées pour les ressources Azure](../../active-directory/managed-identities-azure-resources/overview.md). Vous pouvez utiliser une identité managée pour accorder aux ressources des autorisations d’effectuer certaines opérations. Il peut s’agir, par exemple, d’autoriser une ressource à générer des métriques à propos d’elle-même. Une ressource, ou son identité managée, peut recevoir des autorisations **Publication des métriques de surveillance** pour une autre ressource. Grâce à cette autorisation, l’identité managée peut également émettre des métriques pour d’autres ressources.
- [Principal du service Azure AD](../../active-directory/develop/app-objects-and-service-principals.md). Dans ce scénario, une application (ou service) Azure AD peut se voir accorder les autorisations nécessaires pour générer des métriques concernant une ressource Azure. Pour authentifier la requête, Azure Monitor valide le jeton d’application à l’aide de clés publiques Azure AD. Le rôle **Surveillance de l’éditeur de métriques** dispose déjà de cette autorisation. Cette autorisation est disponible dans le portail Azure. 

  En fonction des ressources pour lesquelles il émettra des métriques personnalisées, le principal de service peut se voir accorder le rôle **Surveillance de l’éditeur de métriques** selon la portée nécessaire. Il peut s’agir d’un abonnement, d’un groupe de ressources ou d’une ressource.

> [!TIP]  
> Lorsque vous demandez à un jeton Azure AD d’émettre des métriques personnalisées, vérifiez que l’audience ou la ressource pour laquelle le jeton est demandé est `https://monitoring.azure.com/`. Veillez à inclure la barre oblique de fin.

### <a name="subject"></a>Objet
La propriété d’objet capture l’ID de ressource Azure pour lequel la métrique personnalisée est rapportée. Cette information sera codée dans l’URL de l’appel d’API. Chaque API peut soumettre des valeurs de métrique pour une seule ressource Azure.

> [!NOTE]  
> Vous ne pouvez pas générer de métriques personnalisées pour l’ID de ressource d’un abonnement ou d’un groupe de ressources.

### <a name="region"></a>Région
La propriété de région capture la région Azure dans laquelle est déployée la ressource pour laquelle vous émettez des métriques. Les métriques doivent être émises vers le point de terminaison régional Azure Monitor correspondant à la région dans laquelle la ressource est déployée. Par exemple, les métriques personnalisées concernant une machine virtuelle déployée dans la région USA Ouest doivent être envoyées au point de terminaison Azure Monitor régional WestUS. Les informations de région sont également codées dans l’URL de l’appel d’API.

> [!NOTE]  
> Dans le cadre de la préversion publique, les métriques personnalisées sont disponibles uniquement dans un sous-ensemble de régions Azure. Une liste des régions prises en charge figure dans une [section ultérieure de cet article](#supported-regions).

### <a name="timestamp"></a>Timestamp
Chaque point de données envoyé à Azure Monitor doit être marqué par un timestamp. Ce timestamp capture la date et l’heure auxquelles la valeur de métrique a été mesurée ou collectée. Azure Monitor accepte les données métriques dont les horodatages ne datent pas de plus de 20 minutes et ne dépassent pas les 5 minutes à venir. Le timestamp doit être au format ISO 8601.

### <a name="namespace"></a>Espace de noms
Les espaces de noms offrent un moyen de grouper ou classer par catégorie des métriques similaires. Les espaces de noms permettent d’isoler les groupes de métriques collectant différents insights ou indicateurs de performances. On peut avoir par exemple un espace de noms nommé **contosomemorymetrics** qui effectue le suivi des métriques d’utilisation de la mémoire caractéristiques de l’application. Un autre espace de noms appelé **contosoapptransaction** pourrait effectuer le suivi de toutes les métriques relatives aux transactions utilisateur de l’application.

### <a name="name"></a>Nom
La propriété de nom correspond au nom de la métrique rapportée. Généralement, le nom est suffisamment descriptif pour identifier ce qui est mesuré. Par exemple, il peut s’agir d’une métrique qui mesure le nombre d’octets de mémoire utilisés sur une machine virtuelle. Le nom de cette métrique pourrait être **Memory Bytes In Use** (Octets de mémoire en cours d’utilisation).

### <a name="dimension-keys"></a>Clés de dimension
Une dimension est une paire clé-valeur simplifiant la description des caractéristiques supplémentaires sur la métrique collectée. Ces caractéristiques supplémentaires permettent de collecter plus d’informations sur la métrique, offrant des insights plus approfondis. 

Par exemple, la métrique **Memory Bytes In Use** peut disposer d’une clé de dimension nommée **Process**, qui capture le nombre d’octets de mémoire consommés par chaque processus sur une machine virtuelle. Cette clé vous permet de filtrer les résultats de la métrique pour connaître la quantité de mémoire utilisée par certains processus ou identifier les cinq processus utilisant le plus de mémoire.

Les dimensions sont facultatives ; certaines métriques n’ont pas de dimensions. Une métrique personnalisée peut avoir jusqu'à 10 dimensions.

### <a name="dimension-values"></a>Valeurs de dimension
Lorsque vous rapportez un point de données de métrique, chaque clé de dimension de la métrique rapportée est associée à une valeur de dimension. Par exemple, vous pouvez rapporter la mémoire utilisée par ContosoApp sur votre machine virtuelle :

* Le nom de la métrique sera **Memory Bytes In Use**.
* La clé de dimension sera **Process**.
* La valeur de dimension sera **ContosoApp.exe**.

Lorsque vous publiez une valeur de métrique, vous ne pouvez spécifier qu’une seule valeur de dimension par clé de dimension. Si vous collectez une même utilisation de la mémoire pour plusieurs processus sur la machine virtuelle, vous pouvez rapporter plusieurs valeurs de métrique pour cet horodatage. Chaque valeur de métrique spécifiera une valeur de dimension différente pour la clé de dimension **Process**.

Bien que les dimensions soient facultatives, si une publication de métriques définit des clés de dimension, les valeurs de dimension correspondantes sont obligatoires.

### <a name="metric-values"></a>Valeurs de métrique
Azure Monitor stocke toutes les métriques à intervalles réguliers (avec une granularité d’une minute). Au cours d’une minute donnée, une métrique peut devoir être échantillonnée plusieurs fois. C’est le cas, par exemple, avec l’utilisation du processeur. Il se peut également qu’une métrique doive être mesurée pour de nombreux événements discrets, tels que les latences de transaction de connexion. 

Pour limiter le nombre de valeurs brutes que vous devez émettre et payer dans Azure Monitor, vous pouvez préagréger les valeurs localement et les émettre :

* **Min** : valeur minimale observée parmi tous les échantillons et mesures au cours d’une minute donnée.
* **Max** : valeur maximale observée parmi tous les échantillons et mesures au cours d’une minute donnée.
* **Sum** : somme de toutes les valeurs observées parmi tous les échantillons et mesures au cours d’une minute donnée.
* **Nombre** : nombre d’échantillons et de mesures relevés au cours d’une minute donnée.

Par exemple, si quatre transactions de connexion à votre application ont été effectuées pendant une minute donnée, les latences mesurées pour chacune d’elles sont les suivantes :

|Transaction 1|Transaction 2|Transaction 3|Transaction 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

La publication des métriques dans Azure Monitor se présentera ainsi :
* Min : 4
* Max : 16
* Somme : 40
* Nombre : 4

Si votre application ne peut pas préagréger les valeurs localement et doit émettre chaque événement ou échantillon discret immédiatement après collecte, vous pouvez émettre les valeurs de mesure brutes. Par exemple, à chaque exécution d’une transaction de connexion à votre application, vous publiez une métrique dans Azure Monitor avec une seule mesure. Par conséquent, pour une transaction de connexion qui a duré 12 millisecondes, la publication de métrique se présentera ainsi :
* Min : 12
* Max : 12
* Somme : 12
* Nombre : 1

Ce processus vous permet d’émettre plusieurs valeurs pour la même combinaison métrique-dimension au cours d’une minute donnée. Azure Monitor prend ensuite toutes les valeurs brutes émises pour une minute donnée et les agrège.

### <a name="sample-custom-metric-publication"></a>Exemple de publication de métrique personnalisée
Dans l’exemple suivant, vous créez une métrique personnalisée appelée **Memory Bytes in Use** sous l’espace de noms de métrique **Memory Profile** pour une machine virtuelle. La métrique a une seule dimension appelée **Process**. Pour le timestamp, les valeurs de métrique sont émises pour deux processus.

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, l’extension de diagnostics et l’agent InfluxData Telegraf sont déjà configurés pour générer des valeurs de métrique sur le bon point de terminaison régional, ainsi que pour intégrer toutes les propriétés précédentes dans chaque émission.

## <a name="custom-metric-definitions"></a>Définitions de métriques personnalisées
Il n’est pas nécessaire de prédéfinir une métrique personnalisée dans Azure Monitor avant son émission. Chaque point de données de métrique publié contient un espace de noms, un nom et des informations de dimension. Ainsi, la première fois qu’une métrique personnalisée est émise vers Azure Monitor, une définition de métrique est automatiquement créée. Cette définition de métrique peut ensuite être découverte sur toute ressource pour laquelle la métrique est émise via les définitions de métrique.

> [!NOTE]  
> Azure Monitor ne prend pas encore en charge la définition des **unités** pour une métrique personnalisée.

## <a name="using-custom-metrics"></a>Utilisation de métriques personnalisées
Une fois les métriques personnalisées envoyées à Azure Monitor, vous pouvez les parcourir dans le portail Azure et les interroger par l’intermédiaire des API REST Azure Monitor. Vous pouvez également créer des alertes les concernant, afin d’être averti lorsque certaines conditions sont remplies.

> [!NOTE]
> Vous devez avoir un rôle Lecteur ou Contributeur pour afficher des métriques personnalisées. Consultez [Lecteur d’analyse](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Parcourir vos métriques personnalisées dans le Portail Azure
1. Accédez au [portail Azure](https://portal.azure.com).
2. Sélectionnez le volet **Surveiller**.
3. Sélectionnez **Métriques**.
4. Sélectionnez une ressource pour laquelle vous avez émis des métriques personnalisées.
5. Sélectionnez l’espace de noms de métrique de votre métrique personnalisée.
6. Sélectionnez la métrique personnalisée.

Pour plus d’informations sur l’affichage des métriques dans le portail Azure, consultez [Prise en main d’Azure Metrics Explorer](./metrics-getting-started.md).

## <a name="supported-regions"></a>Régions prises en charge
Dans la préversion publique, la publication des métriques personnalisées n’est possible que dans un sous-ensemble de régions Azure. Cette restriction signifie que vous ne pouvez publier des métriques que pour les ressources qui sont situées dans l’une de ces régions. Pour plus d’informations sur les régions Azure, consultez [Zones géographiques Azure](https://azure.microsoft.com/global-infrastructure/geographies/). 

Le tableau suivant répertorie les régions Azure que les métriques personnalisées prennent en charge. Il répertorie également les points de terminaison correspondants sur lesquels les métriques des ressources de ces régions doivent être publiées. Le code de région Azure utilisé dans le préfixe du point de terminaison est simplement le nom de la région en anglais, sans espace. 

|Région Azure |Préfixe du point de terminaison régional|
|---|---|
| Toutes les régions de cloud public | https://<azure_region_code>.monitoring.azure.com |

## <a name="latency-and-storage-retention"></a>Conservation de stockage et latence

L’affichage d’une métrique ou dimension nouvellement ajoutée à une métrique peut prendre jusqu’à trois minutes. Une fois que les données sont dans le système, elles devraient apparaître en moins de 30 secondes dans 99 % des cas. 

Si vous supprimez une métrique ou une dimension, il faudra peut-être d’une semaine à un mois pour que la modification soit supprimée du système.

## <a name="quotas-and-limits"></a>Quotas et limites
Azure Monitor impose les limites d’utilisation suivantes quant aux métriques personnalisées :

|Category|Limite|
|---|---|
|Nombre total de séries chronologiques actives dans un abonnement dans toutes les régions sur lesquelles vous les avez déployées|50 000|
|Clés de dimension par métrique|10|
|Longueur de chaîne pour les espaces de noms de métrique, les noms de métrique, les clés de dimension et les valeurs de dimension|256 caractères|

Une série chronologique active se définit comme toute combinaison unique de métriques, clés de dimension ou valeurs de dimension pour laquelle des valeurs de métrique ont été publiées au cours des 12 dernières heures.

Pour comprendre la limite des 50 000 séries chronologiques, considérez la métrique suivante :

> *Temps de réponse du serveur* avec les dimensions *Region*, *Department*, *CustomerID*

Avec cette métrique, si vous avez 10 régions, 20 départements et 100 clients, cela donne 10 x 20 x 100 = 2 000 séries chronologiques. 

Si vous avez 100 régions, 200 départements et 2 000 clients, cela donne 100 x 200 x 2 000 = 40 millions de séries chronologiques, ce qui est bien au-delà de la limite, rien que pour cette métrique. 

Rappelons que cette limite ne s’applique pas à une métrique individuelle, mais à la somme de toutes ces métriques à l’échelle d’un abonnement et d’une région.  

## <a name="design-limitations-and-considerations"></a>Limitations et considérations relatives à la conception

**Utilisation d’Application Insights à des fins d’audit**. Le pipeline de télémétrie d’Application Insights est optimisé pour réduire l’impact sur les performances et limiter le trafic lié à la surveillance de votre application. Ainsi, il limite ou échantillonne (n'utilise qu'un pourcentage de vos données de télémétrie et ignore le reste) si le jeu de données initial devient trop volumineux. En raison de ce comportement, vous ne pouvez pas l’utiliser à des fins d’audit, car certains enregistrements sont susceptibles d’être ignorés. 

**Métriques dont le nom contient une variable**. N’utilisez pas de variable dans le nom de la métrique. Utilisez une constante à la place. Chaque fois que la variable change de valeur, Azure Monitor génère une nouvelle métrique. Azure Monitor atteindra alors rapidement la limite du nombre de métriques. En règle générale, lorsque les développeurs souhaitent inclure une variable dans le nom de la métrique, ils veulent suivre plusieurs séries chronologiques au sein d’une même métrique et doivent utiliser des dimensions plutôt que des noms de métriques variables. 

**Dimensions de métriques à cardinalité élevée**. Les métriques avec un trop grand nombre de valeurs valides dans une dimension (*cardinalité élevée*) sont beaucoup plus susceptibles d’atteindre la limite de 50 000. En général, vous ne devez jamais utiliser une valeur qui change constamment dans une dimension. Le timestamp, par exemple, ne doit jamais être une dimension. Vous pouvez utiliser l’ID d’un serveur, d’un client ou d’un produit, mais uniquement si vous avez un nombre réduit de chacun de ces types. 

En guise de test, demandez-vous si vous pourriez créer un graphique de ce type de données. Si vous avez 10, voire 100 serveurs, il pourrait être utile de les afficher tous sur un graphique à des fins de comparaison. En revanche, si vous en avez 1 000, le graphique résultant sera probablement difficile, voire impossible à lire. Une meilleure pratique consiste à limiter le nombre de valeurs valides à moins de 100. Jusqu’à 300, vous êtes dans une zone délicate. Si vous avez besoin de dépasser ce nombre, utilisez plutôt des journaux personnalisés Azure Monitor.   

En cas de variable dans le nom ou de dimension à cardinalité élevée, voici ce qui peut se produire :
- Les métriques ne sont plus fiables en raison de la limitation.
- Metrics Explorer ne fonctionne pas.
- Les alertes et les notifications deviennent imprévisibles.
- Les coûts peuvent augmenter de façon inattendue. Microsoft ne facture pas les métriques personnalisées avec dimensions tant que cette fonctionnalité est en préversion publique. Cependant, lorsque les frais seront facturés à l’avenir, vous risquez d’encourir des frais inattendus. Nous prévoyons de facturer la consommation de métriques en fonction du nombre de séries chronologiques supervisées et du nombre d’appels d’API effectués.

Si le nom de la métrique ou la valeur de la dimension est rempli avec un identificateur ou une dimension de cardinalité élevée par erreur, vous pouvez facilement corriger l’erreur en supprimant la partie variable.

Toutefois, si une cardinalité élevée est essentielle pour votre scénario, les métriques agrégées ne sont probablement pas le bon choix. Passez à l’utilisation de journaux personnalisés (c’est-à-dire appels d’API trackMetric avec [trackEvent](../app/api-custom-events-metrics.md#trackevent)). Toutefois, tenez compte du fait que les journaux n’agrègent pas les valeurs, si bien que chaque entrée unique est stockée. Par conséquent, si le volume de journaux est important sur une courte période (1 million par seconde par exemple), cela peut entraîner des limitations et des retards d’ingestion. 

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez utiliser les métriques personnalisées à partir de différents services : 
 - [Machine virtuelle](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Groupe de machines virtuelles identiques](../essentials/collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Machine virtuelle Azure (classique)](../essentials/collect-custom-metrics-guestos-vm-classic.md)
 - [Machine virtuelle Linux utilisant l’agent Telegraf](../essentials/collect-custom-metrics-linux-telegraf.md)
 - [REST API](./metrics-store-custom-rest-api.md)
 - [Service cloud classique](../essentials/collect-custom-metrics-guestos-vm-cloud-service-classic.md)