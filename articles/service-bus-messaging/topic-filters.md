---
title: Filtres de rubrique Azure Service Bus | Microsoft Docs
description: Cet article explique comment les abonnés peuvent définir les messages qu’ils souhaitent recevoir d’une rubrique en spécifiant des filtres.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 5df343ff63c01a7cf10315b758e3d6fba8ac5674
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88066744"
---
# <a name="topic-filters-and-actions"></a>Actions et filtres de rubrique

Les abonnés peuvent définir les messages qu’ils veulent recevoir d’une rubrique. Ces messages sont spécifiés sous la forme d’une ou de plusieurs règles d’abonnement nommées. Chaque règle se compose d’une condition qui sélectionne des messages spécifiques et d’une action qui annote le message sélectionné. Pour chaque condition de règle de correspondance, l’abonnement crée une copie du message, qui peut être annotée différemment selon la règle de correspondance utilisée.

Chaque nouvel abonnement à une rubrique est associé à une règle d’abonnement par défaut initiale. Si vous ne spécifiez pas explicitement une condition de filtre pour la règle, le filtre **true** est appliqué et sélectionne tous les messages dans l’abonnement. La règle par défaut n’est associée à aucune action d’annotation.

Service Bus prend en charge trois conditions de filtre :

-   *Filtres booléens* : **TrueFilter** et **FalseFilter** sélectionnent tous les messages entrants (**true**) ou aucun des messages entrants (**false**) de l’abonnement.

-   *Filtres SQL* : **SqlFilter** contient une expression conditionnelle de type SQL qui est évaluée dans le répartiteur par rapport aux propriétés utilisateur et système des messages entrants. Toutes les propriétés système doivent avoir le préfixe `sys.` dans l’expression conditionnelle. Le [sous-ensemble du langage SQL pour les conditions de filtre](service-bus-messaging-sql-filter.md) recherche la présence de propriétés (`EXISTS`), de valeurs Null (`IS NULL`), d’opérateurs de relation logiques NOT/AND/OR, d’une arithmétique numérique simple et de critères spéciaux de texte simples avec `LIKE`.

-   *Filtres de corrélation* : **CorrelationFilter** contient un ensemble de conditions qui sont mises en correspondance par rapport à une ou plusieurs propriétés utilisateur et système d’un message entrant. En général, la mise en correspondance s’effectue par rapport à la propriété **CorrelationId**, mais l’application peut également choisir de le faire par rapport aux propriétés suivantes :

    - **ContentType**
     - **Étiquette**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **To**
     - et à des propriétés définies par l’utilisateur. 
     
     Il y a correspondance quand la valeur d’une propriété d’un message entrant est identique à la valeur spécifiée dans le filtre de corrélation. Pour les expressions de chaîne, la comparaison respecte la casse. Quand vous spécifiez plusieurs propriétés de correspondance, le filtre les combine en une condition AND logique, ce qui implique que toutes les conditions du filtre doivent être remplies pour qu’il y ait correspondance.

Tous les filtres évaluent les propriétés des messages. Ils ne peuvent pas évaluer le corps des messages.

Les règles de filtre complexes nécessitent une plus grande capacité de traitement. En particulier, l’utilisation de règles de filtre SQL diminue le débit global des messages dans les abonnements, rubriques et espaces de noms. Dans la mesure du possible, les applications doivent utiliser des filtres de corrélation plutôt que des filtres de type SQL, car les filtres de corrélation étant beaucoup plus performants, ils ont moins d’impact sur le débit.

## <a name="actions"></a>Actions

Avec les conditions de filtre SQL, vous pouvez définir une action qui annote le message en ajoutant, en supprimant ou en remplaçant des propriétés et leurs valeurs. L’action [utilise une expression de type SQL](service-bus-messaging-sql-filter.md) dont la syntaxe se rapproche de celle de l’instruction SQL UPDATE. L’action est effectuée sur le message après la mise en correspondance du message et avant la sélection du message dans l’abonnement. Les modifications apportées aux propriétés du message s’appliquent uniquement au message copié dans l’abonnement.

## <a name="usage-patterns"></a>Modèles d’usage

Le scénario d’usage le plus simple pour une rubrique est que chaque abonnement récupère une copie de chaque message envoyé dans une rubrique, pour obtenir un modèle de diffusion.

Les filtres et les actions implémentent deux autres groupes de modèles : le partitionnement et le routage.

Le partitionnement utilise des filtres pour distribuer les messages aux différents abonnements à une rubrique existants de manière prévisible et en mode mutuellement exclusif. Le modèle de partitionnement est utilisé quand la taille des instances d’un système est augmentée pour traiter de nombreux contextes différents dans des compartiments fonctionnellement identiques qui contiennent chacun une partie de l’ensemble des données, par exemple, les informations de profil d’un client. Avec le partitionnement, un éditeur envoie le message dans une rubrique sans avoir besoin de connaître le modèle de partitionnement. Le message est ensuite déplacé vers l’abonnement approprié, à partir duquel il peut ensuite être récupéré par le gestionnaire de messages de la partition.

Le routage utilise des filtres pour distribuer les messages aux différents abonnements à la rubrique de manière prévisible, mais pas nécessairement en mode mutuellement exclusif. Utilisés conjointement avec la fonctionnalité de [transfert automatique](service-bus-auto-forwarding.md), les filtres de rubrique permettent de créer des graphiques de routage complexes dans un espace de noms Service Bus pour la distribution de messages dans une région Azure. Avec Azure Functions ou Azure Logic Apps servant de pont entre les espaces de noms Azure Service Bus, vous pouvez créer des topologies globales complexes avec une intégration directe dans les applications métier.


> [!NOTE]
> Le portail Azure prenant désormais en charge la fonctionnalité Service Bus Explorer, des filtres d’abonnement peuvent être créés ou modifiés à partir du portail. 

## <a name="next-steps"></a>Étapes suivantes
Consultez les exemples suivants : 

- [.NET : tutoriel d’envoi et de réception de base avec des filtres](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET : filtres de rubrique](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Modèle Azure Resource Manager](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)