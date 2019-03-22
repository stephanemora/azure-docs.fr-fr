---
title: Fichier Include
description: Fichier Include
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: cd64bdabc2b7b34687296c855c27882925d80f63
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58124384"
---
Le tableau suivant liste les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Étendue | Notes | Valeur |
| --- | --- | --- | --- |
| Nombre d’espaces de noms Event Hubs par abonnement |Abonnement |- |1 000 |
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
