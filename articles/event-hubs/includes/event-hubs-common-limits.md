---
title: include file description: include file services: event-hubs author: spelluru ms.service: event-hubs ms.topic: include ms.date: 05/10/2021 ms.author: spelluru ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

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

