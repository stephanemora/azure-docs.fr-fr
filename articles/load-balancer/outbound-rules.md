---
title: Règles de trafic sortant Azure Load Balancer
description: Cet article explique comment configurer des règles de trafic sortant pour contrôler la sortie du trafic Internet avec Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 51810876e3636b7023ce9c9318a071636bb00c4c
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002605"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Règles de trafic sortant Azure Load Balancer

Les règles de trafic sortant vous permettent de configurer la SNAT (traduction d’adresses réseau source) sortante publiques dans Standard Load Balancer. Cette configuration vous permet d’utiliser la ou les adresses IP publique(s) de votre équilibreur de charge en tant que proxy.

Cette configuration permet :

* L’usurpation d’adresse IP
* La simplification de vos listes d’autorisation.
* La réduction du nombre de ressources IP publiques pour le déploiement.

Avec les règles de trafic sortant, vous disposez d’un contrôle déclaratif complet sur la connectivité Internet sortante. Les règles de trafic sortant vous permettent de mettre à l’échelle et de régler cette capacité en fonction de vos besoins spécifiques. 

Les règles de trafic sortant sont uniquement suivies si la machine virtuelle principale n’a pas d’adresse IP publique au niveau de l’instance (ILPIP).

![Règles de trafic sortant dans Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Avec les règle de trafic sortant, vous pouvez définir explicitement le comportement **SNAT** sortant.

Les règles de trafic sortant vous permettent de déterminer :

* **Quelles machines virtuelles doivent être traduites vers quelles adresses IP publiques.**
     * Deux règles : le pool principal A utilise l’adresse IP A et B, le pool principal B utilise l’adresse IP C et D.
* **Comment les ports SNAT de trafic sortant sont fournis.**
     * Le pool principal B est le seul pool effectuant des connexions sortantes, attribuez au pool principal B tous les ports SNAT et aucun au pool principal A.
* **Les protocoles qui fournissent la traduction sortante.**
     * Le pool principal B a besoin de ports UDP pour le trafic sortant. Le pool principal A a besoin de TCP. Donnez les ports TCP à A et les ports UDP à B.
* **La durée à utiliser comme délai d’inactivité des connexions sortantes ( 4 à 120 minutes).**
     * S’il existe des connexions de longue durée avec les KeepAlive, réservez les ports inactifs pour les connexions de longue durée pendant jusqu’à 120 minutes. Supposer que les connexions obsolètes sont abandonnées et les mettre en production les ports en 4 minutes pour les nouvelles connexions 
* **La réinitialisation TCP au terme du délai d’inactivité.**
     * lors du délai d’expiration des connexions inactives, est-ce que nous envoyons un RST TCP au client et au serveur afin qu’ils sachent que le flux est abandonné ?

## <a name="outbound-rule-definition"></a>Définition de règle de trafic sortant

Les règles de trafic sortant suivent la syntaxe habituelle des règles d’équilibrage de charge et des règles NAT de trafic entrant : **serveur frontal** + **paramètres** + **pool principal**. 

Une règle de trafic sortant configure la NAT de trafic sortant pour que _toutes les machines virtuelles identifiées par le pool backend_ soient traduites sur le _frontend_.  

Les _paramètres_ permettent de contrôler avec plus de précision l’algorithme de la NAT de trafic sortant.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Mettre à l’échelle la NAT de trafic sortant avec plusieurs adresses IP

Chaque adresse IP supplémentaire fournie par un serveur frontal met à disposition 64 000 ports éphémères que l’équilibreur de charge peut utiliser en tant que ports SNAT. 

Utilisez plusieurs adresses IP pour planifier des scénarios à grande échelle. Utilisez des règles de trafic sortant pour atténuer [l’épuisement SNAT](troubleshoot-outbound-connection.md#snatexhaust). 

Vous pouvez utiliser un [préfixe d’IP publique](https://aka.ms/lbpublicipprefix) directement avec une règle de trafic sortant. 

Un préfixe d’adresse IP publique augmente la mise à l’échelle de votre déploiement. Le préfixe peut être ajouté à la liste d’autorisation des flux provenant de vos ressources Azure. Vous pouvez configurer une configuration IP front-end au sein de l’équilibreur de charge pour référencer un préfixe d’adresse IP publique.  

L’équilibreur de charge contrôle le préfixe d’adresse IP publique. La règle de trafic sortant utilise automatiquement toutes les adresses IP publiques contenues dans le préfixe d’adresse IP publique pour les connexions sortantes. 

Chacune des adresses IP du préfixe d’IP publique fournit 64 000 ports éphémères supplémentaires par adresse IP que l’équilibreur de charge peut utiliser en tant que ports SNAT.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a>Délai d’inactivité des flux sortants et réinitialisation TCP

Les règles de trafic sortant ont un paramètre de configuration qui permet de contrôler le délai d’inactivité des flux sortants et de l’ajuster en fonction des besoins de votre application. Le délai d’inactivité des flux sortants est de 4 minutes par défaut. Pour plus d’informations, consultez [Configurer les délais d’inactivité](load-balancer-tcp-idle-timeout.md). 

Le comportement par défaut de l’équilibreur de charge consiste à supprimer silencieusement des flux quand le délai d’inactivité d’un flux sortant est atteint. Le paramètre `enableTCPReset` active un comportement et un contrôle prévisibles de l’application. Le paramètre détermine s’il faut envoyer une réinitialisation TCP bidirectionnelle (RST TCP) au délai d’expiration du délai d’inactivité sortant. 

Pour plus d’informations, y compris sur la disponibilité dans les régions, consultez [Réinitialisation TCP au terme du délai d’inactivité](https://aka.ms/lbtcpreset).

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Sécurisation et contrôle explicites de la connectivité sortante

Les règles d’équilibrage de charge fournissent une programmation automatique de la NAT de trafic sortant. Dans certains scénarios, il est préférable, ou obligatoire, de désactiver la programmation automatique de la NAT de trafic sortant par la règle d’équilibrage de charge. La désactivation via la règle vous permet de contrôler ou d’adapter vous-même le comportement.  

Vous pouvez utiliser ce paramètre de deux manières :

1. Prévention de l’adresse IP entrante pour la SNAT de trafic sortant. Désactivez la NAT de trafic sortant dans la règle d’équilibrage de charge.
  
2. Paramétrage de la **SNAT** de trafic sortant d’une adresse IP utilisée à la fois pour le trafic entrant et le trafic sortant. La NAT automatique de trafic sortant doit être désactivée pour permettre le contrôle par une règle de trafic sortant. Pour changer l’allocation de ports SNAT d’une adresse qui est également utilisée pour le trafic entrant, le paramètre `disableOutboundSnat` doit être défini sur true. 

L’opération de configuration d’une règle de trafic sortant échoue si vous tentez de redéfinir une adresse IP utilisée pour le trafic entrant.  Désactivez d’abord la NAT de trafic sortant de la règle d’équilibrage de charge.

>[!IMPORTANT]
> Votre machine virtuelle n’aura pas de connectivité sortante si vous définissez ce paramètre sur true et que vous n’avez pas créé de règle de trafic sortant pour définir la connectivité sortante.  Certaines opérations de votre machine virtuelle ou application peuvent avoir besoin d’une connectivité sortante. Assurez-vous de bien comprendre toutes les dépendances de votre scénario, ainsi que les conséquences d’un tel changement.

Dans certains cas, il est préférable pour une machine virtuelle de ne pas créer de flux sortant. Il peut exister une condition qui détermine les destinations qui peuvent recevoir des flux sortants ou celles qui lancent des flux entrants. Utilisez des [groupes de sécurité réseau](../virtual-network/security-overview.md) pour déterminer les destinations que la machine virtuelle atteint. Utilisez des groupes de sécurité réseau pour déterminer les destinations publiques qui lancent des flux entrants.

Quand vous appliquez un groupe de sécurité réseau à une machine virtuelle à charge équilibrée, vérifiez les [balises de service](../virtual-network/security-overview.md#service-tags) et les [règles de sécurité par défaut](../virtual-network/security-overview.md#default-security-rules). 

Assurez-vous que la machine virtuelle peut recevoir des demandes de sonde d’intégrité d’Azure Load Balancer.

Si un groupe de sécurité réseau bloque les demandes de sonde d’intégrité depuis la balise par défaut AZURE_LOADBALANCER, votre sonde d’intégrité de la machine virtuelle échoue et la machine virtuelle est marquée comme non disponible. L’équilibreur de charge arrête l’envoi de nouveaux flux vers cette machine virtuelle.

## <a name="limitations"></a>Limites

- Le nombre maximal de ports éphémères utilisables par adresse IP frontend est de 64 000.
- Le délai d’inactivité défini pour un flux sortant doit être compris entre 4 et 120 minutes (240 à 7 200 secondes).
- L’équilibreur de charge ne prend pas en charge ICMP pour la NAT de trafic sortant.
- Les règles de trafic sortant peuvent uniquement être appliquées à la configuration d’adresse IP principale d’une carte réseau.  Vous ne pouvez pas créer une règle de trafic sortant pour l’adresse IP secondaire d’une machine virtuelle ou d’une appliance virtuelle réseau. La présence de plusieurs cartes réseau est prise en charge.
- Toutes les machines virtuelles d’un **groupe à haute disponibilité** doivent être ajoutées au pool principal pour la connectivité sortante. 
- Toutes les machines virtuelles d’un **groupe de machines virtuelles identiques** doivent être ajoutées au pool principal pour la connectivité sortante.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur [Azure Standard Load Balancer](load-balancer-overview.md)
- Consultez notre [foire aux questions relative à Azure Load Balancer](load-balancer-faqs.md)

