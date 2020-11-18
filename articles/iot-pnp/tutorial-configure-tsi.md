---
title: Utiliser Time Series Insights pour stocker et analyser la télémétrie des appareils Plug-and-Play Azure IoT | Microsoft Docs
description: Configurez un environnement Time Series Insights et connectez votre hub IoT pour afficher et analyser la télémétrie de vos appareils IoT Plug-and-Play.
author: lyrana
ms.author: lyhughes
ms.date: 10/14/2020
ms.topic: tutorial
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: aa99b9059fe8e3cd5b0dfe6f7e62bd02012fd144
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422261"
---
# <a name="tutorial-create-and-connect-to-time-series-insights-gen2-to-store-visualize-and-analyze-iot-plug-and-play-device-telemetry"></a>Tutoriel : Créer et se connecter à Time Series Insights Gen2 pour stocker, visualiser et analyser la télémétrie des appareils IoT Plug-and-Play.

Dans ce tutoriel, vous allez apprendre à créer et à configurer correctement un environnement [Azure Time Series Insights Gen2](https://docs.microsoft.com/azure/time-series-insights/overview-what-is-tsi) (TSI) pour l’intégrer à votre solution IoT Plug-and-Play. Avec TSI, vous pouvez collecter, traiter, stocker, interroger et visualiser des données de série chronologique à l’échelle IoT (Internet des objets).

Tout d’abord, vous allez provisionner un environnement TSI et connecter votre hub IoT en tant que source d’événements de streaming. Ensuite, vous utiliserez la synchronisation de modèle pour créer le modèle Time Series de votre environnement TSI d’après les exemples de fichiers de modèle [DTDL (Digital Twins Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) que vous avez utilisés pour les appareils de contrôleur de température et de thermostat.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [iot-pnp-prerequisites](../../includes/iot-pnp-prerequisites.md)]

Pour éviter d’avoir à installer Azure CLI localement, vous pouvez configurer les services cloud à l’aide d’Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="time-series-id-selection"></a>Sélection de l’ID Time Series

Lors du provisionnement de votre environnement TSI, vous devrez sélectionner un ID Time Series. La sélection de l’ID Time Series approprié est critique, car la propriété est immuable et ne peut plus être modifiée une fois qu’elle a été définie. Le choix d’un ID de série chronologie est identique au choix d’une clé de partition pour une base de données. En règle générale, votre ID TS doit être le nœud terminal de votre modèle de ressource. En d’autres termes, vous devrez généralement sélectionner la propriété ID de l’élément ou du capteur le plus précis qui émet des données de télémétrie.

En tant qu’utilisateur IoT Plug-and-Play, le facteur le plus pertinent pour sélectionner votre ID TS est la présence de [composants](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#component) dans vos modèles d’appareil. 

![Sélection de l’ID TS](./media/tutorial-configure-tsi/ts-id-selection-pnp.png)

* Si vous avez suivi le guide démarrage rapide et que votre appareil IoT Hub représente le [Thermostat](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/Thermostat.json), utilisez `iot-hub-connection-device-id` comme ID TS.

* Si vous avez suivi l’un des tutoriels pour le [TemperatureController](https://raw.githubusercontent.com/Azure/opendigitaltwins-dtdl/master/DTDL/v2/samples/TemperatureController.json) à plusieurs composants, utilisez une clé composite dans la section ci-dessous, écrite au format `iot-hub-connection-device-id, dt-subject`

## <a name="provision-your-azure-time-series-insights-gen2-environment"></a>Provisionner votre environnement Azure Time Series Insights Gen2

La commande ci-dessous exécute les actions suivantes :

* Création d’un compte Stockage Azure pour le [magasin froid](https://docs.microsoft.com/azure/time-series-insights/concepts-storage#cold-store) de votre environnement, destiné à la conservation à long terme et à l’analyse des données historiques
  * Remplacement de `mytsicoldstore` par un nom unique pour votre compte
* Création d’un environnement Azure Time Series Insights GEN, y compris un stockage chaud avec une période de conservation de sept jours et un magasin froid pour une conservation infinie 
  * Remplacement de `my-tsi-env` par un nom unique pour votre environnement TSI 
  * Remplacement de `my-pnp-resourcegroup` par le nom du groupe de ressources que vous avez utilisé lors de la configuration
  * Remplacement de `my-ts-id-property` par la valeur de votre propriété d’ID TS en fonction des critères de sélection ci-dessus

```azurecli-interactive
storage=mytsicoldstore
rg=my-pnp-resourcegroup
az storage account create -g $rg -n $storage --https-only
key=$(az storage account keys list -g $rg -n $storage --query [0].value --output tsv)
az timeseriesinsights environment longterm create --name my-tsi-env --resource-group $rg --time-series-id-properties my-ts-id-property --sku-name L1 --sku-capacity 1 --data-retention 7 --storage-account-name $storage --storage-management-key $key --location eastus2
```

À présent, vous allez configurer le hub IoT que vous avez créé plus tôt en tant que [source d’événements](https://docs.microsoft.com/azure/time-series-insights/concepts-streaming-ingestion-event-sources) de votre environnement. Lorsque votre source d’événements est connectée, TSI commence à indexer les événements en provenance de votre hub, en commençant par l’événement le plus ancien dans la file d’attente.

Commencez par créer un groupe de consommateurs unique sur votre hub IoT pour votre environnement TSI. Remplacez `my-pnp-hub` par le nom du hub IoT que vous avez utilisé précédemment.

```azurecli-interactive
az iot hub consumer-group create --hub-name my-pnp-hub --name tsi-consumer-group 
```

Connectez le hub IoT. Remplacez `my-pnp-resourcegroup`, `my-pnp-hub` et `my-tsi-env` par vos valeurs respectives.

```azurecli-interactive
rg=my-pnp-resourcegroup
iothub=my-pnp-hub
env=my-tsi-env
es_resource_id=$(az iot hub create -g $rg -n $iothub --query id --output tsv)
shared_access_key=$(az iot hub policy list -g $rg --hub-name $iothub --query "[?keyName=='service'].primaryKey" --output tsv)
az timeseriesinsights event-source iothub create -g $rg --environment-name $env -n iot-hub-event-source --consumer-group-name tsi-consumer-group  --key-name iothubowner --shared-access-key $shared_access_key --event-source-resource-id $es_resource_id
```
Accédez à votre groupe de ressources dans le [portail Azure](https://portal.azure.com) et sélectionnez votre nouvel environnement Time Series Insights. Accédez à l’*URL de l’Explorateur Time Series Insights* affichée dans la vue d’ensemble de l’instance.

![Page de présentation du portail](./media/tutorial-configure-tsi/view-environment.png)

Dans l’Explorateur, vous devez voir vos deux thermostats sous « Toutes les hiérarchies ». Ensuite, vous allez organiser votre modèle Time Series en fonction de votre modèle d’appareil.

![Vue de l’Explorateur 1](./media/tutorial-configure-tsi/tsi-env-first-view.png)

## <a name="model-synchronization-between-digital-twins-definition-language-and-time-series-insights-gen2"></a>Synchronisation de modèle entre Digital Twins Definition Language et Time Series Insights Gen2

Ensuite, vous allez traduire votre modèle d’appareil DTDL en modèle de ressource dans Azure Time Series Insights (TSI). Le modèle Time Series de TSI est un outil de modélisation sémantique pour la contextualisation des données dans TSI. Un modèle de série chronologique comprend trois composants de base :

* [Instances de modèle Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-instances). Les instances sont des représentations virtuelles des séries chronologiques proprement dites. Elles sont identifiées de manière unique par votre ID TS.
* [Hiérarchies de modèle Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-hierarchies). Les hiérarchies classent les instances en spécifiant les noms de propriétés et leurs relations.
* [Types de modèle Time Series](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview#time-series-model-types). Les types vous aident à définir des [variables](https://docs.microsoft.com/azure/time-series-insights/concepts-variables) ou des formules permettant d’effectuer des calculs. Les types sont associés à une instance spécifique.

> [!NOTE]
> Les exemples ci-dessous concernent le TemperatureController à plusieurs composants.

### <a name="define-your-types"></a>Définir vos types

Vous pouvez commencer à ingérer des données dans Azure Time Series Insights Gen2 sans avoir prédéfini de modèle. Quand la télémétrie arrive, TSI tente de résoudre automatiquement les instances de série chronologique en fonction de la valeur de la propriété d’ID TS. Le *Type par défaut* est attribué à toutes les instances. Vous devez créer manuellement un nouveau type pour représenter vos modèles. L’image ci-dessous illustre une méthode simple pour synchroniser un modèle DTDL et un type TSM :

![DTDL vers le type TSM](./media/tutorial-configure-tsi/DTDL-to-TSM-Type.png)

* Votre identificateur DTMI (Digital Twin Model Identifier) deviendra votre ID de type.
* Le nom du type peut être le nom ou le nom complet du modèle.
* La description du modèle devient la description du type.
* Au moins une variable Type est créée pour chaque composant de télémétrie ayant un schéma numérique. 
  * Seuls les types de données numériques peuvent être utilisés comme variables, mais si une valeur est envoyée sous la forme d’une chaîne pouvant être analysée, par exemple `"0"`, vous pouvez utiliser une fonction de [conversion](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax#conversion-functions) telle que `toDouble`.
* Le nom de la variable peut être le nom ou le nom complet de la télémétrie.
* Lors de la définition de l’expression TSX (Time Series Expression) de la variable, reportez-vous au nom de la télémétrie sur le câble et à son type de données.

> [!NOTE]
> Cet exemple montre uniquement deux variables, un agrégat et un nombre, mais chaque type peut en avoir jusqu’à 100. Différentes variables peuvent faire référence à la même valeur de télémétrie pour effectuer différents calculs en fonction des besoins. Pour obtenir la liste complète des filtres, agrégats et fonctions scalaires, consultez la documentation sur la [syntaxe Time Series Expression dans Time Series Insights Gen2](https://docs.microsoft.com/rest/api/time-series-insights/reference-time-series-expression-syntax).

Ouvrez l’éditeur de texte de votre choix et enregistrez le fichier JSON ci-dessous sur votre disque local :

```JSON
{
  "put": [
    {
      "id": "dtmi:com:example:Thermostat;1",
      "name": "Thermostat",
      "description": "Reports current temperature and provides desired temperature control.",
      "variables": {
        "EventCount": {
          "kind": "aggregate",
          "aggregation": {
            "tsx": "count()"
          }
        },
        "Temperature": {
          "kind": "numeric",
          "value": {
            "tsx": "$event.temperature.Double"
          },
          "aggregation": {
            "tsx": "avg($value)"
          }
        }
      }
    }
  ]
}
```

Dans l’Explorateur Time Series Insights, accédez à l’onglet Modèle en cliquant sur l’icône de modèle sur la gauche. Cliquez sur **Types** puis sur **Charger un fichier JSON** :

![Télécharger](./media/tutorial-configure-tsi/upload-type.png)

Sélectionnez **Choisir un fichier**, sélectionnez le fichier JSON que vous avez enregistré, puis cliquez sur **Charger**

Vous devriez voir le type Thermostat qui vient d’être défini.

### <a name="optional---create-a-hierarchy"></a>Facultatif - Créer une hiérarchie

Vous pouvez éventuellement créer une hiérarchie pour organiser les deux composants de thermostat sous leur parent TemeraptureController.

Cliquez sur *Hiérarchies* et sélectionnez *Ajouter une hiérarchie*. Entrez `Device Fleet` comme nom et créez un niveau nommé `Device Name`, puis cliquez sur *Enregistrer*.

![Ajouter une hiérarchie](./media/tutorial-configure-tsi/add-hierarchy.png)

### <a name="assign-your-instances-to-the-correct-type"></a>Affecter vos instances au type correct

Ensuite, vous allez changer le type de vos instances et éventuellement les placer dans la hiérarchie.

Sélectionnez l’onglet *Instances*, puis cliquez sur l’icône *Modifier* à l’extrême droite.

![Modifier des instances](./media/tutorial-configure-tsi/edit-instance.png)

Cliquez sur la liste déroulante Type, puis sélectionnez `Thermostat`. 

![Changer le type d’instance](./media/tutorial-configure-tsi/change-type.png)

Si vous avez créé une hiérarchie, sélectionnez *Champs d’instance* et cochez la case `Device Fleet`. Entrez `Temperature Controller` comme valeur de l’appareil parent, puis cliquez sur *Enregistrer*.

![Affecter à la hiérarchie](./media/tutorial-configure-tsi/assign-to-hierarchy.png)

Répétez les étapes ci-dessus pour votre deuxième thermostat.

### <a name="view-your-data"></a>Affichage de vos données

Revenez au volet de graphique et développez la flotte d’appareils et TemperatureController. Cliquez sur thermostat1, sélectionnez la variable `Temperature`, puis cliquez sur *Ajouter* pour représenter la valeur sous forme de graphique. Procédez de la même façon pour thermostat2.

![Changer le type d’instance pour thermostat2](./media/tutorial-configure-tsi/charting-values.png)

## <a name="next-steps"></a>Étapes suivantes

* Pour en savoir plus sur les différentes options de graphique, notamment le dimensionnement des intervalles et les contrôles de l’axe Y, consultez la documentation sur l’[Explorateur Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/concepts-ux-panels).

* Pour obtenir une vue d’ensemble détaillée du modèle Time Series de votre environnement, consultez [cet article](https://docs.microsoft.com/azure/time-series-insights/concepts-model-overview).

* Pour explorer les API de requête et la syntaxe Time Series Expression, consultez [cet article](https://docs.microsoft.com/rest/api/time-series-insights/reference-query-apis).




