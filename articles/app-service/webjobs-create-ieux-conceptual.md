---
title: Tâche web et tâches en arrière-plan sur Azure
description: Découvrez les tâches web.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 7714b090399b0b184e2e216ff6da7b10f2bf4386
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452269"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Les tâches web exécutent des tâches en arrière-plan dans Azure App Service

Cet article explique comment déployer WebJobs à l’aide du [portail Azure](https://portal.azure.com) pour charger un fichier exécutable ou un script. Pour plus d’informations sur la façon de développer et de déployer WebJobs à l’aide de Visual Studio, consultez [Déploiement de WebJobs à l’aide de Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Vue d’ensemble
WebJobs est une fonctionnalité [Azure App Service](index.yml) qui vous permet d’exécuter un programme ou un script dans une même instance, en tant qu’application web, application API ou application mobile. L’utilisation des tâches web n’entraîne aucun coût supplémentaire.

> [!IMPORTANT]
> WebJobs n’est pas encore pris en charge pour App Service sur Linux.

Le SDK Azure WebJobs peut être utilisé avec WebJobs pour simplifier de nombreuses tâches de programmation. Pour plus d’informations, consultez [Présentation du Kit de développement logiciel (SDK) WebJobs Azure](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions fournit une autre façon d’exécuter des programmes et des scripts. Pour obtenir une comparaison entre WebJobs et Functions, consultez [Choisir entre Flow, Logic Apps, Functions et WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Types de tâches web

Le tableau suivant décrit les différences entre une tâche Web *continue* et une tâche web *déclenchée*.


|Continue  |Déclenchée  |
|---------|---------|
| Démarre immédiatement lorsque la tâche web est créée. Pour empêcher la tâche de se terminer, le programme ou le script est généralement exécuté à l’intérieur d’une boucle sans fin. Si la tâche se termine, vous pouvez la redémarrer. | Démarre uniquement en cas de déclenchement manuel ou selon une planification. |
| S’exécute sur toutes les instances sur lesquelles l’application web s’exécute. Vous pouvez limiter la tâche web à une seule instance. |S’exécute sur une seule instance sélectionnée par Azure pour l’équilibrage de charge.|
| Prend en charge le débogage à distance. | Ne prend pas en charge le débogage à distance.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Ajouter une tâche web au contrôle de code source

Si vous avez configuré un contrôle de code source avec votre application, les tâches web doivent être déployées dans le cadre de l’intégration du contrôle de code source. Une fois que le contrôle de code source est configuré avec votre application, une tâche web ne peut pas être ajoutée à partir du portail Azure.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Types de fichier pris en charge pour les scripts ou les programmes

Les types de fichiers suivants sont pris en charge :

* .cmd, .bat, .exe (en utilisant une commande Windows)
* .ps1 (en utilisant PowerShell)
* .sh (en utilisant un interpréteur de commandes)
* .php (en utilisant PHP)
* .py (en utilisant Python)
* .js (en utilisant Node.js)
* .jar (en utilisant Java)

## <a name="next-steps"></a>Étapes suivantes

* En savoir plus sur la [création d’une tâche web](./webjobs-create-ieux.md)
* Afficher l’historique des journaux de [tâches web](./webjobs-create-ieux-view-log.md)
