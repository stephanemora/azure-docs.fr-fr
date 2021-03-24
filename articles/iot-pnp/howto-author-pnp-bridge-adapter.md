---
title: Comment créer un adaptateur pour le pont IoT Plug-and-Play | Microsoft Docs
description: Identifiez les composants de l’adaptateur du pont IoT Plug-and-Play. Découvrez comment étendre le pont en écrivant votre propre adaptateur.
author: usivagna
ms.author: ugans
ms.date: 1/20/2021
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 9a7028dfaeb94e87366de7acfa8cebc4c2f4c767
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98746808"
---
# <a name="extend-the-iot-plug-and-play-bridge"></a>Étendre le pont IoT Plug-and-Play
Le [pont IoT Plug-and-Play](concepts-iot-pnp-bridge.md#iot-plug-and-play-bridge-architecture) vous permet de connecter les appareils existants attachés à une passerelle à votre hub IoT. Ce pont peut être utilisé pour mapper les interfaces IoT Plug-and-Play aux appareils attachés. Une interface IoT Plug-and-Play définit la télémétrie envoyée par un appareil, les propriétés synchronisées entre l’appareil et le cloud, ainsi que les commandes auxquelles l’appareil répond. Vous pouvez installer et configurer l’application de pont open source sur des passerelles Windows ou Linux. En outre, le pont peut être exécuté en tant que module du runtime Azure IoT Edge.

Cet article explique comment dans le détail :

- Étendre le pont IoT Plug-and-Play avec un adaptateur.
- Implémenter les rappels courants pour un adaptateur de pont.

Pour obtenir un exemple simple d’utilisation du pont, consultez [Comment connecter l’exemple de pont Plug-and-Play qui s’exécute sur Linux ou Windows à un hub IoT](howto-use-iot-pnp-bridge.md).

L’aide et les exemples de cet article supposent que vous maîtrisez les bases de [Azure Digital Twins ](../digital-twins/overview.md) et [IoT Plug-and-Play](overview-iot-plug-and-play.md). En outre, cet article suppose que vous savez comment [créer et déployer un pont IoT Plug-and-Play](howto-build-deploy-extend-pnp-bridge.md).

## <a name="design-guide-to-extend-the-iot-plug-and-play-bridge-with-an-adapter"></a>Guide de conception pour l’extension du pont IoT Plug-and-Play Bridge avec un adaptateur

Pour étendre les capacités du pont, vous pouvez créer vos propres adaptateurs de pont.

Le pont utilise des adaptateurs pour effectuer les opérations suivantes :

- établir une connexion entre un appareil et le cloud ;
- activer le flux de données entre un appareil et le cloud ;
- activer la gestion des périphériques à partir du cloud.

Chaque adaptateur de pont doit :

- Créez une interface de jumeau numérique.
- Utilisez l’interface pour lier les fonctionnalités côté appareil aux fonctionnalités basées sur le cloud, telles que les données de télémétrie, les propriétés et les commandes.
- Établissez des communications de contrôle et de données avec le matériel ou le microprogramme de l’appareil.

Chaque adaptateur de pont interagit avec un type spécifique d’appareil en fonction de la façon dont l’adaptateur se connecte à l’appareil et interagit avec lui. Même si la communication avec un appareil utilise un protocole de liaison, un adaptateur de pont peut avoir plusieurs manières d’interpréter les données de l’appareil. Dans ce scénario, l’adaptateur de pont utilise les informations de l’adaptateur dans le fichier config pour déterminer la *configuration d’interface* que l’adaptateur doit utiliser pour analyser les données.

Pour interagir avec l’appareil, un adaptateur de pont utilise un protocole de communication pris en charge par l’appareil, ainsi que les API fournies par le système d’exploitation sous-jacent ou le fournisseur de l’appareil.

Pour interagir avec le cloud, un adaptateur de pont utilise les API fournies par le kit de développement logiciel (SDK) Azure IoT Device C pour envoyer des données de télémétrie, créer des interfaces de jumeau numérique, envoyer des mises à jour des propriétés et créer des fonctions de rappel pour les mises à jour de propriétés et les commandes.

### <a name="create-a-bridge-adapter"></a>Créer un adaptateur de pont

Le pont attend qu’un adaptateur de pont implémente les API définies dans l’interface [_PNP_ADAPTER](https://github.com/Azure/iot-plug-and-play-bridge/blob/9964f7f9f77ecbf4db3b60960b69af57fd83a871/pnpbridge/src/pnpbridge/inc/pnpadapter_api.h#L296) :

```c
typedef struct _PNP_ADAPTER {
  // Identity of the IoT Plug and Play adapter that is retrieved from the config
  const char* identity;

  PNPBRIDGE_ADAPTER_CREATE createAdapter;
  PNPBRIDGE_COMPONENT_CREATE createPnpComponent;
  PNPBRIDGE_COMPONENT_START startPnpComponent;
  PNPBRIDGE_COMPONENT_STOP stopPnpComponent;
  PNPBRIDGE_COMPONENT_DESTROY destroyPnpComponent;
  PNPBRIDGE_ADAPTER_DESTOY destroyAdapter;
} PNP_ADAPTER, * PPNP_ADAPTER;
```

Dans cette interface :

- `PNPBRIDGE_ADAPTER_CREATE` crée l’adaptateur et définit les ressources de gestion de l’interface. Un adaptateur peut également s’appuyer sur les paramètres de l’adaptateur global pour la création de l’adaptateur. Cette fonction est appelée une fois pour un seul adaptateur.
- `PNPBRIDGE_COMPONENT_CREATE` crée les interfaces du client de jumeau numérique et lie les fonctions de rappel. L’adaptateur initie le canal de communication vers l’appareil. L’adaptateur peut configurer les ressources pour activer le flux des données de télémétrie, mais ne démarre pas l’envoi des données de télémétrie tant que `PNPBRIDGE_COMPONENT_START` n’est pas appelé. Cette fonction est appelée une fois pour chaque composant d’interface dans le fichier config.
- `PNPBRIDGE_COMPONENT_START` est appelé pour permettre à l’adaptateur de pont de commencer à transférer les données de télémétrie de l’appareil vers le client de jumeau numérique. Cette fonction est appelée une fois pour chaque composant d’interface dans le fichier config.
- `PNPBRIDGE_COMPONENT_STOP` arrête le flux des données de télémétrie.
- `PNPBRIDGE_COMPONENT_DESTROY` détruit le client de jumeau numérique et les ressources d’interface associées. Cette fonction est appelée une fois pour chaque composant d’interface dans le fichier config lorsque le pont est détruit ou lorsqu’une erreur irrécupérable se produit.
- `PNPBRIDGE_ADAPTER_DESTROY` nettoie les ressources de l’adaptateur de pont.

### <a name="bridge-core-interaction-with-bridge-adapters"></a>Interaction centrale du pont avec les adaptateurs de pont

La liste suivante décrit ce qui se produit au démarrage du pont :

1. Lorsque le pont démarre, le gestionnaire d’adaptateurs de pont examine chaque composant d’interface défini dans le fichier config et appelle `PNPBRIDGE_ADAPTER_CREATE` sur l’adaptateur approprié. L’adaptateur peut utiliser les paramètres de configuration de l’adaptateur global pour configurer les ressources afin de prendre en charge les différentes *configurations d’interface*.
1. Pour chaque appareil du fichier config, le gestionnaire de pont lance la création de l’interface en appelant `PNPBRIDGE_COMPONENT_CREATE` dans l’adaptateur de pont approprié.
1. L’adaptateur reçoit tous les paramètres de configuration d’adaptateur facultatifs pour le composant d’interface et utilise ces informations pour configurer des connexions à l’appareil.
1. L’adaptateur crée les interfaces du client de jumeau numérique et lie les fonctions de rappel pour les mises à jour de propriétés et les commandes. L’établissement de connexions à l’appareil ne doit pas bloquer le retour des rappels après la création d’une interface de jumeau numérique. La connexion active à l’appareil est indépendante du client de l’interface active que le pont crée. En cas d’échec d’une connexion, l’adaptateur part du principe que l’appareil est inactif. L’adaptateur de pont peut choisir de réessayer d’établir cette connexion.
1. Une fois que le gestionnaire d’adaptateurs de pont a créé tous les composants d’interface spécifiés dans le fichier config, il inscrit toutes les interfaces auprès d’Azure IoT Hub. L’inscription est un appel bloquant et asynchrone. Lorsque l’appel est terminé, il déclenche un rappel dans l’adaptateur de pont, qui peut ensuite commencer à gérer les rappels de propriétés et de commandes à partir du cloud.
1. Le gestionnaire d’adaptateurs de pont appelle ensuite `PNPBRIDGE_INTERFACE_START` sur chaque composant et l’adaptateur de pont commence à fournir les données de télémétrie au client de jumeau numérique.

### <a name="design-guidelines"></a>Instructions de conception

Suivez ces instructions lorsque vous développez un nouvel adaptateur de pont :

- Déterminez quelles sont les fonctionnalités de l’appareil prises en charge et à quoi ressemble la définition d’interface des composants à l’aide de cet adaptateur.
- Déterminez l’interface et les paramètres globaux qui doivent être définis dans le fichier config pour votre adaptateur.
- Identifiez la communication d’appareil de bas niveau requise pour prendre en charge les commandes et les propriétés du composant.
- Déterminez comment l’adaptateur doit analyser les données brutes de l’appareil et les convertir en types de données de télémétrie que la définition d’interface IoT Plug-and-Play spécifie.
- Implémentez l’interface d’adaptateur de pont décrite précédemment.
- Ajoutez le nouvel adaptateur au manifeste de l’adaptateur et générez le pont.

### <a name="enable-a-new-bridge-adapter"></a>Activer un nouvel adaptateur de pont

Pour activer les adaptateurs dans le pont, ajoutez une référence dans [adapter_manifest.c](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/shared/adapter_manifest.c) :

```c
  extern PNP_ADAPTER MyPnpAdapter;
  PPNP_ADAPTER PNP_ADAPTER_MANIFEST[] = {
    .
    .
    &MyPnpAdapter
  }
```

> [!IMPORTANT]
> Les rappels d’adaptateur de pont sont invoqués de manière séquentielle. Un adaptateur ne doit pas bloquer un rappel, car cela empêche le noyau du pont de progresser.

### <a name="sample-camera-adapter"></a>Exemple d’adaptateur de caméra

Le fichier [Lisez-moi de l’adaptateur de caméra](https://github.com/Azure/iot-plug-and-play-bridge/blob/master/pnpbridge/src/adapters/src/Camera/readme.md) fournit un exemple d’adaptateur de caméra que vous pouvez activer.

## <a name="code-examples-for-common-adapter-scenarioscallbacks"></a>Exemples de code pour les scénarios d’adaptateurs courants/les rappels

La section suivante fournit des détails sur la façon dont un adaptateur pour le pont implémenterait les rappels pour un certain nombre de scénarios et d’utilisations courants. Cette section couvre les rappels suivants :
- [Recevoir la mise à jour des propriétés (cloud vers appareil)](#receive-property-update-cloud-to-device)
- [Signaler une mise à jour de propriété (appareil vers cloud)](#report-a-property-update-device-to-cloud)
- [Envoyer des données de télémétrie (appareil vers cloud)](#send-telemetry-device-to-cloud)
- [Recevoir le rappel de mise à jour de commande à partir du cloud et le traiter côté appareil (cloud vers appareil)](#receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device)
- [Répondre à la mise à jour des commandes côté appareil (de l’appareil vers le cloud)](#respond-to-command-update-on-the-device-side-device-to-cloud)

Les exemples ci-dessous sont basés sur [l’adaptateur exemple de capteur d’environnement](https://github.com/Azure/iot-plug-and-play-bridge/tree/master/pnpbridge/src/adapters/samples/environmental_sensor).

### <a name="receive-property-update-cloud-to-device"></a>Recevoir la mise à jour des propriétés (cloud vers appareil)
La première étape consiste à inscrire une fonction de rappel :

```c
PnpComponentHandleSetPropertyUpdateCallback(BridgeComponentHandle, EnvironmentSensor_ProcessPropertyUpdate);
```
L’étape suivante consiste à implémenter la fonction de rappel pour lire la mise à jour de propriété sur l’appareil :

```c
void EnvironmentSensor_ProcessPropertyUpdate(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    void* userContextCallback
)
{
  // User context for the callback is set to the IoT Hub client handle, and therefore can be type-cast to the client handle type
    SampleEnvironmentalSensor_ProcessPropertyUpdate(userContextCallback, PropertyName, PropertyValue, version, PnpComponentHandle);
}

// SampleEnvironmentalSensor_ProcessPropertyUpdate receives updated properties from the server.  This implementation
// acts as a simple dispatcher to the functions to perform the actual processing.
void SampleEnvironmentalSensor_ProcessPropertyUpdate(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
  if (strcmp(PropertyName, sampleEnvironmentalSensorPropertyBrightness) == 0)
    {
        SampleEnvironmentalSensor_BrightnessCallback(ClientHandle, PropertyName, PropertyValue, version, PnpComponentHandle);
    }
    else
    {
        // If the property is not implemented by this interface, presently we only record a log message but do not have a mechanism to report back to the service
        LogError("Environmental Sensor Adapter:: Property name <%s> is not associated with this interface", PropertyName);
    }
}

// Process a property update for bright level.
static void SampleEnvironmentalSensor_BrightnessCallback(
    void * ClientHandle,
    const char* PropertyName,
    JSON_Value* PropertyValue,
    int version,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT iothubClientResult;
    STRING_HANDLE jsonToSend = NULL;
    char targetBrightnessString[32];

    LogInfo("Environmental Sensor Adapter:: Brightness property invoked...");

    PENVIRONMENT_SENSOR EnvironmentalSensor = PnpComponentHandleGetContext(PnpComponentHandle);

    if (json_value_get_type(PropertyValue) != JSONNumber)
    {
        LogError("JSON field %s is not a number", PropertyName);
    }
    else if(EnvironmentalSensor == NULL || EnvironmentalSensor->SensorState == NULL)
    {
        LogError("Environmental sensor device context not initialized correctly.");
    }
    else if (SampleEnvironmentalSensor_ValidateBrightness(json_value_get_number(PropertyValue)))
    {
        EnvironmentalSensor->SensorState->brightness = (int) json_value_get_number(PropertyValue);
        if (snprintf(targetBrightnessString, sizeof(targetBrightnessString), 
            g_environmentalSensorBrightnessResponseFormat, EnvironmentalSensor->SensorState->brightness) < 0)
        {
            LogError("Unable to create target brightness string for reporting result");
        }
        else if ((jsonToSend = PnP_CreateReportedPropertyWithStatus(EnvironmentalSensor->SensorState->componentName,
                    PropertyName, targetBrightnessString, PNP_STATUS_SUCCESS, g_environmentalSensorPropertyResponseDescription,
                    version)) == NULL)
        {
            LogError("Unable to build reported property response");
        }
        else
        {
            const char* jsonToSendStr = STRING_c_str(jsonToSend);
            size_t jsonToSendStrLen = strlen(jsonToSendStr);

            if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(ClientHandle, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
                                        SampleEnvironmentalSensor_PropertyCallback,
                                        (void*) &EnvironmentalSensor->SensorState->brightness)) != IOTHUB_CLIENT_OK)
            {
                LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteReportedState for brightness failed, error=%d", iothubClientResult);
            }
            else
            {
                LogInfo("Environmental Sensor Adapter:: Successfully queued Property update for Brightness for component=%s", EnvironmentalSensor->SensorState->componentName);
            }

            STRING_delete(jsonToSend);
        }
    }
}

```

### <a name="report-a-property-update-device-to-cloud"></a>Signaler une mise à jour de propriété (appareil vers cloud)
À tout moment après la création de votre composant, votre appareil peut signaler les propriétés vers le cloud avec l’état : 
```c
// Environmental sensor's read-only property, device state indiciating whether its online or not
//
static const char sampleDeviceStateProperty[] = "state";
static const unsigned char sampleDeviceStateData[] = "true";
static const int sampleDeviceStateDataLen = sizeof(sampleDeviceStateData) - 1;

// Sends a reported property for device state of this simulated device.
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_ReportDeviceStateAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const char * ComponentName)
{

    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    STRING_HANDLE jsonToSend = NULL;

    if ((jsonToSend = PnP_CreateReportedProperty(ComponentName, sampleDeviceStateProperty, (const char*) sampleDeviceStateData)) == NULL)
    {
        LogError("Unable to build reported property response for propertyName=%s, propertyValue=%s", sampleDeviceStateProperty, sampleDeviceStateData);
    }
    else
    {
        const char* jsonToSendStr = STRING_c_str(jsonToSend);
        size_t jsonToSendStrLen = strlen(jsonToSendStr);

        if ((iothubClientResult = SampleEnvironmentalSensor_RouteReportedState(NULL, PnpComponentHandle, (const unsigned char*)jsonToSendStr, jsonToSendStrLen,
            SampleEnvironmentalSensor_PropertyCallback, (void*)sampleDeviceStateProperty)) != IOTHUB_CLIENT_OK)
        {
            LogError("Environmental Sensor Adapter:: Unable to send reported state for property=%s, error=%d",
                                sampleDeviceStateProperty, iothubClientResult);
        }
        else
        {
            LogInfo("Environmental Sensor Adapter:: Sending device information property to IoTHub. propertyName=%s, propertyValue=%s",
                        sampleDeviceStateProperty, sampleDeviceStateData);
        }

        STRING_delete(jsonToSend);
    }

    return iothubClientResult;
}


// Routes the reported property for device or module client. This function can be called either by passing a valid client handle or by passing
// a NULL client handle after components have been started such that the client handle can be extracted from the PnpComponentHandle
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteReportedState(
    void * ClientHandle,
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
    const unsigned char * ReportedState,
    size_t Size,
    IOTHUB_CLIENT_REPORTED_STATE_CALLBACK ReportedStateCallback,
    void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;

    PNP_BRIDGE_CLIENT_HANDLE clientHandle = (ClientHandle != NULL) ?
            (PNP_BRIDGE_CLIENT_HANDLE) ClientHandle : PnpComponentHandleGetClientHandle(PnpComponentHandle);

    if ((iothubClientResult = PnpBridgeClient_SendReportedState(clientHandle, ReportedState, Size,
            ReportedStateCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendReportedState failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendReportedState succeeded");
    }

exit:
    return iothubClientResult;
}

```

### <a name="send-telemetry-device-to-cloud"></a>Envoyer des données de télémétrie (appareil vers cloud)
```c
//
// SampleEnvironmentalSensor_SendTelemetryMessagesAsync is periodically invoked by the caller to
// send telemetry containing the current temperature and humidity (in both cases random numbers
// so this sample will work on platforms without these sensors).
//
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_SendTelemetryMessagesAsync(
    PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle)
{
    IOTHUB_CLIENT_RESULT result = IOTHUB_CLIENT_OK;
    IOTHUB_MESSAGE_HANDLE messageHandle = NULL;
    PENVIRONMENT_SENSOR device = PnpComponentHandleGetContext(PnpComponentHandle);

    float currentTemperature = 20.0f + ((float)rand() / RAND_MAX) * 15.0f;
    float currentHumidity = 60.0f + ((float)rand() / RAND_MAX) * 20.0f;

    char currentMessage[128];
    sprintf(currentMessage, "{\"%s\":%.3f, \"%s\":%.3f}", SampleEnvironmentalSensor_TemperatureTelemetry, 
            currentTemperature, SampleEnvironmentalSensor_HumidityTelemetry, currentHumidity);


    if ((messageHandle = PnP_CreateTelemetryMessageHandle(device->SensorState->componentName, currentMessage)) == NULL)
    {
        LogError("Environmental Sensor Adapter:: PnP_CreateTelemetryMessageHandle failed.");
    }
    else if ((result = SampleEnvironmentalSensor_RouteSendEventAsync(PnpComponentHandle, messageHandle,
            SampleEnvironmentalSensor_TelemetryCallback, device)) != IOTHUB_CLIENT_OK)
    {
        LogError("Environmental Sensor Adapter:: SampleEnvironmentalSensor_RouteSendEventAsync failed, error=%d", result);
    }

    IoTHubMessage_Destroy(messageHandle);

    return result;
}

// Routes the sending asynchronous events for device or module client
IOTHUB_CLIENT_RESULT SampleEnvironmentalSensor_RouteSendEventAsync(
        PNPBRIDGE_COMPONENT_HANDLE PnpComponentHandle,
        IOTHUB_MESSAGE_HANDLE EventMessageHandle,
        IOTHUB_CLIENT_EVENT_CONFIRMATION_CALLBACK EventConfirmationCallback,
        void * UserContextCallback)
{
    IOTHUB_CLIENT_RESULT iothubClientResult = IOTHUB_CLIENT_OK;
    PNP_BRIDGE_CLIENT_HANDLE clientHandle = PnpComponentHandleGetClientHandle(PnpComponentHandle);
    if ((iothubClientResult = PnpBridgeClient_SendEventAsync(clientHandle, EventMessageHandle,
            EventConfirmationCallback, UserContextCallback)) != IOTHUB_CLIENT_OK)
    {
        LogError("IoTHub client call to _SendEventAsync failed with error code %d", iothubClientResult);
        goto exit;
    }
    else
    {
        LogInfo("IoTHub client call to _SendEventAsync succeeded");
    }

exit:
    return iothubClientResult;
}

```
### <a name="receive-command-update-callback-from-the-cloud-and-process-it-on-the-device-side-cloud-to-device"></a>Recevoir le rappel de mise à jour de commande à partir du cloud et le traiter côté appareil (cloud vers appareil)
```c
// SampleEnvironmentalSensor_ProcessCommandUpdate receives commands from the server.  This implementation acts as a simple dispatcher
// to the functions to perform the actual processing.
int SampleEnvironmentalSensor_ProcessCommandUpdate(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    const char* CommandName,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    if (strcmp(CommandName, sampleEnvironmentalSensorCommandBlink) == 0)
    {
        return SampleEnvironmentalSensor_BlinkCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOn) == 0)
    {
        return SampleEnvironmentalSensor_TurnOnLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else if (strcmp(CommandName, sampleEnvironmentalSensorCommandTurnOff) == 0)
    {
        return SampleEnvironmentalSensor_TurnOffLightCallback(EnvironmentalSensor, CommandValue, CommandResponse, CommandResponseSize);
    }
    else
    {
        // If the command is not implemented by this interface, by convention we return a 404 error to server.
        LogError("Environmental Sensor Adapter:: Command name <%s> is not associated with this interface", CommandName);
        return SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_NotImplemented);
    }
}

// Implement the callback to process the command "blink". Information pertaining to the request is
// specified in the CommandValue parameter, and the callback fills out data it wishes to
// return to the caller on the service in CommandResponse.

static int SampleEnvironmentalSensor_BlinkCallback(
    PENVIRONMENT_SENSOR EnvironmentalSensor,
    JSON_Value* CommandValue,
    unsigned char** CommandResponse,
    size_t* CommandResponseSize)
{
    int result = PNP_STATUS_SUCCESS;
    int BlinkInterval = 0;

    LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);

    if (json_value_get_type(CommandValue) != JSONNumber)
    {
        LogError("Cannot retrieve blink interval for blink command");
        result = PNP_STATUS_BAD_FORMAT;
    }
    else
    {
        BlinkInterval = (int)json_value_get_number(CommandValue);
        LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
        EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
        EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;

        result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
    }

    return result;
}

```
### <a name="respond-to-command-update-on-the-device-side-device-to-cloud"></a>Répondre à la mise à jour des commandes côté appareil (de l’appareil vers le cloud)

```c
    static int SampleEnvironmentalSensor_BlinkCallback(
        PENVIRONMENT_SENSOR EnvironmentalSensor,
        JSON_Value* CommandValue,
        unsigned char** CommandResponse,
        size_t* CommandResponseSize)
    {
        int result = PNP_STATUS_SUCCESS;
        int BlinkInterval = 0;
    
        LogInfo("Environmental Sensor Adapter:: Blink command invoked. It has been invoked %d times previously", EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled);
    
        if (json_value_get_type(CommandValue) != JSONNumber)
        {
            LogError("Cannot retrieve blink interval for blink command");
            result = PNP_STATUS_BAD_FORMAT;
        }
        else
        {
            BlinkInterval = (int)json_value_get_number(CommandValue);
            LogInfo("Environmental Sensor Adapter:: Blinking with interval=%d second(s)", BlinkInterval);
            EnvironmentalSensor->SensorState->numTimesBlinkCommandCalled++;
            EnvironmentalSensor->SensorState->blinkInterval = BlinkInterval;
    
            result = SampleEnvironmentalSensor_SetCommandResponse(CommandResponse, CommandResponseSize, sampleEnviromentalSensor_BlinkResponse);
        }
    
        return result;
    }
    
    // SampleEnvironmentalSensor_SetCommandResponse is a helper that fills out a command response
    static int SampleEnvironmentalSensor_SetCommandResponse(
        unsigned char** CommandResponse,
        size_t* CommandResponseSize,
        const unsigned char* ResponseData)
    {
        int result = PNP_STATUS_SUCCESS;
        if (ResponseData == NULL)
        {
            LogError("Environmental Sensor Adapter:: Response Data is empty");
            *CommandResponseSize = 0;
            return PNP_STATUS_INTERNAL_ERROR;
        }
    
        *CommandResponseSize = strlen((char*)ResponseData);
        memset(CommandResponse, 0, sizeof(*CommandResponse));
    
        // Allocate a copy of the response data to return to the invoker. Caller will free this.
        if (mallocAndStrcpy_s((char**)CommandResponse, (char*)ResponseData) != 0)
        {
            LogError("Environmental Sensor Adapter:: Unable to allocate response data");
            result = PNP_STATUS_INTERNAL_ERROR;
        }
    
        return result;
}
```

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur le pont IoT Plug-and-Play, rendez-vous sur le référentiel GitHub [Pont IoT Plug-and-Play](https://github.com/Azure/iot-plug-and-play-bridge).
