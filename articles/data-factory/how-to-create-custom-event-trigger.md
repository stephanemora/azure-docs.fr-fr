---
title: Créer des déclencheurs d’événements personnalisés dans Azure Data Factory
description: Découvrez comment créer dans Azure Data Factory un déclencheur personnalisé qui exécute un pipeline en réponse à un événement personnalisé publié dans Event Grid.
ms.service: data-factory
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 2d2f26b24e7fa10d9244de8f99d78c64a44b3d61
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785646"
---
# <a name="create-a-trigger-that-runs-a-pipeline-in-response-to-a-custom-event-preview"></a>Créer un déclencheur qui exécute un pipeline en réponse à un événement personnalisé (préversion)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article décrit les déclencheurs d’événements personnalisés que vous pouvez créer dans vos pipelines Data Factory.

L’architecture basée sur les événements (EDA) est un modèle d’intégration de données courant qui implique la production, la détection, la consommation et la réaction à des événements. Les scénarios d’intégration de données nécessitent souvent des clients Data Factory pour déclencher des pipelines basés sur certains événements. L’intégration native de Data Factory avec [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) couvre maintenant les [Événements personnalisés](../event-grid/custom-topics.md): les clients envoient des événements arbitraires à une rubrique Event Grid, et Data Factory s’abonne à celle-ci et l’écoute, puis déclenche des pipelines en conséquence.

