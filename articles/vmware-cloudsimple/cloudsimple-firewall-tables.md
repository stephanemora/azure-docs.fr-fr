---
title: Pare-feu tables - VMware Solution par CloudSimple - Azure
description: En savoir plus sur les tables de pare-feu de cloud privé de CloudSimple et règles de pare-feu.
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 861c2e86d623c46c14366f19457d1f689386a316
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/26/2019
ms.locfileid: "64577344"
---
# <a name="firewall-tables-overview"></a>Vue d’ensemble des tables de pare-feu

Une table de pare-feu répertorie les règles pour filtrer le trafic réseau vers et à partir de ressources de cloud privé. Vous pouvez les appliquer à un réseau local virtuel ou un sous-réseau. Ensuite, les règles de contrôlent le trafic réseau entre un réseau de la source ou l’adresse IP et un réseau de destination ou l’adresse IP.

## <a name="firewall-rules"></a>Règles de pare-feu

Le tableau suivant décrit les paramètres dans une règle de pare-feu.

| Propriété | Détails |
| ---------| --------|
| **Name** | Un nom qui identifie de façon unique la règle de pare-feu et de son objectif. |
| **Priorité** | Un nombre compris entre 100 et 4096, avec 100 étant la priorité la plus élevée. Les règles sont traitées par ordre de priorité. Lorsque le trafic rencontre une correspondance de règle, le traitement des règles s’arrête. Par conséquent, toutes les règles qui existent avec une priorité plus faible qui ont les mêmes attributs que les règles de priorité plus élevée ne sont pas traités.  Prenez soin d’éviter les règles en conflit. |
| **État de suivi** | Le suivi peut être sans état (Cloud privé, Internet ou VPN) ou avec état (adresse IP publique).  |
| **Protocole** | Les options incluent Any, TCP ou UDP. Si vous avez besoin d’ICMP, utilisez un. |
| **Direction** | Indique si la règle s’applique au trafic entrant ou sortant. |
| **Action** | Autoriser ou refuser pour le type de trafic défini dans la règle. |
| **Source** | Une adresse IP, bloc inter-domain routing (CIDR) (10.0.0.0/24, par exemple) ou Any.  En spécifiant une plage, une balise de service ou un groupe de sécurité d’application vous permet de créer des règles de sécurité moins. |
| **Port source** | Port réseau sur lequel le trafic est issue.  Vous pouvez spécifier un port individuel ou une plage de ports, tels que 443 ou 8000-8080. La spécification de plages vous permet de créer moins de règles de sécurité. |
| **Destination** | Une adresse IP, bloc inter-domain routing (CIDR) (10.0.0.0/24, par exemple) ou Any.  En spécifiant une plage, une balise de service ou un groupe de sécurité d’application vous permet de créer des règles de sécurité moins.  |
| **Port de destination** | Port auquel le trafic réseau des flux.  Vous pouvez spécifier un port individuel ou une plage de ports, tels que 443 ou 8000-8080. La spécification de plages vous permet de créer moins de règles de sécurité.|

### <a name="stateless"></a>Sans état

Une règle sans état examine uniquement les paquets et les filtre en fonction de la règle.  
Règles supplémentaires peuvent être nécessaires pour le flux de trafic dans le sens inverse.  Utilisez les règles sans état pour le trafic entre les points suivants :

* Sous-réseaux de Clouds privés
* Sous-réseau local et un sous-réseau de Cloud privé
* Trafic Internet à partir de nuages privés

### <a name="stateful"></a>Avec état

 Une règle avec état est prenant en charge des connexions qui le traversent. Un enregistrement de flux est créé pour les connexions existantes. La communication est autorisée ou refusée en fonction de l’état de connexion de l’enregistrement de flux.  Utilisez ce type de règle pour les adresses IP publiques pour filtrer le trafic à partir d’Internet.

### <a name="default-rules"></a>Règles par défaut

Les règles par défaut suivantes sont créées sur chaque table de pare-feu.

|Priorité|Nom|État de suivi|Direction|Type de trafic|Protocol|Source|Port source|Destination|Port de destination|Action|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65 000|allow-all-to-internet|Avec état|Règle de trafic sortant|Trafic IP ou l’internet public|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|
|65 001|deny-all-from-internet|Avec état|Trafic entrant|Trafic IP ou l’internet public|Tous|Quelconque|Quelconque|Quelconque|Quelconque|Deny|
|65002|allow-all-to-intranet|Sans état|Règle de trafic sortant|Cloud privé interne ou le trafic VPN|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|
|65003|allow-all-from-intranet|Sans état|Trafic entrant|Cloud privé interne ou le trafic VPN|Tous|Quelconque|Quelconque|Quelconque|Quelconque|AUTORISER|

## <a name="next-steps"></a>Étapes suivantes

* [Définir les règles et les tables de pare-feu](https://docs.azure.cloudsimple.com/firewall/)