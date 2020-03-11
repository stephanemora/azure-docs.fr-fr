---
title: Règles de trafic sortant – Azure Load Balancer
description: Dans ce parcours d’apprentissage, commencez à utiliser des règles de trafic sortant pour définir des traductions d’adresses réseau sortantes.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304189"
---
# <a name="load-balancer-outbound-rules"></a>Règles de trafic sortant dans Load Balancer

Azure Load Balancer fournit la connectivité sortante à partir d’un réseau virtuel en plus de la connectivité entrante.  Les règles de trafic sortant facilitent la configuration de la traduction des adresses réseau sortantes publiques dans [Standard Load Balancer](load-balancer-standard-overview.md).  Vous pouvez contrôler entièrement la connectivité sortante de manière déclarative pour la mettre à l’échelle et l’adapter en fonction de vos besoins spécifiques.

![Règles de trafic sortant dans Load Balancer](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Avec des règles de trafic sortant, vous pouvez utiliser Load Balancer pour : 
- définir la NAT de trafic sortant à partir de zéro ;
- mettre à l’échelle et adapter le comportement de la NAT de trafic sortant existante. 

Les règles de trafic sortant vous permettent de déterminer :
- quelles machines virtuelles doivent être traduites vers quelles adresses IP publiques, 
- le mode d’allocation des [ports SNAT sortants](load-balancer-outbound-connections.md#snat),
- les protocoles qui fournissent la traduction sortante,
- la durée à utiliser comme délai d’inactivité des connexions sortantes (de 4 à 120 minutes),
- la réinitialisation TCP au terme du délai d’inactivité.

Les règles de trafic sortant étendent le [scénario 2](load-balancer-outbound-connections.md#lb) décrit dans l’article sur les [connexions sortantes](load-balancer-outbound-connections.md), mais la priorité du scénario reste la même.

## <a name="outbound-rule"></a>Règle de trafic sortant

Comme toutes les règles dans Load Balancer, les règles de trafic sortant suivent la syntaxe habituelle des règles d’équilibrage de charge et des règles de NAT de trafic entrant :

**frontend** + **parameters** + **backend pool**

Une règle de trafic sortant configure la NAT de trafic sortant pour que _toutes les machines virtuelles identifiées par le pool backend_ soient traduites sur le _frontend_.  Les _paramètres_ permettent de contrôler avec plus de précision l’algorithme de la NAT de trafic sortant.

Dans l’API version « 2018-07-01 », une définition de règle de trafic sortant a la structure suivante :

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>La configuration de la NAT de trafic sortant effective est composée à partir de l’ensemble des règles de trafic sortant et règles d’équilibrage de charge. Les règles de trafic sortant sont incrémentielles pour les règles d’équilibrage de charge. Pour savoir comment gérer la NAT de trafic sortant effective quand plusieurs règles s’appliquent à une machine virtuelle, consultez [Désactiver la NAT de trafic sortant pour une règle d’équilibrage de charge](#disablesnat). Vous devez [désactiver la SNAT de trafic sortant](#disablesnat) quand vous définissez une règle de trafic sortant qui utilise la même adresse IP publique qu’une règle d’équilibrage de charge.

### <a name="scale"></a> Mettre à l’échelle la NAT de trafic sortant avec plusieurs adresses IP

Une règle de trafic sortant peut être utilisée avec une seule adresse IP publique, mais les règles de trafic sortant facilitent la configuration de la mise à l’échelle de la NAT de trafic sortant. Vous pouvez utiliser plusieurs adresses IP pour vos scénarios à grande échelle et utiliser des règles de trafic sortant afin de limiter les risques [d’épuisement de ports SNAT](load-balancer-outbound-connections.md#snatexhaust).  

Chaque adresse IP supplémentaire fournie par un frontend met à disposition 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT. Alors que les règles d’équilibrage de charge ou les règles NAT de trafic entrant ont un seul frontend, la règle de trafic sortant étend la notion de frontend et permet l’utilisation de plusieurs frontends par règle.  Avec plusieurs frontends par règle, le nombre de ports SNAT disponibles est multiplié avec chaque adresse IP publique, ce qui rend possible la prise en charge de scénarios à grande échelle.

De plus, vous pouvez utiliser un [préfixe d’adresse IP publique](https://aka.ms/lbpublicipprefix) directement avec une règle de trafic sortant.  L’utilisation d’un préfixe d’adresse IP publique simplifie la mise à l’échelle et la mise sur liste verte des flux provenant de votre déploiement Azure. Vous pouvez configurer une configuration IP frontend au sein de la ressource Load Balancer pour référencer directement un préfixe d’adresse IP publique.  De cette manière, Load Balancer conserve un contrôle exclusif sur le préfixe d’adresse IP publique, et la règle de trafic sortant utilise automatiquement toutes les adresses IP publiques contenues dans le préfixe d’adresse IP publique pour les connexions sortantes.  Chacune des adresses IP dans la plage du préfixe d’adresse IP publique fournit 64 000 ports éphémères que Load Balancer peut utiliser en tant que ports SNAT.   

Avec cette option, vous ne pouvez pas créer des ressources d’adresse IP publique de manière individuelle à partir du préfixe d’adresse IP publique, car la règle de trafic sortant doit avoir un contrôle total sur le préfixe d’adresse IP publique.  Si vous souhaitez un contrôle plus précis, créez une ressource d’adresse IP publique individuelle à partir du préfixe d’adresse IP publique et assignez plusieurs adresses IP publiques individuellement au frontend d’une règle de trafic sortant.

### <a name="snatports"></a> Paramétrer l’allocation de ports SNAT

Vous pouvez utiliser des règles de trafic sortant pour paramétrer [l’allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports) et pour changer le nombre de ports SNAT alloués automatiquement.

Utilisez le paramètre suivant pour allouer 10 000 ports SNAT par machine virtuelle (configuration IP de carte réseau).
 

          "allocatedOutboundPorts": 10000

Chaque adresse IP publique de tous les frontends d’une règle de trafic sortant fournit jusqu’à 64 000 ports éphémères en tant que ports SNAT.  Load Balancer alloue des ports SNAT par multiples de huit. Si vous indiquez une valeur non divisible par huit, l’opération de configuration est rejetée.  Si vous essayez d’allouer plus de ports SNAT qu’il n’y a de ports disponibles par rapport au nombre d’adresses IP publiques, l’opération de configuration est rejetée.  Par exemple, si vous allouez 10 000 ports par machine virtuelle et que sept machines virtuelles d’un pool principal partagent la même adresse IP publique, la configuration est rejetée (7 x 10 000 ports SNAT > 64 000 ports SNAT).  Dans ce scénario, vous devrez donc ajouter des adresses IP publiques supplémentaires sur le frontend de la règle de trafic sortant.

Vous pouvez revenir à une [allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports) en spécifiant 0 comme nombre de ports. Dans ce cas, les 50 premières instances de machine virtuelle obtiennent 1 024 ports, les instances de machine virtuelle 51 à 100 obtiennent 512 ports, et ainsi de suite selon la table.

### <a name="idletimeout"></a> Contrôler le délai d’inactivité des flux sortants

Les règles de trafic sortant ont un paramètre de configuration qui permet de contrôler le délai d’inactivité des flux sortants et de l’ajuster en fonction des besoins de votre application.  Le délai d’inactivité des flux sortants est de 4 minutes par défaut.  Le paramètre peut être défini sur une valeur comprise entre 4 et 120, selon le nombre de minutes du délai d’inactivité nécessaire pour les flux auxquels cette règle particulière s’applique.

Pour définir le délai d’inactivité des flux sortants à une (1) heure, utilisez le paramètre suivant :

          "idleTimeoutInMinutes": 60

### <a name="tcprst"></a> <a name="tcpreset"></a> Activer la réinitialisation TCP au terme du délai d’inactivité

Le comportement par défaut de Load Balancer consiste à supprimer silencieusement des flux quand le délai d’inactivité d’un flux sortant est atteint.  Avec le paramètre enableTCPReset, vous pouvez définir un comportement plus prévisible de l’application et contrôler s’il faut déclencher une réinitialisation TCP (TCP RST) bidirectionnelle quand le délai d’inactivité d’un flux sortant est atteint. 

Pour activer la réinitialisation TCP sur une règle de trafic sortant, utilisez le paramètre suivant :

           "enableTcpReset": true

Pour plus d’informations, y compris sur la disponibilité dans les régions, consultez [Réinitialisation TCP au terme du délai d’inactivité](https://aka.ms/lbtcpreset).

### <a name="proto"></a> Prendre en charge les protocoles de transport TCP et UDP avec une seule règle

Vous choisirez probablement l’option "All" pour le protocole de transport de la règle de trafic sortant, mais vous pouvez également appliquer la règle de trafic sortant à un protocole de transport spécifique, par choix ou par nécessité.

Pour définir le protocole sur TCP ou UDP, utilisez le paramètre suivant :

          "protocol": "All"

### <a name="disablesnat"></a> Désactiver la NAT de trafic sortant pour une règle d’équilibrage de charge

Comme indiqué précédemment, les règles d’équilibrage de charge fournissent une programmation automatique de la NAT de trafic sortant. Toutefois, dans certains scénarios, il est préférable, ou obligatoire, de désactiver la programmation automatique de la NAT de trafic sortant par la règle d’équilibrage de charge pour que vous puissiez contrôler ou adapter vous-même le comportement.  Dans certains scénarios de règles de trafic sortant, il est important de désactiver la programmation automatique de la NAT de trafic sortant.

Vous pouvez utiliser ce paramètre de deux manières :
- Suppression facultative de l’utilisation de l’adresse IP entrante pour la NAT de trafic sortant.  Les règles de trafic sortant sont incrémentielles pour les règles d’équilibrage de charge et, avec ce paramètre défini, la règle de trafic sortant est contrôlée.
  
- Paramétrage de la NAT de trafic sortant d’une adresse IP utilisée à la fois pour le trafic entrant et le trafic sortant.  La programmation automatique de la NAT de trafic sortant doit être désactivée pour permettre le contrôle par une règle de trafic sortant.  Par exemple, ce paramètre doit être défini sur true si vous souhaitez changer l’allocation de ports SNAT d’une adresse qui est également utilisée pour le trafic entrant.  Si vous essayez d’utiliser une règle de trafic sortant pour redéfinir les paramètres d’une adresse IP également utilisée pour le trafic entrant, mais que vous n’avez pas désactivé la programmation de la NAT de trafic sortant pour la règle d’équilibrage de charge, la configuration d’une règle de trafic sortant échoue.

>[!IMPORTANT]
> Votre machine virtuelle n’aura pas de connectivité sortante si vous définissez ce paramètre sur true et que vous n’avez pas créé de règle de trafic sortant (ou un [scénario d’adresse IP publique de niveau d’instance](load-balancer-outbound-connections.md#ilpip)) pour définir la connectivité sortante.  Certaines opérations de votre machine virtuelle ou application peuvent avoir besoin d’une connectivité sortante. Assurez-vous de bien comprendre toutes les dépendances de votre scénario, ainsi que les conséquences d’un tel changement.

Pour désactiver la SNAT de trafic sortant sur la règle d’équilibrage de charge, définissez ce paramètre de configuration :

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Le paramètre disableOutboundSNAT est défini sur false par défaut, ce qui signifie que la règle d’équilibrage de charge **fournit** une NAT de trafic sortant automatique comme image miroir de la configuration de la règle d’équilibrage de charge.  

Si vous définissez disableOutboundSnat sur true pour la règle d’équilibrage de charge, celle-ci redonne le contrôle à la place de la programmation automatique de la NAT de trafic sortant.  En conséquence, la SNAT de trafic sortant est désactivée.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Réutiliser des pools backend existants ou en définir de nouveaux

Les règles de trafic sortant n’introduisent pas de nouveau concept pour la définition du groupe de machines virtuelles auquel la règle doit s’appliquer.  À la place, elles reprennent le concept de pool backend, qui est également utilisé pour les règles d’équilibrage de charge. Cela simplifie la configuration, car vous pouvez ainsi soit réutiliser une définition de pool backend existant, soit créer un pool backend spécifique pour une règle de trafic sortant.

## <a name="scenarios"></a>Scénarios

### <a name="groom"></a> Grouper les connexions sortantes dans un ensemble spécifique d’adresses IP publiques

Vous pouvez utiliser une règle de trafic sortant pour préparer les connexions sortantes de sorte qu’elles semblent provenir d’un ensemble spécifique d’adresses IP publiques. Cette pratique facilite les scénarios de mise sur liste verte.  Cette adresse IP publique source peut être la même que celle utilisée par une règle d’équilibrage de charge ou être un ensemble d’adresses IP publiques différent de celui utilisé par une règle d’équilibrage de charge.  

1. Créer un [préfixe d’adresse IP publique](https://aka.ms/lbpublicipprefix) (ou plusieurs adresses IP publiques à partir du préfixe d’adresse IP publique)
2. Créer un équilibreur de charge standard public
3. Créer des frontends référençant le préfixe d’adresse IP publique (ou les adresses IP publiques) que vous souhaitez utiliser
4. Réutiliser un pool backend existant ou créer un pool backend et placer les machines virtuelles dans un pool backend de l’équilibreur de charge public
5. Configurer une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles utilisant les frontends
   
Si vous ne souhaitez pas utiliser la règle d’équilibrage de charge pour le trafic sortant, vous devez [désactiver la SNAT de trafic sortant](#disablesnat) sur la règle d’équilibrage de charge.

### <a name="modifysnat"></a> Modifier l’allocation de ports SNAT

Vous pouvez utiliser des règles de trafic sortant pour paramétrer [l’allocation de ports SNAT automatique en fonction de la taille du pool backend](load-balancer-outbound-connections.md#preallocatedports).

Par exemple, si vous avez deux machines virtuelles qui partagent une seule adresse IP publique pour la NAT de trafic sortant, vous pouvez augmenter le nombre de ports SNAT alloués (1 024 ports par défaut) en cas d’épuisement des ports SNAT. Chaque adresse IP publique peut fournir jusqu’à 64 000 ports éphémères.  Si vous configurez une règle de trafic sortant avec une seule adresse IP publique frontend, vous pouvez distribuer un total de 64 000 ports SNAT sur les machines virtuelles du pool backend.  Pour deux machines virtuelles, un maximum de 32 000 ports SNAT peut être alloué avec une règle de trafic sortant (2 x 32 000 = 64 000).

Consultez l’article [Connexions sortantes](load-balancer-outbound-connections.md) et la section sur l’allocation et l’utilisation des ports [SNAT](load-balancer-outbound-connections.md#snat).

### <a name="outboundonly"></a> Activer pour le trafic sortant uniquement

Vous pouvez utiliser un équilibreur de charge standard public afin de fournir la NAT de trafic sortant dans un groupe de machines virtuelles. Dans ce scénario, vous pouvez utiliser une règle de trafic sortant uniquement, sans avoir besoin d’autres règles.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>NAT de trafic sortant pour les machines virtuelles uniquement (pas en entrée)

Définissez un équilibreur de charge standard public, placez les machines virtuelles dans le pool backend, et configurez une règle de trafic sortant pour programmer la NAT de trafic sortant et préparer les connexions sortantes de sorte qu’elles semblent provenir d’une adresse IP publique spécifique. Vous pouvez également utiliser un préfixe d’adresse IP publique pour simplifier la mise sur liste verte des connexions sortantes sources.

1. Créez un équilibreur de charge standard public.
2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public.
3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>NAT de trafic sortant dans les scénarios avec un équilibreur de charge standard interne

Lorsque vous utilisez un équilibreur de charge standard interne, la NAT de trafic sortant n’est pas disponible tant que la connectivité sortante n’a pas été explicitement déclarée. Vous pouvez définir la connectivité sortante à l’aide d’une règle de trafic sortant pour fournir la connectivité sortante requise par les machines virtuelles placées derrière un équilibreur de charge standard interne comme suit :

1. Créez un équilibreur de charge standard public.
2. Créez un pool backend et placez les machines virtuelles dans un pool backend de l’équilibreur de charge public en plus de l’équilibreur de charge interne.
3. Configurez une règle de trafic sortant sur l’équilibreur de charge public pour programmer la NAT de trafic sortant sur ces machines virtuelles.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Activer les protocoles TCP et UDP pour la NAT de trafic sortant avec un équilibreur de charge standard public

- Quand vous utilisez un équilibreur de charge standard public, la programmation automatique de la NAT de trafic sortant utilise le protocole de transport de la règle d’équilibrage de charge.  

   1. Désactivez la NAT de trafic sortant sur la règle d’équilibrage de charge.
   2. Configurez une règle de trafic sortant sur le même équilibreur de charge.
   3. Réutilisez le pool backend de vos machines virtuelles.
   4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant.

- Si vous utilisez uniquement des règles de NAT de trafic entrant, aucune NAT de trafic sortant n’est fournie.

   1. Placez les machines virtuelles dans un pool backend.
   2. Définissez une ou plusieurs configurations IP frontend avec une ou plusieurs adresses IP publiques ou avec un préfixe d’adresse IP publique.
   3. Configurez une règle de trafic sortant sur le même équilibreur de charge.
   4. Spécifiez "protocol": "All" dans la définition de la règle de trafic sortant

## <a name="limitations"></a>Limites

- Le nombre maximal de ports éphémères utilisables par adresse IP frontend est de 64 000.
- Le délai d’inactivité défini pour un flux sortant doit être compris entre 4 et 120 minutes (240 à 7 200 secondes).
- L’équilibreur de charge ne prend pas en charge ICMP pour la NAT de trafic sortant.
- Les règles de trafic sortant peuvent uniquement être appliquées à la configuration d’adresse IP principale d’une carte réseau.  La présence de plusieurs cartes réseau est prise en charge.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment utiliser [Load Balancer pour les connexions sortantes](load-balancer-outbound-connections.md).
- En savoir plus sur [Standard Load Balancer](load-balancer-standard-overview.md).
- Découvrez-en plus sur la [réinitialisation TCP bidirectionnelle au terme du délai d’inactivité](load-balancer-tcp-reset.md).
- [Configurez des règles de trafic sortant avec Azure CLI 2.0](configure-load-balancer-outbound-cli.md).
