---
title: Fichier include
description: Fichier include
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 09/10/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 8700bbfe697a6b5fb81380831950d704fcb1f5ff
ms.sourcegitcommit: 5d7f8c57eaae91f7d9cf1f4da059006521ed4f9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/10/2020
ms.locfileid: "90012823"
---
Le tableau suivant répertorie les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

Les limites suivantes sont communes aux niveaux de base et standard. 

| Limite | Étendue | Notes | Valeur |
| --- | --- | --- | --- |
| Nombre d’espaces de noms Event Hubs par abonnement |Abonnement |- |100 |
| Nombre d’Event Hubs par espace de noms |Espace de noms |Les demandes suivantes de création d’un Event Hub sont rejetées. |10 |
| Nombre de partitions par Event Hub |Entité |- |32 |
| Taille maximale du nom d’un Event Hub |Entité |- | 256 caractères |
| Taille maximale d’un nom de groupe de consommateurs |Entité |- | 256 caractères |
| Nombre de récepteurs non epoch par groupe de consommateurs |Entité |- |5 |
| Unités de débit maximales |Espace de noms |Le dépassement de la limite d’unités de débit entraîne la limitation de vos données et la génération d’une exception de [serveur occupé](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Vous pouvez demander une plus grande quantité d’unités de débit pour le niveau Standard en remplissant une [demande de support](/azure/azure-portal/supportability/how-to-create-azure-support-request). Les [unités de débit supplémentaires](../articles/event-hubs/event-hubs-auto-inflate.md) sont disponibles par blocs de 20 sur la base d’un engagement d’achat ferme. |20 |
| Nombre de règles d’autorisation par espace de noms |Espace de noms|Les demandes suivantes pour la création de règle d’autorisation sont rejetées.|12 |
| Nombre d’appels à la méthode GetRuntimeInformation | Entité | - | 50 par seconde | 
| Nombre de règles de réseau virtuel (VNet) et de configuration IP | Entité | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Event Hubs De base et Standard - Quotas et limites
| Limite | Étendue | Notes | De base | Standard |
| --- | --- | --- | -- | --- |
| Taille maximale de l’événement Event Hubs|Entité | &nbsp; | 256 KB | 1 Mo |
| Nombre de groupes de consommateurs par Event Hub |Entité | &nbsp; |1 |20 |
| Nombre de connexions AMQP par espace de noms |Espace de noms |Les demandes suivantes de connexions supplémentaires sont rejetées et le code appelant reçoit une exception. |100 |5 000|
| Période de rétention maximale des données d’événement |Entité | &nbsp; |1 jour |1 à 7 jours |
|Espace de noms où Apache Kafka est activé|Espace de noms |L’espace de noms Event Hubs diffuse en streaming des applications en utilisant le protocole Kafka. Pour plus d’informations, consultez [Utiliser Azure Event Hubs à partir d’applications Apache Kafka](../articles/event-hubs/event-hubs-for-kafka-ecosystem-overview.md). |Non | Oui |
|Capture |Entité | En cas d’activation, des microlots sont créés sur le même flux. Pour plus d’informations, consultez [Capturer des événements avec Azure Event Hubs dans Stockage Blob Azure ou Azure Data Lake Storage](../articles/event-hubs/event-hubs-capture-overview.md). |Non |Oui |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Quotas et limites de l’offre Event Hubs Dedicated
L’offre Event Hubs Dedicated est facturée à un tarif mensuel fixe, avec un minimum de 4 heures d’utilisation. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise et les limites pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | limites |
| --- | ---|
| Bande passante |  20 CUS |
| Espaces de noms | 50 par unité de capacité |
| Event Hubs |  1 000 par espace de noms |
| Événements d’entrée | Inclus |
| Taille des messages | 1 Mo |
| Partitions | 2 000 par unité de capacité |
| Groupes de consommateurs | Aucune limite par unité de capacité, 1 000 par hub d’événements |
| Connexions réparties | 100 K inclus |
| Rétention des messages | 90 jours, 10 To inclus per unité de capacité |
| Capture | Inclus |
