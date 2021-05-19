---
title: SNAT (Source Network Address Translation) pour les connexions sortantes
titleSuffix: Azure Load Balancer
description: Découvrez comment Azure Load Balancer est utilisé pour la connectivité Internet sortante (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 05/05/2021
ms.author: allensu
ms.openlocfilehash: 08064d4d9b08a35fe59673478faa001a13d50d0f
ms.sourcegitcommit: 2cb7772f60599e065fff13fdecd795cce6500630
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108804481"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Utilisation de SNAT (Source Network Address Translation) pour les connexions sortantes

Certains scénarios requièrent des machines virtuelles ou des instances de calcul pour disposer d’une connectivité sortante à Internet. Les adresses IP front-end d’un équilibreur de charge public Azure peuvent être utilisées pour fournir une connectivité sortante à Internet pour des instances back-end. Cette configuration utilise la **traduction d’adresses réseau source (SNAT)** , car l’adresse IP de la machine virtuelle ou de la **source** est traduite en adresse IP publique. La SNAT mappe l’adresse IP du back-end sur l’adresse IP publique de votre équilibreur de charge. Cela permet d’empêcher des sources externes d’avoir une adresse directe vers les instances back-end.

## <a name="azures-outbound-connectivity-methods"></a><a name="scenarios"></a>Méthodes de connectivité sortante d’Azure

La connectivité sortante à Internet peut être activée des manières suivantes :

| # | Méthode | Type d’allocation des ports | Qualité de production ? | Rating |
| ------------ | ------------ | ------ | ------------ | ------------ |
| 1 | Utilisation des adresses IP frontales d’un équilibreur de charge pour le trafic sortant via des règles sortantes | Statique, explicite | Oui, mais pas à l’échelle | OK | 
| 2 | Association d’une passerelle NAT au sous-réseau | Statique, explicite | Oui | La meilleure | 
| 3 | Attribution d’une adresse IP publique à la machine virtuelle | Statique, explicite | Oui | OK | 
| 4 | Utilisation des adresses IP frontales d’un équilibreur de charge pour les messages sortants (et entrants) | Par défaut, implicite | Non | Pire


## <a name="leveraging-the-frontend-ip-address-of-a-load-balancer-for-outbound-via-outbound-rules"></a><a name="outboundrules"></a>Utilisation de l’adresse IP frontale d’un équilibreur de charge pour le trafic sortant via des règles sortantes

Les règles de trafic sortant vous permettent de définir explicitement la traduction d’adresses réseau sources (source network address translation, SNAT) pour un équilibreur de charge Standard public. Cette configuration vous permet d’utiliser les adresses IP publiques de votre équilibreur de charge pour fournir une connectivité Internet sortante pour vos instances de serveur principal.

Cette configuration permet :

- L’usurpation d’adresse IP
- La simplification de vos listes d’autorisation.
- La réduction du nombre de ressources IP publiques pour le déploiement

Avec les règles de trafic sortant, vous disposez d’un contrôle déclaratif complet sur la connectivité Internet sortante. Les règles de trafic sortant vous permettent de mettre à l’échelle et de régler cette capacité en fonction de vos besoins spécifiques.

Pour plus d’informations sur les règles de trafic sortant, consultez [Règles de trafic sortant](outbound-rules.md).



## <a name="associating-a-vnet-nat-to-the-subnet"></a>Association d’un réseau virtuel NAT au sous-réseau

La traduction d’adresses réseau (NAT) simplifie la connectivité Internet sortante-uniquement pour les réseaux virtuels. Quand il est configuré sur un sous-réseau, toute la connectivité sortante utilise vos adresses IP publiques statiques spécifiées. Une connectivité sortante est possible sans équilibreur de charge ni adresses IP publiques directement attachées aux machines virtuelles. NAT est complètement managé et hautement résilient.

L’utilisation d’un NAT de réseau virtuel est la meilleure méthode pour la connectivité sortante, car elle est hautement évolutive, fiable et n’est pas susceptible à l’épuisement des ports SNAT.

Pour plus d’informations sur le service NAT de réseau virtuel Azure, consultez [Qu’est-ce que le service NAT de réseau virtuel ?](../virtual-network/nat-overview.md)

##  <a name="assigning-a-public-ip-to-the-virtual-machine"></a>Attribution d’une adresse IP publique à la machine virtuelle

 | Associations | Méthode | Protocoles IP |
 | ---------- | ------ | ------------ |
 | Équilibreur de charge public ou autonome | [SNAT (Source Network Address Translation, traduction d’adresses réseau sources)](#snat) </br> non utilisée. | Protocole TCP (Transmission Control Protocol) </br> Protocole UDP (User Datagram Protocol) </br> Protocole ICMP (Internet Control Message Protocol) </br> Protection ESP (Encapsulating Security Payload) |

 Tout le trafic est retourné au client demandeur à partir de l’adresse IP publique de la machine virtuelle (IP au niveau de l’instance).
 
 Azure utilise l’IP publique attribuée à la configuration IP de la carte d’interface réseau de l’instance pour tous les flux sortants. L’instance a tous les ports éphémères disponibles. Peu importe si la machine virtuelle est équilibrée en charge ou non. Ce scénario prend le pas sur les autres. 

 Une adresse IP publique affectée à une machine virtuelle constitue une relation 1:1 (non pas une relation 1-à-plusieurs) ; elle est implémentée comme un NAT 1:1 sans état.



## <a name="leveraging-the-frontend-ip-address-of-a-load-balancer-for-outbound-and-inbound"></a><a name="snat"></a> Utilisation de l’adresse IP frontale d’un équilibreur de charge pour les messages sortants (et entrants)
>[!NOTE]
> Cette méthode n’est **PAS recommandée** pour les charges de travail de production, car elle augmente le risque d’épuisement des ports. N’hésitez pas à utiliser cette méthode pour les charges de travail de production afin d’éviter les échecs de connexion potentiels. 


Si les ressources principales d’un équilibreur de charge n’ont pas de règles de trafic sortant, d’adresses IP publiques de niveau instance ou de NAT de réseau virtuel configurés, elles établissent une connectivité sortante via l’adresse IP frontale de l’équilibreur de charge. C’est ce qu’on appelle SNAT par défaut.


### <a name="what-are-snat-ports"></a>Que sont les ports SNAT ?
Les ports sont utilisés pour générer des identificateurs uniques, eux-mêmes utilisés pour gérer des flux distincts. Internet utilise cinq tuples pour fournir cette distinction.

Si un port est utilisé pour des connexions entrantes, il a un **écouteur** pour les requêtes de connexions entrantes sur ce port. Ce port ne peut pas être utilisé pour les connexions sortantes. Pour établir une connexion sortante, utilisez un **port éphémère** pour fournir un port à la destination, afin de communiquer et de maintenir un flux de trafic distinct. Quand ces ports éphémères sont utilisés pour SNAT, ils sont appelés **ports SNAT** 

Par définition, chaque adresse IP a 65 535 ports. Chaque port peut être utilisé pour les connexions entrantes ou sortantes pour les protocoles TCP (Transmission Control Protocol) et UDP (User Datagram Protocol). Quand une IP publique est ajoutée en tant qu’IP front-end à un équilibreur de charge, Azure accorde 64 000 ports éligibles pour SNAT.

Chaque port utilisé pour une règle NAT de trafic entrant ou d’équilibrage de charge utilise une plage de huit ports dans ces 64 000 ports, ce qui réduit le nombre de ports éligibles à la SNAT. Si une règle NAT entrante ou d’équilibrage de charge se trouve dans la même plage de huit qu’une autre règle, elle ne consomme pas de ports supplémentaires. 

### <a name="how-does-default-snat-work"></a>Comment fonctionne le SNAT par défaut ?
Quand une machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source en adresse IP publique du front-end de l’équilibreur de charge public. Cette traduction s’effectue par le biais de la [SNAT](#snat). 

Si vous utilisez la SNAT par défaut par le biais d’une règle d’équilibrage de charge, les ports SNAT sont pré-alloués comme décrit dans le [tableau d’allocation des ports SNAT par défaut](#snatporttable).
 
Lorsque vous utilisez un équilibreur de charge interne Standard, il n’y a pas d’utilisation d’adresses IP éphémères pour SNAT. Cette fonctionnalité prend en charge la sécurité par défaut. Cette fonctionnalité garantit que toutes les adresses IP utilisées par les ressources sont configurables et peuvent être réservées. Pour obtenir une connectivité sortante à Internet lors de l’utilisation d’un équilibreur de charge interne standard, configurez :
- Une adresse IP publique au niveau de l’instance 
- NAT de réseau virtuel
-  les instances back-end à un équilibreur de charge public standard avec une règle de trafic sortant configurée.  

### <a name="what-is-the-ip-for-default-snat"></a>Quelle est l’adresse IP SNAT par défaut ?
Quand la machine virtuelle crée un flux sortant, Azure convertit l’adresse IP source en adresse IP source publique spécifiée dynamiquement. Cette adresse IP publique **n’est pas configurable** et ne peut pas être réservée. Cette adresse n’est pas comptabilisée dans la limite des ressources IP publiques de l’abonnement. 

L’adresse IP publique est publiée et une nouvelle adresse IP publique est demandée si vous redéployez ce qui suit : 
 * Machine virtuelle
 * Groupe à haute disponibilité
 * Jeu de mise à l’échelle de machine virtuelle 

>[!NOTE]
> Cette méthode n’est **PAS recommandée** pour les charges de travail de production, car elle augmente le risque d’épuisement des ports. N’hésitez pas à utiliser cette méthode pour les charges de travail de production afin d’éviter les échecs de connexion potentiels. 

| Type | Comportement sortant | 
 | ------------ | ------ | 
 | Équilibreur de charge public Standard | Utilisation des IP front-end de l’équilibreur de charge pour la SNAT 
 | Équilibreur de charge interne Standard | Aucune connectivité Internet, sécurisé par défaut | 
Équilibreur de charge public de base | Utilisation des IP front-end de l’équilibreur de charge pour la SNAT |
Équilibreur de charge interne de base | SNAT avec adresse IP dynamique inconnue| 

## <a name="exhausting-ports"></a> Épuisement des ports

Chaque connexion à une même adresse IP de destination et au même port de destination utilisera un port SNAT. Cette connexion gère un **flux de trafic** distinct depuis l’instance ou le **client** back-end vers un **serveur**. Ce processus fournit au serveur un port distinct vers lequel envoyer le trafic. Sans ce processus, l’ordinateur client ignore dans quel flux trouver tel ou tel paquet.

Imaginez que plusieurs navigateurs vont vers https://www.microsoft.com, à savoir :

* IP de destination = 23.53.254.142
* Port de destination = 443
* Protocole = TCP

Sans ports de destination différents pour le trafic de retour (le port SNAT utilisé pour établir la connexion), le client n’a aucun moyen de séparer le résultat d’une requête d’un autre.

Le nombre de connexions sortantes peut augmenter rapidement. L’instance back-end risque de se retrouver en manque de ports. Si la **réutilisation des connexions** n’est pas activée, le risque d’**épuisement des ports** SNAT augmente.

Un épuisement des ports provoque l’échec des nouvelles connexions sortantes vers une adresse IP de destination. Les connexions sont correctement établies lorsqu’un port est disponible. Cet épuisement se produit lorsque les 64 000 ports à partir d’une adresse IP sont répartis sur de nombreuses instances back-end. Pour obtenir des conseils sur l’atténuation de l’épuisement des ports SNAT, consultez ce [guide de résolution des problèmes](./troubleshoot-outbound-connection.md).  

Pour les connexions TCP, l’équilibreur de charge utilise un seul port SNAT pour chaque adresse IP et port de destination. Ce multiusage permet plusieurs connexions à la même adresse IP de destination avec le même port SNAT. Ce multiusage est limité si la connexion ne dirige pas vers différents ports de destination.

Pour les connexions UDP, l’équilibreur de charge utilise un algorithme nommé « **port-restricted cone NAT** » (ou « NAT à cône restrictif sur les ports »), qui consomme un port SNAT par adresse IP de destination, quel que soit le port de destination. 

Cela permet de réutiliser un port pour un nombre illimité de connexions. Le port est réutilisé uniquement si l’adresse IP ou le port de destination est différent.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Allocation des ports par défaut

Chaque adresse IP publique allouée en tant qu’adresse IP front-end de votre équilibreur de charge reçoit 64 000 ports SNAT pour ses membres de pool back-end. Les ports ne peuvent pas être partagés avec des membres du pool back-end. Une plage de ports SNAT ne peut être utilisée que par une seule instance back-end pour garantir le routage correct des paquets de retour. 

Si vous utilisez l’allocation automatique de port SNAT sortant par le biais d’une règle d’équilibrage de charge, la table d’allocation définira votre allocation de port.

Le tableau suivant <a name="snatporttable"></a>présente les préaffectations de ports SNAT pour les différents niveaux de tailles de pool back-end :

| Taille du pool (instances de machine virtuelle) | Ports de traduction d’adresses réseau sources par défaut par configuration IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1 000 | 32 | 

## <a name="constraints"></a>Contraintes

*   Lorsqu’une connexion est inactive et qu’aucun nouveau paquet n’est envoyé, les ports sont libérés après un délai allant de 4 à 120 minutes.
  * Ce délai peut être configuré à l’aide des règles de trafic sortant.
*   Chaque adresse IP fournit 64 000 ports qui peuvent être utilisés pour la SNAT.
*   Chaque port peut être utilisé pour les connexions TCP et UDP à une adresse IP de destination
  * Un port UDP SNAT est nécessaire si le port de destination est unique ou non. Pour chaque connexion UDP à une adresse IP de destination, un port UDP SNAT est utilisé.
  * Un port TCP SNAT peut être utilisé pour plusieurs connexions à la même adresse IP de destination, à condition que les ports de destination soient différents.
*   L’épuisement des ports SNAT se produit lorsqu’une instance back-end n’a pas assez de ports SNAT. Un équilibreur de charge peut toujours avoir des ports SNAT inutilisés. Si une instance back-end utilise plus de ports SNAT que ceux qui lui sont alloués, elle ne pourra pas établir de nouvelles connexions sortantes.

## <a name="next-steps"></a>Étapes suivantes

*   [Résoudre les échecs de connexion sortante provoqués par l’épuisement des ports SNAT](./troubleshoot-outbound-connection.md)
*   [Passez en revue les métriques SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) et familiarisez-vous avec la méthode appropriée pour les filtrer, les fractionner et les afficher.
