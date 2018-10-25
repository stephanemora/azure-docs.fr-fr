---
title: Métriques personnalisées dans Azure Monitor
description: Découvrez les métriques personnalisées dans Azure Monitor et comment elles sont modélisées.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 1bdf1e1f5e58ecb0939d5876e0cef349e32de517
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344744"
---
# <a name="custom-metrics-in-azure-monitor"></a>Métriques personnalisées dans Azure Monitor

Quand vous déployez des ressources et des applications dans Azure, il est généralement utile de collecter des données de télémétrie pour obtenir des insights sur leurs performances et leur intégrité. Azure met directement à votre disposition des métriques lorsque vous déployez des ressources. Il s’agit de métriques standard ou de plateforme. Toutefois, ces métriques sont limitées par nature. Vous pouvez collecter certains indicateurs de performance personnalisés ou des métriques métier pour obtenir des insights plus approfondis.
Ces métriques « personnalisées » peuvent être collectées par le biais des données de télémétrie de votre application, d’un agent s’exécutant sur vos ressources Azure ou même d’un système de surveillance d’interaction passive, puis soumises directement à Azure Monitor. Après leur publication sur Azure Monitor, vous pouvez parcourir et interroger les métriques personnalisées relatives à vos ressources et applications Azure (et créer des alertes sur ces métriques) parallèlement aux métriques standard émises par Azure.

## <a name="send-custom-metrics"></a>Envoyer des métriques personnalisées
Les métriques personnalisées peuvent être envoyées à Azure Monitor à l’aide de différentes méthodes.
- Instrumenter votre application en utilisant le Kit de développement logiciel (SDK) Application Insights et envoyer des données de télémétrie personnalisées à Azure Monitor 
- Installer l’extension de diagnostics Microsoft sur votre [machine virtuelle Azure](metrics-store-custom-guestos-resource-manager-vm.md), votre [groupe de machines virtuelles identiques](metrics-store-custom-guestos-resource-manager-vmss.md), votre [machine virtuelle classique](metrics-store-custom-guestos-classic-vm.md) ou votre [service cloud classique](metrics-store-custom-guestos-classic-cloud-service.md) et envoyer des compteurs de performances à Azure Monitor 
- Installer [l’agent InfluxDB Telegraf](metrics-store-custom-linux-telegraf.md) sur votre machine virtuelle Linux Azure et envoyer les métriques à l’aide du plug-in de sortie Azure Monitor
- Envoyer des métriques personnalisées [directement à l’API REST Azure Monitor](metrics-store-custom-rest-api.md) https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics

Lorsque vous envoyez des métriques personnalisées à Azure Monitor, chaque point de données (ou valeur) rapporté doit inclure les informations suivantes :

### <a name="authentication"></a>Authentification
Pour soumettre des métriques personnalisées à Azure Monitor, l’entité qui soumet la métrique doit disposer d’un jeton Azure Active Directory valide, figurant dans l’en-tête « porteur » de la requête. Il existe plusieurs moyens d’acquérir un jeton du porteur valide :
1. [Identités managées pour les ressources Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) : donne une identité à une ressource Azure elle-même (comme une machine virtuelle). MSI a été conçu pour accorder aux ressources les autorisations nécessaires pour effectuer certaines opérations (pour permettre à une ressource d’émettre des métriques sur elle-même, par exemple). Une ressource (ou l’identité MSI correspondante) peut se voir accorder des autorisations de « Publication des métriques de surveillance » sur une autre ressource, permettant à l’identité MSI d’émettre des métriques pour d’autres ressources également.
2. [Principal de Service AAD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) : dans ce scénario, une application AAD (un service) peut se voir accorder les autorisations nécessaires pour émettre des métriques concernant une ressource Azure.
Pour authentifier la requête, Azure Monitor valide le jeton d’application à l’aide de clés publiques AAD. Le rôle « Publication des métriques de surveillance » dispose déjà de cette autorisation, disponible dans le Portail Azure. En fonction des ressources pour lesquelles il émettra des métriques personnalisées, le principal de service peut se voir accorder le rôle « Publication des métriques de surveillance » selon la portée requise (abonnement, groupe de ressources ou ressource spécifique).

> [!NOTE]
> Lorsque vous demandez à un jeton AAD d’émettre des métriques personnalisées, assurez-vous que l’audience/la ressource pour laquelle le jeton est demandé est https://monitoring.azure.com/ (veillez à inclure le signe « / » à la fin).

### <a name="subject"></a>Objet
Cette propriété capture l’ID de ressource Azure pour lequel la métrique personnalisée est rapportée. Cette information sera codée dans l’URL de l’appel d’API effectué. Chaque API peut soumettre des valeurs de métrique pour une ressource Azure uniquement.

> [!NOTE]
> Vous ne pouvez pas émettre de métriques personnalisées pour l’ID de ressource d’un abonnement ou d’un groupe de ressources.
>
>

