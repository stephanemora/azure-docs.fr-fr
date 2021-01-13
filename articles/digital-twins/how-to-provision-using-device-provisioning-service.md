---
title: Gérer automatiquement les appareils à l’aide de DPS
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer un processus automatisé pour approvisionner et mettre hors service des appareils IoT dans Azure Digital Twins à l’aide du service Device Provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 9/1/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e783e5dd3b0f1952928d1c36c682c5be1cba2599
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/08/2021
ms.locfileid: "98044388"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gérer automatiquement les appareils dans Azure Digital représentations à l’aide du service Device Provisioning (DPS)

Dans cet article, vous allez apprendre à intégrer Azure Digital Twins avec [le service Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).

La solution décrite dans cet article vous permet d’automatiser le processus d’**_approvisionnement_** et de **_mise hors service_** d’appareils IoT Hub dans Azure Digital Twins, à l’aide du service Device Provisioning. 

Pour plus d’informations sur les étapes d’_approvisionnement_ et de _mise hors service_ et mieux comprendre l’ensemble des étapes générales de la gestion des appareils qui sont communes à tous les projets IoT d’entreprise, consultez la [section *Cycle de vie des appareils*](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) de la documentation sur la gestion des appareils d’IoT Hub.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer l’approvisionnement, vous devez disposer d’une **instance Azure Digital Twins** contenant des modèles et des représentations. Cette instance doit également être configurée avec la possibilité de mettre à jour les informations sur les représentations numériques en fonction des données. 

Si vous ne l’avez pas déjà configurée, vous pouvez la créer en suivant le [*tutoriel Azure Digital Twins : Connecter une solution de bout en bout*](tutorial-end-to-end.md). Ce didacticiel vous guide tout au long de la configuration d’une instance Azure Digital Twins avec des modèles et représentations, un Azure [IoT Hub](../iot-hub/about-iot-hub.md) connecté et plusieurs [Azure Functions](../azure-functions/functions-overview.md) pour propager le flux de données.

