---
title: Gérer automatiquement des appareils à l’aide du service Device Provisioning
titleSuffix: Azure Digital Twins
description: Découvrez comment configurer un processus automatisé pour approvisionner et mettre hors service des appareils IoT dans Azure Digital Twins à l’aide du service Device Provisioning (DPS).
author: baanders
ms.author: baanders
ms.date: 3/21/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 7078c136ebb072b3834af07e69fbd7d3c3d82a2b
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771654"
---
# <a name="auto-manage-devices-in-azure-digital-twins-using-device-provisioning-service-dps"></a>Gérer automatiquement les appareils dans Azure Digital représentations à l’aide du service Device Provisioning (DPS)

Dans cet article, vous allez apprendre à intégrer Azure Digital Twins avec [le service Device Provisioning (DPS)](../iot-dps/about-iot-dps.md).

La solution décrite dans cet article vous permet d’automatiser le processus d’**_approvisionnement_** et de **_mise hors service_** d’appareils IoT Hub dans Azure Digital Twins, à l’aide du service Device Provisioning. 

Pour plus d’informations sur les étapes d’_approvisionnement_ et de _mise hors service_ et mieux comprendre l’ensemble des étapes générales de la gestion des appareils qui sont communes à tous les projets IoT d’entreprise, consultez la section [Cycle de vie des appareils](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) de la documentation sur la gestion des appareils d’IoT Hub.

## <a name="prerequisites"></a>Prérequis