### <a name="region"></a>Région
Cette propriété capture la région Azure dans laquelle est déployée la ressource pour laquelle vous émettez des métriques. Les métriques doivent être émises vers le point de terminaison régional Azure Monitor correspondant à la région dans laquelle la ressource est déployée. Par exemple, les métriques personnalisées pour une machine virtuelle déployées dans la région USA Ouest doivent être envoyées au point de terminaison Azure Monitor régional WestUS. Les informations de région sont également codées dans l’URL de l’appel d’API.

> [!NOTE]
> Dans le cadre de la préversion publique, les métriques personnalisées sont disponibles dans un sous-ensemble de régions Azure uniquement. Une liste des régions prises en charge figure dans une section ultérieure de cet article.
>
>

### <a name="timestamp"></a>Timestamp
Chaque point de données envoyé à Azure Monitor doit être marqué par un timestamp. Ce timestamp capture la date et l’heure auxquelles la valeur de métrique a été mesurée/collectée. Azure Monitor accepte les données de métrique dont les timestamps ne datent pas de plus de 20 minutes et ne dépassent pas les 5 minutes à venir.

### <a name="namespace"></a>Espace de noms
Les espaces de noms offrent un moyen de grouper ou classer par catégorie des métriques similaires. Les espaces de noms permettent d’isoler les groupes de métriques collectant différents insights ou indicateurs de performances. Par exemple, vous pouvez disposer d’un espace de noms appelé *ContosoMemoryMetrics* utilisé pour suivre les métriques d’utilisation de la mémoire qui profilent votre application et un autre espace de noms appelé *ContosoAppTransaction* qui suit toutes les métriques sur les transactions utilisateur dans votre application.

### <a name="name"></a>NOM
Il s’agit du nom de la métrique rapportée. Généralement, le nom est suffisamment descriptif pour simplifier l’identification de ce qui est mesuré. Par exemple, une métrique qui mesure le nombre d’octets de mémoire utilisés sur une machine virtuelle spécifique peut être nommée « Memory Bytes In Use ».

### <a name="dimension-keys"></a>Clés de dimension
Une dimension est une paire clé/valeur simplifiant la description des caractéristiques supplémentaires sur la métrique collectée. Ces caractéristiques supplémentaires permettent de collecter plus d’informations sur la métrique, offrant des insights plus approfondis. Par exemple, la métrique « Memory Bytes In Use » peut disposer d’une clé de dimension nommée « Process », qui capture le nombre d’octets de mémoire consommés par chaque processus sur une machine virtuelle. Vous pouvez ainsi filtrer la métrique pour connaître la quantité de mémoire utilisée par des processus spécifiques ou pour identifier les 5 processus utilisant le plus de mémoire.
Chaque métrique personnalisée peut avoir jusqu’à 10 dimensions.

### <a name="dimension-values"></a>Valeurs de dimension
Lorsqu’un point de données de métrique est rapporté, une valeur de dimension correspond à chaque clé de dimension sur la métrique rapportée. Par exemple, si vous souhaitez que la mémoire utilisée par l’application ContosoApp sur votre machine virtuelle soit rapportée :

* Le nom de la métrique sera *Memory Bytes In Use*.
* La clé de dimension sera *Process*.
* La valeur de dimension sera *ContosoApp.exe*.

Lorsque vous publiez une valeur de métrique, vous pouvez spécifier une valeur de dimension par clé de dimension uniquement. Si vous collectez l’utilisation de la mémoire de la même façon pour plusieurs processus sur la machine virtuelle, vous pouvez rapporter plusieurs valeurs de métrique pour ce timestamp. Chaque valeur de métrique spécifiera une valeur de dimension différente pour la clé de dimension Process.

### <a name="metric-values"></a>Valeurs de métrique
Azure Monitor stocke toutes les métriques à intervalles réguliers (avec une granularité d’une minute). Il est entendu qu’il peut être nécessaire d’échantillonner une métrique plusieurs fois (par exemple, dans le cadre de l’utilisation de l’UC) ou de la mesurer pour une multitude d’événements discrets (par exemple, pour les latences des transactions de connexion) pendant une minute donnée. Pour limiter le nombre de valeurs brutes que vous devez émettre et payer dans Azure Monitor, vous pouvez pré-agréger les valeurs localement et les émettre :

* Min : la valeur minimale observée parmi tous les échantillons/mesures pendant la minute
* Max : la valeur maximale observée parmi tous les échantillons/mesures pendant la minute
* Sum : la somme de toutes les valeurs observées parmi tous les échantillons/mesures pendant la minute
* Count : le nombre d’échantillons/mesures relevés pendant la minute

Par exemple, si 4 transactions de connexion à votre application ont été effectuées pendant une minute donnée et que les latences mesurées pour chacune d’elles sont les suivantes :

|Transaction 1|Transaction 2|Transaction 3|Transaction 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

La publication des métriques dans Azure Monitor se présentera comme suit :
* Min : 4
* Max : 16
* Sum : 40
* Count : 4