Plus tard dans cet article, vous aurez besoin des valeurs suivantes utilisées pour configurer votre instance. Si vous devez regrouper ces valeurs, utilisez les liens ci-dessous pour obtenir des instructions.
* **_Nom d’hôte_** de l’instance Azure Digital Twins ([à rechercher dans le portail](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))
* **_Chaîne de connexion_** Azure Event Hubs ([Rechercher dans le portail](../event-hubs/event-hubs-get-connection-string.md#get-connection-string-from-the-portal))

Cet exemple utilise également un **simulateur d’appareil** qui comprend l’approvisionnement à l’aide du service Device Provisioning. Le simulateur d’appareil se trouve ici : [Exemple d’intégration d’Azure Digital Twins et d’IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Téléchargez l’exemple de projet sur votre machine en suivant le lien de l’exemple, puis en sélectionnant le bouton *Télécharger le zip* sous le titre. Décompressez le dossier téléchargé.

Le simulateur d’appareil est basé sur **Node.js**, version 10.0.x ou ultérieure. L’article [*Préparer votre environnement de développement*](https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md) décrit l’installation de Node.js pour ce didacticiel sur Windows ou sur Linux.

## <a name="solution-architecture"></a>Architecture de solution

L’image ci-dessous illustre l’architecture de cette solution à l’aide d’Azure Digital Twins avec le service Device Provisioning. Il affiche à la fois le processus d’approvisionnement et de mise hors service de l’appareil.

:::image type="content" source="media/how-to-provision-using-dps/flows.png" alt-text="Vue d’un appareil et de plusieurs services Azure dans un scénario de bout en bout. Les données transitent entre un appareil à thermostat et un DPS. Les données passent également du DPS à IoT Hub et à Azure Digital Twins par le biais d’une fonction Azure appelée « allocation ». Les données d’une action manuelle « Supprimer l’appareil » passent par IoT Hub > Event Hubs > Azure Functions > Azure Digital Twins.":::

Cet article est divisé en deux sections :
* [*Provisionner automatiquement un appareil à l’aide du service Device Provisioning*](#auto-provision-device-using-device-provisioning-service)
* [*Mettre hors service automatiquement un appareil à l’aide d’événements de cycle de vie IoT Hub*](#auto-retire-device-using-iot-hub-lifecycle-events)

Pour obtenir des explications plus détaillées sur chaque étape de l’architecture, consultez leurs sections individuelles plus loin dans cet article.

## <a name="auto-provision-device-using-device-provisioning-service"></a>Provisionner automatiquement un appareil à l’aide du service Device Provisioning

Dans cette section, vous allez attacher le service Device Provisioning à Azure Digital Twins pour approvisionner automatiquement des appareils via le chemin d’accès ci-dessous. Il s’agit d’un extrait de l’architecture complète présentée [plus tôt](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/provision.png" alt-text="Flux d’approvisionnement : un extrait du diagramme de l’architecture de la solution, avec des numéros pour étiqueter les sections du flux. Les données transitent entre un appareil à thermostat et un DPS (1 pour appareil > DPS et 5 pour DPS > appareil). Les données passent également du DPS à IoT Hub (4) et à Azure Digital Twins (3) par le biais d’une fonction Azure appelée « allocation » (2).":::

Voici une description du processus :
1. L’appareil contacte le point de terminaison DPS, en transmettant les informations d’identification pour prouver son identité.
2. Le DPS valide l’identité de l’appareil en validant l’ID d’inscription et la clé par rapport à la liste d’inscriptions, et appelle une [Azure Function](../azure-functions/functions-overview.md) pour effectuer l’allocation.
3. La fonction Azure crée une nouvelle [représentation](concepts-twins-graph.md) dans Azure Digital Twins pour l’appareil.
4. DPS inscrit l’appareil sur un hub IoT et renseigne l’état souhaité du jumeau de l’appareil.
5. IoT Hub renvoie les informations d’ID d’appareil et les informations de connexion IoT Hub à l’appareil. L’appareil peut maintenant se connecter à IoT Hub.

Les sections suivantes décrivent les étapes à suivre pour configurer ce flux d’approvisionnement automatique d’appareil.

### <a name="create-a-device-provisioning-service"></a>Créer un service Device Provisioning

Lorsqu’un nouvel appareil est configuré à l’aide du service Device Provisioning, une nouvelle représentation de cet appareil peut être créée dans Azure Digital Twins.

Créez une instance de service Device Provisioning, qui sera utilisée pour approvisionner des appareils IoT. Vous pouvez utiliser les instructions Azure CLI ci-dessous ou utiliser le portail Azure : [*Démarrage rapide : Configurer le service d’approvisionnement d’appareil IoT Hub avec le portail Azure*](../iot-dps/quick-setup-auto-provision.md).

La commande Azure CLI suivante crée un service Device Provisioning. Vous devez spécifier un nom, un groupe de ressources et une région. La commande peut être exécutée dans [Cloud Shell](https://shell.azure.com) ou localement si Azure CLI est [installée sur votre ordinateur](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az iot dps create --name <Device Provisioning Service name> --resource-group <resource group name> --location <region; for example, eastus>
```

### <a name="create-an-azure-function"></a>Création d’une fonction Azure

Ensuite, vous allez créer une fonction déclenchée par une requête HTTP à l’intérieur d’une application de fonction. Vous pouvez utiliser l’application de fonction créée dans le tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)), ou votre propre fonction.

Cette fonction sera utilisée par le service Device Provisioning dans une [stratégie d’allocation personnalisée](../iot-dps/how-to-use-custom-allocation-policies.md) pour approvisionner un nouvel appareil. Pour plus d’informations sur l’utilisation de requêtes HTTP avec Azure Functions, consultez [*Déclencheur par requête HTTP Azure pour Azure Functions*](../azure-functions/functions-bindings-http-webhook-trigger.md).

Dans votre projet d’application de fonction, ajoutez une nouvelle fonction. Ajoutez également un nouveau package NuGet au projet : `Microsoft.Azure.Devices.Provisioning.Service`.

Dans le fichier de code de fonction nouvellement créé, collez le code suivant.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIotHub_allocate.cs":::

Enregistrez le fichier, puis republiez votre application de fonction. Pour obtenir des instructions sur la publication de l’application de fonction, consultez la section [*Publier l’application*](tutorial-end-to-end.md#publish-the-app) du didacticiel de bout en bout.

### <a name="configure-your-function"></a>Configurer votre fonction

Ensuite, vous devez définir des variables d’environnement dans votre application de fonction à partir d’une version antérieure, contenant la référence à l’instance Azure Digital Twins que vous avez créée. Si vous avez utilisé le tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)), le paramètre est déjà configuré.

Ajoutez le paramètre à l’aide de cette commande Azure CLI :

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Assurez-vous que les autorisations et l’attribution de rôle d’identité gérée sont correctement configurées pour l’application de fonction, comme décrit dans la section [*Affecter des autorisations à l’application de fonction*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) dans le didacticiel de bout en bout.

### <a name="create-device-provisioning-enrollment"></a>Créer une inscription Device Provisioning

Ensuite, vous devez créer une inscription dans le service Device Provisioning à l’aide d’une **fonction d’allocation personnalisée**. Suivez les instructions pour effectuer cette opération dans les sections [*Créer l’inscription*](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) et [*Dériver les clés d’appareil uniques*](../iot-dps/how-to-use-custom-allocation-policies.md#derive-unique-device-keys) de l’article Services Device Provisioning sur les stratégies d’allocation personnalisées.

Lorsque vous parcourez ce processus, vous liez l’inscription à la fonction que vous venez de créer en sélectionnant votre fonction au cours de l’étape pour **Sélectionner le mode d’attribution des appareils aux hubs**. Une fois l’inscription créée, le nom d’inscription et la clé SAS principale ou secondaire seront utilisés ultérieurement pour configurer le simulateur d’appareil pour cet article.

### <a name="set-up-the-device-simulator"></a>Configurer le simulateur d’appareil

Cet exemple utilise un simulateur d’appareil qui comprend l’approvisionnement à l’aide du service Device Provisioning. Le simulateur d’appareil se trouve ici : [Exemple d’intégration d’Azure Digital Twins et d’IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Si vous n’avez pas encore téléchargé l’exemple, récupérez-le maintenant en suivant le lien de l’exemple, puis en sélectionnant le bouton *Télécharger le zip* sous le titre. Décompressez le dossier téléchargé.

Ouvrez une fenêtre de commande et accédez au dossier téléchargé, puis au répertoire *device-simulator*. Installez les dépendances pour le projet à l’aide de la commande suivante :

```cmd
npm install
```

Ensuite, copiez le fichier *.env.template* dans un nouveau fichier appelé *.env*, puis renseignez les paramètres suivants :

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device Provisioning Service Scope ID>"
PROVISIONING_REGISTRATION_ID = "<Device Registration ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device Provisioning Service enrollment primary or secondary SAS key>"
```

Enregistrez et fermez le fichier.

### <a name="start-running-the-device-simulator"></a>Lancer l’exécution du simulateur d’appareil

Toujours dans le répertoire *device-simulator*, dans votre fenêtre de commande, démarrez le simulateur d’appareil à l’aide de la commande suivante :

```cmd
node .\adt_custom_register.js
```

Vous devriez voir que l’appareil est enregistré et connecté à IoT Hub, puis qu’il commence à envoyer des messages.
:::image type="content" source="media/how-to-provision-using-dps/output.png" alt-text="Fenêtre de commande affichant de l’inscription et de l’envoi de messages":::

### <a name="validate"></a>Valider

En raison du flux que vous avez configuré dans cet article, l’appareil est automatiquement inscrit dans Azure Digital Twins. Utilisez la commande [Azure Digital Twins CLI](how-to-use-cli.md) suivante pour trouver la représentation de l’appareil dans l’instance Azure Digital Twins que vous avez créée.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

La représentation de l’appareil doit se trouver dans l’instance Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-provisioned-twin.png" alt-text="Fenêtre de commande affichant la représentation nouvellement créée":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Mettre hors service automatiquement un appareil à l’aide d’événements de cycle de vie IoT Hub

Dans cette section, vous allez attacher des événements de cycle de vie IoT Hub à Azure Digital Twins pour mettre hors service automatiquement les appareils en suivant le procédé ci-dessous. Il s’agit d’un extrait de l’architecture complète présentée [plus tôt](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-dps/retire.png" alt-text="Flux de mise hors service d’appareil : un extrait du diagramme de l’architecture de la solution, avec des numéros pour étiqueter les sections du flux. L’appareil à thermostat est affiché sans connexion aux services Azure dans le diagramme. Les données d’une action « Supprimer l’appareil » manuelle passent par IoT Hub (1) > Event Hubs (2) > Azure Functions > Azure Digital Twins (3).":::

Voici une description du processus :
1. Un processus externe ou manuel déclenche la suppression d’un appareil dans IoT Hub.
2. IoT Hub supprime l’appareil et génère un événement de [cycle de vie de l’appareil](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) qui est routé vers un [Event Hub](../event-hubs/event-hubs-about.md).
3. Une fonction Azure supprime la représentation de l’appareil dans Azure Digital Twins.

Les sections suivantes décrivent les étapes à suivre pour configurer ce flux de mise hors service automatique d’appareil.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Vous devez maintenant créer une instance Azure [Event Hub](../event-hubs/event-hubs-about.md), qui sera utilisée pour recevoir les événements de cycle de vie d’IoT Hub. 

Suivez les étapes décrites dans le démarrage rapide [*Créer un Event Hub*](../event-hubs/event-hubs-create.md), à l’aide des informations suivantes :
* Si vous utilisez le Tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)), vous pouvez réutiliser le groupe de ressources que vous avez créé pour le didacticiel.
* Nommez votre Event Hub *lifecycleevents* ou un autre nom de votre choix, et souvenez-vous de l’espace de noms que vous avez créé. Vous l’utiliserez lorsque vous configurerez la fonction de cycle de vie et l’itinéraire IoT Hub dans les sections suivantes.

### <a name="create-an-azure-function"></a>Création d’une fonction Azure

Ensuite, vous allez créer une fonction déclenchée par Event Hubs à l’intérieur d’une application de fonction. Vous pouvez utiliser l’application de fonction créée dans le tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](tutorial-end-to-end.md)), ou votre propre fonction. 

Nommez votre déclencheur Event Hub *lifecycleevents* et connectez le déclencheur Event Hub à l’instance Event Hub que vous avez créée à l’étape précédente. Si vous avez utilisé un nom d’Event Hub différent, modifiez-le pour qu’il corresponde au nom du déclencheur ci-dessous.

Cette fonction utilisera l’événement du cycle de vie de l’appareil IoT Hub pour mettre un appareil existant hors service. Pour plus d’informations sur les événements de cycle de vie, consultez [*Événements IoT Hub autres que les événements de télémétrie*](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Pour plus d’informations sur l’utilisation d’Event Hubs avec Azure Functions, consultez [*Déclencheur Azure Event Hubs pour Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

Dans votre application de fonction publiée, ajoutez une nouvelle classe de fonction de type *Déclencheur Event Hub* et collez le code ci-dessous.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/adtIotHub_delete.cs":::

Enregistrez le projet, puis publiez de nouveau l’application de fonction. Pour obtenir des instructions sur la publication de l’application de fonction, consultez la section [*Publier l’application*](tutorial-end-to-end.md#publish-the-app) du didacticiel de bout en bout.

### <a name="configure-your-function"></a>Configurer votre fonction

Ensuite, vous devez définir des variables d’environnement dans votre application de fonction à partir d’une version antérieure, contenant la référence à l’instance Azure Digital Twins que vous avez créée et l’Event Hub. Si vous avez utilisé le tutoriel de bout en bout ([*Tutoriel : Connecter une solution de bout en bout*](./tutorial-end-to-end.md)), le premier paramètre est déjà configuré.

Ajoutez le paramètre à l’aide de cette commande Azure CLI. La commande peut être exécutée dans [Cloud Shell](https://shell.azure.com) ou localement si Azure CLI est [installée sur votre ordinateur](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).

```azurecli-interactive
az functionapp config appsettings set --settings "ADT_SERVICE_URL=https://<Azure Digital Twins instance _host name_>" -g <resource group> -n <your App Service (function app) name>
```

Ensuite, vous devrez configurer la variable d’environnement de la fonction pour la connexion à l’instance Event Hub nouvellement créée.

```azurecli-interactive
az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event Hubs SAS connection string Listen>" -g <resource group> -n <your App Service (function app) name>
```

Assurez-vous que les autorisations et l’attribution de rôle d’identité gérée sont correctement configurées pour l’application de fonction, comme décrit dans la section [*Affecter des autorisations à l’application de fonction*](tutorial-end-to-end.md#assign-permissions-to-the-function-app) dans le didacticiel de bout en bout.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Créer un itinéraire IoT Hub pour les événements de cycle de vie

Vous devez maintenant configurer un itinéraire IoT Hub pour acheminer les événements de cycle de vie des appareils. Dans ce cas, vous écoutez spécifiquement les événements de suppression d’appareil, identifiés par `if (opType == "deleteDeviceIdentity")`. Cela déclenche la suppression de la représentation numérique, en finalisant la mise hors service d’un appareil et de sa représentation numérique.

Les instructions de création d’un itinéraire IoT Hub sont décrites dans cet article : [*Utiliser le routage des messages IoT Hub pour envoyer des messages appareil-à-cloud à différents points de terminaison*](../iot-hub/iot-hub-devguide-messages-d2c.md). La section *Événements autres que les événements de télémétrie* explique que vous pouvez utiliser les **événements de cycle de vie de l’appareil** comme source de données pour l’itinéraire.

Les étapes à suivre pour effectuer cette configuration sont les suivantes :
1. Créez un point de terminaison Event Hub IoT Hub personnalisé. Ce point de terminaison doit cibler l’instance Event Hub que vous avez créée dans la section [*Créer un Event Hub*](#create-an-event-hub).
2. Ajoutez un itinéraire *Événements de cycle de vie d’appareil*. Utilisez le point de terminaison créé à l’étape précédente. Vous pouvez limiter les événements de cycle de vie des appareils pour qu’ils envoient uniquement les événements de suppression en ajoutant la requête de routage `opType='deleteDeviceIdentity'`.
    :::image type="content" source="media/how-to-provision-using-dps/lifecycle-route.png" alt-text="Ajouter un itinéraire":::

Une fois que vous avez parcouru ce processus, tout est configuré pour mettre les appareils hors service de bout en bout.

### <a name="validate"></a>Valider

Pour déclencher le processus de mise hors service, vous devez supprimer manuellement l’appareil d’IoT Hub.

Dans la [première moitié de cet article](#auto-provision-device-using-device-provisioning-service), vous avez créé un appareil dans IoT Hub et sa représentation numérique correspondante. 

À présent, accédez à l’IoT Hub et supprimez cet appareil (vous pouvez le faire avec une [commande Azure CLI](/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest&preserve-view=true#ext-azure-cli-iot-ext-az-iot-hub-device-identity-delete) ou dans le [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Devices%2FIotHubs)). 

L’appareil est automatiquement supprimé d’Azure Digital Twins. 

Utilisez la commande [Azure Digital Twins CLI](how-to-use-cli.md) suivante pour vérifier que la représentation de l’appareil dans l’instance Azure Digital Twins a été supprimée.

```azurecli-interactive
az dt twin show -n <Digital Twins instance name> --twin-id <Device Registration ID>"
```

Vous devez voir que la représentation de l’appareil est introuvable dans l’instance Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-dps/show-retired-twin.png" alt-text="Fenêtre de commande signalant que la représentation est introuvable":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources créées dans cet article, effectuez les étapes suivantes pour les supprimer.

Dans Azure Cloud Shell ou Azure CLI en local, vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Sont ainsi supprimés le groupe de ressources ; l’instance Azure Digital Twins ; le hub IoT et l’inscription de l’appareil hub ; la rubrique Event Grid et les abonnements associés ; l’espace de noms Event Hub et les deux applications Azure Functions, y compris les ressources associées telles que le stockage.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ensuite, supprimez de votre ordinateur local le dossier d’exemple de projet que vous avez téléchargé.

## <a name="next-steps"></a>Étapes suivantes

Les jumeaux numériques créés pour les appareils sont stockés sous la forme d’une hiérarchie plate dans Azure Digital Twins, mais ils peuvent être enrichis avec des informations de modèle et une hiérarchie à plusieurs niveaux pour l’organisation. Pour en savoir plus sur ce concept, lisez :

* [*Concepts : Jumeaux numériques et graphe de jumeaux*](concepts-twins-graph.md)

Vous pouvez écrire une logique personnalisée pour fournir automatiquement ces informations à l’aide des données de modèle et de graphique déjà stockées dans Azure Digital Twins. Pour en savoir plus sur la gestion, la mise à niveau et la récupération d’informations à partir du graphique de jumeaux, consultez ce qui suit :

* [*Guide pratique : Gérer un jumeau numérique*](how-to-manage-twin.md)
* [*Guide pratique : Interroger le graphe de jumeaux*](how-to-query-graph.md)