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
ms.openlocfilehash: d0015868f6b53c2903b83795839e2dbcffd09ce4
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92116821"
---
Le tableau suivant répertorie les quotas et les limites propres à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Pour plus d’informations sur la tarification des hubs d’événements, consultez la rubrique [Tarification des hubs d’événements](https://azure.microsoft.com/pricing/details/event-hubs/).

### <a name="common-limits-for-all-tiers"></a>Limites communes à tous les niveaux
Les limites suivantes sont communes à tous les niveaux. 

| Limite |  Notes | Valeur |
| --- |  --- | --- |
| Nombre d’espaces de noms Event Hubs par abonnement |- |100 |
| Nombre d’Event Hubs par espace de noms | Les demandes suivantes de création d’un Event Hub sont rejetées. |10 |
| Nombre de partitions par Event Hub |- |32 |
| Taille du nom d’un Event Hub |- | 256 caractères |
| Taille du nom d’un groupe de consommateurs |- | 256 caractères |
| Nombre de récepteurs non epoch par groupe de consommateurs |- |5 |
| Nombre de règles d’autorisation par espace de noms | Les demandes suivantes pour la création de règle d’autorisation sont rejetées.|12 |
| Nombre d’appels à la méthode GetRuntimeInformation |  - | 50 par seconde | 
| Nombre de règles de réseau virtuel (VNet) et de configuration IP | - | 128 | 


### <a name="basic-and-standard-tiers"></a>Niveaux de base et standard
Le tableau suivant indique les limites qui peuvent être différentes pour les niveaux de base et standard. 

| Limite | Notes | De base | Standard |
| --- |  --- | -- | --- |
| Taille maximale de l’événement Event Hubs| &nbsp; | 256 KB | 1 Mo |
| Nombre de groupes de consommateurs par Event Hub | &nbsp; |1 |20 |
| Nombre de connexions AMQP par espace de noms | Les demandes suivantes de connexions supplémentaires sont rejetées et le code appelant reçoit une exception. |100 |5 000|
| Période de rétention maximale des données d’événement | &nbsp; |1 jour |1 à 7 jours |
| Unités de débit maximales |Le dépassement de la limite d’unités de débit entraîne la limitation de vos données et la génération d’une exception de [serveur occupé](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Vous pouvez demander une plus grande quantité d’unités de débit pour le niveau Standard en remplissant une [demande de support](/azure/azure-portal/supportability/how-to-create-azure-support-request). Les [unités de débit supplémentaires](../articles/event-hubs/event-hubs-auto-inflate.md) sont disponibles par blocs de 20 sur la base d’un engagement d’achat ferme. |20 | 20 | 

### <a name="dedicated-tier"></a>Niveau Dedicated
L’offre Event Hubs Dedicated est facturée à un tarif mensuel fixe, avec un minimum de 4 heures d’utilisation. Le niveau Dedicated offre toutes les fonctionnalités du plan Standard, mais avec la capacité de mise à l’échelle de classe entreprise et les limites pour les clients avec des charges de travail exigeantes. 

| Fonctionnalité | limites |
| --- | ---|
| Bande passante |  20 CUS |
| Espaces de noms | 50 par unité de capacité |
| Hubs d'événements |  1 000 par espace de noms |
| Taille des messages | 1 Mo |
| Partitions | 2 000 par unité de capacité |
| Groupes de consommateurs | Aucune limite par unité de capacité, 1 000 par hub d’événements |
| Connexions réparties | 100 K inclus |
| Durée de conservation des messages | 90 jours, 10 To inclus per unité de capacité |
| Événements d’entrée | Inclus |
| Capture | Inclus |


### <a name="schema-registry-limitations"></a>Limitations du registre de schémas

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Limites identiques pour les niveaux **standard** et **dedicated** 
| Fonctionnalité | Limite | 
| --- |  --- | -- |
| Longueur maximale du nom d’un groupe de schémas | 50 |  
| Longueur maximale du nom d’un schéma | 100 |    
| Taille en octets par schéma | 1 Mo |   
| Nombre de propriétés par groupe de schémas | 1 024 |
| Taille en octets par clé de propriété de groupe | 256 | 
| Taille en octets par valeur de propriété de groupe | 1 024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Limites différentes pour les niveaux **standard** et **dedicated** 

| Limite | standard | Dédié | 
| --- |  --- | -- | --- |
| Taille du registre de schémas (espace de noms) en mégaoctets | 25 |  1 024 |
| Nombre de groupes de schémas dans un registre de schémas (espace de noms)| 1 (sans compter celui par défaut) | 1 000 |
| Nombre de versions de schéma pour tous les groupes de schémas | 25 | 10000 |





