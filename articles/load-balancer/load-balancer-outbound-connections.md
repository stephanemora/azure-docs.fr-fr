---
title: Utilisation d’un équilibreur de charge Azure en tant que proxy sortant
description: Décrit comment utiliser un équilibreur de charge Azure en tant que proxy pour la connectivité Internet sortante
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 422f8106ac52c85f0680d54e420d0f1b4d326910
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/14/2020
ms.locfileid: "92017690"
---
# <a name="outbound-proxy-azure-load-balancer"></a>Utilisation d’un équilibreur de charge Azure en tant que proxy sortant

Vous pouvez utiliser un équilibreur de charge Azure en tant que proxy pour la connectivité Internet sortante. L’équilibreur de charge fournit la connectivité sortante pour les instances de serveur back-end. 

Cette configuration utilise une **SNAT (« Source Network Address Translation », ou « traduction d’adresses réseau sources »)** . La SNAT remplace l’adresse IP du back-end par l’adresse IP publique de votre équilibreur de charge. 

La SNAT permet le **masquage d’IP** de l’instance back-end. Ce masquage permet d’empêcher des sources externes d’avoir une adresse directe vers les instances back-end. 

Le partage d’une adresse IP entre des instances back-end réduit le coût des adresses IP publiques statiques et prend en charge des scénarios tels que la simplification des listes d’adresses IP autorisées avec un trafic provenant d’adresses IP publiques connues. 

## <a name="sharing-ports-across-resources"></a><a name ="snat"></a> Partage de ports entre des ressources

Si les ressources back-end d’un équilibreur de charge n’ont pas d’adresses IP publiques au niveau de l’instance (ILPIP), elles établissent une connectivité sortante via l’adresse IP front-end de l’équilibreur de charge public.

Les ports sont utilisés pour générer des identificateurs uniques, eux-mêmes utilisés pour gérer des flux distincts. Internet utilise cinq tuples pour fournir cette distinction.

Ces cinq tuples sont constitués des éléments suivants :

* Adresse IP de destination
* Port de destination
* IP Source
* Le port et le protocole source pour fournir cette distinction.

Si un port est utilisé pour des connexions entrantes, il aura un **écouteur** pour ses requêtes de connexions entrantes et ne pourra pas être utilisé pour les connexions sortantes. 

Pour établir une connexion sortante, vous devez utiliser un **port éphémère** pour fournir un port à la destination, afin de communiquer et de maintenir un flux de trafic distinct. 

Chaque adresse IP a 65 535 ports. Les 1024 premiers ports sont réservés en tant que **ports système**. Chaque port peut être utilisé pour les connexions entrantes ou sortantes pour TCP et UDP. 

Azure permet d’utiliser une partie des ports restants (64 000) en tant que **ports éphémères**. Quand une adresse IP est ajoutée en tant que configuration d’adresse IP front-end, ces ports éphémères peuvent être utilisés pour la SNAT.

Grâce aux règles de trafic sortant, ces ports SNAT peuvent être distribués aux instances back-end pour leur permettre de partager les adresses IP publiques de l’équilibreur de charge pour les connexions sortantes.

La mise en réseau sur l’hôte pour chaque instance back-end effectuera une traduction d’adresses réseau sources (SNAT) sur les paquets qui font partie d’une connexion sortante. L’hôte remplace l’adresse IP source par l’une des adresses IP publiques. L’hôte remplace le port source de chaque paquet sortant par l’un des ports SNAT.

## <a name="exhausting-ports"></a><a name="scenarios"></a> Épuisement des ports

Chaque connexion à une même adresse IP de destination et au même port de destination utilisera un port SNAT. Cette connexion gère un **flux de trafic** distinct depuis l’instance ou le **client** back-end vers un **serveur**. Ce processus fournit au serveur un port distinct vers lequel envoyer le trafic. Sans ce processus, l’ordinateur client ignore dans quel flux trouver tel ou tel paquet.

Imaginez que plusieurs navigateurs vont vers https://www.microsoft.com, à savoir :

* IP de destination = 23.53.254.142
* Port de destination = 443
* Protocole = TCP

Sans ports de destination différents pour le trafic de retour (le port SNAT utilisé pour établir la connexion), le client n’a aucun moyen de séparer le résultat d’une requête d’un autre.

Le nombre de connexions sortantes peut augmenter rapidement. L’instance back-end risque de se retrouver en manque de ports. Si la **réutilisation des connexions** n’est pas activée, le risque d’**épuisement des ports** SNAT augmente.

