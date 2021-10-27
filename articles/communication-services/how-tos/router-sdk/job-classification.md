---
title: Classifier un travail
titleSuffix: An Azure Communication Services how-to guide
description: Utiliser les kits SDK Azure Communication Services pour modifier les propriétés d’un travail
author: jasonshave
ms.author: jassha
ms.service: azure-communication-services
ms.topic: how-to
ms.date: 10/14/2021
ms.custom: template-how-to
ms.openlocfilehash: 0648334ef4baef6c753afa222a5532356ad67590
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075081"
---
# <a name="classifying-a-job"></a>Classification d’un travail

Découvrez comment utiliser une stratégie de classification dans le routeur de travaux pour résoudre dynamiquement la file d’attente et la priorité tout en liant également des sélecteurs de Worker à un travail.

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Une ressource Communication Services déployée. [Créez une ressource Communication Services](../../quickstarts/create-communication-resource.md).
- Facultatif : suivez le Guide de démarrage rapide pour [commencer à utiliser le routeur de travaux](../../quickstarts/router/get-started-router.md)

## <a name="static-classification"></a>Classification statique

Quand vous créez un travail avec le kit SDK, spécifiez uniquement les sélecteurs de file d’attente, de priorité et de travail. Cette méthode est connue sous le nom de **classification statique**. L’exemple suivant place un travail dans `XBOX_DEFAULT_QUEUE` avec une priorité de `1` et exige que les Workers aient une compétence `XBOX_Hardware` supérieure ou égale à `5`.

> [!NOTE]
> Un travail peut être [reclassifié après envoi](#reclassify-a-job-after-submission), même s’il a été initialement créé sans stratégie de classification. Dans ce cas, le routeur de travaux évalue le comportement de la stratégie par rapport aux **étiquettes** et applique les ajustements nécessaires aux sélecteurs de file d’attente, de priorité et de travail.

```csharp
var job = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "12345",
    queueId: queue.Value.Id,
    priority: 1,
    workerSelectors: new List<LabelSelector>
    {
        new (
            key: "Location",
            @operator: LabelOperator.Equal,
            value: "Edmonton")
    });

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="dynamic-classification"></a>Classification dynamique

Comme décrit ci-dessus, un moyen simple d’envoyer un travail consiste à spécifier les sélecteurs de file d’attente, de priorité et de travail lors de l’envoi. Dans ce cas, l’expéditeur doit avoir connaissance de ces caractéristiques. Pour éviter que l’expéditeur ait des connaissances explicites sur le fonctionnement interne du comportement du routeur de travaux, l’expéditeur peut spécifier une **stratégie de classification** avec une collection **d’étiquettes** génériques pour invoquer le comportement dynamique.

### <a name="create-a-classification-policy"></a>Créer une stratégie de classification

La stratégie de classification suivante utilise le langage à faible code [PowerFx](https://powerapps.microsoft.com/en-us/blog/what-is-microsoft-power-fx/) pour sélectionner la file d’attente et la priorité. L’expression tente de faire correspondre l’étiquette du travail appelée `Region``NA`. Par conséquent, si le travail est trouvé, il est placé dans `XBOX_NA_QUEUE`, dans le cas contraire, il est placé dans `XBOX_DEFAULT_QUEUE`.  Si la file d’attente `XBOX_DEFAULT_QUEUE` est également introuvable, le travail est automatiquement envoyé à la file d’attente de secours `DEFAULT_QUEUE`, comme défini par `fallbackQueueId`.  En outre, la priorité est `10` si une étiquette appelée `Hardware_VIP` a été mise en correspondance. Dans le cas contraire, la priorité est `1`.

```csharp
var policy = await client.SetClassificationPolicyAsync(
    id: "XBOX_NA_QUEUE_Priority_1_10",
    name: "Select XBOX Queue and set priority to 1 or 10",
    queueSelector: new QueueIdSelector(
        new ExpressionRule("If(job.Region = \"NA\", \"XBOX_NA_QUEUE\", \"XBOX_DEFAULT_QUEUE\")")
    ),
    workerSelectors: new List<LabelSelectorAttachment>
    {
        new StaticLabelSelector(
            new LabelSelector(
                key: "Language",
                @operator: LabelOperator.Equal,
                value: "English")
        )
    },
    prioritizationRule: new ExpressionRule("If(job.Hardware_VIP = true, 10, 1)"),
    fallbackQueueId: "DEFAULT_QUEUE"
);
```

### <a name="submit-the-job"></a>Envoi du travail

L’exemple suivant entraîne l’évaluation des étiquettes de travail par la stratégie de classification. Le résultat place le travail dans la file d’attente appelée `XBOX_NA_QUEUE` et définit la priorité sur `1`.

```csharp
var dynamicJob = await client.CreateJobAsync(
    channelId: ManagedChannels.AcsVoiceChannel,
    channelReference: "my_custom_reference_number",
    classificationPolicyId: "XBOX_NA_QUEUE_Priority_1_10",
    labels: new LabelCollection()
    {
        { "Region", "NA" },
        { "Caller_Id", "tel:7805551212" },
        { "Caller_NPA_NXX", "780555" },
        { "XBOX_Hardware", 7 }
    }
);

// returns a new GUID such as: 4ad7f4b9-a0ff-458d-b3ec-9f84be26012b
```

## <a name="reclassify-a-job-after-submission"></a>Reclassifier un travail après envoi

Une fois que le routeur de travaux a reçu et classifié un travail à l’aide d’une stratégie, vous avez la possibilité de le reclassifier à l’aide du kit SDK. L’exemple suivant illustre une façon d’augmenter la priorité du travail à `10`, en spécifiant simplement **ID de travail**, en appelant la méthode `ReclassifyJobAsync` et en incluant l’étiquette `Hardware_VIP`.

```csharp
var reclassifiedJob = await client.ReclassifyJobAsync(
    jobId: "4ad7f4b9-a0ff-458d-b3ec-9f84be26012b",
    classificationPolicyId: null,
    labelsToUpdate: new LabelCollection()
    {
        { "Hardware_VIP", true }
    }
);
```
