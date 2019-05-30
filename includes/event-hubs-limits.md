---
title: Fichier Include
description: Fichier Include
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3f3b60c3744ce9dea61054b3fa0aaccfea27d784
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66238840"
---
Le tableau suivant liste les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | `Scope` | Notes | Valeur |
| --- | --- | --- | --- |
| Nombre d’espaces de noms Event Hubs par abonnement |Abonnement |- |100 |
| Nombre d’Event Hubs par espace de noms |Espace de noms |Les requêtes suivantes pour la création d’un concentrateur d’événements sont rejetées. |10 |
| Nombre de partitions par Event Hub |Entité |- |32 |
| Nombre de groupes de consommateurs par Event Hub |Entité |- |20 |
| Nombre de connexions AMQP par espace de noms |Espace de noms |Les requêtes suivantes pour les connexions supplémentaires sont rejetées et une exception est reçue par le code appelant. |5 000 |
| Taille maximale de l’événement Event Hubs|Entité |- |1 Mo |
| Taille maximale du nom d’un Event Hub |Entité |- |50 caractères |
| Nombre de récepteurs non epoch par groupe de consommateurs |Entité |- |5. |
| Période de rétention maximale des données d’événement |Entité |- |1 à 7 jours |
| Unités de débit maximales |Espace de noms |Dépassement de la limite d’unité de débit provoque vos données limitées et génère un [exception de serveur occupé](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Pour demander un plus grand nombre d’unités de débit pour le niveau Standard, de fichiers un [demande de support](/azure/azure-supportability/how-to-create-azure-support-request). Les [unités de débit supplémentaires](../articles/event-hubs/event-hubs-auto-inflate.md) sont disponibles par blocs de 20 sur la base d’un engagement d’achat ferme. |20 |
| Nombre de règles d’autorisation par espace de noms |Espace de noms|Les requêtes suivantes pour la création de règles d’autorisation sont rejetées.|12 |
| Nombre d’appels à la méthode GetRuntimeInformation | Entité | - | 50 par seconde | 

### <a name="event-hubs-dedicated---quotas-and-limits"></a>Event Hubs Dedicated - quotas et limites
L’offre Event Hubs Dedicated est facturé à un tarif mensuel fixe, avec un minimum de 4 heures d’utilisation. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise et les limites pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | limites |
| --- | ---|
| Bande passante |  20 unités de capacité |
| Espaces de noms | 50 par unité de capacité |
| Event Hubs |  Aucune limite sur event hubs/rubriques |
| Événements d’entrée | Inclus |
| Taille des messages | 1 million d’octets |
| Partitions | 2000 par unité de capacité |
| Groupes de consommateurs | Aucune limite par unité de capacité, 1000 par event hub |
| Connexions réparties | 100 K inclus |
| Rétention des messages | 90 jours, 10 To inclus par unité de capacité |
| Capture | Inclus |
