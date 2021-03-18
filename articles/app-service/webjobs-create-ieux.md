---
title: Exécuter des tâches en arrière-plan avec WebJobs
description: Découvrez comment utiliser WebJobs pour exécuter des tâches en arrière-plan dans Azure App Service. Choisissez un des différents formats de script et exécutez-les avec des expressions CRON.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;suwatch;pbatum;naren.soni
ms.custom: seodec18
zone_pivot_groups: app-service-webjob
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 0d49323e2bc3c0522b1fb9ad49ffcc14f476e2dc
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/08/2021
ms.locfileid: "102452796"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Exécuter des tâches en arrière-plan avec WebJobs dans Azure App Service

Le concept des [tâches en arrière-plan](./webjobs-create-ieux-conceptual.md) exécutées sur Azure est assuré par WebJobs dans Azure App Service. Découvrez comment déployer des <abbr title="Programme ou script dans la même instance qu’une application web, application API ou application mobile.">WebJobs</abbr> avec le [portail Azure](https://portal.azure.com) pour charger un fichier exécutable ou un script. 

Trois types de tâches web sont pris en charge :

* **Continu** : Démarre immédiatement, en s’exécutant généralement dans une boucle infinie.
* **Planifié** : Démarre à partir du déclencheur planifié.
* **Manuel** : Démarre à partir du déclencheur manuel.

::: zone pivot="webjob-type-continuous"

[!INCLUDE [Continuous](./includes/webjobs-create-ieux-continuous.md)]

::: zone-end

::: zone pivot="webjob-type-scheduled"

[!INCLUDE [Scheduled](./includes/webjobs-create-ieux-scheduled.md)]

::: zone-end

::: zone pivot="webjob-type-manual"

[!INCLUDE [Manual](./includes/webjobs-create-ieux-manual.md)]

::: zone-end
   
## <a name="next-steps"></a><a name="NextSteps"></a>Étapes suivantes

* [En savoir plus sur les tâches en arrière-plan utilisées comme tâches web](./webjobs-create-ieux-conceptual.md)
* [Afficher l’historique des journaux des tâches web](./webjobs-create-ieux-view-log.md)

* Utiliser le [SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) pour simplifier de nombreuses tâches de programmation

* Apprendre à [développer et déployer des tâches web avec Visual Studio](webjobs-dotnet-deploy-vs.md)
