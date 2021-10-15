---
title: Publier un événement dans une rubrique Azure Event Grid personnalisée
description: Cet article explique comment publier un événement dans une rubrique personnalisée. Il montre le format des données de publication et d’événement.
ms.topic: conceptual
ms.date: 08/19/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 489ad5ab07a411612bdbce1a2dc71dc1a3441f3c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361512"
---
# <a name="publish-events-to-azure-event-grid-custom-topics-using-access-keys"></a>Publier des événements dans des rubriques personnalisées Azure Event Grid à l’aide de clés d’accès

Cet article explique comment publier un événement dans une rubrique personnalisée à l’aide d’une clé d’accès. Il montre le format des données de publication et d’événement. Le [Contrat de niveau de service (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) s’applique uniquement aux publications qui correspondent au format attendu.


> [!NOTE]
> L’authentification Azure AD fournit une prise en charge d’authentification supérieure à celle proposée par l’authentification par jeton de signature d’accès partagé ou par clé d’accès. Avec l’authentification Azure AD, l’identité est validée par rapport au fournisseur d’identité Azure AD. En tant que développeur, vous n’aurez pas à gérer les clés dans votre code si vous utilisez l’authentification Azure AD. Vous bénéficierez également de toutes les fonctionnalités de sécurité intégrées à la plateforme Microsoft Identity, telles que l’accès conditionnel, qui peuvent vous aider à renforcer la posture de sécurité de votre application. Pour plus d’informations, consultez [Authentifier les clients de publication avec Azure Active Directory](authenticate-with-active-directory.md).

## <a name="endpoint"></a>Point de terminaison

Quand vous envoyez la demande HTTP POST à une rubrique personnalisée, utilisez le format d’URI `https://<topic-endpoint>?api-version=2018-01-01`.

Par exemple, un URI valide est `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.

Pour obtenir le point de terminaison pour une rubrique personnalisée avec l’interface de ligne de commande Azure, utilisez :

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Pour obtenir le point de terminaison pour une rubrique personnalisée avec Azure PowerShell, utilisez :

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>En-tête

Dans la demande, incluez une valeur d’en-tête nommée `aeg-sas-key` qui contient une clé pour l’authentification.

Par exemple, une valeur d’en-tête valide est `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==`.

Pour obtenir la clé pour une rubrique personnalisée avec l’interface de ligne de commande Azure, utilisez :

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Pour obtenir la clé pour une rubrique personnalisée avec PowerShell, utilisez :

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Données d’événement

Pour les rubriques personnalisées, les données de premier niveau contiennent les mêmes champs que les événements standard définis par les ressources. Une de ces propriétés est une propriété de données qui contient des propriétés propres à la rubrique personnalisée. En tant qu’éditeur d’événements, vous déterminez les propriétés de cet objet de données. Utilisez le schéma suivant :

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Pour obtenir une description de ces propriétés, consultez [Schéma d’événement Azure Event Grid](event-schema.md). Lorsque les événements sont envoyés vers une rubrique Event Grid, le tableau peut avoir une taille totale de 1 Mo. La taille maximale autorisée pour un événement est également de 1 Mo. Les événements de plus de 64 Ko donnent lieu à une facturation par incréments de 64 Ko. 

Par exemple, un schéma de données d’événement valide est :

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>response

Après la validation pour le point de terminaison de la rubrique, vous recevez une réponse. La réponse est un code de réponse HTTP standard. Certaines réponses courantes sont :

|Résultats  |response  |
|---------|---------|
|Succès  | 200 OK  |
|Les données d’événement ont un format incorrect | 400 Demande incorrecte |
|Clé d’accès non valide | 401 Non autorisé |
|Point de terminaison incorrecte | 404 Introuvable |
|La taille du tableau ou de l’événement dépasse la limite autorisée | 413 charge utile maximale dépassée |

Pour les erreurs, le corps du message a le format suivant :

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Étapes suivantes

* Pour plus d’information sur la surveillance des remises des événements, consultez [Surveiller la remise des messages Event Grid](monitor-event-delivery.md).
* Pour plus d’informations sur la clé d’authentification, consultez la page [Sécurité et authentification Azure Event Grid](security-authentication.md).
* Pour plus d’informations sur la création d’un abonnement Azure Event Grid, consultez [Schéma d’abonnement à Event Grid](subscription-creation-schema.md).
