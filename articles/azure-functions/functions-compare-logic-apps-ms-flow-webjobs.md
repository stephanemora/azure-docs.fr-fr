---
title: Comparer Flow, Logic Apps, Functions et WebJobs - Azure
description: 'Comparez les services de cloud computing Microsoft qui sont optimisés pour les tâches d’intégration : Flow, Logic Apps, Functions et WebJobs.'
services: functions,app-service\logic
documentationcenter: na
author: tdykstra
manager: cfowler
tags: ''
keywords: microsoft flow, flow, logic apps, azure functions, functions, azure webjobs, webjobs, traitement d’événements, calcul dynamique, architecture sans serveur
ms.service: functions
ms.devlang: multiple
ms.topic: overview
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/09/2018
ms.author: tdykstra
ms.custom: mvc
ms.openlocfilehash: 6df97a40be7bf1c437c5228006d114ace768f8ca
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2018
---
# <a name="compare-flow-logic-apps-functions-and-webjobs"></a>Comparer Flow, Logic Apps, Functions et WebJobs

Cet article compare les services de cloud computing Microsoft suivants :

* [Microsoft Flow](https://flow.microsoft.com/)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/web-sites-create-web-jobs.md)

Tous ces services peuvent résoudre des problèmes d’intégration et automatiser des processus métier. Ils peuvent tous définir des entrées, des actions, des conditions et des sorties. Vous pouvez exécuter chacun d’eux selon une planification ou à l’aide d’un déclencheur. Toutefois, chaque service présente des avantages uniques, et cet article explique les différences.

## <a name="compare-microsoft-flow-and-azure-logic-apps"></a>Comparer Microsoft Flow et Azure Logic Apps

Flow et Logic Apps sont des services d’intégration *orientés concepteur* permettant de créer des flux de travail. Ils s’intègrent à différentes applications SaaS et d’entreprise. 

Flow est basé sur Logic Apps. Ils partagent le même concepteur de flux de travail et les mêmes [connecteurs](../connectors/apis-list.md). 

Flow permet à n’importe quel employé de bureau d’effectuer des intégrations simples (par exemple, un processus d’approbation dans une bibliothèque de documents SharePoint) sans solliciter les développeurs ni le service informatique. En revanche, les applications de Logic Apps prennent quant à elles en charge des intégrations avancées (par exemple des processus B2B) nécessitant des pratiques DevOps et de sécurité au niveau de l’entreprise. Il est courant qu’un flux de travail métier se complexifie au fil du temps. Par conséquent, vous pouvez commencer par un flux et le convertir ensuite en application logique selon vos besoins.

Le tableau suivant vous aide à déterminer si Flow ou Logic Apps convient le mieux à une intégration donnée.

|  | Flux | Logic Apps |
| --- | --- | --- |
| Utilisateurs |Employés de bureau, utilisateurs de l’entreprise, administrateurs SharePoint |Intégrateurs et développeurs professionnels, professionnels de l’informatique |
| Scénarios |Libre-service |Intégrations avancées |
| Outil de conception |Dans le navigateur et application mobile, interface utilisateur uniquement |Dans le navigateur et [Visual Studio](../logic-apps/logic-apps-deploy-from-vs.md), [mode Code](../logic-apps/logic-apps-author-definitions.md) disponible |
| Application Lifecycle Management (ALM) |Concevez et testez au sein d’environnements hors production, passez en production lorsque vous êtes prêt. |DevOps : contrôle de code source, tests, support, et automatisation et gestion simplifiée dans [Gestion des ressources Azure](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md) |
| Expérience administrateur |Gérer les stratégies des environnements Flow et de prévention contre la perte des données, suivre la gestion des licences [https://admin.flow.microsoft.com](https://admin.flow.microsoft.com) |Gérer les groupes de ressources, les connexions, la gestion des accès et la connexion [https://portal.azure.com](https://portal.azure.com) |
| Sécurité |Journaux d’audit de sécurité et conformité Office 365, prévention contre la perte de données, [chiffrement au repos](https://wikipedia.org/wiki/Data_at_rest#Encryption) pour les données sensibles, etc. |Garantie de sécurité d’Azure : [Azure Security](https://www.microsoft.com/trustcenter/Security/AzureSecurity), [Security Center](https://azure.microsoft.com/services/security-center/), [journaux d’audit](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/), etc. |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Comparer Azure Functions et Azure Logic Apps

Functions et Logic Apps sont des services Azure qui proposent des charges de travail sans serveur. Azure Functions est un service de calcul sans serveur, alors qu’Azure Logic Apps fournit des flux de travail sans serveur. Ces services peuvent créer des *orchestrations* complexes. Une orchestration est une collection de fonctions ou d’étapes, appelées *actions* dans Logic Apps, qui sont exécutées pour accomplir une tâche complexe. Par exemple, pour traiter un lot de commandes vous pourrez exécuter plusieurs instances d’une fonction en parallèle, attendre que toutes les instances se terminent et exécuter une fonction qui calcule un résultat de l’agrégat.

Pour Azure Functions, vous développez des orchestrations en écrivant du code et en utilisant [l’extension Fonctions durables ](durable-functions-overview.md) (en préversion). Pour Logic Apps, vous créez des orchestrations en utilisant une interface graphique utilisateur ou en modifiant des fichiers config.

Vous pouvez mélanger et associer les services lorsque vous créez une orchestration, en appelant des fonctions à partir d’applications logiques et inversement. Vous pouvez opter pour l’un ou l’autre des services pour créer chacune de vos orchestrations en fonction des capacités de ceux-ci ou de vos préférences. Le tableau suivant répertorie certaines des principales différences entre ces services :
 
|  | Fonctions durables | Logic Apps |
| --- | --- | --- |
| Développement | Orienté code (impératif) | Orienté concepteur (déclaratif) |
| Connectivité | [Environ une douzaine de types de liaison intégrés](functions-triggers-bindings.md#supported-bindings), écriture de code pour les liaisons personnalisées | [Grande collection de connecteurs](../connectors/apis-list.md), [Enterprise Integration Pack pour les scénarios B2B](../logic-apps/logic-apps-enterprise-integration-overview.md), [intégration de connecteurs personnalisés](../logic-apps/custom-connector-overview.md) |
| Actions | Chaque activité est une fonction Azure ; écriture de code pour les fonctions de l’activité |[Grande collection d’actions prédéfinies](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| Surveillance | [Azure Application Insights](../application-insights/app-insights-overview.md) | [Portail Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Operations Management Suite](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md), [Log Analytics](../logic-apps/logic-apps-monitor-your-logic-apps.md)|
| gestion | [API REST](durable-functions-http-api.md), [Visual Studio](https://docs.microsoft.com/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Portail Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), [API REST](https://docs.microsoft.com/rest/api/logic/), [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp/?view=azurermps-5.6.0), [Visual Studio](https://docs.microsoft.com/azure/logic-apps/manage-logic-apps-with-visual-studio) |
| Contexte d’exécution | Exécution possible [en local](functions-runtime-overview.md) ou dans le cloud. | Exécution uniquement dans le cloud.|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Comparer Functions et WebJobs

Comme Azure Functions, Azure App Service WebJobs avec le Kit de développement logiciel (SDK) WebJobs est un service d’intégration *orienté code* conçu pour les développeurs. Les deux reposent sur [Azure App Service](../app-service/app-service-web-overview.md) et prennent en charge des fonctionnalités telles que [l’intégration du contrôle de code source](../app-service/app-service-continuous-deployment.md), [l’authentification](../app-service/app-service-authentication-overview.md) et la [surveillance avec l’intégration Application Insights](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs et Kit de développement logiciel (SDK) WebJobs

La fonctionnalité *WebJobs* d’App Service vous permet d’exécuter un script ou du code dans le contexte d’une application web App Service. Le *Kit de développement logiciel (SDK) WebJobs* est une infrastructure conçue pour les WebJobs qui simplifie le code que vous écrivez pour répondre aux événements dans les services Azure. Par exemple, vous pouvez répondre à la création d’un objet blob d’image dans Stockage Azure en créant une image miniature. Le Kit de développement logiciel (SDK) WebJobs s’exécute comme une application de console .NET, que vous pouvez déployer sur un WebJob. 

Les WebJobs et le Kit de développement logiciel (SDK) WebJobs fonctionnent mieux ensemble, mais vous pouvez utiliser des WebJobs sans Kit de développement logiciel (SDK) WebJobs et vice versa. Un WebJob peut exécuter n’importe quel programme ou script s’exécutant dans le bac à sable App Service. Une application de console de Kit de développement logiciel (SDK) WebJobs peut s’exécuter là où les applications de console s’exécutent, tels que des serveurs locaux.

### <a name="comparison-table"></a>Tableau de comparaison

Azure Functions repose sur le Kit de développement logiciel (SDK) WebJobs, donc il partage beaucoup de déclencheurs d’événements et de connexions à d’autres services Azure. Voici quelques facteurs à prendre en compte quand vous devez choisir entre Azure Functions et WebJobs avec le Kit de développement logiciel (SDK) WebJobs :

|  | Functions | WebJobs avec le Kit de développement logiciel (SDK) WebJobs |
| --- | --- | --- |
|[Modèle d’application sans serveur](https://azure.microsoft.com/overview/serverless-computing/) avec [mise à l’échelle automatique](functions-scale.md#how-the-consumption-plan-works)|✔||
|[Développement et test dans un navigateur](functions-create-first-azure-function.md) |✔||
|[Paiement à l’utilisation](functions-scale.md#consumption-plan)|✔||
|[Intégration avec Logic Apps](functions-twitter-email.md)|✔||
| Événements déclencheurs |[Minuteur](functions-bindings-timer.md)<br>[Objets blob et files d’attente Stockage Azure](functions-bindings-storage-blob.md)<br>[Files d’attente et rubriques Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Minuteur](functions-bindings-timer.md)<br>[Objets blob et files d’attente Stockage Azure](functions-bindings-storage-blob.md)<br>[Files d’attente et rubriques Azure Service Bus](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Système de fichiers](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| Langues prises en charge  |C#<br>F#<br>JavaScript<br>Java (préversion) |C#<sup>1</sup>|
|Gestionnaires de package|NPM et NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (sans le Kit de développement logiciel (SDK) WebJobs) prend en charge C#, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python, etc. Cette liste n’est pas exhaustive ; un WebJob peut exécuter n’importe quel programme ou script pouvant s’exécuter dans le bac à sable App Service.

<sup>2</sup> WebJobs (sans le Kit de développement logiciel (SDK) WebJobs) prend en charge NPM et NuGet.

### <a name="summary"></a>Résumé

Azure Functions permet au développeur d’être plus productif et offre davantage d’options de langage de programmation, d’environnement de développement, d’intégration de service Azure et de tarification. Pour la plupart des scénarios, Azure Functions est le meilleur choix.

Voici deux scénarios pour lesquels WebJobs peut être le meilleur choix :

* Vous avez besoin de davantage de contrôle sur le code qui écoute les événements, l’objet `JobHost`. Azure Functions offre un nombre limité de méthodes de personnalisation du comportement `JobHost` dans le fichier [host.json](functions-host-json.md). Parfois, vous devez effectuer les opérations qui ne peuvent pas être spécifiées par une chaîne dans un fichier JSON. Par exemple, seul le Kit de développement logiciel (SDK) WebJobs vous permet de configurer une stratégie de nouvelles tentatives personnalisée pour Stockage Azure.
* Vous avez une application App Service pour laquelle vous souhaitez exécuter des extraits de code, et vous voulez les gérer ensemble dans le même environnement DevOps.

Pour d’autres scénarios où vous souhaitez exécuter des extraits de code pour l’intégration de services Azure ou tiers, préférez Azure Functions à WebJobs avec le Kit de développement logiciel (SDK) WebJobs.

<a name="together"></a>

## <a name="flow-logic-apps-functions-and-webjobs-together"></a>Flow, Logic Apps, Functions et WebJobs ensemble

Vous n’êtes pas obligé de choisir un seul de ces services ; ils s’intègrent les uns aux autres, comme avec les services externes.

Un flux peut appeler une application logique. Une application logique peut appeler une fonction, et une fonction peut appeler une application logique. Consultez par exemple [Créer une fonction qui s’intègre avec Azure Logic Apps](functions-twitter-email.md).

L’intégration entre Flow, Logic Apps et Functions continue de s’améliorer au fil du temps. Vous pouvez créer quelque chose dans un service et l’utiliser dans les autres services.

## <a name="next-steps"></a>Étapes suivantes

Commencez en créant votre premier flux, application logique ou application de fonction. Cliquez sur l’un des liens suivants :

* [Prise en main de Microsoft Flow](https://flow.microsoft.com/en-us/documentation/getting-started/)
* [Créer une application logique](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Créer votre première fonction Azure](functions-create-first-azure-function.md)

Vous pouvez également obtenir des informations supplémentaires sur ces services d’intégration en cliquant sur les liens suivants :

* [Leveraging Azure Functions &amp; Azure App Service for integration scenarios (Tirer parti d’Azure Functions et Azure App Service pour les scénarios d’intégration), par Christopher Anderson](http://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple (Les intégrations simplifiées), par Charles Lamanna](http://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Diffusion web sur Logic Apps](http://aka.ms/logicappslive)
* [Forum Aux Questions sur Microsoft Flow](https://flow.microsoft.com/documentation/frequently-asked-questions/)
