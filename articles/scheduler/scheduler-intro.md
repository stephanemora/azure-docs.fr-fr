---
title: Présentation d’Azure Scheduler | Microsoft Docs
description: Découvrez comment créer, planifier et exécuter des travaux automatisés qui appellent des services à l’intérieur ou à l’extérieur d’Azure
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2f418a78f80d65cbb784685804a4cc6790c28b99
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300898"
---
# <a name="what-is-azure-scheduler"></a>Présentation d’Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) remplace Azure Scheduler, qui est en phase de [mise hors service](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date). Pour poursuivre les travaux que vous avez configurés dans Scheduler, veuillez [migrer vers Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) dès que possible.

[Azure Scheduler](https://azure.microsoft.com/services/scheduler/) vous permet de créer des [travaux](../scheduler/scheduler-concepts-terms.md) qui s’exécutent dans le cloud en décrivant les actions de manière déclarative. Le service planifie et exécute ensuite ces actions automatiquement. Par exemple, vous pouvez appeler des services à l’intérieur et à l’extérieur d’Azure, notamment des points de terminaison HTTP ou HTTPS, et poster des messages sur des files d’attente Stockage Azure, ainsi que sur des files d’attente ou des rubriques Azure Service Bus. Vous pouvez exécuter des travaux immédiatement ou plus tard. Scheduler prend facilement en charge les [planifications complexes et la périodicité avancée](../scheduler/scheduler-advanced-complexity.md). Scheduler spécifie le moment où les travaux doivent être exécutés, conserve un historique des résultats des travaux pour que vous puissiez le consulter, et planifie l’exécution des charges de travail de manière prévisible et fiable.

Bien que vous puissiez utiliser Scheduler pour créer, gérer et exécuter des charges de travail planifiées, Scheduler n’héberge pas les charges de travail et n’exécute pas le code. Le service *appelle* uniquement les services ou le code hébergé ailleurs, par exemple, dans Azure, localement ou chez un autre fournisseur. Scheduler peut appeler via HTTP et HTTPS, une file d’attente de stockage, une file d’attente Service Bus ou une rubrique Service Bus. Pour créer, gérer et planifier des travaux, vous pouvez utiliser le [portail Azure](../scheduler/scheduler-get-started-portal.md), du code, l’[API REST de Scheduler](https://docs.microsoft.com/rest/api/scheduler/) ou les [informations de référence sur les applets de commande PowerShell d’Azure Scheduler](scheduler-powershell-reference.md). Par exemple, vous pouvez créer, afficher, mettre à jour, gérer ou supprimer des travaux et des [collections de travaux](../scheduler/scheduler-concepts-terms.md) par programmation à l’aide de scripts et dans le Portail Azure.

D’autres fonctionnalités de planification Azure utilisent également Scheduler en arrière-plan, par exemple, [Azure WebJobs](../app-service/webjobs-create.md), qui est une fonctionnalité [Web Apps](https://azure.microsoft.com/services/app-service/web/) dans Azure App Service. Vous pouvez gérer la communication de ces actions à l’aide de l’[API REST de Scheduler](https://docs.microsoft.com/rest/api/scheduler/). permet de gérer la communication de ces actions.

Voici quelques scénarios où Scheduler peut vous aider :

* **Exécuter des actions d’application récurrentes** : par exemple, collecter périodiquement des données de Twitter dans un flux.

* **Effectuer une maintenance quotidienne** : par exemple, nettoyer quotidiennement les journaux d’activité, faire des sauvegardes et effectuer d’autres tâches de maintenance. 

  Par exemple, en tant qu’administrateur, vous pouvez être amené à sauvegarder votre base de données à 1 h 00 tous les jours, pendant les neuf prochains mois.

## <a name="next-steps"></a>Étapes suivantes

* [Bien démarrer avec Scheduler dans le portail Azure](scheduler-get-started-portal.md)
* En savoir plus sur les [plans et la facturation pour Azure Scheduler](scheduler-plans-billing.md)
* Découvrez [comment créer des planifications complexes et une périodicité avancée avec Azure Scheduler](scheduler-advanced-complexity.md)