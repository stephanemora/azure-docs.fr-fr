---
title: Activité d’échec
titleSuffix: Azure Data Factory & Azure Synapse
description: L’activité d’échec dans Azure Data Factory et Synapse Analytics lève intentionnellement une erreur dans un pipeline
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 95c4a7245a39ad717ad47ac9e8a7c72b2b5365fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059041"
---
# <a name="execute-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Exécuter l’activité d’échec dans des pipelines Azure Data Factory et Synapse Analytics (préversion)

Il peut vous arriver d’être amené à lever intentionnellement une erreur dans un pipeline, par exemple, si une [activité de recherche](control-flow-lookup-activity.md) ne retourne aucune donnée, ou si une [activité personnalisée](transform-data-using-dotnet-custom-activity.md), bien qu’ayant retourné 200 réponses, a levé une erreur interne. Quelle que soit la raison, vous pouvez désormais utiliser une activité d’échec dans un pipeline, ainsi que personnaliser le code d’erreur et le message d’erreur.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>Propriétés type

Propriété | Description | Valeurs autorisées | Obligatoire
-------- | ----------- | -------------- | --------
name | Nom de l’activité `Fail`. | String | Oui
type | Doit être défini sur **Échec**. | String | Oui
message | Message d’erreur affiché dans l’activité d’échec. Peut être du contenu dynamique évalué au moment de l’exécution du runtime. | String | Oui
errorCode | Code d’erreur catégorisant le type d’erreur de l’activité d’échec. Peut être du contenu dynamique évalué au moment de l’exécution du runtime. | String | Oui

## <a name="understand-fail-activity-error-code"></a>Comprendre le code d’erreur de l’activité d’échec

En général, le code d’erreur et le message d’erreur d’une activité d’échec sont définis par les clients. Pour comprendre les significations spécifiques des codes d’erreur, contactez le développeur de pipeline. Toutefois, dans les cas marginaux suivants, ADF définit le code d’erreur et/ou les messages d’erreur.

Description de la situation | Message d’erreur | Code d'erreur
-------- | ----------- | --------------
Contenu (Dynamique) dans `message` et `errorCode` interprété correctement | Message d’erreur défini par l’utilisateur | Code d’erreur défini par l’utilisateur
Le contenu dans `message` et `errorCode` ne peut pas être interprété | 'N’a pas pu interpréter le message d’échec ou le code d’erreur _<activity_name>_ | `ErrorCodeNotString`
Le contenu dynamique dans `message` ne peut pas être interprété en tant que chaîne | 'Le paramètre de message d’échec _<activity_name>_ n’a pas pu être interprété comme une chaîne' | Code d’erreur défini par l’utilisateur
Le contenu dynamique dans `message` correspond à une valeur null, une chaîne vide ou des espaces blancs | 'N’a pas pu interpréter le message d’échec ou le code d’erreur _<activity_name>_ ' | Code d’erreur défini par l’utilisateur
Le contenu dynamique dans `errorCode` ne peut pas être interprété en tant que chaîne | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString`
Le contenu dynamique dans `errorCode` correspond à une valeur null, une chaîne vide ou des espaces blancs | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString`
La valeur pour `message` ou `errorCode` fournie par l’utilisateur n’est pas convertible en chaîne * | Le pipeline __échoue__ avec l’erreur 'valeur non valide de la propriété <`errorCode`/`message`>'
Champ `message` manquant * | 'Le message d’échec n’a pas été fourni' | Code d’erreur défini par l’utilisateur
Champ `errorCode` manquant * | Message d’erreur défini par l’utilisateur | `ErrorCodeNotString`

Les situations marquées d’un astérisque * ne devraient pas se produire si le pipeline est développé avec l’interface utilisateur web de Data Factory.

## <a name="next-steps"></a>Étapes suivantes

Consultez d’autres activités de flux de contrôle prises en charge, à savoir :

- [Activité IfCondition](control-flow-if-condition-activity.md)
- [Activité d’exécution du pipeline](control-flow-execute-pipeline-activity.md)
- [Pour chaque activité](control-flow-for-each-activity.md)
- [Activité d’obtention des métadonnées](control-flow-get-metadata-activity.md)
- [Activité de recherche](control-flow-lookup-activity.md)
- [Activité Web](control-flow-web-activity.md)
- [Activité jusqu’à](control-flow-until-activity.md)
