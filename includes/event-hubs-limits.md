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
ms.openlocfilehash: 84a51f65ef46c390f84308ab17cf83859a16c3b8
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55428506"
---
Le tableau suivant liste les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

| Limite | Étendue | Notes | Valeur |
| --- | --- | --- | --- | --- |
| Nombre d’espaces de noms Event Hubs par abonnement |Abonnement |- |1 000 |
| Nombre d’Event Hubs par espace de noms |Espace de noms |Les demandes suivantes de création d’un Event Hub seront rejetées. |10 |
| Nombre de partitions par Event Hub |Entité |- |32 |
| Nombre de groupes de consommateurs par Event Hub |Entité |- |20 |
| Nombre de connexions AMQP par espace de noms |Espace de noms |Les demandes suivantes de connexions supplémentaires sont rejetées et le code appelant reçoit une exception. |5 000 |
| Taille maximale de l’événement Event Hubs|Entité |- |1 Mo |
| Taille maximale du nom d’un Event Hub |Entité |- |50 caractères |
| Nombre de récepteurs non epoch par groupe de consommateurs |Entité |- |5. |
| Période de rétention maximale des données d’événement |Entité |- |1 à 7 jours |
| Unités de débit maximales |Espace de noms |Le dépassement de la limite d’unités de débit entraîne la limitation de vos données et la génération d’une exception **[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)**. Vous pouvez demander une plus grande quantité d’unités de débit pour le niveau Standard en remplissant une [demande de support](/azure/azure-supportability/how-to-create-azure-support-request). Les [unités de débit supplémentaires](../articles/event-hubs/event-hubs-auto-inflate.md) sont disponibles par blocs de 20 sur la base d’un engagement d’achat ferme. |20 |
| Nombre de règles d’autorisation par espace de noms |Espace de noms|Les demandes suivantes pour la création de règle d’autorisation seront rejetées.|12 |