Un épuisement des ports provoque l’échec des nouvelles connexions sortantes vers une adresse IP de destination. Les connexions sont correctement établies lorsqu’un port est disponible. Cet épuisement se produit lorsque les 64 000 ports à partir d’une adresse IP sont répartis sur de nombreuses instances back-end. Pour obtenir des conseils sur l’atténuation de l’épuisement des ports SNAT, consultez ce [guide de résolution des problèmes](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection).  

Pour les connexions TCP, l’équilibreur de charge utilise un seul port SNAT pour chaque adresse IP et port de destination. Ce multiusage permet plusieurs connexions à la même adresse IP de destination avec le même port SNAT. Ce multiusage est limité si la connexion ne dirige pas vers différents ports de destination.

Pour les connexions UDP, l’équilibreur de charge utilise un algorithme nommé « **port-restricted cone NAT** » (ou « NAT à cône restrictif sur les ports »), qui consomme un port SNAT par adresse IP de destination, quel que soit le port de destination. 

Cela permet de réutiliser un port pour un nombre illimité de connexions. Le port est réutilisé uniquement si l’adresse IP ou le port de destination est différent.

## <a name="port-allocation"></a><a name="preallocatedports"></a> Allocation de port

Chaque adresse IP publique allouée en tant qu’adresse IP front-end de votre équilibreur de charge reçoit 64 000 ports SNAT pour ses membres de pool back-end. Les ports ne peuvent pas être partagés avec des membres du pool back-end. Une plage de ports SNAT ne peut être utilisée que par une seule instance back-end pour garantir le routage correct des paquets de retour. 

Nous vous recommandons d’utiliser une règle de trafic sortant explicite pour configurer l’allocation de port SNAT. Cette règle permet de maximiser le nombre de ports SNAT dont dispose chaque instance de back-end pour les connexions sortantes. 

Si vous utilisez l’allocation automatique de port SNAT sortant par le biais d’une règle d’équilibrage de charge, la table d’allocation définira votre allocation de port.

Le tableau suivant <a name="snatporttable"></a>présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool back-end :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources préaffectés par configuration IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 | 

>[!NOTE]
> Si la taille maximale de votre pool back-end est de 6, chaque instance peut avoir 64 000 / 10 = 6 400 ports si vous définissez une règle de trafic sortant explicite. Selon le tableau ci-dessus, chaque instance n’aura que 1 024 ports si vous choisissez l’allocation automatique.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Règles de trafic sortant et NAT de réseau virtuel

Les règles de trafic sortant de l’équilibreur de charge Azure et le service NAT de réseau virtuel sont des options disponibles pour la sortie à partir d’un réseau virtuel.

Pour plus d’informations sur les règles de trafic sortant, consultez [Règles de trafic sortant](outbound-rules.md).

Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-overview.md)

## <a name="constraints"></a>Contraintes

*   Les ports seront libérés après 15 secondes si une **réinitialisation TCP** (TCP RST) est reçue ou envoyée
*   Les ports seront libérés après 240 secondes si un paquet **FINACK** est reçu ou envoyé
*   Lorsqu’une connexion est inactive et qu’aucun nouveau paquet n’est envoyé, les ports sont libérés après un délai allant de 4 à 120 minutes.
  * Ce délai peut être configuré à l’aide des règles de trafic sortant.
*   Chaque adresse IP fournit 64 000 ports qui peuvent être utilisés pour la SNAT.
*   Chaque port peut être utilisé pour les connexions TCP et UDP à une adresse IP de destination
  * Un port UDP SNAT est nécessaire si le port de destination est unique ou non. Pour chaque connexion UDP à une adresse IP de destination, un port UDP SNAT est utilisé.
  * Un port TCP SNAT peut être utilisé pour plusieurs connexions à la même adresse IP de destination, à condition que les ports de destination soient différents.
*   L’épuisement des ports SNAT se produit lorsqu’une instance back-end n’a pas assez de ports SNAT. Un équilibreur de charge peut toujours avoir des ports SNAT inutilisés. Si une instance back-end utilise plus de ports SNAT que ceux qui lui sont alloués, elle ne pourra pas établir de nouvelles connexions sortantes.

## <a name="next-steps"></a>Étapes suivantes

*   [Résoudre les échecs de connexion sortante provoqués par l’épuisement des ports SNAT](https://docs.microsoft.com/azure/load-balancer/troubleshoot-outbound-connection)
*   [Passez en revue les métriques SNAT](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics#how-do-i-check-my-snat-port-usage-and-allocation) et familiarisez-vous avec la méthode appropriée pour les filtrer, les fractionner et les afficher.

