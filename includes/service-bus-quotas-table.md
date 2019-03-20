---
title: Fichier Include
description: Fichier Include
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 7add8c10fd3224b9c287ea4cc672191157f56a09
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124375"
---
Le tableau suivant répertorie les informations de quota propres à la messagerie Azure Service Bus. Pour plus d’informations sur la tarification et les autres quotas pour Service Bus, consultez [tarification Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Nom du quota | Étendue | Notes | Valeur |
| --- | --- | --- | --- |
| Nombre maximal d’espaces de noms de base ou Standard par abonnement Azure |Espace de noms |Les demandes suivantes des espaces de noms de base ou Standard supplémentaires sont rejetées par le portail Azure. |100|
| Nombre maximal d’espaces de noms Premium par abonnement Azure |Espace de noms |Les demandes suivantes des espaces de noms Premium supplémentaires sont rejetées par le portail. |25 |
| Taille de file d’attente ou rubrique |Entité |Définie lors de la création de la file d’attente ou rubrique. <br/><br/> Les messages entrants suivants sont rejetées et une exception est reçue par le code appelant. |1, 2, 3, 4 ou 5 Go.<br /><br />Dans la référence SKU Premium et la référence SKU Standard avec [partitionnement](/azure/service-bus-messaging/service-bus-partitioning) activé, la taille maximale de file d’attente ou rubrique est de 80 Go. |
| Nombre de connexions simultanées sur un espace de noms |Espace de noms |Les requêtes suivantes pour les connexions supplémentaires sont rejetées et une exception est reçue par le code appelant. Opérations REST ne comptent pas dans les connexions TCP simultanées. |NetMessaging : 1,000.<br /><br />AMQP : 5,000. |
| Nombre de demandes de réception sur une entité de file d’attente, rubrique ou abonnement |Entité |Réception suivantes les demandes sont rejetées et une exception est reçue par le code appelant. Ce quota s’applique au nombre combiné d’opérations de réception simultanées sur tous les abonnements à une rubrique. |5 000 |
| Nombre de rubriques ou files d’attente par espace de noms |Espace de noms |Les requêtes suivantes de création de rubrique ou de file d’attente dans l’espace de noms sont rejetées. Par conséquent, en cas de configuration via le [portail Azure][Azure portal], un message d’erreur est généré. Si elle est appelée à partir de l’API de gestion, une exception est reçue par le code appelant. |1 000 pour le niveau de base ou Standard. Le nombre total de rubriques et files d’attente dans un espace de noms doit être inférieure ou égale à 1 000. <br/><br/>Pour le niveau Premium, 1 000 par unité de messagerie (MU). Limite maximale est de 4 000. |
| Nombre de [partitionnée rubriques ou files d’attente](/azure/service-bus-messaging/service-bus-partitioning) par espace de noms |Espace de noms |Les requêtes suivantes de création de rubrique ou de file d’attente partitionnée dans l’espace de noms sont rejetées. Par conséquent, en cas de configuration via le [portail Azure][Azure portal], un message d’erreur est généré. Si elle est appelée à partir de l’API de gestion, l’exception **QuotaExceededException** est reçu par le code appelant. |Base des niveaux Standard et : 100.<br/><br/>Les entités partitionnées ne sont pas pris en charge dans les [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) niveau.<br/><br />Chaque file d’attente partitionnée ou d’une rubrique est dans le quota de 1 000 entités par espace de noms. |
| Taille maximale de tout chemin d’entité de messagerie : file d’attente ou rubrique |Entité |- |260 caractères. |
| Taille maximale de tout nom d’entité de messagerie : espace de noms, abonnement ou règle d’abonnement |Entité |- |50 caractères. |
| Taille maximale de l’[ID d’un message](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entité |- | 128 |
| Taille maximale d’un message [ID de session](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entité |- | 128 |
| Taille de message pour une entité de file d’attente, rubrique ou abonnement |Entité |Les messages entrants qui dépassent ces quotas sont rejetés et une exception est reçue par le code appelant. |Taille de message maximale : 256 Ko pour [niveau Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 Mo pour [niveau Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />En raison d’une surcharge du système, cette limite est inférieure à ces valeurs.<br /><br />Taille d’en-tête maximale : 64 KO.<br /><br />Nombre maximal de propriétés d’en-tête dans le sac de propriétés : **octets/int. MaxValue**.<br /><br />Taille maximale d’une propriété dans le conteneur de propriétés : pas de limite explicite. Cette valeur est limitée par la taille d’en-tête maximale. |
| Taille de propriété de message pour une entité de file d’attente, rubrique ou abonnement |Entité | L’exception **SerializationException** est généré. |Taille de propriété de message maximale pour chaque propriété est 32 000. Taille cumulée de toutes les propriétés ne peut pas dépasser 64 000. Cette limite s’applique à l’intégralité de l’en-tête de la [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), qui a les propriétés de l’utilisateur et les propriétés système, telles que [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [étiquette](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)et [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Nombre d’abonnements par rubrique |Entité |Les requêtes suivantes de création d’abonnements supplémentaires à la rubrique sont rejetées. Par conséquent, en cas de configuration via le portail, un message d’erreur est affiché. Si elle est appelée à partir de l’API de gestion, une exception est reçue par le code appelant. |Niveau standard : Chaque abonnement compte par rapport au quota de 1 000 entités, autrement dit, files d’attente, rubriques et abonnements, par espace de noms. <br/> <br/> Niveau Premium : 2,000. |
| Nombre de filtres SQL par rubrique |Entité |Les requêtes suivantes pour la création de filtres supplémentaires sur la rubrique sont rejetées et une exception est reçue par le code appelant. |2 000 |
| Nombre de filtres de corrélation par rubrique |Entité |Les requêtes suivantes pour la création de filtres supplémentaires sur la rubrique sont rejetées et une exception est reçue par le code appelant. |100 000 |
| Taille de filtres SQL ou des actions |Espace de noms |Les requêtes suivantes pour la création de filtres supplémentaires sont rejetées et une exception est reçue par le code appelant. |Longueur maximale de la chaîne de condition de filtre : 1 024 (1 KO).<br /><br />Longueur maximale de la chaîne d’action de règle : 1 024 (1 KO).<br /><br />Nombre maximal d’expressions par action de règle : 32. |
| Nombre de règles [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) par espace de noms, file d’attente ou rubrique |Entité, espace de noms |Les requêtes suivantes pour la création de règles supplémentaires sont rejetées et une exception est reçue par le code appelant. |Nombre maximal de règles : 12. <br /><br /> Les règles qui sont configurées sur un espace de noms Service Bus s’appliquent à toutes les files d’attente ou rubriques appartenant à cet espace de noms. |
| Nombre de messages par transaction | Transaction | Les messages entrants supplémentaires sont rejetées et une exception indiquant « Impossible d’envoyer plus de 100 messages dans une transaction unique » est reçu par le code appelant. | 100 <br /><br /> Pour les deux operations **Send()** et **SendAsync()**. |

[Azure portal]: https://portal.azure.com