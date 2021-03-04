---
title: Manifeste de mise à jour pour Device Update pour IoT Hub | Microsoft Docs
description: Découvrez comment les propriétés sont envoyées du service Device Update à l’appareil pendant une mise à jour
author: andbrown
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: ea438a4a8db8dce9cf2acb0b5b3ff4e250c3cf39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660311"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>Manifeste de mise à jour pour le service Device Update pour IoT Hub

## <a name="overview"></a>Vue d’ensemble

Le service Device Update pour IoT Hub utilise un _manifeste de mise à jour_ pour communiquer des actions et également les métadonnées prenant en charge ces actions par le biais des propriétés de l’interface [AzureDeviceUpdateCore.OrchestratorMetadata:4](./device-update-plug-and-play.md).
Ce document décrit les principes fondamentaux de la façon dont la propriété `updateManifest`, dans l’interface `AzureDeviceUpdateCore.OrchestratorMetadata:4`, est utilisée par l’agent Device Update. Les propriétés de l’interface `AzureDeviceUpdateCore.OrchestratorMetadata:4` sont envoyées depuis le service Device Update pour IoT Hub à l’agent Device Update. Le `updateManifest` est un objet JSON sérialisé qui est analysé par l’agent Device Update.

### <a name="an-example-update-manifest"></a>Exemple de manifeste de mise à jour

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

L’objectif du manifeste de mise à jour est de décrire le contenu d’une mise à jour, à savoir son identité, son type, ses critères installés et les métadonnées du fichier de mise à jour. En outre, le manifeste de mise à jour est signé par chiffrement pour permettre à l’agent Device Update de vérifier son authenticité. Reportez-vous à la documentation sur la [sécurité de Device Update](./device-update-security.md) pour plus d’informations sur l’utilisation du manifeste de mise à jour pour importer du contenu de manière sécurisée.

## <a name="import-manifest-vs-update-manifest"></a>Manifeste d’importation et manifeste de mise à jour

Il est important de comprendre les différences entre le manifeste d’importation et les concepts de manifeste de mise à jour dans le service Device Update pour IoT Hub. 
* Le [manifeste d’importation](./import-concepts.md) est créé par quiconque produit la mise à jour correspondante. Il décrit le contenu de la mise à jour à importer dans le service Device Update pour IoT Hub. 
* Le manifeste de mise à jour est généré automatiquement par le service Device Update pour IoT Hub, à l’aide de certaines des propriétés qui ont été définies dans le manifeste d’importation. Il est utilisé pour communiquer des informations pertinentes à l’agent Device Update pendant le processus de mise à jour. 

Chaque type de manifeste a son propre schéma et sa propre version de schéma.

## <a name="update-manifest-properties"></a>Mettre à jour les propriétés du manifeste

Les définitions générales des propriétés du manifeste de mise à jour se trouvent dans les définitions d’interface disponibles [ici](./device-update-plug-and-play.md). Pour fournir un contexte plus approfondi, examinons de plus près les propriétés et la façon dont elles sont utilisées dans le système.

### <a name="updateid"></a>updateId

Contient les `provider`, `name` et `version`, qui représente l’identité exacte de la mise à jour du service Device Update pour IoT Hub utilisée pour déterminer les appareils compatibles pour la mise à jour.

### <a name="updatetype"></a>updateType

Représente le type de mise à jour qui est géré par un type spécifique de gestionnaire de mise à jour. Il se présente sous la forme `microsoft/swupdate:1` pour une mise à jour basée sur une image et `microsoft/apt:1` pour une mise à jour basée sur un package (voir la section `Update Handler Types` ci-dessous).

### <a name="installedcriteria"></a>installedCriteria

Chaîne qui contient les informations dont a besoin le gestionnaire de mise à jour de l’agent Device Update pour déterminer si la mise à jour est installée sur l’appareil. La section `Update Handler Types` décrit le format des `installedCriteria`, pour chaque type de mise à jour pris en charge par le service Device Update pour IoT Hub.

### <a name="files"></a>files

Indique à l’agent Device Update les fichiers à télécharger ainsi que le hachage qui est utilisé pour vérifier que les fichiers ont été téléchargés correctement.
Voici un examen plus approfondi du contenu de la propriété `files` :

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

En dehors de `updateManifest` se trouve le tableau `fileUrls` d’objets JSON.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Les éléments `FILE_ID_STRING`, dans `fileUrls`, et `files` sont les mêmes (par exemple, « 0000 » dans `files` a l’URL à « 0000 » dans `fileUrls`).

### <a name="manifestversion"></a>manifestVersion

Chaîne qui représente la version du schéma.

## <a name="update-handler-types"></a>Types de gestionnaire de mise à jour

|Update, méthode|Type de gestionnaire de mise à jour|Type de mise à jour|Critères installés|Fichiers attendus pour la publication|
|-------------|-------------------|----------|-----------------|--------------|
|Basé sur une image|SWUpdate|"microsoft/swupdate:version"|L’image de référence enregistre l’indicateur de sa version dans le fichier /etc/adu-version.  |Fichier .swu qui contient l’image SWUpdate|
|Basé sur un package|APT|"microsoft/apt:version"|`<name>` + "-" + `<version>` (propriétés définies dans le fichier manifeste APT)|`<APT Update Manifest>`.json contenant la configuration APT et la liste des packages|

