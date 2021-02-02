---
title: Exécuter l’exportation en appelant la commande $export sur API Azure pour FHIR
description: Cet article explique comment exporter des données FHIR en utilisant $export.
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 1/21/2021
ms.author: cavoeg
ms.openlocfilehash: 8ad5ee78a525b3798bbf613168ff74a9e21fe99b
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920255"
---
# <a name="how-to-export-fhir-data"></a>Comment exporter des données FHIR


La fonctionnalité d’exportation en bloc autorise l’exportation de données à partir du serveur FHIR en fonction de la [spécification FHIR](https://hl7.org/fhir/uv/bulkdata/export/index.html). 

Avant d’utiliser $export, vous devez vous assurer que l’API Azure pour FHIR est configurée. Pour configurer les paramètres d’exportation et créer un compte de stockage Azure, consultez la [page Configurer les données d’exportation](configure-export-data.md).

## <a name="using-export-command"></a>Utilisation de la commande $export

Après avoir configuré l’API Azure pour FHIR pour l’exportation, vous pouvez utiliser la commande $export afin d’exporter les données à partir du service. Les données seront stockées dans le compte de stockage que vous avez spécifié lors de la configuration de l’exportation. Pour savoir comment appeler la commande $export dans le serveur FHIR, consultez la documentation sur la [spécification $export HL7 FHIR](https://hl7.org/Fhir/uv/bulkdata/export/index.html). 

L’API Azure pour FHIR prend en charge $export aux niveaux suivants :
* [Système](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---system-level-export) : `GET https://<<FHIR service base URL>>/$export>>`
* [Patient](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---all-patients) : `GET https://<<FHIR service base URL>>/Patient/$export>>`
* [Groupe de patients*](https://hl7.org/Fhir/uv/bulkdata/export/index.html#endpoint---group-of-patients). L’API Azure pour FHIR exporte toutes les ressources associées, mais n’exporte pas les caractéristiques du groupe : `GET https://<<FHIR service base URL>>/Group/[ID]/$export>>`



> [!Note] 
> `Patient/$export` et `Group/[ID]/$export` peuvent exporter les ressources dupliquées si la ressource se trouve dans un compartiment de plusieurs ressources ou dans plusieurs groupes.

En outre, la vérification de l’état d’exportation à l’aide de l’URL renvoyée par l’en-tête Location pendant la mise en file d’attente est prise en charge avec l’annulation du travail d’exportation réel.

## <a name="settings-and-parameters"></a>Paramètres et configurations

### <a name="headers"></a>En-têtes
Deux paramètres d’en-tête doivent être définis pour des travaux $export. Les valeurs sont définies par la [spécification $export](https://hl7.org/Fhir/uv/bulkdata/export/index.html#headers) actuelle.
* **Accept** : application/fhir+json
* **Prefer** : respond-async

### <a name="query-parameters"></a>Paramètres de la requête
L’API Azure pour FHIR prend en charge les paramètres de requête suivants. Tous ces paramètres sont facultatifs :

|Paramètre de requête.        | Défini par la spécification FHIR ?    |  Description|
|------------------------|---|------------|
| \_outputFormat | Oui | Prend actuellement en charge trois valeurs à aligner sur la spécification FHIR : application/fhir+ndjson, application/ndjson ou simplement ndjson. Tous les travaux d’exportation retournent `ndjson` et la valeur transmise n’a aucun effet sur le comportement du code. |
| \_since | Oui | Vous permet d’exporter uniquement les ressources qui ont été modifiées depuis l’heure indiquée. |
| \_type | Oui | Vous permet de spécifier les types de ressources qui seront inclus. Par exemple, \_type=Patient retournera uniquement des ressources relatives aux patients.|
| \_typefilter | Oui | Pour demander un filtrage plus affiné, vous pouvez utiliser \_typefilter avec le paramètre \_type. La valeur du paramètre _typeFilter est une liste séparée par des virgules de requêtes FHIR qui limitent davantage les résultats. |
| \_container | Non |  Spécifie le conteneur dans le compte de stockage configuré où les données doivent être exportées. Si un conteneur est spécifié, les données sont exportées vers celui-ci dans un nouveau dossier portant son nom. Si le conteneur n’est pas spécifié, elles sont exportées vers un nouveau conteneur utilisant le timestamp et l’ID de travail. |


## <a name="next-steps"></a>Étapes suivantes

Dans cet article, vous avez appris à exporter des ressources FHIR à l’aide de la commande $export. Ensuite, apprenez à exporter des données anonymisées :
 
>[!div class="nextstepaction"]
>[Exporter des données anonymisées](de-identified-export.md)
