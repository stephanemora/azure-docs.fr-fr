---
title: Exporter vos données vers Stockage Blob Azure | Microsoft Docs
description: Comment exporter des données de votre application Azure IoT Central vers Stockage Blob Azure
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973271"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>Exporter vos données vers Stockage Blob Azure (fonctionnalités d’évaluation)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Cette rubrique s’applique aux administrateurs.*

Cet article explique comment utiliser la fonctionnalité d’exportation continue des données dans Azure IoT Central pour exporter régulièrement des données vers votre **compte Stockage Blob Azure** ou votre **compte de stockage Azure Data Lake Storage Gen2**. Vous pouvez exporter des **données de télémétrie**, des **appareils** et des **modèles d’appareil** vers des fichiers au format JSON. Utilisez les données exportées pour l’analytique des chemins à froid, comme les modèles d’apprentissage dans Azure Machine Learning ou l’analyse de tendances à long terme dans Microsoft Power BI.

> [!Note]
> Quand vous activez l’exportation de données continue, vous obtenez seulement les données à partir de ce moment. Pour le moment, vous ne pouvez pas récupérer les données d’une période pendant laquelle l’exportation de données continue est désactivée. Pour conserver un historique des données plus étendu, activez l’exportation de données continue tôt dans le processus.


## <a name="prerequisites"></a>Prérequis

- Vous devez être administrateur dans votre application IoT Central

## <a name="create-storage-account"></a>Créer un compte de stockage
En l’absence de données de Stockage existantes à exporter, suivez ces étapes :