Avant de pouvoir configurer l’approvisionnement, vous devez configurer les ressources suivantes :
* une **instance d’Azure Digital Twins**. Suivez les instructions données dans [Configurer une instance et l’authentification](how-to-set-up-instance-portal.md) pour créer une instance Azure Digital Twins. Collectez le **_Nom d’hôte_** de l’instance dans le portail Azure ([instructions](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).
* un **IoT Hub**. Pour obtenir des instructions, consultez la section Créer un IoT Hub de [ce démarrage rapide d’IoT Hub](../iot-hub/quickstart-send-telemetry-cli.md).
* une  [fonction Azure](../azure-functions/functions-overview.md) qui met à jour les informations du jumeau numérique sur la base des données d’IoT Hub. Suivez les instructions données dans [Ingérer des données IoT Hub](how-to-ingest-iot-hub-data.md) pour créer cette fonction Azure. Récupérez le **_nom_** de la fonction pour l’utiliser dans cet article.

Cet exemple utilise également un **simulateur d’appareil** qui comprend l’approvisionnement à l’aide du service Device Provisioning. Le simulateur d’appareil se trouve ici : [Exemple d’intégration d’Azure Digital Twins et d’IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/). Pour obtenir l’exemple de projet sur votre machine, suivez le lien vers l’exemple, puis sélectionnez le bouton **Parcourir le code** sous le titre. Vous accédez alors au référentiel GitHub d’exemples, que vous pouvez télécharger au format .zip en sélectionnant le bouton **Code**, puis **Télécharger le ZIP**. 

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png" alt-text="Capture d’écran du dépôt digital-twins-iothub-integration sur GitHub, mettant en évidence les étapes permettant de le télécharger sous la forme d’un fichier zip." lightbox="media/how-to-provision-using-device-provisioning-service/download-repo-zip.png":::

Décompressez le dossier téléchargé.

[Node.js](https://nodejs.org/download) doit être installé sur votre machine. Le simulateur d’appareil est basé sur **Node.js**, version 10.0.x ou ultérieure.

## <a name="solution-architecture"></a>Architecture de solution

Cette solution comprend les étapes de provisionnement et de mise hors service d’un appareil dans Azure Digital Twins à l’aide du service de provisionnement d’appareil.

Pour allouer des appareils dans la solution, les données circulent entre un appareil à thermostat et le service de provisionnement d’appareil. Les données passent ensuite du service de provisionnement d’appareil à IoT Hub et à Azure Digital Twins via une fonction Azure.

Pour mettre hors service un appareil, les données issues d’une suppression manuelle d’appareil sont transmises à Azure Digital Twins via IoT Hub, Event Hubs et une fonction Azure.

L’image ci-dessous illustre cette architecture.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/flows.png" alt-text="Diagramme d’un appareil et de plusieurs services Azure dans un scénario de bout en bout illustrant le flux de données." lightbox="media/how-to-provision-using-device-provisioning-service/flows.png":::

Cet article est divisé en deux sections, chacune axée sur une partie de cette architecture complète :
* [Provisionner automatiquement un appareil à l’aide du service Device Provisioning](#auto-provision-device-using-device-provisioning-service)
* [Mettre hors service automatiquement un appareil à l’aide d’événements de cycle de vie IoT Hub](#auto-retire-device-using-iot-hub-lifecycle-events)

## <a name="auto-provision-device-using-device-provisioning-service"></a>Provisionner automatiquement un appareil à l’aide du service Device Provisioning

Dans cette section, vous allez attacher le service Device Provisioning à Azure Digital Twins pour approvisionner automatiquement des appareils via le chemin d’accès ci-dessous. Ce diagramme est un extrait de l’architecture complète présentée [plus tôt](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/provision.png" alt-text="Diagramme du flux de provisionnement – extrait du diagramme d’architecture de solution qui suit les données d’un thermostat vers Azure Digital Twins." lightbox="media/how-to-provision-using-device-provisioning-service/provision.png":::

Voici une description du flux de processus :
1. L’appareil contacte le point de terminaison DPS, en transmettant les informations d’identification pour prouver son identité.
2. Le DPS valide l’identité de l’appareil en validant l’ID d’inscription et la clé par rapport à la liste d’inscriptions, et appelle une [Azure Function](../azure-functions/functions-overview.md) pour effectuer l’allocation.
3. La fonction Azure crée une nouvelle [représentation](concepts-twins-graph.md) dans Azure Digital Twins pour l’appareil. Le jumeau numérique aura le même nom que l’**ID d’inscription** de l’appareil.
4. DPS inscrit l’appareil sur un hub IoT et renseigne l’état souhaité du jumeau de l’appareil.
5. IoT Hub renvoie les informations d’ID d’appareil et les informations de connexion IoT Hub à l’appareil. L’appareil peut maintenant se connecter à IoT Hub.

Les sections suivantes décrivent les étapes à suivre pour configurer ce flux d’approvisionnement automatique d’appareil.

### <a name="create-a-device-provisioning-service"></a>Créer un service Device Provisioning

Lorsqu’un nouvel appareil est configuré à l’aide du service Device Provisioning, une nouvelle représentation de cet appareil peut être créée dans Azure Digital Twins avec le même nom que l’ID d’inscription.

Créez une instance de service Device Provisioning, qui sera utilisée pour approvisionner des appareils IoT. Vous pouvez suivre les instructions pour Azure CLI ci-dessous, ou utiliser le portail Azure comme expliqué dans [Configurer le service IoT Hub Device Provisioning avec le portail Azure](../iot-dps/quick-setup-auto-provision.md).

La commande Azure CLI suivante crée un service Device Provisioning. Vous devez spécifier un nom de service de provisionnement des appareils, un groupe de ressources et une région. Pour connaître les régions qui prennent en charge le service de provisionnement des appareils, consultez [Produits Azure disponibles par région](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub).
La commande peut être exécutée dans [Cloud Shell](https://shell.azure.com) ou localement si [Azure CLI est installé sur votre ordinateur](/cli/azure/install-azure-cli).

```azurecli-interactive
az iot dps create --name <Device-Provisioning-Service-name> --resource-group <resource-group-name> --location <region>
```

### <a name="add-a-function-to-use-with-device-provisioning-service"></a>Ajouter une fonction à utiliser avec le service de provisionnement des appareils

Dans votre projet d’application de fonction que vous avez créé dans la [section Prérequis](#prerequisites), vous allez créer une nouvelle fonction à utiliser avec le service de provisionnement d’appareil. Cette fonction sera utilisée par le service Device Provisioning dans une [stratégie d’allocation personnalisée](../iot-dps/how-to-use-custom-allocation-policies.md) pour approvisionner un nouvel appareil.

Commencez par ouvrir le projet d’application de fonction dans Visual Studio sur votre ordinateur et suivez les étapes ci-dessous.

1. Créez d’abord une fonction de type *déclencheur HTTP* dans le projet d’application de fonction dans Visual Studio. Pour obtenir des instructions sur la façon de créer cette fonction, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Ajoutez un nouveau package NuGet au projet : [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Vous devrez peut-être également ajouter d’autres packages à votre projet, si les packages utilisés dans le code ne font pas déjà partie du projet.

3. Dans le fichier de code de fonction nouvellement créé, collez le code suivant, renommez la fonction *DpsAdtAllocationFunc.cs*, et enregistrez le fichier.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DpsAdtAllocationFunc.cs":::

4. Publiez le projet avec la fonction *DpsAdtAllocationFunc.cs* sur l’application de fonction dans Azure. Pour obtenir des instructions sur la façon de publier le projet, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Lors de la création de l’application de fonction pour la première fois dans la [section Prérequis](#prerequisites), vous avez peut-être déjà attribué un rôle d’accès à la fonction et configuré les paramètres d’application pour l’accès à votre instance Azure Digital Twins. Ces opérations doivent être effectuées une seule fois pour l’ensemble de l’application de fonction. Vérifiez qu’elles ont été effectuées dans votre application avant de continuer. Pour obtenir des instructions, consultez la section [Configurer l’application publiée](how-to-authenticate-client.md#configure-published-app) dans l’article *Écrire le code d’authentification de l’application*.

### <a name="create-device-provisioning-enrollment"></a>Créer une inscription Device Provisioning

Ensuite, vous devez créer une inscription dans le service Device Provisioning à l’aide d’une **fonction d’allocation personnalisée**. Pour créer une inscription, suivez les instructions pour effectuer cette opération dans la section [Créer l’inscription](../iot-dps/how-to-use-custom-allocation-policies.md#create-the-enrollment) de l’article sur les stratégies d’allocation personnalisées de la documentation du service de provisionnement des appareils.

Pendant cette procédure, veillez à sélectionner les options suivantes pour lier l’inscription à la fonction que vous avez créé.

* **Sélectionner le mode d’affectation des appareils aux hubs** : Personnalisé (Utiliser Azure Functions).
* **Sélectionner les hubs IoT auxquels ce groupe peut être affecté :** Choisissez le nom de votre IoT Hub ou sélectionnez le bouton *Lier un nouveau hub IoT*, puis choisissez votre hub IoT dans la liste déroulante.

Ensuite, choisissez le bouton *Sélectionner une nouvelle fonction* pour lier votre application de fonction au groupe d’inscription. Renseignez ensuite les valeurs suivantes :

* **Abonnement** : votre abonnement Azure est rempli automatiquement. Assurez-vous qu’il s’agit de l’abonnement approprié.
* **Application de fonction** : nom de votre application de fonction.
* **Fonction** : choisissez DpsAdtAllocationFunc.

Enregistrez ces informations.                  

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png" alt-text="Capture d’écran de la fenêtre de détails du groupe d’inscriptions personnalisées dans le portail Azure." lightbox="media/how-to-provision-using-device-provisioning-service/link-enrollment-group-to-iot-hub-and-function-app.png":::

Une fois l’inscription créée, la **clé principale** de l’inscription sera utilisée ultérieurement pour configurer le simulateur d’appareil pour cet article.

### <a name="set-up-the-device-simulator"></a>Configurer le simulateur d’appareil

Cet exemple utilise un simulateur d’appareil qui comprend l’approvisionnement à l’aide du service Device Provisioning. Le simulateur d’appareil se trouve dans l’[exemple d’intégration Azure Digital Twins et IoT Hub](/samples/azure-samples/digital-twins-iothub-integration/adt-iothub-provision-sample/) que vous avez téléchargé dans la [section Prérequis](#prerequisites).

#### <a name="upload-the-model"></a>Télécharger le modèle

Le simulateur d’appareil est un appareil de type thermostat qui utilise le modèle avec cet ID : `dtmi:contosocom:DigitalTwins:Thermostat;1`. Vous devez charger ce modèle dans Azure Digital Twins avant de pouvoir créer une représentation de ce type pour l’appareil.

[!INCLUDE [digital-twins-thermostat-model-upload.md](../../includes/digital-twins-thermostat-model-upload.md)]

Pour plus d’informations sur les modèles, consultez [Gérer les modèles](how-to-manage-model.md#upload-models).

#### <a name="configure-and-run-the-simulator"></a>Configurer et exécuter le simulateur

Dans votre fenêtre de commande, accédez à l’exemple téléchargé *Intégration Azure Digital Twins et IoT Hub* que vous avez décompressé précédemment, puis au répertoire *device-simulator*. Ensuite, installez les dépendances pour le projet à l’aide de la commande suivante :

```cmd
npm install
```

Ensuite, dans le répertoire de votre simulateur d’appareil, copiez le fichier .env.template dans un nouveau fichier appelé .env et rassemblez les valeurs suivantes pour renseigner les paramètres :

* PROVISIONING_IDSCOPE : pour obtenir cette valeur, accédez à votre service de provisionnement des appareils dans le [portail Azure](https://portal.azure.com/), puis sélectionnez *Vue d’ensemble* dans les options de menu et recherchez l’étendue le champ *Étendue de l’ID*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/id-scope.png" alt-text="Capture d’écran de la vue du portail Azure de la page de présentation du provisionnement des appareils, mettant en évidence la valeur d’étendue de l’ID" lightbox="media/how-to-provision-using-device-provisioning-service/id-scope.png":::

* PROVISIONING_REGISTRATION_ID : vous pouvez choisir un ID d’inscription pour votre appareil.
* ADT_MODEL_ID : `dtmi:contosocom:DigitalTwins:Thermostat;1`
* PROVISIONING_SYMMETRIC_KEY : cette variable d'environnement est la clé primaire pour l’inscription que vous avez configurée précédemment. Pour obtenir à nouveau cette valeur, accédez à votre service de provisionnement des appareils dans le portail Azure, sélectionnez *Gérer les inscriptions*, puis sélectionnez le groupe d’inscription que vous avez créé précédemment et copiez la *Clé primaire*.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png" alt-text="Capture d’écran de la vue du portail Azure de la page de gestion des inscriptions du service de provisionnement des appareils, mettant en évidence la valeur de la clé primaire SAS" lightbox="media/how-to-provision-using-device-provisioning-service/sas-primary-key.png":::

À présent, utilisez les valeurs ci-dessus pour mettre à jour les paramètres du fichier .env.

```cmd
PROVISIONING_HOST = "global.azure-devices-provisioning.net"
PROVISIONING_IDSCOPE = "<Device-Provisioning-Service-Scope-ID>"
PROVISIONING_REGISTRATION_ID = "<Device-Registration-ID>"
ADT_MODEL_ID = "dtmi:contosocom:DigitalTwins:Thermostat;1"
PROVISIONING_SYMMETRIC_KEY = "<Device-Provisioning-Service-enrollment-primary-SAS-key>"
```

Enregistrez et fermez le fichier.

### <a name="start-running-the-device-simulator"></a>Lancer l’exécution du simulateur d’appareil

Toujours dans le répertoire *device-simulator*, dans votre fenêtre de commande, démarrez le simulateur d’appareil à l’aide de la commande suivante :

```cmd
node .\adt_custom_register.js
```

Vous devriez voir que l’appareil est enregistré et connecté à IoT Hub, puis qu’il commence à envoyer des messages.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/output.png" alt-text="Capture d’écran de la fenêtre Commande montrant les messages d’envoi et d’inscription d’appareil" lightbox="media/how-to-provision-using-device-provisioning-service/output.png":::

### <a name="validate"></a>Valider

En raison du flux que vous avez configuré dans cet article, l’appareil est automatiquement inscrit dans Azure Digital Twins. Utilisez la commande [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) suivante pour trouver la représentation de l’appareil dans l’instance Azure Digital Twins que vous avez créée.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

La représentation de l’appareil doit se trouver dans l’instance Azure Digital Twins.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png" alt-text="Capture d’écran de la fenêtre de commande montrant un jumeau numérique nouvellement créé." lightbox="media/how-to-provision-using-device-provisioning-service/show-provisioned-twin.png":::

## <a name="auto-retire-device-using-iot-hub-lifecycle-events"></a>Mettre hors service automatiquement un appareil à l’aide d’événements de cycle de vie IoT Hub

Dans cette section, vous allez attacher des événements de cycle de vie IoT Hub à Azure Digital Twins pour mettre hors service automatiquement les appareils en suivant le procédé ci-dessous. Ce diagramme est un extrait de l’architecture complète présentée [plus tôt](#solution-architecture).

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/retire.png" alt-text="Diagramme du flux de mise hors service d’appareil – extrait du diagramme d’architecture de la solution, suivant les données issues d’une suppression d’appareil vers Azure Digital Twins" lightbox="media/how-to-provision-using-device-provisioning-service/retire.png":::

Voici une description du flux de processus :
1. Un processus externe ou manuel déclenche la suppression d’un appareil dans IoT Hub.
2. IoT Hub supprime l’appareil et génère un événement de [cycle de vie de l’appareil](../iot-hub/iot-hub-device-management-overview.md#device-lifecycle) qui est routé vers un [Event Hub](../event-hubs/event-hubs-about.md).
3. Une fonction Azure supprime la représentation de l’appareil dans Azure Digital Twins.

Les sections suivantes décrivent les étapes à suivre pour configurer ce flux de mise hors service automatique d’appareil.

### <a name="create-an-event-hub"></a>Créer un hub d’événements

Ensuite, vous allez créer un [Event Hub](../event-hubs/event-hubs-about.md) Azure pour recevoir les événements du cycle de vie d’IoT Hub. 

Suivez les étapes décrites dans le guide de démarrage rapide [Créer un Event Hub](../event-hubs/event-hubs-create.md). Nommez votre Event Hub *lifecycleevents*. Vous utiliserez ce nom d’Event Hub lorsque vous configurerez l’itinéraire IoT Hub et une fonction Azure dans les sections suivantes.

La capture d’écran ci-dessous illustre la création du Event Hub.
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png" alt-text="Capture d’écran de la fenêtre du portail Azure montrant comment créer un hub d’événements avec le nom lifecycleevents" lightbox="media/how-to-provision-using-device-provisioning-service/create-event-hub-lifecycle-events.png":::

#### <a name="create-sas-policy-for-your-event-hub"></a>Créer une stratégie SAS pour votre Event Hub

Ensuite, vous devez créer une [stratégie de signature d’accès partagé (SAS)](../event-hubs/authorize-access-shared-access-signature.md) pour configurer Event Hub avec votre application de fonction.
Pour créer la stratégie SAP :
1. Accédez au Event Hub que vous venez de créer dans le portail Azure et sélectionnez **Stratégies d’accès partagé** dans les options de menu de sur la gauche.
2. Sélectionnez **Ajouter**. Dans la fenêtre *Ajouter une stratégie SAS* qui s’ouvre, entrez le nom de stratégie de votre choix et cochez la case *Écouter*.
3. Sélectionnez **Create** (Créer).
    
:::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png" alt-text="Capture d’écran du portail Azure montrant comment ajouter une stratégie SAS de hub d’événements" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-sas-policy.png":::

#### <a name="configure-event-hub-with-function-app"></a>Configurer Event Hub avec l’application de fonction

Ensuite, configurez l’application de fonction Azure que vous avez configurée dans la [section Prérequis](#prerequisites) pour utiliser votre nouveau hub d’événements. Vous configurerez cette fonction en définissant une variable d’environnement à l’intérieur de l’application de fonction avec la chaîne de connexion de l’Event Hub.

1. Ouvrez la stratégie que vous avez créée et copiez la valeur de **Chaîne de connexion - clé primaire**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png" alt-text="Capture d’écran du portail Azure montrant comment copier la clé primaire de la chaîne de connexion" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-sas-policy-connection-string.png":::

2. Ajoutez la chaîne de connexion en tant que variable dans les paramètres de l’application de fonction à l’aide de la commande Azure CLI suivante. La commande peut être exécutée dans [Cloud Shell](https://shell.azure.com) ou localement si [Azure CLI est installé sur votre ordinateur](/cli/azure/install-azure-cli).

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EVENTHUB_CONNECTIONSTRING=<Event-Hubs-SAS-connection-string-Listen>" --resource-group <resource-group> --name <your-function-app-name>
    ```

### <a name="add-a-function-to-retire-with-iot-hub-lifecycle-events"></a>Ajouter une fonction pour mettre hors service avec les événements de cycle de vie IoT Hub

Dans le projet d’application de fonction que vous avez créé dans la [section Prérequis](#prerequisites), vous allez créer une nouvelle fonction pour mettre hors service un appareil existant à l’aide d’événements de cycle de vie IoT Hub.

Pour plus d’informations sur les événements de cycle de vie, consultez [Événements IoT Hub autres que les événements de télémétrie](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Pour plus d’informations sur l’utilisation d’Event Hubs avec Azure Functions, consultez [Déclencheur Azure Event Hubs pour Azure Functions](../azure-functions/functions-bindings-event-hubs-trigger.md).

Commencez par ouvrir le projet d’application de fonction dans Visual Studio sur votre ordinateur et suivez les étapes ci-dessous.

1. Créez d’abord une fonction de type *déclencheur Event Hub* dans le projet d’application de fonction dans Visual Studio. Pour obtenir des instructions sur la façon de créer cette fonction, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#add-a-function-to-your-project).

2. Ajoutez un nouveau package NuGet au projet : [Microsoft.Azure.Devices.Provisioning.Service](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/). Vous devrez peut-être également ajouter d’autres packages à votre projet, si les packages utilisés dans le code ne font pas déjà partie du projet.

3. Dans le fichier de code de fonction nouvellement créé, collez le code suivant, renommez la fonction en *DeleteDeviceInTwinFunc.cs*, puis enregistrez le fichier.

    :::code language="csharp" source="~/digital-twins-docs-samples-dps/functions/DeleteDeviceInTwinFunc.cs":::

4. Publiez le projet avec la fonction *DeleteDeviceInTwinFunc.cs* sur une application de fonction dans Azure. Pour obtenir des instructions sur la façon de publier le projet, consultez [Développer des fonctions Azure Functions à l’aide de Visual Studio](../azure-functions/functions-develop-vs.md#publish-to-azure).

> [!IMPORTANT]
> Lors de la création de l’application de fonction pour la première fois dans la [section Prérequis](#prerequisites), vous avez peut-être déjà attribué un rôle d’accès à la fonction et configuré les paramètres d’application pour l’accès à votre instance Azure Digital Twins. Ces opérations doivent être effectuées une seule fois pour l’ensemble de l’application de fonction. Vérifiez qu’elles ont été effectuées dans votre application avant de continuer. Pour obtenir des instructions, consultez la section [Configurer l’application publiée](how-to-authenticate-client.md#configure-published-app) dans l’article *Écrire le code d’authentification de l’application*.

### <a name="create-an-iot-hub-route-for-lifecycle-events"></a>Créer un itinéraire IoT Hub pour les événements de cycle de vie

Vous devez maintenant configurer un itinéraire IoT Hub pour acheminer les événements de cycle de vie des appareils. Dans ce cas, vous écouterez spécifiquement les événements de suppression d’appareil, identifiés par `if (opType == "deleteDeviceIdentity")`. Cet événement déclenchera la suppression de sa représentation numérique, achevant ainsi le processus de retrait d'un appareil et de sa représentation numérique.

Tout d’abord, vous devez créer un point de terminaison Event Hub dans votre IoT Hub. Ensuite, vous allez ajouter un itinéraire dans IoT Hub pour envoyer des événements de cycle de vie à ce point de terminaison Event Hub.
Pour créer un point de terminaison Event Hub, procédez comme suit :

1. Dans le [portail Azure](https://portal.azure.com/), accédez au hub IoT que vous avez créé dans la [section Prérequis](#prerequisites) et sélectionnez **Routage des messages** dans les options de menu de gauche.
2. Sélectionnez l’onglet **Points de terminaison personnalisés**.
3. Sélectionnez **+ Ajouter** et choisissez **Event Hub** pour ajouter un point de terminaison de type Event Hub.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png" alt-text="Capture d’écran de la fenêtre Visual Studio montrant comment ajouter un point de terminaison personnalisé Event Hub" lightbox="media/how-to-provision-using-device-provisioning-service/event-hub-custom-endpoint.png":::

4. Dans la fenêtre *Ajouter un point de terminaison Event Hub* qui s’ouvre, choisissez les valeurs suivantes :
    * **Nom du point de terminaison** : choisissez un nom de point de terminaison.
    * **Espace de noms Event Hub** : sélectionnez votre espace de noms Event Hub dans la liste déroulante.
    * **Instance Event Hub** : choisissez le nom de l’Event Hub que vous avez créé à l’étape précédente.
5. Sélectionnez **Create** (Créer). Laissez cette fenêtre ouverte pour ajouter un itinéraire à l’étape suivante.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png" alt-text="Capture d’écran de la fenêtre Visual Studio montrant comment ajouter un point de terminaison Event Hub" lightbox="media/how-to-provision-using-device-provisioning-service/add-event-hub-endpoint.png":::

Ensuite, vous allez ajouter un itinéraire qui se connecte au point de terminaison que vous avez créé à l’étape ci-dessus, avec une requête de routage qui envoie les événements de suppression. Procédez comme suit pour créer une route :

1. Accédez à l’onglet *Itinéraires* et sélectionnez **Ajouter** pour ajouter un itinéraire.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/add-message-route.png" alt-text="Capture d’écran de la fenêtre Visual Studio montrant comment ajouter une route pour envoyer des événements" lightbox="media/how-to-provision-using-device-provisioning-service/add-message-route.png":::

2. Dans la page *Ajouter un itinéraire* qui s’ouvre, choisissez les valeurs suivantes :

   * **Nom** : choisissez un nom pour votre itinéraire. 
   * **Point de terminaison** : choisissez le point de terminaison Event Hub que vous avez créé précédemment dans la liste déroulante.
   * **Source de données** : choisissez les *Événements de cycle de vie des appareils*.
   * **Requête de routage** : Entrez `opType='deleteDeviceIdentity'`. Cette requête limite les événements de cycle de vie de l’appareil pour envoyer uniquement les événements de suppression.

3. Sélectionnez **Enregistrer**.

    :::image type="content" source="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png" alt-text="Capture d’écran de la fenêtre du portail Azure montrant comment ajouter une route pour envoyer des événements de cycle de vie" lightbox="media/how-to-provision-using-device-provisioning-service/lifecycle-route.png":::

Une fois que vous avez parcouru ce processus, tout est configuré pour mettre les appareils hors service de bout en bout.

### <a name="validate"></a>Valider

Pour déclencher le processus de mise hors service, vous devez supprimer manuellement l’appareil d’IoT Hub.

Vous pouvez supprimer manuellement l’appareil IoT Hub avec une [commande Azure CLI](/cli/azure/iot/hub/module-identity#az_iot_hub_module_identity_delete) ou dans le portail Azure. Effectuez les étapes ci-dessous pour supprimer l’appareil dans le portail Azure :

1. Accédez à votre IoT Hub, puis sélectionnez **Appareils IoT** dans les options de menu sur la gauche. 
2. Vous verrez un appareil avec l’ID d’inscription de l’appareil que vous avez choisi dans la [première moitié de cet article](#auto-provision-device-using-device-provisioning-service). Vous pouvez également choisir n’importe quel autre appareil à supprimer, à condition qu’il dispose d’un jumeau numérique dans Azure Digital Twins pour vous permettre de vérifier que le jumeau numérique est automatiquement supprimé une fois l’appareil supprimé.
3. Sélectionnez l’appareil et choisissez **Supprimer**.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png" alt-text="Capture d’écran du portail Azure montrant comment supprimer un jumeau d’appareil des appareils IoT" lightbox="media/how-to-provision-using-device-provisioning-service/delete-device-twin.png":::

Plusieurs minutes peuvent être nécessaires pour voir les modifications reflétées dans Azure Digital Twins.

Utilisez la commande [Azure Digital Twins CLI](/cli/azure/dt/twin?view=azure-cli-latest&preserve-view=true#az_dt_twin_show) suivante pour vérifier que la représentation de l’appareil dans l’instance Azure Digital Twins a été supprimée.

```azurecli-interactive
az dt twin show --dt-name <Digital-Twins-instance-name> --twin-id "<Device-Registration-ID>"
```

Vous devez voir que la représentation de l’appareil est introuvable dans l’instance Azure Digital Twins.

:::image type="content" source="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png" alt-text="Capture d’écran de la fenêtre Commande montrant que le jumeau est désormais introuvable" lightbox="media/how-to-provision-using-device-provisioning-service/show-retired-twin.png":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Si vous n’avez plus besoin des ressources créées dans cet article, effectuez les étapes suivantes pour les supprimer.

Dans Azure Cloud Shell ou Azure CLI en local, vous pouvez supprimer toutes les ressources Azure d’un groupe de ressources avec la commande [az group delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete). Cette commande supprime le groupe de ressources ; l’instance Azure Digital Twins ; le hub IoT et l’inscription de l’appareil hub ; la rubrique Event Grid et les abonnements associés ; l’espace de noms Event Hub et les deux applications Azure Functions, y compris les ressources associées telles que le stockage.

> [!IMPORTANT]
> La suppression d’un groupe de ressources est irréversible. Le groupe de ressources et toutes les ressources qu’il contient sont supprimés définitivement. Veillez à ne pas supprimer accidentellement des ressources ou un groupe de ressources incorrects. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Ensuite, supprimez de votre ordinateur local le dossier d’exemple de projet que vous avez téléchargé.

## <a name="next-steps"></a>Étapes suivantes

Les jumeaux numériques créés pour les appareils sont stockés sous la forme d’une hiérarchie plate dans Azure Digital Twins, mais ils peuvent être enrichis avec des informations de modèle et une hiérarchie à plusieurs niveaux pour l’organisation. Pour en savoir plus sur ce concept, lisez :

* [Jumeaux numériques et graphe de jumeaux](concepts-twins-graph.md)

Pour plus d’informations sur l’utilisation des requêtes HTTP avec les fonctions Azure, consultez:

* [Déclencheur de requête HTTP Azure pour Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md)

Vous pouvez écrire une logique personnalisée pour fournir automatiquement ces informations à l’aide des données de modèle et de graphique déjà stockées dans Azure Digital Twins. Pour en savoir plus sur la gestion, la mise à niveau et la récupération d’informations à partir du graphique de jumeaux, consultez les guides suivants :

* [Gérer un jumeau numérique](how-to-manage-twin.md)
* [Interroger le graphe de jumeaux](how-to-query-graph.md)