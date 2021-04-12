---
title: Comprendre comment Device Update pour IoT Hub utilise IoT Plug-and-Play | Microsoft Docs
description: Device Update pour IoT Hub l’utilise pour détecter et gérer les appareils prenant en charge la mise à jour OTA.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561812"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Device Update pour IoT Hub et IoT Plug-and-Play

Device Update pour IoT Hub utilise [IoT Plug-and-Play](../iot-pnp/index.yml) pour détecter et gérer les appareils prenant en charge la mise à jour OTA. Le service Device Update envoie et reçoit des propriétés et des messages vers et depuis des appareils à l’aide d’interfaces PnP. Device Update pour IoT Hub nécessite que les appareils IoT implémentent les interfaces et l’ID de modèle suivants, comme décrit ci-dessous.

## <a name="adu-core-interface"></a>Interface ADU Core

L’interface « ADUCoreInterface » est utilisée pour envoyer aux appareils des actions de mise à jour et des métadonnées, ainsi que pour recevoir l’état de mise à jour des appareils. L’interface « ADU Core » est divisée en deux propriétés d’objet.

Le nom de composant attendu dans votre modèle est **« azureDeviceUpdateAgent »** lors de l’implémentation de cette interface. [En savoir plus sur les composants Azure IoT PnP](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Métadonnées de l’agent

Les métadonnées de l’agent contiennent des champs que l’appareil ou l’agent Device Update utilise pour envoyer des informations et l’état aux services Device Update.

|Nom|schéma|Sens|Description|Exemple|
|----|------|---------|-----------|-----------|
|resultCode|entier|appareil à cloud|Code qui contient des informations sur le résultat de la dernière action de mise à jour. Peut être renseigné pour la réussite ou l’échec et doit suivre la [spécification du code d’état http](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).|500|
|extendedResultCode|entier|appareil à cloud|Code qui contient des informations supplémentaires sur le résultat. Peut être renseigné pour la réussite ou l’échec.|0x80004005|
|state|entier|appareil à cloud|Il s’agit d’un entier qui indique l’état actuel de l’agent Device Update. Voir les détails ci-dessous. |Idle|
|installedUpdateId|string|appareil à cloud|ID de la mise à jour actuellement installée (via Device Update). Cette valeur sera null pour un appareil qui n’a jamais obtenu de mise à jour via Device Update.|Null|
|`deviceProperties`|Mappage|appareil à cloud|Ensemble de propriétés qui contiennent le fabricant et le modèle.|Voir les détails ci-dessous.

#### <a name="state"></a>State

Il s’agit de l’état signalé par l’agent Device Update après avoir reçu une action du service Device Update. `State` est signalé en réponse à une `Action` (voir `Actions` ci-dessous) envoyée à l’agent Device Update à partir du service Device Update. Consultez le [flux de travail de présentation](understand-device-update.md#device-update-agent) pour les demandes transmises entre le service Device Update et l’agent Device Update.

|Nom|Valeur|Description|
|---------|-----|-----------|
|Idle|0|L’appareil est prêt à recevoir une action du service Device Update. Après une mise à jour réussie, l’état est rétabli à l’état `Idle`.|
|DownloadSucceeded|2|Téléchargement réussi.|
|InstallSucceeded|4|Installation réussie.|
|Échec|255|Une défaillance est survenue au cours de la mise à jour.|

#### <a name="device-properties"></a>Propriétés de l’appareil

Il s’agit de l’ensemble de propriétés qui contiennent le fabricant et le modèle.

|Nom|schéma|Sens|Description|
|----|------|---------|-----------|
|manufacturer|string|appareil à cloud|Fabricant d’appareil de l’appareil, signalé par `deviceProperties`. Cette propriété est lue à partir de l’un ou de l’autre emplacement – l’interface « AzureDeviceUpdateCore » tente d’abord de lire la valeur « aduc_manufacturer » à partir du [fichier de configuration](device-update-configuration-file.md).  Si cette valeur n’est pas renseignée dans le fichier de configuration, elle signalera par défaut la définition de ADUC_DEVICEPROPERTIES_MANUFACTURER au moment de la compilation. Cette propriété est signalée uniquement au moment du démarrage.|
|model|string|appareil à cloud|Modèle d’appareil de l’appareil, signalé par `deviceProperties`. Cette propriété est lue à partir de l’un ou de l’autre emplacement – l’interface AzureDeviceUpdateCore tente d’abord de lire la valeur « aduc_model » à partir du [fichier de configuration](device-update-configuration-file.md).  Si cette valeur n’est pas renseignée dans le fichier de configuration, elle signalera par défaut la définition de ADUC_DEVICEPROPERTIES_MODEL au moment de la compilation. Cette propriété est signalée uniquement au moment du démarrage.|
|aduVer|string|appareil à cloud|Version de l’agent Device Update en cours d’exécution sur l’appareil. Cette valeur est lue à partir de la build seulement si, au moment de la compilation, ENABLE_ADU_TELEMETRY_REPORTING a la valeur 1 (true). Les clients peuvent choisir de refuser la création de rapports de version en définissant cette valeur sur 0 (false). [Comment personnaliser les propriétés de l’agent Device Update](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md).|
|doVer|string|appareil à cloud|Version de l’agent d’optimisation de la distribution en cours d’exécution sur l’appareil. Cette valeur est lue à partir de la build seulement si, au moment de la compilation, ENABLE_ADU_TELEMETRY_REPORTING a la valeur 1 (true). Les clients peuvent choisir de refuser la création de rapports de version en définissant cette valeur sur 0 (false).[Comment personnaliser les propriétés de l’agent d’optimisation de la distribution](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components).|

### <a name="service-metadata"></a>Métadonnées du service

Les métadonnées de service contiennent des champs que le service Device Update utilise pour communiquer des actions et des données à l’agent Device Update.

|Nom|schéma|Sens|Description|
|----|------|---------|-----------|
|action|entier|cloud à appareil|Il s’agit d’un entier qui correspond à une action que l’agent doit effectuer. Valeurs listées ci-dessous.|
|updateManifest|string|cloud à appareil|Utilisé pour décrire le contenu d’une mise à jour. Généré à partir du [manifeste d’importation](import-update.md#create-device-update-import-manifest)|
|updateManifestSignature|Objet JSON|cloud à appareil|Signature web JSON (au JWS) avec clés web JSON utilisées pour la vérification de la source.|
|fileUrls|Mappage|cloud à appareil|Mappage de `FileHash` à `DownloadUri`. Indique à l’agent quels fichiers télécharger et le hachage à utiliser pour vérifier que les fichiers ont été téléchargés correctement.|

#### <a name="action"></a>Action

`Actions` ci-dessous représente les actions effectuées par l’agent Device Update selon les instructions données par le service Device Update. L’agent Device Update signale un état `State` (voir la section `State` ci-dessus) de traitement de l’instance `Action` reçue. Consultez le [flux de travail de présentation](understand-device-update.md#device-update-agent) pour les demandes transmises entre le service Device Update et l’agent Device Update.

|Nom|Valeur|Description|
|---------|-----|-----------|
|Téléchargement|0|Télécharger le contenu publié ou la mise à jour, ainsi que tout autre contenu requis|
|Installer|1|Installez le contenu ou la mise à jour. En général, cela implique l’appel du programme d’installation pour le contenu ou la mise à jour.|
|Appliquer|2|Finalisez la mise à jour. Cela signale au système de redémarrer si nécessaire.|
|Annuler|255|Arrêtez le traitement de l’action en cours et revenez à `Idle`. Est également utilisé pour indiquer à l’agent dans l’état `Failed` de revenir à `Idle`.|

## <a name="device-information-interface"></a>Interface d’informations sur l’appareil

L’interface d’informations sur l’appareil est un concept utilisé dans l’[architecture IoT Plug-and-Play](../iot-pnp/overview-iot-plug-and-play.md). Elle contient des propriétés d’appareil vers le cloud qui fournissent des informations sur le matériel et le système d’exploitation de l’appareil. Device Update pour IoT Hub utilise les propriétés DeviceInformation.manufacturer et DeviceInformation.model pour la télémétrie et les diagnostics. Pour en apprendre davantage sur l’interface d’informations sur l’appareil, consultez cet [exemple](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

Le nom de composant attendu dans votre modèle est **deviceInformation** lors de l’implémentation de cette interface. [En savoir plus sur les composants Azure IoT PnP](../iot-pnp/concepts-components.md)

|Nom|Type|schéma|Sens|Description|Exemple|
|----|----|------|---------|-----------|-----------|
|manufacturer|Propriété|string|appareil à cloud|Nom de l’entreprise du fabricant de l’appareil. Il peut s’agir du même nom que celui du fabricant OEM (Original Equipment Manufacturer).|Contoso|
|model|Propriété|string|appareil à cloud|ID ou nom du modèle d’appareil.|Appareil IoT Edge|
|swVersion|Propriété|string|appareil à cloud|Version du logiciel sur votre appareil. swVersion peut être la version de votre microprogramme.|4.15.0-122|
|osName|Propriété|string|appareil à cloud|Nom du système d’exploitation sur l’appareil.|Ubuntu Server 18.04|
|processorArchitecture|Propriété|string|appareil à cloud|Architecture du processeur sur l’appareil.|ARM64|
|processorManufacturer|Propriété|string|appareil à cloud|Nom du fabricant du processeur sur l’appareil.|Microsoft|
|totalStorage|Propriété|string|appareil à cloud|Stockage total disponible sur l’appareil, en kilo-octets.|2 048|
|totalMemory|Propriété|string|appareil à cloud|Quantité totale de mémoire disponible sur l’appareil, en kilo-octets.|256|

## <a name="model-id"></a>ID de modèle 

L’ID de modèle est la manière dont les appareils intelligents publient leurs fonctionnalités dans les applications Azure IoT avec IoT Plug-and-Play. Pour en apprendre davantage sur la façon de créer des appareils intelligents pour publier ces fonctionnalités dans les applications Azure IoT, reportez-vous au [Guide du développeur d’appareils IoT Plug-and-Play](../iot-pnp/concepts-developer-guide-device.md).

Device Update pour IoT Hub nécessite le smart device IoT Plug-and-Play pour annoncer un ID de modèle avec la valeur **« dtmi:AzureDeviceUpdate;1 »** dans le cadre de la connexion de l’appareil. [Découvrez comment annoncer un ID de modèle](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).