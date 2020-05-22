---
title: Définition de schéma d’application
description: L’application LUIS est représentée dans le `.json` ou `.lu` et comprend toutes les intentions, les entités, les exemples de énoncés, les fonctionnalités et les paramètres.
ms.topic: reference
ms.date: 05/05/2020
ms.openlocfilehash: 21b58f79ffd2baf553c6f8b07daa84473e620f77
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599293"
---
# <a name="app-schema-definition"></a>Définition de schéma d’application

L’application LUIS est représentée dans le `.json` ou `.lu` et comprend toutes les intentions, les entités, les exemples de énoncés, les fonctionnalités et les paramètres.

## <a name="format"></a>Format

Lorsque vous importez et exportez l’application, choisissez `.json` ou `.lu`.

|Format|Information|
|--|--|
|`.json`| Format de programmation standard|
|`.lu`|Pris en charge par les outils [Bot Builder](https://github.com/microsoft/botbuilder-tools/blob/master/packages/Ludown/docs/lu-file-format.md) de Bot Framework.|

## <a name="version-7x"></a>Version 7.x

* Si vous passez à la version 7.x, les entités sont représentées en tant qu’entités issues du Machine Learning imbriquées.
* Prise en charge de la création d’entités issues du Machine Learning imbriquées avec la propriété `enableNestedChildren` sur les API de création suivantes :
    * [Ajouter une étiquette](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c08)
    * [Ajouter une étiquette de lot](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c09)
    * [Examiner les étiquettes](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c0a)
    * [Suggérer des requêtes de point de terminaison pour les entités](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2e)
    * [Suggérer des requêtes de point de terminaison pour les intentions](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2d)

```json
{
  "luis_schema_version": "7.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-6x"></a>Version 6.x

* En passant à la version 6. x, utilisez la nouvelle [entité issue du Machine Learning](reference-entity-machine-learned-entity.md) pour représenter vos entités.

```json
{
  "luis_schema_version": "6.0.0",
  "intents": [
    {
      "name": "None",
      "features": []
    }
  ],
  "entities": [],
  "hierarchicals": [],
  "composites": [],
  "closedLists": [],
  "prebuiltEntities": [],
  "utterances": [],
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "patternAnyEntities": [],
  "regex_entities": [],
  "phraselists": [],
  "regex_features": [],
  "patterns": [],
  "settings": []
}
```

## <a name="version-4x"></a>Version 4.x

```json
{
  "luis_schema_version": "4.0.0",
  "versionId": "0.1",
  "name": "example-app",
  "desc": "",
  "culture": "en-us",
  "tokenizerVersion": "1.0.0",
  "intents": [
    {
      "name": "None"
    }
  ],
  "entities": [],
  "composites": [],
  "closedLists": [],
  "patternAnyEntities": [],
  "regex_entities": [],
  "prebuiltEntities": [],
  "model_features": [],
  "regex_features": [],
  "patterns": [],
  "utterances": [],
  "settings": []
}
```

## <a name="next-steps"></a>Étapes suivantes

* Migrer vers la [V3 des API de création](luis-migration-authoring-entities.md)