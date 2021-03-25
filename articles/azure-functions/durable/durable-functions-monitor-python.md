---
title: Moniteurs dans l’extension Fonctions durables (Python) - Azure
description: Découvrez comment implémenter un moniteur d’état à l’aide de l’extension Fonctions durables pour Azure Functions (Python).
author: davidmrdavid
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: azfuncdf
ms.openlocfilehash: 62b3c9bb1c6fd53d9f11227a9d7e774d56859d04
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102434761"
---
# <a name="monitor-scenario-in-durable-functions---github-issue-monitoring-sample"></a>Scénario de surveillance dans l’extension Fonctions durables - Exemple de surveillance d’un problème GitHub

Le modèle de surveillance fait référence à un processus récurrent flexible dans un flux de travail, par exemple l’interrogation jusqu’à ce que certaines conditions soient respectées. Cet article décrit un exemple qui utilise l’extension Fonctions durables pour implémenter la surveillance.

## <a name="prerequisites"></a>Conditions préalables requises

* [Suivez l’article sur le démarrage rapide](quickstart-python-vscode.md)
* [Clonez ou téléchargez les exemples de projets à partir de GitHub](https://github.com/Azure/azure-functions-durable-python/tree/main/samples/)


## <a name="scenario-overview"></a>Présentation du scénario

Cet exemple surveille le nombre de problèmes dans un référentiel GitHub et avertit l’utilisateur s’il existe plus de 3 problèmes ouverts. Vous pouvez utiliser une fonction standard déclenchée par un minuteur pour demander le nombre de problèmes ouverts à intervalles réguliers. Toutefois, cette approche pose le problème de la **gestion de la durée de vie**. Si une seule alerte doit être envoyée, le moniteur doit se désactiver après au moins 3 problèmes détectés. Le modèle de surveillance peut, entre autres avantages, mettre fin à sa propre exécution :

* Les moniteurs s’exécutent selon une périodicité, et non selon des planifications : un déclencheur de minuteur *s’exécute* toutes les heures ; un moniteur *attend* une heure entre les actions. Sauf indication contraire, les actions d’un moniteur ne se superposent pas, ce qui peut être important pour les tâches longues.
* Les moniteurs peuvent avoir des intervalles dynamiques : le délai d’attente peut changer en fonction de certaines conditions.
* Les moniteurs peuvent s’arrêter quand une condition donnée est respectée ou être interrompus par un autre processus.
* Les moniteurs peuvent prendre des paramètres. L’exemple montre comment le même processus de surveillance de référentiel peut être appliqué à n’importe quels référentiel GitHub public et numéro de téléphone demandés.
* Les moniteurs sont évolutifs. Étant donné que chaque moniteur est une instance d’orchestration, il est possible de créer plusieurs moniteurs sans avoir à créer de nouvelles fonctions ou à définir davantage de code.
* Les moniteurs s’intègrent facilement à de plus grands flux de travail. Un moniteur peut être une section d’une fonction d’orchestration plus complexe, ou une [sous-orchestration](durable-functions-sub-orchestrations.md).

## <a name="configuration"></a>Configuration

### <a name="configuring-twilio-integration"></a>Configuration de l’intégration de Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Les fonctions

Cet article explique les fonctions suivantes dans l’exemple d’application :

* `E3_Monitor` : [fonction d’orchestrateur](durable-functions-bindings.md#orchestration-trigger) qui appelle régulièrement `E3_TooManyOpenIssues`. Elle appelle `E3_SendAlert` si la valeur de retour de `E3_TooManyOpenIssues` est `True`.
* `E3_TooManyOpenIssues` : [fonction d’activité](durable-functions-bindings.md#activity-trigger) qui vérifie si un référentiel contient trop de problèmes ouverts. À des fins de démonstration, nous considérons que la limite est atteinte lorsqu’il y a plus de 3 problèmes ouverts.
* `E3_SendAlert` : fonction d’activité qui envoie un SMS via Twilio.

### <a name="e3_monitor-orchestrator-function"></a>Fonction d'orchestrateur E3_Monitor


La fonction **E3_Monitor** utilise le fichier *function.json* standard pour les fonctions d’orchestrateur.

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/function.json)]

Voici le code qui implémente la fonction :

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_Monitor/\_\_init\_\_.py)]


Cette fonction d’orchestrateur effectue les actions suivantes :

1. Obtient le *référentiel* à surveiller et le *numéro de téléphone* auquel il enverra une notification par SMS.
2. Détermine le délai d’expiration du moniteur. Par souci de concision, l’exemple utilise une valeur codée en dur.
3. Appelle **E3_TooManyOpenIssues** pour déterminer s’il y a trop de problèmes ouverts sur le référentiel demandé.
4. S’il y a trop de problèmes, appelle **E3_SendAlert** pour envoyer une notification par SMS au numéro de téléphone demandé.
5. Crée un minuteur durable pour reprendre l’orchestration à l’intervalle d’interrogation suivant. Par souci de concision, l’exemple utilise une valeur codée en dur.
6. Continue de s’exécuter jusqu’à ce que l'heure UTC actuelle passe l’heure d’expiration du moniteur, ou une alerte SMS est envoyée.

