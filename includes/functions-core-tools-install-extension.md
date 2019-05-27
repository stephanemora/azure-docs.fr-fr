---
title: Fichier Include
description: Fichier Include
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66131460"
---
Offres groupées d’extension apporter toutes les liaisons sont publiés par l’équipe Azure Functions disponible via un paramètre dans le *host.json* fichier. Pour un développement local, assurez-vous d’avoir la dernière version de [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

Pour utiliser des offres groupées d’extension, mettre à jour le *host.json* fichier à inclure l’entrée suivante pour `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- Le `id` propriété fait référence à l’espace de noms pour les offres d’extension Microsoft Azure Functions.
- Le `version` fait référence à la version du bundle.

Incrément de versions bundle sous forme de packages dans les modifications de l’offre groupée. Modifications de la version principale produisent uniquement lorsque les packages dans le regroupement déplacent une version majeure. Le `version` propriété utilise le [notation d’intervalle pour la spécification de plages de versions](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Le runtime Functions récupère toujours la version autorisée maximale définie par l’intervalle ou une plage de versions.

Une fois que vous référencez les regroupements d’extension dans votre projet, toutes les liaisons par défaut sont disponibles pour vos fonctions. Les liaisons disponibles dans le [offre d’extension](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) sont :

|Package  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|