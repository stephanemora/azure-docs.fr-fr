---
title: 'Azure ExpressRoute : Exemples de configuration de routeur'
description: Cette page fournit des exemples de configuration pour les routeurs Cisco et Juniper.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: osamaz
ms.openlocfilehash: 3603bc45b920dc62eb8bf6f2eb8557f98e21638e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024810"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>Exemples de configuration de routeur pour configurer et gérer le routage
Cette page fournit une interface et des exemples de configuration de routage pour les routeurs des gammes Cisco IOS-XE et Juniper MX quand vous utilisez Azure ExpressRoute.

> [!IMPORTANT]
> Les exemples de cette page ne sont donnés qu’à titre indicatif. Vous devez vous adresser à l’équipe commerciale ou technique de votre fournisseur et à votre équipe de mise en réseau pour rechercher les configurations adaptées à vos besoins. Microsoft ne prend pas en charge les problèmes liés aux configurations répertoriées dans cette page. Contactez le fournisseur de votre appareil pour la prise en charge des problèmes.
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>Paramètres MTU et TCP MSS sur les interfaces de routeur
L’unité de transmission maximale (MTU) de l’interface ExpressRoute est 1500, qui est la valeur MTU par défaut classique pour une interface Ethernet sur un routeur. À moins que votre routeur ait une taille MTU par défaut différente, il est inutile de spécifier une valeur sur l’interface du routeur.

Contrairement à une passerelle VPN Azure, il n’est pas nécessaire de spécifier la taille maximale des segments (MSS) TCP pour un circuit ExpressRoute.

Les exemples de configuration de routeur présentés dans cet article s’appliquent à tous les Peerings. Pour plus d’informations sur le routage, voir [Peerings ExpressRoute](expressroute-circuit-peerings.md) et [Configuration requise pour le routage ExpressRoute](expressroute-routing.md).


## <a name="cisco-ios-xe-based-routers"></a>Routeurs Cisco IOS-XE
Les exemples de cette section s’appliquent à tous les routeurs exécutant la famille de systèmes d’exploitation IOS-XE.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurer des interfaces et des sous-interfaces
Vous aurez besoin d’une sous-interface par Peering dans chaque routeur que vous connectez à Microsoft. Une sous-interface peut être identifiée par un ID VLAN ou une paire empilée d’ID VLAN, et une adresse IP.

**Définition de l’interface Dot1Q**

Cet exemple fournit la définition d’une sous-interface avec un ID VLAN unique. L’ID de réseau local virtuel est unique pour chaque peering. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**Définition de l’interface QinQ**

Cet exemple fournit la définition d’une sous-interface avec deux ID VLAN. L’ID VLAN externe (s-tag), s’il est utilisé, reste le même pour tous les Peerings. L’ID de réseau local virtuel interne (c-tag) est unique pour chaque peering. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="set-up-ebgp-sessions"></a>Configurer des sessions eBGP
Vous devez configurer une session BGP avec Microsoft pour chaque Peering. Configurez une session BGP à l’aide de l’exemple suivant. Si l’adresse IPv4 utilisée pour votre sous-interface est a.b.c.d, l’adresse IP du voisin BGP (Microsoft) sera a.b.c.d+1. Le dernier octet de l’adresse IPv4 du voisin BGP est toujours un nombre pair.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurer des préfixes à publier sur la session BGP
Configurez votre routeur pour qu’il publie certains préfixes sur Microsoft à l’aide de l’exemple suivant.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="route-maps"></a>Cartes d’itinéraire
Utilisez des cartes d’itinéraire et des listes de préfixes pour filtrer les préfixes propagés sur votre réseau. Consultez l’exemple suivant et vérifiez que les listes de préfixe appropriées sont configurées.

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
     exit-address-family
    !
    route-map <MS_Prefixes_Inbound> permit 10
     match ip address prefix-list <MS_Prefixes>
    !

### <a name="configure-bfd"></a>Configurer BFD

Vous allez configurer BFD à deux emplacements : l’un au niveau de l’interface et l’autre au niveau du protocole BGP. Cet exemple concerne l’interface QinQ. 

    interface GigabitEthernet<Interface_Number>.<Number>
     bfd interval 300 min_rx 300 multiplier 3
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>
    
    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      neighbor <IP#2_used_by_Azure> activate
      neighbor <IP#2_used_by_Azure> fall-over bfd
     exit-address-family
    !


## <a name="juniper-mx-series-routers"></a>Routeurs de la série Juniper MX
Les exemples de cette section s’appliquent à tous les routeurs de la gamme Juniper MX.

### <a name="configure-interfaces-and-subinterfaces"></a>Configurer des interfaces et des sous-interfaces

**Définition de l’interface Dot1Q**

Cet exemple fournit la définition d’une sous-interface avec un ID VLAN unique. L’ID de réseau local virtuel est unique pour chaque peering. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }


**Définition de l’interface QinQ**

Cet exemple fournit la définition d’une sous-interface avec deux ID VLAN. L’ID VLAN externe (s-tag), s’il est utilisé, reste le même pour tous les Peerings. L’ID de réseau local virtuel interne (c-tag) est unique pour chaque peering. Le dernier octet de votre adresse IPv4 est toujours un nombre impair.

    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           

### <a name="set-up-ebgp-sessions"></a>Configurer des sessions eBGP
Vous devez configurer une session BGP avec Microsoft pour chaque Peering. Configurez une session BGP à l’aide de l’exemple suivant. Si l’adresse IPv4 utilisée pour votre sous-interface est a.b.c.d, l’adresse IP du voisin BGP (Microsoft) sera a.b.c.d+1. Le dernier octet de l’adresse IPv4 du voisin BGP est toujours un nombre pair.

    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>Configurer des préfixes à publier sur la session BGP
Configurez votre routeur pour qu’il publie certains préfixes sur Microsoft à l’aide de l’exemple suivant.

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter 
    <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="route-policies"></a>Stratégies de routage
Vous pouvez utiliser des cartes d’itinéraire et des listes de préfixes pour filtrer les préfixes propagés sur votre réseau. Consultez l’exemple suivant et vérifiez que les listes de préfixe appropriées sont configurées.

    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

### <a name="configure-bfd"></a>Configurer BFD
Configurez BFD sous la section Protocole BGP uniquement.

    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }


## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations, consultez le [Forum Aux Questions sur ExpressRoute](expressroute-faqs.md) .



