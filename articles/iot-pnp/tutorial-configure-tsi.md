---
title: Utiliser Time Series Insights pour stocker et analyser la télémétrie des appareils Plug-and-Play Azure IoT | Microsoft Docs
description: Configurez un environnement Time Series Insights et connectez votre hub IoT pour afficher et analyser la télémétrie de vos appareils IoT Plug-and-Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ca2319a78fb4c0c720a21e97944d5b75ada9d008
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014991"
---
# <a name="preview-tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Tutoriel de la préversion : Créer et se connecter à Time Series Insights Gen2 pour stocker, visualiser et analyser la télémétrie des appareils IoT Plug-and-Play.

Dans ce tutoriel, vous apprenez à créer et à configurer correctement un environnement [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) pour l’intégrer à votre solution IoT Plug-and-Play. Utilisez TSI pour collecter, traiter, stocker, interroger et visualiser des données de série chronologique à l’échelle IoT (Internet des objets).

Tout d’abord, vous provisionnez un environnement TSI et connectez votre hub IoT en tant que source d’événements de streaming. Ensuite, vous utilisez la synchronisation de modèle pour créer le [modèle de série chronologique](../time-series-insights/concepts-model-overview.md) d’après les exemples de fichiers de modèle [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) que vous avez utilisés pour les appareils de contrôleur de température et de thermostat.

> [!NOTE]
> Cette intégration est en préversion. Les relations entre les modèles d’appareil DTDL et le modèle de série chronologique sont susceptibles de changer.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

À ce stade, vous disposez des éléments suivants :

* Un IoT Hub Azure.
* Une instance DPS liée à votre hub IoT, avec une inscription d’appareil individuel pour votre appareil IoT Plug-and-Play.
* Une connexion à votre hub IoT à partir d’un appareil mono- ou multicomposant, pour le streaming des données simulées.

Pour éviter d’avoir à installer Azure CLI localement, vous pouvez configurer les services cloud à l’aide d’Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-your-event-source"></a>Préparer votre source d’événements

Le hub IoT que vous avez créé fera office de [source d’événements](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) de votre environnement TSI.

