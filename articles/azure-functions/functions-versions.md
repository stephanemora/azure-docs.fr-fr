---
title: Vue d’ensemble des versions du runtime Azure Functions
description: Azure Functions prend en charge plusieurs versions du runtime. Découvrez les différences entre elles et comment choisir celle qui vous convient.
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 07/29/2018
ms.author: glenga
ms.openlocfilehash: 716f2b537a47c6e721c7393cba340a583c7ed064
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092136"
---
# <a name="azure-functions-runtime-versions-overview"></a>Vue d’ensemble des versions du runtime Azure Functions

 Il existe deux versions principales du runtime Azure Functions : 1.x et 2.x. La version 1.x est destinée à la production. Cet article explique quelles sont les nouveautés de la version 2.x qui est disponible en préversion.

| Runtime | Statut |
|---------|---------|
|1.x|Disponibilité générale (GA)|
|2.x|Préversion<sup>*</sup>|

<sup>*</sup>Pour recevoir des mises à jour importantes sur la version 2.x, y compris des annonces de changements novateurs, regardez le référentiel [Annonces d’Azure App Service](https://github.com/Azure/app-service-announcements/issues).

> [!NOTE] 
> Cet article fait référence au service cloud Azure Functions. Pour plus d’informations sur le produit qui vous permet d’exécuter Azure Functions localement, consultez [Vue d’ensemble du runtime d’Azure Functions](functions-runtime-overview.md).

## <a name="cross-platform-development"></a>Développement multiplateforme

Runtime 1.x prend en charge uniquement le développement et l’hébergement dans le portail ou sur Windows. Runtime 2.x s’exécute sur le .NET Core, ce qui signifie qu’il peut s’exécuter sur toutes les plateformes prises en charge par .NET Core, y compris macOS et Linux. Cela permet un développement multiplateforme et des scénarios d’hébergement qui ne sont pas possibles avec 1.x.

## <a name="languages"></a>Langues

Runtime 2.x utilise un nouveau modèle d’extensibilité de langage. Initialement, JavaScript et Java tirent profit de ce nouveau modèle. Les langages expérimentaux d’Azure Functions 1.x n’ont pas été mis à jour pour utiliser le nouveau modèle et ne sont donc pas pris en charge dans 2.x. Le tableau suivant montre les langages de programmation qui sont pris en charge dans chaque version du runtime.

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

Pour en savoir plus, consultez [Langages pris en charge](supported-languages.md).

## <a name="bindings"></a>Liaisons 

Runtime 2.x utilise un nouveau [modèle d’extensibilité de liaison](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview) qui présente les avantages suivants :

* Prise en charge pour les extensions de liaison de tiers.
* Découplage du runtime et des liaisons. Cela permet de gérer les versions des extensions de liaison et de les publier de façon indépendante. Vous pouvez, par exemple, choisir de mettre à niveau vers une version d’une extension qui s’appuie sur une version plus récente d’un kit de développement logiciel (SDK) sous-jacent.
* Un environnement d’exécution plus léger, où seules les liaisons en cours d’utilisation sont connues et chargées par le runtime.

Toutes les liaisons Azure Functions intégrées ont adopté ce modèle et ne sont plus incluses par défaut, à l’exception du déclencheur du minuteur et du déclencheur HTTP. Ces extensions sont installées automatiquement lorsque vous créez des fonctions par le biais d’outils tels que Visual Studio ou par le biais du portail.

Le tableau suivant indique les liaisons prises en charge dans chaque version du runtime.

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

## <a name="known-issues-in-2x"></a>Problèmes connus dans la version 2.x

Pour plus d’informations sur la prise en charge des liaisons et d’autres écarts fonctionnels dans la version 2.x, consultez [Runtime 2.0 known issues (Problèmes connus dans le runtime 2.0)](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Azure-Functions-runtime-2.0-known-issues).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations, consultez les ressources suivantes :

* [Coder et tester Azure Functions localement](functions-run-local.md)
* [Procédure pour cibler des versions du runtime Azure Functions](set-runtime-version.md)
* [Notes de publication](https://github.com/Azure/azure-functions-host/releases)
