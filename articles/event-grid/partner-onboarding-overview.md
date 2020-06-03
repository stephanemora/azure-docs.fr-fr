---
title: Intégration en tant que partenaire Azure Event Grid
description: Intégration en tant que type de rubrique de partenaire Azure Event Grid. Découvrez comment fonctionne le modèle de ressources et le flux de publication des rubriques de partenaire.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758824"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Intégration en tant que partenaire Azure Event Grid

Cet article explique comment utiliser les ressources du partenaire Event Grid de manière privée et comment effectuer la conversion en type de rubrique de partenaire disponible publiquement.

Vous n’avez pas besoin d’une autorisation spéciale pour commencer à utiliser les types de ressources Event Grid associés à la publication d’événements en tant que partenaire Event Grid. En fait, vous pouvez les utiliser dès aujourd’hui pour publier des événements de manière privée sur vos propres abonnements Azure et tester le modèle de ressource si vous envisagez de devenir partenaire.

## <a name="becoming-an-event-grid-partner"></a>Devenir un partenaire Event Grid

Si vous souhaitez devenir un partenaire Event Grid public, commencez par remplir [ce formulaire](https://aka.ms/gridpartnerform), puis contactez l’équipe Event Grid à l’adresse [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com).

## <a name="how-partner-topics-work"></a>Fonctionnement des rubriques de partenaire
Les rubriques de partenaires exploitent l’architecture déjà utilisée dans Event Grid pour publier des événements à partir de ressources Azure, telles que le stockage et IoT Hub, et rend ces outils accessibles publiquement à tous les utilisateurs. Par défaut, l’utilisation de ces outils est privée pour votre abonnement Azure uniquement. Pour rendre vos événements disponibles publiquement, renseignez le formulaire ci-dessus et [contactez l’équipe Event Grid](mailto:gridpartner@microsoft.com).

Les rubriques de partenaires vous permettent de publier des événements dans Azure Event Grid à des fins de consommation mutualisée.

### <a name="onboarding-and-event-publishing-overview"></a>Présentation de l’intégration et de la publication d’événements

#### <a name="partner-flow"></a>Flux du partenaire

1. Créez un locataire Azure, le cas échéant.
1. À l’aide de l’interface CLI, créez une instance Event Grid `partnerRegistration`. Cette ressource contient des informations telles que le nom d’affichage, la description, l’URI d’installation, etc.

    ![Créer une rubrique de partenaire](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Créez un ou plusieurs éléments `partnerNamespaces` dans chaque région pour laquelle vous souhaitez publier des événements. Dans le cadre de cet article, le service Event Grid approvisionne un point de terminaison de publication (par exemple, `https://contoso.westus-1.eventgrid.azure.net/api/events`) ainsi que des clés d’accès.

    ![Créer un espace de noms de partenaire](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Permet aux clients d’indiquer à votre système qu’ils veulent bénéficier d’une rubrique de partenaire.
1. Contactez l’équipe Event Grid pour nous indiquer que vous souhaitez que votre type de rubrique de partenaire devienne public.

#### <a name="customer-flow"></a>Flux du client

1. Votre client se rend sur le Portail Azure pour noter l’ID d’abonnement Azure et le groupe de ressources au sein desquels créer le sujet partenaire.
1. Le client demande alors un sujet partenaire via votre système. En réponse, vous créez un tunnel d’événements pour votre espace de noms de partenaire.
1. Event Grid crée un sujet partenaire **en attente** dans le groupe de ressources et l’abonnement Azure du client.

    ![Créer un canal d’événements](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Le client active le sujet partenaire via le Portail Azure. Les événements peuvent maintenant passer de votre service à l’abonnement Azure du client.

    ![Activer un sujet partenaire](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modèle de ressource

Vous trouverez ci-dessous le modèle de ressource des sujets partenaire.

### <a name="partner-registrations"></a>Inscriptions de partenaires
* Ressource : `partnerRegistrations`
* Utilisée par : Partenaires
* Description : Capture les métadonnées globales du partenaire SaaS (par exemple, le nom, le nom complet, la description et l’URI d’installation).
    
    La création ou mise à jour d’une inscription de partenaire est une opération que les partenaires peuvent effectuer en libre-service. Ainsi, ils peuvent créer et tester le processus complet de bout en bout.
    
    Seules les inscriptions de partenaires (partnerRegistrations) approuvées par Microsoft peuvent être découvertes par les clients.
* Étendue : Élément créé dans l’abonnement Azure du partenaire. Les métadonnées sont visibles pour les clients une fois devenues publiques.

### <a name="partner-namespaces"></a>Espaces de noms de partenaires
* Ressource : partnerNamespaces
* Utilisée par : Partenaires
* Description : Fournit une ressource régionale dans laquelle publier des événements clients. Chaque espace de noms de partenaire possède un point de terminaison de publication et des clés d’authentification. C’est également par l’espace de noms que le partenaire demande une rubrique de partenaire pour un client donné et répertorie les clients actifs.
* Étendue : Fonctionne au sein de l’abonnement du partenaire.

### <a name="event-channel"></a>Canal d’événements
* Ressource : `partnerNamespaces/eventChannels`
* Utilisée par : Partenaires
* Description : Les tunnels d’événements sont un miroir de la rubrique de partenaire du client. En créant un tunnel d’événements et en spécifiant l’abonnement Azure et le groupe de ressources du client dans les métadonnées, vous indiquez à Event Grid de créer une rubrique de partenaire pour le client. Event Grid émet un appel ARM pour créer un élément partnerTopic correspondant dans l’abonnement du client. La rubrique de partenaire est créée à l’état « En attente ». Il existe également une liaison 1-1 entre chaque élément eventTunnel et partnerTopic.
* Étendue : Fonctionne au sein de l’abonnement du partenaire.

### <a name="partner-topics"></a>Rubriques de partenaire
* Ressource : `partnerTopics`
* Utilisée par : Clients
* Description : Les rubriques de partenaires sont similaires aux rubriques personnalisées et système disponibles dans Event Grid. Chaque rubrique de partenaire est associée à une « source » spécifique (par exemple, `Contoso:myaccount`) et à un type de rubrique de partenaire spécifique (par exemple, « contoso »). Les clients créent des abonnements d’événements dans la rubrique de partenaire, afin d’acheminer les événements vers différents gestionnaires d’événements.

    Les clients ne peuvent pas créer cette ressource directement. La seule façon de créer une rubrique de partenaire consiste à effectuer une opération de partenaire pour créer un tunnel d’événements.
* Étendue : Fonctionne au sein de l’abonnement du client.

### <a name="partner-topic-types"></a>Types de rubrique de partenaire
* Ressource : `partnerTopicTypes`
* Utilisée par : Clients
* Description : Les types de rubriques de partenaire sont des types de ressources permettant aux clients de découvrir la liste des types de rubriques de partenaire approuvés. Les URL se présentent comme suit : https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Étendue : Global

## <a name="publishing-events-to-event-grid"></a>Publication d’événements dans Event Grid
Lorsque vous créez un élément partnerNamespace dans une région Azure, vous obtenez un point de terminaison régional et les clés d’authentification correspondantes. Publiez des lots d’événements sur ce point de terminaison pour tous les tunnels d’événements clients dans cet espace de noms. En fonction du champ « source » de l’événement, Azure Event Grid mappe chaque événement avec la ou les rubriques de partenaire correspondantes.

### <a name="event-schema-cloudevents-v10"></a>Schéma d’événement : CloudEvents v1.0
Publiez des événements sur Azure Event Grid à l’aide du schéma CloudEvents 1.0. Event Grid prend en charge le mode structuré et le mode par lot. CloudEvents 1.0 est le seul schéma d’événement pris en charge pour les espaces de noms de partenaire.

### <a name="example-flow"></a>Exemple de flux

1.  Le service de publication effectue une publication HTTP sur `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.
2.  Dans la requête, incluez une valeur d’en-tête nommée aeg-sas-key, qui contient une clé pour l’authentification. Cette clé est approvisionnée pendant la création de l’élément partnerNamespace. Par exemple, une valeur d’en-tête valide est aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
3.  Définissez l’en-tête Content-Type sur « application/cloudevents-batch+json; charset=UTF-8 ».
4.  Effectuez une publication HTTP sur l’URL de publication ci-dessus, avec un lot d’événements correspondant à cette région. Par exemple :

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Après la publication sur le point de terminaison partnerNamespace, vous recevez une réponse. La réponse est un code de réponse HTTP standard. Certaines réponses courantes sont :

| Résultats                             | response              |
|------------------------------------|-----------------------|
| Succès                            | 200 OK                |
| Les données d’événement ont un format incorrect    | 400 Demande incorrecte       |
| Clé d’accès non valide                 | 401 Non autorisé      |
| Point de terminaison incorrecte                 | 404 Introuvable         |
| La taille du tableau ou de l’événement dépasse la limite autorisée | 413 charge utile maximale dépassée |

## <a name="reference"></a>Informations de référence

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modèle ARM](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Schéma de modèle ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [API REST](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Extension CLI](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>Kits SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Étapes suivantes
- [Vue d’ensemble des rubriques de partenaire](partner-topics-overview.md)
- [Formulaire d’intégration des rubriques de partenaire](https://aka.ms/gridpartnerform)
- [Rubrique de partenaire Auth0](auth0-overview.md)
- [Comment utiliser la rubrique de partenaire Auth0](auth0-how-to.md)
