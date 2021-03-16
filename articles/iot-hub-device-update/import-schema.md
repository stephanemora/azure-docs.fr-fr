---
title: Importation de mises à jour dans Device Update pour IoT Hub – Schéma et autres informations | Microsoft Docs
description: Schéma et informations associées (dont des objets) utilisés lors de l’importation de mises à jour dans Device Update pour IoT Hub.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 13044b8f087b403f83516a32a490d2dee8db700f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054100"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Importation de mises à jour dans Device Update pour IoT Hub – Schéma et autres informations
Si vous souhaitez importer une mise à jour dans Device Update pour IoT Hub, veillez à consulter d’abord les [concepts](import-concepts.md) et le [guide pratique](import-update.md). Pour des détails sur le schéma utilisé lors de la construction d’un manifeste d’importation, ainsi que des informations sur les objets associés, voir ci-dessous.

## <a name="import-manifest-schema"></a>Schéma de manifeste d’importation

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| UpdateId | l'objet `UpdateId` | Identité de la mise à jour. |
| UpdateType | string | Type de mise à jour : <br/><br/> * Spécifiez `microsoft/apt:1` quand vous effectuez une mise à jour basée sur un package à l’aide d’un agent de référence.<br/> * Spécifiez `microsoft/swupdate:1` quand vous effectuez une mise à jour basée sur une image à l’aide d’un agent de référence.<br/> * Spécifiez `microsoft/simulator:1` quand vous utilisez un exemple de simulateur d’agent.<br/> * Spécifiez un type personnalisé si vous développez un agent personnalisé. | Format: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> Longueur maximale totale de 32 caractères |
| InstalledCriteria | string | Chaîne interprétée par l’agent pour déterminer si la mise à jour a été appliquée avec succès :  <br/> * Spécifiez la **valeur** de SWVersion pour le type de mise à jour `microsoft/swupdate:1`.<br/> * Spécifiez `{name}-{version}` pour le type de mise à jour `microsoft/apt:1`, dont le nom et la version proviennent du fichier APT.<br/> * Spécifiez le code de hachage du fichier de mise à jour pour le type de mise à jour `microsoft/simulator:1`.<br/> * Spécifiez une chaîne personnalisée si vous développez un agent personnalisé.<br/> | 64 caractères maximum |
| Compatibilité | Tableau d’objets `CompatibilityInfo` | Informations de compatibilité de l’appareil compatible avec cette mise à jour. | Maximum de 10 éléments |
| CreatedDateTime | date/heure | Date et heure de création de la mise à jour. | Format de date et heure ISO 8601 délimité, en UTC |
| ManifestVersion | string | Version du schéma du manifeste d’importation. Spécifiez `2.0`, qui sera compatible avec l’interface `urn:azureiot:AzureDeviceUpdateCore:1` et l’interface `urn:azureiot:AzureDeviceUpdateCore:4`. | Doit être `2.0` |
| Fichiers | Tableau d’objets `File` | Mettre à jour les fichiers de charge utile | Maximum de 5 fichiers |

## <a name="updateid-object"></a>Objet UpdateId

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| Fournisseur | string | Partie fournisseur de l’identité de mise à jour. | 1 à 64 caractères, alphanumériques, point et tiret. |
| Nom | string | Partie nom de l’identité de mise à jour. | 1 à 64 caractères, alphanumériques, point et tiret. |
| Version | version | Partie version de l’identité de mise à jour. | 2 à 4 parties, numéro de version séparé par des points, compris entre 0 et 2147483647. Les zéros non significatifs seront supprimés. |

## <a name="file-object"></a>Objet File

| Nom | Type | Description | Restrictions |
| --------- | --------- | --------- | --------- |
| Nom de fichier | string | Nom du fichier | Doit être unique au sein d’une mise à jour |
| SizeInBytes | Int64 | Taille du fichier en octets. | Maximum 800 Mo par fichier individuel, ou 800 Mo collectivement par mise à jour |
| Codes de hachage | l'objet `Hashes` | Objet JSON contenant le ou les hachages du fichier |

## <a name="compatibilityinfo-object"></a>Objet CompatibilityInfo

| Nom | Type | Description | Restrictions |
| --- | --- | --- | --- |
| DeviceManufacturer | string | Fabricant de l’appareil avec lequel la mise à jour est compatible. | 1 à 64 caractères, alphanumériques, point et tiret. |
| DeviceModel | string | Modèle de l’appareil avec lequel la mise à jour est compatible. | 1 à 64 caractères, alphanumériques, point et tiret. |

## <a name="hashes-object"></a>Objet Hashes

| Nom | Obligatoire | Type | Description |
| --------- | --------- | --------- | --------- |
| Sha256 | True | string | Hachage encodé en base64 du fichier à l’aide de l’algorithme SHA-256. |

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les [concepts d’importation](./import-concepts.md).

Si vous êtes prêt, suivez le [Guide pratique d’importation](./import-update.md), qui vous guidera pas à pas tout au long du processus d’importation.