> [!NOTE]
> L’intégration décrite dans cet article dépend [d’Azure Event Grid](https://azure.microsoft.com/services/event-grid/). Vérifiez que votre abonnement est inscrit auprès du fournisseur de ressources Event Grid. Pour plus d’informations, consultez [Types et fournisseurs de ressources](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal). Vous devez pouvoir effectuer l’action *Microsoft.EventGrid/eventSubscriptions/* *. Cette action fait partie du rôle intégré Contributeur EventGrid EventSubscription.

En outre, la combinaison des paramètres de pipeline et du déclencheur d’événements personnalisé permet aux clients d’analyser et de référencer la charge utile de _données_ personnalisée dans des exécutions de pipeline. Le champ de _données_ dans la charge utile d’événement personnalisé est une structure clé-valeur JSON de forme libre qui offre aux clients un contrôle maximal sur les exécutions du pipeline pilotées par les événements.

> [!IMPORTANT]
> De temps en temps, il se peut qu’une clé référencée dans le paramétrage soit manquante dans la charge utile d’événement personnalisé. L’_exécution du déclencheur_ échoue avec une erreur, indiquant que l’expression ne peut pas être évaluée parce que la propriété _keyName_ n’existe pas. __Aucune__ _exécution de pipeline_ n’est déclenchée par l’événement.

## <a name="setup-event-grid-custom-topic"></a>Configurer une rubrique personnalisée Event Grid

Pour utiliser le déclencheur d’événements personnalisé dans Data Factory, vous devez d’_abord_ configurer une [rubrique personnalisée dans Event Grid](../event-grid/custom-topics.md). Le flux de travail diffère de celui du déclencheur d’événements de stockage où Data Factory configure la rubrique pour vous. Ici, vous devez parcourir Azure Event Grid et créer la rubrique vous-même. Pour plus d’informations sur la création de la rubrique personnalisée, consultez les [tutoriels du portail](../event-grid/custom-topics.md#azure-portal-tutorials) Azure Event Grid et les [tutoriels de CLI](../event-grid/custom-topics.md#azure-cli-tutorials).

Les fabriques de données s’attendent à ce que les événements suivent le [schéma d’événement Event Grid](../event-grid/event-schema.md). Assurez-vous que les charges utiles d’événement contiennent les champs suivants.

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

## <a name="data-factory-ui"></a>IU de la fabrique de données

Cette section vous montre comment créer un déclencheur d’événements de stockage dans l’interface utilisateur d’Azure Data Factory.

1. Basculez sur l’onglet **Modifier**, indiqué par un symbole de crayon.

1. Sélectionnez **Déclencheur** dans le menu, puis sélectionnez **Nouveau/Modifier**.

1. Sur la page **Ajouter des déclencheurs**, sélectionnez **Choisir un déclencheur…** , puis **+Nouveau**.

1. Sélectionnez le type de déclencheur **Événements personnalisés**.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-1-creation.png" alt-text="Capture d’écran de la page dédiée à la création d’un déclencheur d’événements de stockage dans l’interface utilisateur de Data Factory." lightbox="media/how-to-create-custom-event-trigger/custom-event-1-creation-expanded.png":::

1. Sélectionnez votre rubrique personnalisée dans la liste déroulante d’abonnement Azure ou entrez manuellement l’étendue de la rubrique d’événement.

   > [!NOTE]
   > Pour créer ou modifier un déclencheur d’événements personnalisé, le compte Azure utilisé pour se connecter à Data Factory et publier le déclencheur d’événements de stockage doit disposer de l’autorisation de contrôle d’accès en fonction du rôle (Azure RBAC) appropriée sur la rubrique. Aucune autorisation supplémentaire n’est requise : le principal de service pour Azure Data Factory n’a _pas_ besoin d’une autorisation spéciale sur Event Grid. Pour plus d’informations sur le contrôle d’accès, consultez la page [Contrôle d’accès en fonction du rôle](#role-based-access-control).

1. Les propriétés **Subject begins with** (le sujet commence par) et **Subject ends with** (le sujet se termine par) vous permettent de filtrer les événements pour lesquels vous souhaitez déclencher le pipeline. Les deux propriétés sont facultatives.

1. Utilisez **+ Nouveau** pour ajouter les **Types d’événements** que vous souhaitez filtrer. Le déclencheur d’événements personnalisé a une relation OR pour la liste : si un événement personnalisé a une propriété _eventType_ correspondant à un élément répertorié ici, il déclenche une exécution de pipeline. Le type de chiffrement ne tient pas compte de la casse. Par exemple, dans la capture d’écran ci-dessous, le déclencheur correspond à tous les événements _copycompleted_ ou _copysucceeded_ dont l’objet commence par _factories_.

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-2-properties.png" alt-text="Capture d’écran de la page Modifier le déclencheur montrant les types d’événements et le filtrage d’objet dans l’interface utilisateur de Data Factory.":::

1. Le déclencheur d’événements personnalisé peut analyser et envoyer une charge utile de _données_ personnalisée à votre pipeline. Commencez par créer les paramètres du pipeline, puis renseignez les valeurs sur la page **Paramètres**. Utilisez le format **@triggerBody().event.data._KeyName_** pour analyser la charge utile de données et transmettre les valeurs aux paramètres de pipeline. Pour une explication détaillée, consultez [Référencer des métadonnées de déclencheur dans des pipelines](how-to-use-trigger-parameterization.md) et [Variables système dans le déclencheur d’événements personnalisé](control-flow-system-variables.md#custom-event-trigger-scope).

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-4-trigger-values.png" alt-text="Capture d’écran de Paramètres du pipeline.":::

    :::image type="content" source="media/how-to-create-custom-event-trigger/custom-event-3-parameters.png" alt-text="Capture d’écran de la page Paramètres pour référencer la charge utile des données dans un événement personnalisé.":::

1. Quand vous avez terminé, cliquez sur **OK**.

## <a name="json-schema"></a>Schéma JSON

Le tableau suivant donne une vue d’ensemble des éléments de schéma associés à des déclencheurs d’événements personnalisés :

| **Élément JSON** | **Description** | **Type** | **Valeurs autorisées** | **Obligatoire** |
| ---------------- | --------------- | -------- | ------------------ | ------------ |
| **scope** | ID de ressource Azure Resource Manager de la rubrique Event Grid. | String | ID d’Azure Resource Manager | Oui |
| **events** | Type des événements qui entraîne l’activation de ce déclencheur. | Tableau de chaînes    |  | Oui, au moins une valeur est attendue |
| **subjectBeginsWith** | Le champ Objet doit commencer par le modèle fourni pour activer le déclencheur. Par exemple, `factories` active uniquement le déclencheur pour l’objet d’événement commençant par `factories`. | String   | | Non |
| **subjectEndsWith** | Le champ Objet doit se terminer par le modèle fourni pour activer le déclencheur. | String   | | Non |

## <a name="role-based-access-control"></a>Contrôle d’accès en fonction du rôle

Azure Data Factory utilise un contrôle d’accès en fonction du rôle Azure (RBAC Azure) pour veiller à ce que l’accès non autorisé pour écouter, l’abonnement à des mises à jour et le déclenchement de pipelines liés à des événements personnalisés, soient strictement interdits.

* Pour créer ou mettre à jour un déclencheur d’événements de stockage, le compte Azure connecté au Data Factory doit avoir un accès approprié au compte de stockage concerné. Dans le cas contraire, l’opération échoue avec _Accès refusé_.
* Data Factory n’a besoin d’aucune autorisation spéciale sur votre Event Grid, et vous n’avez _pas_ besoin d’attribuer une autorisation RBAC Azure spéciale au principal de service Data Factory pour l’opération.

Plus précisément, le client a besoin de l’autorisation _Microsoft.EventGrid/EventSubscriptions/Write_ sur _/subscriptions/####/resourceGroups//####/providers/Microsoft.EventGrid/topics/someTopics_.

## <a name="next-steps"></a>Étapes suivantes

* Vous trouverez des informations détaillées sur les déclencheurs sur la page [Exécution de pipelines et déclencheurs](concepts-pipeline-execution-triggers.md#trigger-execution).
* Découvrez comment référencer les métadonnées d’un déclencheur dans le pipeline avec la section [Référencer les métadonnées de déclencheur dnas l’exécution de pipeline](how-to-use-trigger-parameterization.md)
