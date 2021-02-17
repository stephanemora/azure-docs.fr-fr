---
title: Modes de distribution d’Azure Load Balancer
description: Commencez à en savoir plus sur les différents modes de distribution d’Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 02/04/2021
ms.custom: template-concept
ms.openlocfilehash: fcea2e962722773f59e73df898e0188c3f6454b6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2021
ms.locfileid: "99551086"
---
# <a name="azure-load-balancer-distribution-modes"></a>Modes de distribution d’Azure Load Balancer

Azure Load Balancer prend en charge deux modes de distribution pour le routage des connexions à votre application à charge équilibrée :

* Basé sur un hachage
* Affinité IP source

## <a name="hash-based"></a>Basé sur un hachage

Par défaut, le mode de distribution pour Azure Load Balancer est un hachage à cinq tuples. 

Le tuple est composé des éléments suivants :
* **IP source**
* **Port source**
* **IP de destination**
* **Port de destination**
* **Type de protocole**

Le hachage est utilisé pour mapper le trafic aux serveurs disponibles. L’algorithme fournit l’adhérence uniquement dans une session de transport. Les paquets de la même session sont dirigés vers la même instance IP de centre de données derrière le point de terminaison d’équilibrage de charge. Lorsque le client démarre une nouvelle session à partir du même IP source, le port source change et contraint le trafic à se diriger vers un autre point de terminaison du centre de données.

![Mode de distribution basé sur le hachage à cinq tuples](./media/distribution-mode-concepts/load-balancer-distribution.png)

Le mode basé sur le hachage a un type de configuration :

* **Aucun (basé sur le hachage)**  : spécifie que les demandes successives provenant du même client peuvent être gérées par n’importe quelle machine virtuelle.

## <a name="source-ip-affinity"></a>Affinité IP source

Ce mode de distribution est également connu sous le nom d’affinité de session ou d’affinité d’IP client. Il utilise un hachage à deux tuples (IP source et IP de destination) ou à trois tuples (IP source, IP de destination et type de protocole) pour mapper le trafic vers les serveurs disponibles. 

En utilisant l’affinité d’IP source, les connexions démarrées depuis le même ordinateur client s’orientent vers le même point de terminaison du centre de données.

L’image suivante illustre une configuration à deux tuples. Notez comment la distribution à deux tuples passe de l’équilibreur de charge à la machine virtuelle 1 (VM1). La machine virtuelle VM1 est ensuite sauvegardée par VM2 et VM3.

![Mode de distribution d’affinité de session à deux tuples](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Le mode d’affinité d’IP source a deux types de configuration :

* **Adresse IP du client (tuple de 2 éléments d’affinité d’adresse IP source)** : spécifie que les demandes successives provenant de la même adresse IP du client seront gérées par le même ordinateur virtuel.
* **Adresse IP et protocole du client (tuple de 3 éléments d’affinité d’adresse IP source)** : spécifie que les demandes successives provenant de la même combinaison d’adresse IP et de protocole du client seront gérées par le même ordinateur virtuel.

## <a name="use-cases"></a>Cas d'utilisation

L’affinité d’IP source avec adresse IP et protocole du client (affinité d’IP source à trois tuples) permet de résoudre une incompatibilité entre Azure Load Balancer et Passerelle Bureau à distance. 

Le chargement de médias constitue un autre scénario d’utilisation. Le chargement des données se produit via UDP, mais le plan de contrôle est obtenu via TCP :

* Un client démarre une session TCP à l’adresse publique à charge équilibrée et est dirigé vers une adresse DIP spécifique. Ce canal reste actif, de sorte que l’intégrité de la connexion puisse être surveillée.
* Une nouvelle session UDP issue du même ordinateur client est démarrée sur le même point de terminaison public d’équilibrage de charge. La connexion est dirigée vers le même point de terminaison DIP que la connexion TCP précédente. Le chargement de médias peut être exécuté à débit élevé alors que le canal de contrôle via TCP est maintenu.

> [!NOTE]
> Lorsqu’un jeu d’équilibrage de charge est modifié (suppression ou ajout d’une machine virtuelle), la distribution des demandes du client est recalculée. Vous ne pouvez pas dépendre de nouvelles connexions à partir de clients existants qui se retrouvent sur le même serveur. En outre, le mode de distribution d’affinité d’IP source peut entraîner une distribution inégale du trafic. Les clients qui s’exécutent derrière des serveurs proxy peuvent être vus comme une application cliente unique.


## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur la configuration du mode de distribution d’Azure Load Balancer, consultez [Configuration du mode de distribution pour Azure Load Balancer](load-balancer-distribution-mode.md).