Si votre application ne parvient pas à pré-agréger les valeurs localement et doit émettre chaque événement ou échantillon discret immédiatement après collecte, vous pouvez émettre les valeurs de mesure brutes.
Par exemple, à chaque exécution d’une transaction de connexion à votre application, vous publiez une métrique dans Azure Monitor avec une mesure uniquement. Par conséquent, pour une transaction de connexion durant 12 ms, la publication de métrique se présentera comme suit :
* Min : 12
* Max : 12
* Sum : 12
* Count : 1

Ce processus vous permet d’émettre plusieurs valeurs pour la même combinaison métrique + dimension pendant une minute donnée. Azure Monitor prend ensuite toutes les valeurs brutes émises pendant une minute donnée et les agrège.

### <a name="sample-custom-metric-publication"></a>Exemple de publication de métrique personnalisée
Dans l’exemple suivant, vous créez une métrique personnalisée appelée « Memory Bytes in Use » sous l’espace de noms de métrique « Memory Profile » pour une machine virtuelle. La métrique a une seule dimension appelée « Process ». Pour le timestamp donné, nous émettons des valeurs de métrique pour deux processus différents :

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
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
> Application Insights, l’extension de diagnostics Microsoft Azure et l’agent InfluxData Telegraf sont déjà configurés pour émettre des valeurs de métrique sur le point de terminaison régional correct et intégrer toutes les propriétés ci-dessus dans chaque émission.
>
>

## <a name="custom-metric-definitions"></a>Définitions de métriques personnalisées
Il est inutile de définir préalablement une métrique personnalisée dans Azure Monitor avant son émission. Étant donné que chaque point de données de métrique publié contient les informations d’espace de noms, de nom et de dimension, une définition de métrique est automatiquement créée la première fois qu’une métrique personnalisée est émise vers Azure Monitor. Cette définition de métrique est ensuite détectable sur n’importe quelle ressource pour laquelle la métrique a été émise via les définitions de métriques.

> [!NOTE]
> Azure Monitor ne prend pas encore en charge la définition d’« unités » pour une métrique personnalisée.

## <a name="using-custom-metrics"></a>Utilisation de métriques personnalisées
Une fois les métriques personnalisées émises vers Azure Monitor, vous pouvez les parcourir dans le Portail Azure, les interroger à l’aide des API REST Azure Monitor ou créer des alertes sur ces métriques pour recevoir des notifications lorsque certaines conditions sont remplies.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Parcourir vos métriques personnalisées dans le Portail Azure
1.  Accédez au [Portail Azure](https://portal.azure.com).
2.  Sélectionnez le panneau Surveiller.
3.  Cliquez sur Métriques.
4.  Sélectionnez une ressource pour laquelle vous avez émis des métriques personnalisées.
5.  Sélectionnez l’espace de noms de métrique pour votre métrique personnalisée.
6.  Sélectionnez la métrique personnalisée.

## <a name="supported-regions"></a>Régions prises en charge
Dans le cadre de la préversion publique, il est possible de publier des métriques personnalisées dans un sous-ensemble de régions Azure uniquement. Cela signifie que les métriques peuvent être publiées pour les ressources dans l’une des régions prises en charge uniquement. Le tableau ci-dessous répertorie l’ensemble des régions Azure prises en charge pour les métriques personnalisées et les points de terminaison dans lesquels doivent être publiées les métriques pour les ressources de ces régions.

|Région Azure|Préfixe du point de terminaison régional|
|---|---|
|USA Est|https://eastus.monitoring.azure.com/|
|USA Centre Sud|https://southcentralus.monitoring.azure.com/|
|USA Centre-Ouest|https://westcentralus.monitoring.azure.com/|
|USA Ouest 2|https://westus2.monitoring.azure.com/|
|Asie Sud-Est|https://southeastasia.monitoring.azure.com/|
|Europe Nord|https://northeurope.monitoring.azure.com/|
|Europe Ouest|https://westeurope.monitoring.azure.com/|

## <a name="quotas-and-limits"></a>Quotas et limites
Azure Monitor impose les limites d’utilisation suivantes quant aux métriques personnalisées.

|Catégorie|Limite|
|---|---|
|Série chronologique active/abonnements/région|50 000|
|Clés de dimension par métrique|10|
|Longueur de chaîne pour les espaces de noms de métrique, les noms de métrique, les clés de dimension et les valeurs de dimension|256 caractères|
Une série chronologique active se définit comme toute combinaison unique de métrique, clé de dimension et valeur de dimension pour laquelle des valeurs de métrique ont été publiées au cours des 12 dernières heures.

## <a name="next-steps"></a>Étapes suivantes
Utilisation de métriques personnalisées à partir de différents services 
 - [Machine virtuelle](metrics-store-custom-guestos-resource-manager-vm.md)
 - [Groupe de machines virtuelles identiques](metrics-store-custom-guestos-resource-manager-vmss.md)
 - [Machine virtuelle (classique)](metrics-store-custom-guestos-classic-vm.md)
 - [Machine virtuelle Linux utilisant l’agent Telegraf](metrics-store-custom-linux-telegraf.md)
 - [API REST](metrics-store-custom-rest-api.md)
 - [Service cloud (classique)](metrics-store-custom-guestos-classic-cloud-service.md)
 