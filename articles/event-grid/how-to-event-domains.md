---
title: Guide pratique pour gérer de grands ensembles de rubriques dans Azure Event Grid et y publier des événements à l’aide de domaines d’événement
description: Montre comment créer et gérer des rubriques dans Azure Event Grid et y publier des événements à l’aide de domaines d’événement.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 48a5356b03e38e864ba76f048febdb0b040893f5
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669296"
---
# <a name="manage-topics-and-publish-events-using-event-domains"></a>Gérer des rubriques et publier des événements à l’aide de domaines de l’événement

Cet article montre comment :

* Créer un domaine Event Grid
* S’abonner à des rubriques
* Lister les clés
* Publier des événements sur un domaine

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="create-an-event-domain"></a>Créer un domaine d’événement

Vous pouvez créer un domaine d’événement par le biais de l’extension `eventgrid` pour [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). Une fois que vous avez créé un domaine, vous pouvez l’utiliser pour gérer de grands ensembles de rubriques.

```azurecli-interactive
# if you haven't already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid domain create \
  -g <my-resource-group> \
  --name <my-domain-name>
  -l <location>
```

Une création réussie retourne le résultat suivant :

```json
{
  "endpoint": "https://<my-domain-name>.westus2-1.eventgrid.azure.net/api/events",
  "id": "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>",
  "inputSchema": "EventGridSchema",
  "inputSchemaMapping": null,
  "location": "westus2",
  "name": "<my-domain-name>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<my-resource-group>",
  "tags": null,
  "type": "Microsoft.EventGrid/domains"
}
```

Notez les paramètres `endpoint` et `id`, dont vous aurez besoin pour gérer le domaine et publier des événements.

## <a name="create-topics-and-subscriptions"></a>Créer des rubriques et des abonnements

Le service Event Grid crée et gère automatiquement la rubrique correspondante dans un domaine en fonction de l’appel destiné à créer un abonnement aux événements pour une rubrique de domaine. Il n’existe pas d’autre étape pour créer une rubrique dans un domaine. De même, quand le dernier abonnement aux événements pour une rubrique est supprimé, celle-ci est également supprimée.

L’abonnement à une rubrique dans un domaine est identique à l’abonnement à toute autre ressource Azure :

```azurecli-interactive
az eventgrid event-subscription create \
  --name <event-subscription> \
  --resource-id "/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/<my-topic>" \
  --endpoint https://contoso.azurewebsites.net/api/f1?code=code
```

L’ID de ressource donné est le même ID que celui retourné au moment de la création du domaine. Pour spécifier la rubrique à laquelle vous voulez vous abonner, ajoutez `/topics/<my-topic>` à la fin de l’ID de ressource.

Pour créer un abonnement d’événement de portée de domaine qui reçoit tous les événements dans le domaine, indiquez celui-ci en guise de `resource-id` sans spécifier de rubriques, par exemple `/subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>`.

Si vous avez besoin d’un point de terminaison de test auquel abonner vos événements, vous pouvez toujours déployer une [application web prédéfinie](https://github.com/Azure-Samples/azure-event-grid-viewer) qui affiche les événements entrants. Vous pouvez envoyer vos événements à votre site web de test à l’adresse `https://<your-site-name>.azurewebsites.net/api/updates`.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-event-grid-viewer%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

Les autorisations définies pour une rubrique sont stockées dans Azure Active Directory et doivent être supprimées explicitement. La suppression d’un abonnement aux événements ne révoque pas l’accès des utilisateurs à la création d’abonnements aux événements s’ils ont un accès en écriture à une rubrique.

## <a name="manage-access-to-topics"></a>Gérer l’accès aux rubriques

La gestion de l’accès aux rubriques est effectuée par le biais d’une [attribution de rôle](https://docs.microsoft.com/en-us/azure/role-based-access-control/role-assignments-cli). L’attribution de rôle utilise le contrôle d’accès en fonction du rôle pour limiter les opérations sur les ressources Azure aux utilisateurs autorisés dans une étendue donnée.

Event Grid a deux rôles intégrés que vous pouvez utiliser pour attribuer à des utilisateurs spécifiques l’accès à différentes rubriques au sein d’un domaine. Ces rôles sont `EventGrid EventSubscription Contributor (Preview)`, qui permet de créer et de supprimer des abonnements, et `EventGrid EventSubscription Reader (Preview)`, qui permet uniquement de répertorier les abonnements aux événements.

La commande suivante limite `alice@contoso.com` à la création et à la suppression d’abonnements aux événements uniquement sur la rubrique `foo` :

```azurecli-interactive
az role assignment create --assignee alice@contoso.com --role "EventGrid EventSubscription Contributor (Preview)" --scope /subscriptions/<sub-id>/resourceGroups/<my-resource-group>/providers/Microsoft.EventGrid/domains/<my-domain-name>/topics/foo
```

Consultez [Sécurité et authentification pour Event Grid](./security-authentication.md) pour en savoir plus sur les thèmes suivants :

* Contrôle d’accès à la gestion
* Types d’opération
* Création de définitions de rôle personnalisées

## <a name="publish-events-to-an-event-grid-domain"></a>Publier des événements sur un domaine Event Grid

La publication d’événements sur un domaine est similaire à la [publication sur une rubrique personnalisée](./post-to-custom-topic.md). La seule différence est que vous devez spécifier la rubrique à laquelle vous souhaitez destiner chaque événement. Dans le tableau d’événements suivant, l’événement avec `"id": "1111"` est destiné à la rubrique `foo`, alors que l’événement avec `"id": "2222"` est destiné à la rubrique `bar` :

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Pour obtenir les clés d’un domaine, utilisez :

```azurecli-interactive
az eventgrid domain key list \
  -g <my-resource-group> \
  -n <my-domain>
```

Ensuite, publiez vos événements sur votre domaine Event Grid en effectuant une requête HTTP POST selon la méthode de votre choix.

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’informations sur les concepts généraux des domaines d’événement et sur ce qui fait leur utilité, consultez la [vue d’ensemble conceptuelle des domaines d’événement](./event-domains.md).