1. Créez un [compte de stockage sur le Portail Azure](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Découvrez comment créer des [comptes de stockage Blob Azure](https://aka.ms/blobdocscreatestorageaccount) ou des [comptes de stockage Azure Data Lake Storage v2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account).

    > [!Note] 
    > Si vous choisissez d’exporter des données vers un compte de stockage ADLS v2, vous devez définir**Type de compte** sur **BlobStorage**. 

    > [!Note] 
    > Vous pouvez exporter des données vers des comptes de stockage compris dans des abonnements autres que celui de votre application de paiement à l’utilisation IoT Central. Vous vous connecterez à l’aide d’une chaîne de connexion dans ce cas.

2. Créez un conteneur dans votre compte de stockage. Accédez à votre compte de stockage. Sous **Service blob**, sélectionnez **Parcourir les objets blob**. Sélectionnez **+ Conteneur**, en haut, pour créer un conteneur.


## <a name="set-up-continuous-data-export"></a>Configurer l’exportation de données en continu

Maintenant que vous avez une destination de Stockage pour exporter les données, suivez ces étapes pour configurer l’exportation continue des données. 

1. Connectez-vous à votre application IoT Central.

2. Dans le menu de gauche, sélectionnez **Exportation de données**.

    > [!Note]
    > Si vous ne voyez pas l’option d’exportation de données dans le menu de gauche, vous n’êtes pas l’administrateur de votre application. Contactez un administrateur pour configurer l’exportation de données.

3. Sélectionnez le bouton **+ Nouveau** dans le coin supérieur droit. Choisissez **Stockage Blob Azure** comme destination de votre exportation. 

    > [!NOTE] 
    > Le nombre maximal d’exportations par application est de cinq. 

    ![Créer une exportation de données continue](media/howto-export-data-pnp/export-new2.png)

4. Dans la zone de liste déroulante, sélectionnez l’**espace de noms de votre compte de stockage**. Vous pouvez également choisir la dernière option de la liste : **Entrer une chaîne de connexion**. 

    > [!NOTE] 
    > Vous verrez seulement les espaces de noms des comptes de stockage se trouvant dans le **même abonnement que votre application IoT Central**. Si vous souhaitez exporter les données vers une destination en dehors de cet abonnement, choisissez **Entrer une chaîne de connexion** et reportez-vous à l’étape 5.

    > [!NOTE] 
    > Pour les applications d’évaluation de 7 jours, le seul moyen de configurer l’exportation des données est d’utiliser une chaîne de connexion. En effet, ces applications ne sont associées à aucun abonnement Azure.

    ![Créer une exportation vers un objet blob](media/howto-export-data-pnp/export-create-blob2.png)

5. (Facultatif) Si vous avez choisi **Entrer une chaîne de connexion**, une nouvelle zone vous permettant de coller votre chaîne de connexion s’affiche. Pour obtenir la chaîne de connexion de votre compte de stockage, accédez à celui-ci dans le portail Azure :
    - Sous **Paramètres**, sélectionnez **Clés d’accès**.
    - Copiez soit la chaîne de connexion key1 soit la chaîne de connexion key2
 
6. Choisissez un conteneur dans la liste déroulante. Si vous n’avez pas de conteneur, accédez à votre compte de stockage dans le portail Azure :
    - Sous **Service blob**, sélectionnez **Objets blob**. Cliquez sur **+ Conteneur** et donnez un nom à votre conteneur. Choisissez un niveau d’accès public pour vos données (tous sont compatibles avec l’exportation de données continue). Pour en savoir plus, consultez les [documents sur le Stockage Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

7.  Sous **Données à exporter**, spécifiez chaque type de données à exporter en définissant le type avec la valeur **Activé**.
   
    > [!NOTE] 
    > Les données sont exportées au format JSON.

8. Pour activer l’exportation des données continue, vérifiez que le bouton bascule **Exportation de données** est sur Activé. Sélectionnez **Enregistrer**.

   ![Configurer l’exportation de données continue](media/howto-export-data-pnp/export-list-blob2.png)

9. Après quelques minutes, vos données s’affichent dans votre compte de stockage.


## <a name="path-structure"></a>Structure du chemin

Les données de télémétrie, d’appareils et de modèles d’appareil sont exportées vers votre compte de stockage une fois par minute. Chaque fichier contient le lot de modifications apportées depuis la dernière exportation de fichier. Les données exportées sont placées dans trois dossiers au format JSON. Les chemins d’accès par défaut dans votre compte de stockage sont les suivants :
- Données de télémétrie : {conteneur}/{id-app}/telemetry/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}
- Appareils : {conteneur}/{id-app}/devices/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}
- Modèles d’appareil : {conteneur}/deviceTemplates/{AAAA}/{MM}/{jj}/{hh}/{mm}/{nom-fichier}

Vous pouvez parcourir les fichiers exportés dans le portail Azure en accédant au fichier puis en choisissant l’onglet **Modifier l’objet blob**.

## <a name="data-format"></a>Format de données
### <a name="telemetry"></a>Télémétrie

Les données de télémétrie exportées contiennent tous les nouveaux messages reçus par IoT Central en provenance de tous les appareils pendant cette période. Les fichiers exportés utilisent le même format que les fichiers de messages exportés par [le routage des messages IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c) vers Stockage Blob.

> [!NOTE]
> Vérifiez que vos appareils envoient bien des messages avec `contentType: application/JSON` et `contentEncoding:utf-8` (ou `utf-16`, `utf-32`). Pour obtenir un exemple, consultez la [documentation IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body).

> [!NOTE]
> Les appareils qui envoient les données de télémétrie sont représentés par leur ID d’appareil (voir les sections suivantes). Pour obtenir les noms des appareils, exportez les instantanés d’appareil. Associez chaque enregistrement avec le **connectionDeviceId** qui correspond au **deviceId** de l’enregistrement de l’appareil.

L’exemple suivant montre un enregistrement au format JSON.

```json
{ 
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{ 

  },
  "SystemProperties":{ 
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{ 
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

### <a name="devices"></a>Appareils

Quand l’exportation de données continue est activée pour la première fois, un instantané unique avec tous les appareils est exporté. Chaque appareil inclut les éléments suivants :
- `@id` de l’appareil dans IoT Central
- `name` de l’appareil
- `deviceId` issu du [service de provisionnement des appareils](https://aka.ms/iotcentraldocsdps)
- Informations sur le modèle d’appareil
- Valeurs de propriétés

Un nouvel instantané est écrit chaque minute. L’instantané inclut les éléments suivants :

- Nouveaux appareils ajoutés depuis le dernier instantané.
- Appareils pour lesquels des valeurs de propriétés ont changé depuis le dernier instantané.

> [!NOTE]
> Les appareils supprimés depuis le dernier instantané ne sont pas exportés. À l’heure actuelle, les instantanés n’ont pas d’indicateurs pour les appareils supprimés.
>
> Le modèle d’appareil auquel chaque appareil appartient est représenté par le champ `instanceOf`. Pour obtenir le nom du modèle d’appareil, exportez les instantanés des modèles d’appareil.

Les fichiers exportés contiennent une seule ligne par enregistrement. L’exemple suivant montre un enregistrement au format JSON.

```json
{ 
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{ 
    "$cloudProperties":{ 
        "Color":"blue"
    },
    "EnvironmentalSensor":{ 
      "thsensormodel":{ 
        "reported":{ 
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{ 
        "reported":{ 
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{ 
      "totalStorage":{ 
        "reported":{ 
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{ 
        "reported":{ 
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates"></a>Modèles d’appareil

Quand l’exportation de données continue est activée pour la première fois, un instantané unique avec tous les modèles d’appareil est exporté. Chaque modèle d’appareil inclut les éléments suivants :
- `@id` du modèle d’appareil
- `name` du modèle d’appareil
- `version` du modèle d’appareil
- L’appareil `capabilityModel`, y compris ses `interfaces`, et les définitions de télémétrie, de propriétés et de commandes
- Définitions `cloudProperties`
- Remplacements et valeurs initiales, alignés sur le `capabilityModel`

Un nouvel instantané est écrit chaque minute. L’instantané inclut les éléments suivants :

- Nouveaux modèles d’appareil ajoutés depuis le dernier instantané. Cela comprend les nouvelles versions des modèles d’appareil.
- Modèles d’appareil avec des remplacements, des valeurs initiales et des définitions de propriétés cloud qui ont été modifiés depuis le dernier instantané.

> [!NOTE]
> Les modèles d’appareil supprimés depuis le dernier instantané ne sont pas exportés. À l’heure actuelle, les captures instantanées n’ont pas d’indicateurs pour les modèles d’appareil supprimés.

Les fichiers exportés contiennent une seule ligne par enregistrement. L’exemple suivant montre un enregistrement au format JSON.

```json
{ 
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{ 
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[ 
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{ 
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{ 
                  "@value":"50"
                }
              },
              "visualizationDetail":{ 
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      { 
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{ 
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[ 
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            { 
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{ 
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[ 
      { 
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{ 
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{ 
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous savez comment exporter vos données, passez à l’étape suivante :

> [!div class="nextstepaction"]
> [Comment utiliser le pont d’appareil IoT Central pour connecter d’autres clouds IoT](howto-build-iotc-device-bridge.md)
