---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

Le tableau suivant répertorie les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limites communes à tous les niveaux
Les limites suivantes sont communes à tous les niveaux. 

| Limite |  Notes | Valeur |
| --- |  --- | --- |
 Taille du nom d’un Event Hub |- | 256 caractères |
| Taille du nom d’un groupe de consommateurs | Le protocole Kafka ne nécessite pas la création d’un groupe de consommateurs. | <p>Kafka : 256 caractères</p><p>AMQP : 50 caractères |
| Nombre de récepteurs non epoch par groupe de consommateurs |- |5 |
| Nombre de règles d’autorisation par espace de noms | Les demandes suivantes pour la création de règle d’autorisation sont rejetées.|12 |
| Nombre d’appels à la méthode GetRuntimeInformation |  - | 50 par seconde | 
| Nombre de réseaux virtuels (VNet) | - | 128 | 
| Nombre de règles de configuration IP | - | 128 | 
| Longueur maximale du nom d’un groupe de schémas | | 50 |  
| Longueur maximale du nom d’un schéma | | 100 |    
| Taille en octets par schéma | | 1 Mo |   
| Nombre de propriétés par groupe de schémas | | 1 024 |
| Taille en octets par clé de propriété de groupe de schéma | | 256 | 
| Taille en octets par valeur de propriété de groupe de schéma | | 1 024 | 

### <a name="basic-vs-standard-vs-dedicated-tiers"></a>Comparaison des niveaux de base, standard et dédié
Le tableau suivant indique les limites qui peuvent être différentes pour les niveaux de base, standard et dédié. Dans le tableau, UC signifie [unité de capacité](../articles/event-hubs/event-hubs-dedicated-overview.md) et UD [unité de débit](../articles/event-hubs/event-hubs-faq.yml#what-are-event-hubs-throughput-units-). 

| Limite | De base | standard | Dédié |
| ----- | ----- | -------- | -------- | 
| Taille maximale d’une publication Event Hubs | 256 KB | 1 Mo | 1 Mo |
| Nombre de groupes de consommateurs par Event Hub | 1 | 20 | Aucune limite par unité de capacité, 1 000 par hub d’événements |
| Nombre de connexions AMQP par espace de noms | 100 | 5 000 | 100 000 inclus et maximum |
| Période de rétention maximale des données d’événement | 1 jour | 1 à 7 jours | 90 jours, 10 To inclus per unité de capacité |
| Nombre maximal d’UD et d’UC |20 UD | 20 UD | 20 CUS |
| Nombre de partitions par Event Hub | 32 | 32 | 1 024 par hub d’événements<br/> 2 000 par unité de capacité |
| Nombre d’espaces de noms par abonnement | 100 | 100 | 100 (50 par UC) |
| Nombre d’Event Hubs par espace de noms | 10 | 10 | 1 000 |
| Événements d’entrée | | Paiement par million d’événements | Inclus|
| Capture | N/A | Paiement par heure | Inclus |
| Taille du registre de schémas (espace de noms) en mégaoctets | N/A | 25 |  1 024 |
| Nombre de groupes de schémas dans un registre de schémas ou espace de noms | N/A | 1 - sans compter le groupe par défaut | 1 000 |
| Nombre de versions de schéma pour tous les groupes de schémas | N/A | 25 | 10000 |

> [!NOTE]
> Vous pouvez publier les événements individuellement ou par lots. La limite de publication (selon le SKU) s’applique, qu’il s’agisse d’un événement unique ou d’un lot. La publication d’événements plus volumineux que le seuil maximum sera rejetée.

