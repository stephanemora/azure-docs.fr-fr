---
title: Langages pris en charge dans Azure Functions
description: Découvrez les langages qui sont pris en charge (Disponibilité générale) et ceux qui sont en version expérimentale ou en préversion.
ms.topic: conceptual
ms.date: 11/27/2019
ms.openlocfilehash: 029ea753439dca3093bf214a5adfb6d58a1fe567
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "74942254"
---
# <a name="supported-languages-in-azure-functions"></a>Langages pris en charge dans Azure Functions

Cet article explique les niveaux de prise en charge offerts pour les langages que vous pouvez utiliser avec Azure Functions.

## <a name="levels-of-support"></a>Niveaux de prise en charge

Il existe trois niveaux de prise en charge :

* **Disposition générale (GA)** : entièrement pris en charge et approuvé pour la production.
* **Préversion** : pas encore pris en charge, mais le statut de disponibilité générale est prévu à l’avenir.
* **Expérimental** : pas pris en charge et peut être abandonné à l’avenir. Aucune garantie quant à une éventuelle préversion ou un passage à l’état de disponibilité générale.

## <a name="languages-by-runtime-version"></a>Langues par version du runtime 

[Trois versions du runtime Azure Functions](functions-versions.md) sont disponibles. Le tableau suivant montre les langages qui sont pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

### <a name="experimental-languages"></a>Langages expérimentaux

Les langages expérimentaux dans la version 1.x ne sont pas très propices à la mise à l’échelle et ne prennent pas en charge toutes les liaisons.

N’utilisez pas les fonctionnalités expérimentales pour tout ce qui est important, car il n’existe aucune prise en charge officielle pour celles-ci. Évitez d’ouvrir des cas de support pour les problèmes liés aux langages expérimentaux. 

Les versions ultérieures du runtime ne prennent pas en charge les langages expérimentaux. La prise en charge de nouveaux langages est ajoutée uniquement lorsque le langage peut être pris en charge dans les environnements de production. 

### <a name="language-extensibility"></a>Extensibilité de langage

À compter de la version 2.x, le runtime est conçu pour offrir une [extensibilité de langage](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Language-Extensibility). Les langages JavaScript et Java dans le runtime 2.x sont générés avec cette extensibilité.

## <a name="next-steps"></a>Étapes suivantes

Pour savoir plus en détails comment développer des fonctions dans les langages pris en charge, voir les ressources suivantes :

+ [Informations de référence pour les développeurs de bibliothèques de classes C#](functions-dotnet-class-library.md)
+ [Informations de référence pour les développeurs de scripts C#](functions-reference-csharp.md)
+ [Informations de référence pour les développeurs Java](functions-reference-java.md)
+ [Informations de référence pour les développeurs JavaScript](functions-reference-node.md)
+ [Informations de référence pour les développeurs PowerShell](functions-reference-powershell.md)
+ [Informations de référence pour les développeurs Python](functions-reference-python.md)
+ [Informations de référence pour les développeurs TypeScript](functions-reference-node.md#typescript)
