---
title: Escalader un travail dans le routeur de travaux
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les kits SDK Azure Communication Services pour escalader un travail
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 3ed75c4e418e5e7494502e4f70d3c5419a5b162c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176032"
---
# <a name="escalate-a-job"></a>Escalader un travail

Ce guide vous montre comment escalader un travail dans une file d’attente en utilisant une stratégie d’exception.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Facultatif : suivez le guide de démarrage rapide pour [commencer à utiliser le routeur de travaux](../../quickstarts/router/get-started-router.md).
- Facultatif : passez en revue le [guide pratique en matière de classification des travaux](job-classification.md).

## <a name="escalation-overview"></a>Vue d’ensemble de l’escalade

L’escalade peut prendre plusieurs formes, comme le déplacement d’un travail dans une file d’attente différente et/ou la spécification d’une priorité plus élevée. Les travaux sont distribués aux Workers par ordre de priorité. Dans ce guide pratique, nous allons utiliser une stratégie d’escalade et une stratégie de classification pour atteindre cet objectif.

## <a name="exception-policy-configuration"></a>Configuration d’une stratégie d’exception

Créez une stratégie d’exception, à attacher à la file d’attente régulière, qui est déclenchée par un minuteur et prend l’action du travail en cours de reclassification.

```csharp
// create the exception policy
await client.SetExceptionPolicyAsync(
    id: "Escalate_XBOX_Policy",
    name: "Add escalated label and reclassify XBOX Job requests after 5 minutes",
    rules: new List<ExceptionRule>()
    {
        new (
            id: "Escalated_Rule",
            trigger: new WaitTimeExceptionTrigger(TimeSpan.FromMinutes(5)),
            actions: new List<ExceptionAction>
            {
                new ReclassifyExceptionAction("EscalateReclassifyExceptionAction")
                {
                    LabelsToUpsert = new LabelCollection(
                        new Dictionary<string, object>
                    {
                        ["Escalated"] = true,
                    })
                }
            }
        )
    });
```

## <a name="classification-policy-configuration"></a>Configuration de la stratégie de classification

Créez une stratégie de classification pour gérer la nouvelle étiquette ajoutée au travail. Cette stratégie évalue l’étiquette `Escalated` et affecte le travail à l’une ou l’autre des files d’attente. La stratégie utilise également [RulesEngine](../../concepts/router/router-rule-concepts.md) pour augmenter la priorité du travail de `1` à `10`.

```csharp
await client.SetClassificationPolicyAsync(
    id: "Classify_XBOX_Voice_Jobs",
    name: "Classify XBOX Voice Jobs",
    queueSelector: new QueueIdSelector(
        new ExpressionRule(
            "If(job.Escalated = true, \"XBOX_Queue\", \"XBOX_Escalation_Queue\")")),
    workerSelectors: null,
    prioritizationRule: new ExpressionRule("If(job.Escalated = true, 10, 1)"),
    fallbackQueueId: "Default");
```

## <a name="queue-configuration"></a>Configuration de la file d’attente

Créez les files d’attente nécessaires pour les travaux réguliers et escaladés, puis affectez la stratégie d’exception à la file d’attente régulière.

> [!NOTE]
> Cette étape part du principe que vous avez déjà créé une stratégie de distribution nommée `Round_Robin_Policy`.

```csharp
// create a queue for regular Jobs and attach the exception policy
await client.SetQueueAsync(
    id: "XBOX_Queue",
    name: "XBOX Queue",
    distributionPolicyId: "Round_Robin_Policy",
    exceptionPolicyId: "XBOX_Escalate_Policy"
);

// create a queue for escalated Jobs
await client.SetQueueAsync(
    id: "XBOX_Escalation_Queue",
    name: "XBOX Escalation Queue",
    distributionPolicyId: "Round_Robin_Policy"
);
```

## <a name="job-lifecycle"></a>Cycle de vie de tâche

Quand vous soumettez le travail, spécifiez l’ID de la stratégie de classification comme suit. Dans cet exemple particulier, un Worker avec une étiquette appelée `XBOX_Hardware`, dont la valeur est supérieure ou égale à `7`, doit être trouvé.

```csharp
await client.CreateJobWithClassificationPolicyAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    classificationPolicyId: "Classify_XBOX_Voice_Jobs",
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "XBOX_Hardware",
            @operator: LabelOperator.GreaterThanEqual,
            value: 7)
    }
);
```

Les étapes suivantes du cycle de vie sont effectuées une fois la configuration terminée et le travail prêt à être soumis :

1. Le travail est envoyé au routeur de travaux. Dans la mesure où une stratégie de classification est attachée, il est évalué et produit un `RouterJobReceived` et un `RouterJobClassified`.
2. Ensuite, le minuteur de 5 minutes démarre et se déclenche si aucun Worker ne peut être affecté. Dans l’hypothèse où aucun Worker n’est inscrit, un `RouterJobExceptionTriggered` et un autre `RouterJobClassified` sont produits.
3. À ce stade, le travail se trouve dans la file d’attente `XBOX_Escalation_Queue` et la priorité est définie sur `10`.