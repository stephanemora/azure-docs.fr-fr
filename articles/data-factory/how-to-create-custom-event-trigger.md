---
title: Créer des déclencheurs d’événements personnalisés dans Azure Data Factory
description: Apprenez à créer dans Azure Data Factory un déclencheur qui exécute un pipeline en réponse à un événement personnalisé publié dans Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: d91e1f52f0844317b049086489bda25c079ee9be
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109634290"
---
# <a name="create-a-custom-event-trigger-to-run-a-pipeline-in-azure-data-factory-preview"></a>Créer un déclencheur d'événements personnalisé pour exécuter un pipeline dans Azure Data Factory (préversion)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d'intégration de données exigent souvent que les clients Azure Data Factory déclenchent des pipelines lorsque certains événements se produisent. L'intégration native de Data Factory à [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) couvre désormais les [rubriques personnalisées](../event-grid/custom-topics.md). Vous devez envoyer les événements à une rubrique Event Grid. Data Factory s'abonne à la rubrique, écoute, puis déclenche les pipelines en conséquence.

> [!NOTE]
> L’intégration décrite dans cet article dépend [d’Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Vérifiez que votre abonnement est inscrit auprès du fournisseur de ressources Event Grid. Pour plus d’informations, consultez [les types et les fournisseurs de ressources](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Vous devez être en mesure d'effectuer l'action `Microsoft.EventGrid/eventSubscriptions/`. Cette action fait partie du rôle intégré [Contributeur EventGrid EventSubscription](../role-based-access-control/built-in-roles.md#eventgrid-eventsubscription-contributor).

Si vous combinez des paramètres du pipeline et un déclencheur d'événements personnalisé, vous pouvez analyser et référencer des charges utiles `data` personnalisées dans les exécutions de pipeline. Dans la mesure où le champ `data` d'une charge utile d'événement personnalisé est une structure clé-valeur JSON de forme libre, vous pouvez contrôler les exécutions de pipeline pilotées par les événements.

> [!IMPORTANT]
> Si une clé référencée dans le paramétrage est manquante dans la charge utile de l'événement personnalisé, `trigger run` échoue. Vous obtenez une erreur indiquant que l'expression ne peut pas être évaluée parce que la propriété `keyName` n'existe pas. Dans ce cas, **aucune** `pipeline run` n'est déclenchée par l'événement.

## <a name="set-up-a-custom-topic-in-event-grid"></a>Configurer une rubrique personnalisée dans Event Grid

Pour utiliser le déclencheur d'événements personnalisé dans Data Factory, vous devez *d'abord* configurer une [rubrique personnalisée dans Event Grid](../event-grid/custom-topics.md).

Accédez à Azure Event Grid et créez la rubrique vous-même. Pour plus d'informations sur la création de la rubrique personnalisée, consultez les [tutoriels du portail](../event-grid/custom-topics.md#azure-portal-tutorials) Azure Event Grid et les [tutoriels de CLI](../event-grid/custom-topics.md#azure-cli-tutorials).

> [!NOTE]
> Le workflow diffère de celui du déclencheur d'événements de stockage. Ici, Data Factory ne configure pas la rubrique pour vous.

Data Factory s'attend à ce que les événements suivent le [schéma d'événement Event Grid](../event-grid/event-schema.md). Vérifiez que les charges utiles d'événement contiennent les champs suivants :

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

## <a name="use-data-factory-to-create-a-custom-event-trigger"></a>Utiliser Data Factory pour créer un déclencheur d'événements personnalisé

1. Accédez à Azure Data Factory et connectez-vous.

1. Basculez vers l'onglet **Modifier**. Recherchez l'icône en forme de crayon.

1. Sélectionnez **Déclencheur** dans le menu, puis choisissez **Nouveau/Modifier**.

1. Sur la page **Ajouter des déclencheurs**, sélectionnez **Choisir un déclencheur**, puis **+Nouveau**.

1. Sélectionnez le **Type** **Événements personnalisés**.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Capture d’écran de la page dédiée à la création d’un déclencheur d’événements de stockage dans l’interface utilisateur de Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Sélectionnez votre rubrique personnalisée dans la liste déroulante d’abonnement Azure ou entrez manuellement l’étendue de la rubrique d’événement.

   > [!NOTE]
   > Pour créer ou modifier un déclencheur d'événements personnalisé dans Data Factory, vous devez utiliser un compte Azure avec un contrôle d'accès en fonction du rôle (Azure RBAC) approprié. Aucune autorisation supplémentaire n'est requise. Le principal de service Data Factory n'a besoin d'*aucune* autorisation spéciale pour votre instance d'Event Grid. Pour plus d'informations sur le contrôle d'accès, consultez la section [Contrôle d'accès en fonction du rôle](#role-based-access-control).

1. Les propriétés **Subject begins with** (le sujet commence par) et **Subject ends with** (le sujet se termine par) vous permettent de filtrer les événements déclencheurs. Les deux propriétés sont facultatives.

1. Utilisez **+ Nouveau** pour ajouter les **Types d'événements** à utiliser comme filtres. La liste des déclencheurs d'événements personnalisés utilise une relation OR (OU). Lorsqu'un événement personnalisé présentant une propriété `eventType` correspondant à une propriété de la liste, une exécution de pipeline est déclenchée. Le type de chiffrement ne tient pas compte de la casse. Par exemple, dans la capture d'écran suivante, le déclencheur correspond à tous les événements `copycompleted` ou `copysucceeded` dont le sujet commence par *factories*.

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Capture d’écran de la page Modifier le déclencheur montrant les types d’événements et le filtrage d’objet dans l’interface utilisateur de Data Factory.":::

1. Un déclencheur d'événements personnalisé peut analyser et envoyer une charge utile de `data` personnalisée à votre pipeline. Créez les paramètres du pipeline, puis renseignez les valeurs sur la page **Paramètres**. Utilisez le format `@triggerBody().event.data._keyName_` pour analyser la charge utile de données et transmettre les valeurs aux paramètres du pipeline.
 
   Pour une explication détaillée, consultez les articles suivants :
   - [Référencer des métadonnées de déclencheur dans des pipelines](how-to-use-trigger-parameterization.md)
   - [Variables système dans le déclencheur d'événements personnalisé](control-flow-system-variables.md#custom-event-trigger-scope)

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Capture d'écran des paramètres du pipeline.":::

   :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Capture d'écran de la page Paramètres pour référencer la charge utile des données dans un événement personnalisé.":::

1. Après avoir entré les paramètres, sélectionnez **OK**.

## <a name="json-schema"></a>Schéma JSON

Le tableau suivant donne une vue d’ensemble des éléments de schéma associés à des déclencheurs d’événements personnalisés :

| Élément JSON | Description | Type | Valeurs autorisées | Obligatoire |
|---|----------------------------|---|---|---|
| `scope` | ID de ressource Azure Resource Manager de la rubrique Event Grid. | String | ID d’Azure Resource Manager | Oui |
| `events` | Type des événements qui entraîne l’activation de ce déclencheur. | Tableau de chaînes    |  | Oui, au moins une valeur est attendue. |
| `subjectBeginsWith` | Le champ `subject` doit commencer par le modèle fourni pour que le déclencheur s'active. Par exemple, *factories* active uniquement le déclencheur pour les sujets d'événement qui commencent par *factories*. | String   | | Non |
| `subjectEndsWith` | Le champ `subject` doit se terminer par le modèle fourni pour que le déclencheur s'active. | String   | | Non |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Azure Data Factory utilise Azure RBAC pour interdire les accès non autorisés. Pour fonctionner correctement, Data Factory a besoin d'un accès lui permettant d'effectuer les tâches suivantes :
- Écouter les événements
- S’abonner aux mises à jour des événements
- Déclencher des pipelines liés à des événements personnalisés

Pour réussir à créer ou à mettre à jour un déclencheur d'événements personnalisé, vous devez vous connecter à Data Factory avec un compte Azure disposant d'un accès approprié. Sinon, l'opération échoue en affichant le message d'erreur _Accès refusé_.

Data Factory n'a besoin d'aucune autorisation spéciale pour votre instance d'Event Grid. Et vous n'avez *pas* besoin d'attribuer une autorisation RBAC Azure spéciale au principal de service Data Factory pour l'opération.

En revanche, vous devez disposer d'une autorisation `Microsoft.EventGrid/EventSubscriptions/Write` sur `/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics`.

## <a name="next-steps"></a>Étapes suivantes

* Obtenir des informations détaillées sur l'[exécution des déclencheurs](concepts-pipeline-execution-triggers.md#trigger-execution)
* Apprendre à [référencer des métadonnées de déclencheur dans des exécutions de pipeline](how-to-use-trigger-parameterization.md)
