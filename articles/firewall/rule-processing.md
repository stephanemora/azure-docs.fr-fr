---
title: Logique de traitement des règles du service Pare-feu Azure
description: En savoir plus sur la logique de traitement des règles du service Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 542682037a932a2e3b08c71b38b64b2694ad40f3
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228097"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logique de traitement des règles du service Pare-feu Azure
Le service Pare-feu Azure comporte des règles de traduction d’adresses réseau (NAT), des règles de réseau et des règles d’application. Les règles sont traitées selon leur type.


## <a name="network-rules-and-applications-rules"></a>Règles de réseau et règles d’application 
Le service applique d’abord les règles de réseau, puis les règles d’application. Ce processus prend fin dès qu’une règle est exécutée. Par conséquent, si une correspondance est trouvée dans les règles de réseau, les règles d’application ne sont pas traitées.  S’il n’y a pas de correspondance avec les règles de réseau, et si le paquet a un protocole HTTP/HTTPS, il est évalué par les règles d’application. Si aucune correspondance n’est trouvée, le paquet est évalué par rapport à la collection de règles de l’infrastructure. S’il n’existe toujours pas de correspondance, le paquet est refusé par défaut.

## <a name="nat-rules"></a>Règles NAT
Vous pouvez activer la connectivité entrante en configurant le mode DNAT (Destination Network Address Translation), comme décrit dans l’article [Didacticiel : Filtrer le trafic entrant avec pare-feu Azure DNAT via le portail Azure](tutorial-firewall-dnat.md). Les règles DNAT sont appliquées en premier. Si une correspondance est trouvée, une règle de réseau correspondante implicite autorisant le trafic traduit est ajoutée. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit. Aucune règle d’application n’est appliquée pour ces connexions.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).