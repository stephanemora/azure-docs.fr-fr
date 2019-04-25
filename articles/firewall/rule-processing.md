---
title: Logique de traitement des règles du service Pare-feu Azure
description: En savoir plus sur la logique de traitement des règles du service Pare-feu Azure
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/27/2018
ms.author: victorh
ms.openlocfilehash: 12d86793c0d75413559aad77c558c4adb7ac91af
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193643"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logique de traitement des règles du service Pare-feu Azure
Le service Pare-feu Azure comporte des règles de traduction d’adresses réseau (NAT), des règles de réseau et des règles d’application. Les règles sont traitées selon leur type.


## <a name="network-rules-and-applications-rules"></a>Règles de réseau et règles d’application 
Le service applique d’abord les règles de réseau, puis les règles d’application. Ce processus prend fin dès qu’une règle est exécutée. Par conséquent, si une correspondance est trouvée dans les règles de réseau, les règles d’application ne sont pas traitées.  S’il n’y a pas de correspondance avec les règles de réseau, et si le paquet a un protocole HTTP/HTTPS, il est évalué par les règles d’application. Si aucune correspondance n’est trouvée, le paquet est évalué par rapport à la collection de règles de l’infrastructure. S’il n’existe toujours pas de correspondance, le paquet est refusé par défaut.

## <a name="nat-rules"></a>Règles NAT
Connectivité entrante peut être activée en configurant la traduction d’adresse de Destination réseau (DNAT) comme décrit dans [didacticiel : Filtrer le trafic entrant avec DNAT de pare-feu Azure à l’aide du portail Azure](tutorial-firewall-dnat.md). Les règles DNAT sont appliquées en premier. Si une correspondance est trouvée, une règle de réseau correspondante implicite autorisant le trafic traduit est ajoutée. Vous pouvez remplacer ce comportement en ajoutant explicitement une collection de règles de réseau avec des règles de refus correspondant au trafic traduit. Aucune règle d’application n’est appliquée pour ces connexions.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [déployer et configurer un Pare-feu Azure](tutorial-firewall-deploy-portal.md).