Plusieurs instances d’orchestrateur peuvent s’exécuter simultanément en appelant la fonction d’orchestrateur plusieurs fois. Le référentiel à surveiller et le numéro de téléphone auquel envoyer une alerte SMS peuvent être spécifiés. Enfin, notez que la fonction d’orchestrateur n’est *pas* en cours d’exécution en attendant le minuteur, et vous ne serez donc pas facturé.


### <a name="e3_toomanyopenissues-activity-function"></a>Fonction d’activité E3_TooManyOpenIssues

Comme avec d’autres exemples, les fonctions d’activité d’assistance sont des fonctions régulières qui utilisent la liaison de déclencheur `activityTrigger`. La fonction **E3_TooManyOpenIssues** obtient une liste des problèmes actuellement ouverts sur le référentiel et détermine s’il y en a « trop » : plus de 3 dans notre exemple.


Le fichier *function.json* est défini comme suit :

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Et voici l’implémentation.

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/\_\_init\_\_.py)]


### <a name="e3_sendalert-activity-function"></a>Fonction d’activité E3_SendAlert

La fonction **E3_SendAlert** utilise la liaison Twilio pour envoyer un SMS à l’utilisateur final et l’avertir qu’il y a au moins 3 problèmes ouverts à résoudre.


Son fichier *function.json* est simple :

[!code-json[Main](~/samples-durable-functions-python/samples/monitor/E3_TooManyOpenIssues/function.json)]

Et voici le code qui envoie le SMS :

[!code-python[Main](~/samples-durable-functions-python/samples/monitor/E3_SendAlert/\_\_init\_\_.py)]


## <a name="run-the-sample"></a>Exécution de l'exemple

Vous aurez besoin d’un compte [GitHub](https://github.com/). Ce compte vous permet de créer un référentiel public temporaire dans lequel vous pouvez ouvrir des problèmes.

En utilisant les fonctions déclenchées via HTTP incluses dans l’exemple, vous pouvez démarrer l’orchestration en envoyant la requête HTTP POST suivante :

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "repo": "<your github handle>/<a new github repo under your user>", "phone": "+1425XXXXXXX" }
```

Par exemple, si votre nom d’utilisateur GitHub est `foo` et que votre référentiel est `bar`, votre valeur pour `"repo"` doit être `"foo/bar"`.

```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

L’instance **E3_Monitor** démarre et interroge le référentiel fourni pour les problèmes ouverts. Si au moins 3 problèmes ouverts sont détectés, elle appelle une fonction d’activité pour envoyer une alerte ; dans le cas contraire, elle définit un minuteur. Quand le minuteur expire, l’orchestration reprend.

Vous pouvez voir l’activité de l’orchestration en examinant les journaux d’activité de fonction dans le portail Azure Functions.

```
[2020-12-04T18:24:30.007Z] Executing 'Functions.HttpStart' (Reason='This function was programmatically 
called via the host APIs.', Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6)
[2020-12-04T18:24:30.769Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=058e656e-bcb1-418c-95b3-49afcd07bd08)
[2020-12-04T18:24:30.847Z] Started orchestration with ID = '788420bb31754c50acbbc46e12ef4f9c'.
[2020-12-04T18:24:30.932Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=058e656e-bcb1-418c-95b3-49afcd07bd08, Duration=174ms)
[2020-12-04T18:24:30.955Z] Executed 'Functions.HttpStart' (Succeeded, Id=93772f6b-f4f0-405a-9d7b-be9eb7a38aa6, Duration=1028ms)
[2020-12-04T18:24:31.229Z] Executing 'Functions.E3_TooManyOpenIssues' (Reason='(null)', Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3)
[2020-12-04T18:24:31.772Z] Executed 'Functions.E3_TooManyOpenIssues' (Succeeded, Id=6fd5be5e-7f26-4b0b-98df-c3ac39125da3, Duration=555ms)
[2020-12-04T18:24:40.754Z] Executing 'Functions.E3_Monitor' (Reason='(null)', Id=23915e4c-ddbf-46f9-b3f0-53289ed66082)
[2020-12-04T18:24:40.789Z] Executed 'Functions.E3_Monitor' (Succeeded, Id=23915e4c-ddbf-46f9-b3f0-53289ed66082, Duration=38ms)
(...trimmed...)
```

L’orchestration se termine une fois que son délai d’attente est atteint ou que plus de 3 problèmes ouverts sont détectés. Vous pouvez également utiliser l’API `terminate` à l’intérieur d’une autre fonction, ou appeler le Webhook HTTP POST **terminatePostUri** référencé dans la réponse 202 ci-dessus. Pour utiliser le webhook, remplacez `{text}` par la raison de l’arrêt anticipé. L’URL HTTP POST ressemble à peu près à ce qui suit :

```
POST https://{host}/runtime/webhooks/durabletask/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="next-steps"></a>Étapes suivantes

Cet exemple a montré comment utiliser l’extension Fonctions durables pour surveiller l’état d’une source externe à l’aide de [minuteurs durables](durable-functions-timers.md) et d’une logique conditionnelle. L’exemple suivant montre comment utiliser les événements externes et les [minuteurs durables](durable-functions-timers.md) pour gérer l’interaction humaine.

> [!div class="nextstepaction"]
> [Exécuter l’exemple d’interaction humaine](durable-functions-phone-verification.md)