> [!IMPORTANT]
> Désactivez les routes IoT Hub existants. Il existe un problème connu quand vous utilisez un hub IoT comme source d’événements TSI avec [routage](../iot-hub/iot-hub-devguide-messages-d2c.md#routing-endpoints) configuré. Désactivez temporairement tous les points de terminaison de routage, que vous pourrez réactiver une fois votre hub IoT connecté à TSI.

Créez sur votre hub IoT un groupe de consommateurs unique destiné à être consommé par TSI. Remplacez `my-pnp-hub` par le nom du hub IoT que vous avez utilisé :

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group
```

## <a name="choose-your-time-series-id"></a>Choisir votre ID de série chronologique

Quand vous provisionnez votre environnement TSI, vous devez sélectionner un *ID de série chronologique*. Il est important de sélectionner l’ID de série chronologique approprié, car cette propriété est immuable et ne peut plus être changée une fois qu’elle a été définie. Un ID de série chronologique est semblable à une clé de partition de base de données. L’ID de série chronologique fait office de clé primaire pour votre modèle de série chronologique. Pour plus d’informations, consultez [Bonnes pratiques pour choisir un ID de série chronologique](../time-series-insights/how-to-select-tsid.md).

En tant qu’utilisateur IoT Plug-and-Play, spécifiez une _clé composite_ constituée de `iothub-connection-device-id` et `dt-subject` en guise d’ID de série chronologique. IoT Hub ajoute ces propriétés système, qui contiennent respectivement votre ID d’appareil IoT Plug-and-Play et les noms de vos composants d’appareil.

Même si vos modèles d’appareil IoT Plug-and-Play n’utilisent pas de composants, vous devez inclure `dt-subject` dans le cadre d’une clé composite afin de pouvoir les utiliser à l’avenir. Votre ID de série chronologique étant immuable, Microsoft recommande d’activer cette option au cas où vous en auriez besoin.

> [!NOTE]
> Les exemples ci-dessous concernent l’appareil **TemperatureController** multicomposant, mais les concepts sont les mêmes pour l’appareil **Thermostat** sans composant.

## <a name="provision-your-tsi-environment"></a>Provisionner votre environnement TSI

Cette section explique comment provisionner votre environnement Azure Time Series Insights Gen2.

La commande ci-après effectue les opérations suivantes :

* Création d’un compte Stockage Azure pour le [magasin froid](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) de votre environnement, destiné à la conservation à long terme et à l’analyse des données historiques
  * Remplacez `mytsicoldstore` par un nom unique pour votre compte de stockage froid.
* Création d’un environnement Azure Time Series Insights Gen2, y compris un stockage chaud avec une période de conservation de sept jours et un stockage froid pour une conservation infinie
  * Remplacez `my-tsi-env` par un nom unique pour votre environnement TSI.
  * Remplacez `my-pnp-resourcegroup` par le nom du groupe de ressources que vous avez utilisé lors de la configuration.
  * `iothub-connection-device-id, dt-subject` est la propriété de votre ID de série chronologique.

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties iothub-connection-device-id, dt-subject --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

Connectez votre source d’événements IoT Hub. Remplacez `my-pnp-resourcegroup`, `my-pnp-hub` et `my-tsi-env` par les valeurs que vous avez choisies. La commande suivante référence le groupe de consommateurs pour TSI que vous avez créé :

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```

Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez votre nouvel environnement Time Series Insights. Accédez à l’*URL de l’Explorateur Time Series Insights* affichée dans la vue d’ensemble de l’instance :

![Page de présentation du portail](./media/tutorial-configure-tsi/view-environment.png)

Dans l’Explorateur, vous voyez trois instances :

* &lt;Votre ID d’appareil pnp&gt;, thermostat1
* &lt;Votre ID d’appareil pnp&gt;, thermostat2
* &lt;Votre ID d’appareil pnp&gt;, `null`

> [!NOTE]
> La troisième étiquette représente la télémétrie de **TemperatureController**, telle que la plage de travail de la mémoire de l’appareil. Étant donné qu’il s’agit d’une propriété de niveau supérieur, la valeur du nom du composant est null. Dans une étape ultérieure, vous la mettrez à jour avec un nom plus convivial.

![Vue de l’Explorateur 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="configure-model-translation"></a>Configurer la traduction du modèle

Ensuite, vous traduisez votre modèle d’appareil DTDL en modèle de ressource dans Azure Time Series Insights (TSI). Le modèle Time Series de TSI est un outil de modélisation sémantique pour la contextualisation des données dans TSI. Un modèle de série chronologique comprend trois composants de base :

* [Instances de modèle Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-instances). Les instances sont des représentations virtuelles des séries chronologiques proprement dites. Les instances sont identifiées de manière unique par votre ID de série chronologique.
* [Hiérarchies de modèle Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-hierarchies). Les hiérarchies classent les instances en spécifiant les noms de propriétés et leurs relations.
* [Types de modèle Time Series](../time-series-insights/concepts-model-overview.md#time-series-model-types). Les types vous aident à définir des [variables](../time-series-insights/concepts-variables.md) ou des formules pour des calculs. Les types sont associés à une instance spécifique.

### <a name="define-your-types"></a>Définir vos types

Vous pouvez commencer à ingérer des données dans Azure Time Series Insights Gen2 sans avoir prédéfini de modèle. Quand la télémétrie arrive, TSI tente de résoudre automatiquement les instances de série chronologique en fonction de la valeur ou des valeurs de propriété de votre ID de série chronologique. Le *type par défaut* est attribué à toutes les instances. Vous devez créer manuellement un type pour classer correctement vos instances. Les informations suivantes montrent la méthode la plus simple pour synchroniser vos modèles DTDL d’appareil avec vos types de modèles de série chronologique :

* Votre identificateur DTMI de modèle de jumeau numérique devient votre ID de type.
* Le nom du type peut être le nom du modèle ou le nom d’affichage.
* La description du modèle devient la description du type.
* Au moins une variable de type est créée pour chaque télémétrie avec un schéma numérique.
  * Seuls les types de données numériques peuvent être utilisés comme variables, mais si une valeur est envoyée sous la forme d’un autre type pouvant être converti, par exemple `"0"`, vous pouvez utiliser une fonction de [conversion](/rest/api/time-series-insights/reference-time-series-expression-syntax.md#conversion-functions) telle que `toDouble`.
* Le nom de la variable peut être le nom de la télémétrie ou le nom d’affichage.
* Quand vous définissez l’expression de série chronologique d’une variable, faites référence au nom de la télémétrie transmise et à son type de données.

| JSON DTDL | JSON du type de modèle de série chronologique | Valeur d'exemple |
|-----------|------------------|-------------|
| `@id` | `id` | `dtmi:com:example:TemperatureController;1` |
| `displayName`    | `name`   |   `Temperature Controller`  |
| `description`  |  `description`  |  `Device with two thermostats and remote reboot.` |  
|`contents` (tableau)| `variables` (objet)  | Reportez-vous à l’exemple ci-dessous

![Correspondance entre DTDL et le type de modèle de série chronologique](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

> [!NOTE]
> Cet exemple montre trois variables, mais chaque type peut en avoir jusqu’à 100. Différentes variables peuvent référencer la même valeur de télémétrie pour effectuer différents calculs en fonction des besoins. Pour obtenir la liste complète des filtres, agrégats et fonctions scalaires, consultez la [syntaxe Time Series Expression dans Time Series Insights Gen2](/rest/api/time-series-insights/reference-time-series-expression-syntax.md).

Ouvrez un éditeur de texte et enregistrez le code JSON suivant sur votre disque local :

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:TemperatureController;1",
      "name": "Temperature Controller",
      "description": "Device with two thermostats and remote reboot.",
      "variables": {
        "workingSet": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.workingSet.Long, toLong($event.workingSet.Double))"
          }, 
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "coalesce($event.temperature.Long, toLong($event.temperature.Double))"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        },
        "eventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        }
      }
    }
  ]
}
```

Dans l’Explorateur Time Series Insights, accédez à l’onglet **Modèle** en sélectionnant l’icône de modèle sur la gauche. Sélectionnez **Types**, puis sélectionnez **Charger un fichier JSON** :

![Télécharger](./media/tutorial-configure-tsi/upload-type.png)

Sélectionnez **Choisir un fichier**, sélectionnez le fichier JSON que vous avez enregistré, puis sélectionnez **Charger**.

Vous voyez le type **Temperature Controller** (Contrôleur de température) nouvellement défini.

### <a name="create-a-hierarchy"></a>Créer une hiérarchie

Créez une hiérarchie pour organiser les étiquettes sous leur parent **TemperatureController**. L’exemple simple suivant présente un seul niveau, mais les solutions IoT ont généralement de nombreux niveaux d’imbrication pour contextualiser les étiquettes dans leur position physique et sémantique au sein d’une organisation.

Sélectionnez **Hiérarchies**, puis **Ajouter une hiérarchie**. Entrez *Device Fleet* (Parc d’appareils) comme nom et créez un niveau appelé *Device Name* (nom de l’appareil). Ensuite, sélectionnez **Enregistrer**.

![Ajouter une hiérarchie](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Affecter vos instances au type correct

Ensuite, vous changez le type de vos instances et les placez dans la hiérarchie.

Sélectionnez l’onglet **Instances**, recherchez l’instance qui représente la plage de travail de l’appareil, puis sélectionnez l’icône **Modifier** à l’extrême droite :

![Modifier des instances](./media/tutorial-configure-tsi/edit-instance.png)

Sélectionnez la liste déroulante **Type**, puis **Temperature Controller**. Entrez *defaultComponent, <your device name>* pour mettre à jour le nom de l’instance qui représente toutes les étiquettes de niveau supérieur associées à votre appareil.

![Changer le type d’instance](./media/tutorial-configure-tsi/change-type.png)

Avant de sélectionner Enregistrer, sélectionnez l’onglet **Champs d’instance** et cochez la case **Device Fleet**. Entrez `<your device name> - Temp Controller` pour regrouper les données de télémétrie, puis sélectionnez **Enregistrer**.

![Affecter à la hiérarchie](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Répétez les étapes précédentes pour affecter à vos étiquettes de thermostat les type et hiérarchie appropriés.

## <a name="view-your-data"></a>Affichage de vos données

Revenez au volet de graphique et développez **Device Fleet > votre appareil**. Sélectionnez **thermostat1**, sélectionnez la variable **Temperature**, puis sélectionnez **Ajouter** pour représenter la valeur sous forme graphique. Procédez de la même façon pour **thermostat2** et la valeur **workingSet** **defaultComponent**.

![Changer le type d’instance pour thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différentes options de graphique, notamment le dimensionnement des intervalles et les contrôles de l’axe Y, consultez l’[Explorateur Azure Time Series Insights](../time-series-insights/concepts-ux-panels.md).

* Pour obtenir une présentation détaillée du modèle de série chronologique de votre environnement, consultez l’article [Modèle de série chronologique dans Azure Time Series Insights Gen2](../time-series-insights/concepts-model-overview.md).

* Pour explorer les API de requête et la syntaxe des expressions de série chronologique, consultez [API de requête Azure Time Series Insights Gen2](/rest/api/time-series-insights/reference-query-apis.md).
