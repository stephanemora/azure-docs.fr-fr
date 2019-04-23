---
title: Schéma d’analytique du trafic Azure | Microsoft Docs
description: Comprendre le schéma d’Analytique du trafic à analyser les journaux de flux de groupe de sécurité réseau Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 246c5256f56fd0b891d4e7d642c421b1e340fc6d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799320"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agrégation de schéma et les données dans le trafic Analytique

Traffic Analytics est une solution cloud qui offre une visibilité de l’activité des utilisateurs et des applications dans vos réseaux cloud. Traffic Analytics analyse les journaux de flux de groupe de sécurité réseau Network Watcher pour fournir des informations sur le flux de trafic dans votre cloud Azure. Avec Traffic Analytics, vous pouvez effectuer les actions suivantes :

- Visualiser l’activité réseau de l’ensemble de vos abonnements Azure et identifier les zones réactives.
- Identifier les menaces de sécurité pour votre réseau et le protéger, avec des informations comme les ports ouverts, les applications qui tentent d’accéder à Internet et les machines virtuelles se connectant à des réseaux non fiables.
- Comprendre les modèles de flux de trafic entre les régions Azure et Internet pour optimiser le déploiement de votre réseau, afin de bénéficier de performances et d’une capacité adéquates.
- Identifier les erreurs de configuration réseau à l’origine d’échecs de connexion dans votre réseau.
- Connaître l’utilisation du réseau en octets, des paquets ou des flux.

### <a name="data-aggregation"></a>Agrégation de données

1. Tous les journaux de flux à un groupe de sécurité réseau entre « FlowIntervalStartTime_t » et « FlowIntervalEndTime_t » sont capturées à des intervalles d’une minute dans le compte de stockage en tant qu’objets BLOB avant d’être traité par l’Analytique du trafic. 
2. Intervalle de traitement d’Analytique du trafic par défaut est 60 minutes. Cela signifie que toutes les 60 minutes que Analytique du trafic récupère des objets BLOB à partir du stockage pour l’agrégation.
3. Flux qui ont la même adresse IP Source, adresse IP de Destination, port de Destination, nom de groupe de sécurité réseau, règle NSG, Direction de flux et Transport de couche protocole (TCP ou UDP) (Remarque : Port source est exclue pour l’agrégation) sont proposée en un flux unique par Analytique du trafic
4. Cet enregistrement unique est décoré (détails dans la section ci-dessous) et ingéré dans Analytique de journal par Analytique du trafic.
5. FlowStartTime_t champ indique la première occurrence de ce type un flux agrégé (même de quatre tuples) dans l’intervalle entre « FlowIntervalStartTime_t » et « FlowIntervalEndTime_t » de traitement des journaux de flux. 
6. Pour n’importe quelle ressource dans TA, le flux indiqué dans l’interface utilisateur est visibles par le groupe de sécurité réseau total des flux, mais dans Log Analytics utilisateur verra uniquement l’enregistrement unique, réduit. Pour afficher tous les flux, utilisez le champ blob_id, qui peut être référencé à partir du stockage. Le débit total compter pour qu’enregistrement correspondra le flux individuels vu dans l’objet blob.


### <a name="fields-used-in-traffic-analytics-schema"></a>Champs utilisés dans le schéma de trafic Analytique

Analytique du trafic repose sur l’Analytique de journal, afin de pouvoir exécuter des requêtes personnalisées sur les données décoré par Analytique du trafic et de définir des alertes sur le même.

Vous trouverez ci-dessous les champs dans le schéma et qu’elles signifient

| Champ | Format | Commentaires | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Table pour les données de Traffic Analytics
| SubType_s | FlowLog | Sous-type pour les journaux de flux |
| FASchemaVersion_s |   1   | Le schéma version. Ne reflète pas la version de journaux de flux NSG |
| TimeProcessed_t   | Date et heure UTC  | Heure à laquelle l’Analytique du trafic traité les journaux de flux brutes à partir du compte de stockage |
| FlowIntervalStartTime_t | Date et heure UTC |  Heure de début de l’intervalle de traitement des journaux de flux. Il s’agit d’heure à partir de laquelle l’intervalle de flux est mesurée |
| FlowIntervalEndTime_t | Date et heure UTC | Heure de l’intervalle de traitement de journal de flux de fin |
| FlowStartTime_t | Date et heure UTC |  Première occurrence du flux (qui sera obtenir agrégé) dans l’intervalle de traitement des journaux de flux entre « FlowIntervalStartTime_t » et « FlowIntervalEndTime_t ». Ce flux obtient agrégé basé sur la logique d’agrégation |
| FlowEndTime_t | Date et heure UTC | Dernière occurrence du flux (qui sera obtenir agrégé) dans l’intervalle de traitement des journaux de flux entre « FlowIntervalStartTime_t » et « FlowIntervalEndTime_t ». En termes de v2 du journal de flux, ce champ contient l’heure du dernier flux avec le même jeu de quatre tuples démarrage (marqué comme « B » dans l’enregistrement de flux brutes) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Inconnu privé <br> * Inconnu | Définition de commentaires sous la table |
| SrcIP_s | Adresse IP source | Sera vide en cas de AzurePublic et les flux de ExternalPublic |
| DestIP_s | Adresse IP de destination | Sera vide en cas de AzurePublic et les flux de ExternalPublic |
| VMIP_s | Adresse IP de la machine virtuelle | Utilisé pour les flux AzurePublic et ExternalPublic |
| PublicIP_S | Adresses IP publiques | Utilisé pour les flux AzurePublic et ExternalPublic |
| DestPort_d | Port de destination | Port auquel le trafic est entrant | 
| L4Protocol_s  | * T <br> * U  | Protocole de transport. T = TCP <br> U = UDP | 
| L7Protocol_s  | Nom du protocole | Dérivé de port de destination |
| FlowDirection_s | * I = entrant<br> * O = sortant | Direction du flux entre le groupe de sécurité réseau en fonction de journal de flux | 
| FlowStatus_s  | * Un = autorisé par la règle de groupe de sécurité réseau <br> * D = refusée par la règle de groupe de sécurité réseau  | État de flux autorisés/nblocked par groupe de sécurité réseau en fonction de journal de flux |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Groupe de sécurité réseau (NSG) associée au flux |
| NSGRules_s | \<Index de la valeur 0) >< NSG_RULENAME >\<Direction de flux >\<état de flux >\<FlowCount ProcessedByRule > |  Règle de groupe de sécurité réseau qui autorisé ou refusé ce flux |
| NSGRuleType_s | * Définis par l’utilisateur * par défaut |   Le type de règle de groupe de sécurité réseau utilisé par le flux |
| MACAddress_s | Adresse MAC | Adresse MAC de la carte réseau à laquelle le flux a été capturé |
| Subscription_s | Abonnement du réseau virtuel Azure / de l’interface réseau / de la machine virtuelle est renseignée dans ce champ | Applicable uniquement pour le type de flux = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow et UnknownPrivate flux types (types de flux où seul côté est azure) |
| Subscription1_s | Identifiant d’abonnement | ID d’abonnement du réseau virtuel / de l’interface réseau / machine virtuelle à laquelle appartient l’adresse IP source dans le flux |
| Subscription2_s | Identifiant d’abonnement | ID d’abonnement du réseau virtuel / de l’interface réseau / machine virtuelle à laquelle appartient l’adresse IP de destination dans le flux |
| Region_s | Région Azure de réseau virtuel / de l’interface réseau / machine virtuelle à laquelle appartient l’adresse IP dans le flux | Applicable uniquement pour le type de flux = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow et UnknownPrivate flux types (types de flux où seul côté est azure) |
| Region1_s | Région Azure | Région Azure de réseau virtuel / de l’interface réseau / machine virtuelle à laquelle appartient l’adresse IP source dans le flux |
| Region2_s | Région Azure | Région Azure de réseau virtuel à laquelle appartient l’adresse IP de destination dans le flux |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Carte réseau associée à la machine virtuelle envoyer ou recevoir le trafic |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Carte réseau associée à l’adresse IP source dans le flux |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Carte réseau associée à l’adresse IP de destination dans le flux |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Machine virtuelle associée à l’interface réseau NIC_s |
| VM1_s | <resourcegroup_Name>/\<VirtualMachineName> | Machine virtuelle associée à l’adresse IP source dans le flux |
| VM2_s | <resourcegroup_Name>/\<VirtualMachineName> | Machine virtuelle associée à l’adresse IP de destination dans le flux |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sous-réseau associé à la NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Sous-réseau associé à l’adresse IP Source dans le flux |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Sous-réseau associé à l’adresse IP de Destination dans le flux |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Passerelle d’application associé à l’adresse IP Source dans le flux | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Passerelle d’application associé à l’adresse IP de Destination dans le flux |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Équilibreur de charge associé à l’adresse IP Source dans le flux |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Équilibreur de charge associé à l’adresse IP de Destination dans le flux |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Passerelle de réseau local associée à l’adresse IP Source dans le flux |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Passerelle de réseau local associée à l’adresse IP de Destination dans le flux |
| ConnectionType_s | Les valeurs possibles sont VNetPeering, passerelle VPN et ExpressRoute |    Type de connexion |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nom de connexion |
| ConnectingVNets_s | Liste séparée par des espaces de noms de réseau virtuel | Dans le cas d’une topologie hub and spoke, les réseaux virtuels hub contiendra ici |
| Country_s | Code de pays (ISO 3166-1 alpha-2) à deux lettres | Remplie pour le type de flux ExternalPublic. Toutes les adresses IP dans le champ de PublicIPs_s partagent le même code de pays |
| AzureRegion_s | Emplacements de la région Azure | Remplie pour le type de flux AzurePublic. Toutes les adresses IP dans le champ de PublicIPs_s partageront la région Azure |
| AllowedInFlows_d | | Nombre de flux entrants qui ont été autorisés. Cela représente le nombre de flux qui partage le même jeu de quatre tuples entrants à l’interface netweork au niveau duquel le flux a été capturé | 
| DeniedInFlows_d |  | Nombre de flux entrants qui ont été refusés. (Trafic entrant à l’interface réseau à laquelle le flux a été capturé) |
| AllowedOutFlows_d | | Nombre de flux sortants qui ont été autorisés (sortants à l’interface réseau à laquelle le flux a été capturé) |
| DeniedOutFlows_d  | | Nombre de flux sortants qui ont été refusés (sortants à l’interface réseau à laquelle le flux a été capturé) |
| FlowCount_d | Action déconseillée. Total des flux qui a correspondu le même jeu de quatre tuples. En cas de types de flux ExternalPublic et AzurePublic nombre inclura les flux à partir de plusieurs adresses adresse IP publique également.
| InboundPackets_d | Paquets reçus puisque capturés à l’interface réseau dans lequel la règle NSG a été appliquée | Il est rempli uniquement pour le schéma de journal de flux de Version 2 du groupe de sécurité réseau |
| OutboundPackets_d  | Paquets envoyés comme capturées à l’interface réseau dans lequel la règle NSG a été appliquée | Il est rempli uniquement pour le schéma de journal de flux de Version 2 du groupe de sécurité réseau |
| InboundBytes_d |  Octets reçus puisque capturés à l’interface réseau dans lequel la règle NSG a été appliquée | Il est rempli uniquement pour le schéma de journal de flux de Version 2 du groupe de sécurité réseau |
| OutboundBytes_d | Octets envoyés comme capturées à l’interface réseau dans lequel la règle NSG a été appliquée | Il est rempli uniquement pour le schéma de journal de flux de Version 2 du groupe de sécurité réseau |
| CompletedFlows_d  |  | Ce champ est renseigné avec la valeur différente de zéro uniquement pour le schéma de journal de flux de Version 2 du groupe de sécurité réseau |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Séparée par des entrées par des barres |
    
### <a name="notes"></a>Notes
    
1. En cas de flux AzurePublic et ExternalPublic, le client appartient QU'IP de machine virtuelle Azure est renseigné dans le champ VMIP_s, tandis que les adresses IP publiques seront bientôt disponibles dans le champ PublicIPs_s. Pour ces types de deux flux, nous devons utiliser VMIP_s et PublicIPs_s au lieu de champs SrcIP_s et DestIP_s. Pour les adresses AzurePublic et ExternalPublicIP, nous rassemblons les davantage, afin que le nombre d’enregistrements ingérées dans l’espace de travail client log analytique est minime. (Ce champ sera bientôt déconseillé et nous devons utiliser SrcIP_ et DestIP_s selon que la machine virtuelle azure a la source ou la destination dans le flux) 
1. Détails des types de flux : Selon les adresses IP impliquées dans le flux, nous classons les flux dans les types de flux suivants : 
1. IntraVNet – les adresses IP dans le flux se trouvent dans le même réseau virtuel Azure. 
1. Inter-réseaux virtuels - adresses IP dans le flux se trouvent dans les différents réseaux virtuels Azure deux. 
1. S2S – un (Site à Site) des adresses IP appartient au réseau virtuel Azure pendant que l’autre adresse IP appartient au réseau de client (Site) connecté au réseau virtuel Azure via la passerelle VPN ou Expressroute. 
1. P2S - (Point à Site) une des adresses IP appartient au réseau virtuel Azure, tandis que l’autre adresse IP appartient au réseau de client (Site) est connecté au réseau virtuel Azure via la passerelle VPN.
1. AzurePublic - une des adresses IP appartient au réseau virtuel Azure pendant que l’autre adresse IP appartient à des adresses IP publique interne Azure détenues par Microsoft. Clients appartenant à des adresses IP publiques ne sont pas faire partie de ce type de flux. Par exemple, tous les clients appartenant à la machine virtuelle de l’envoi du trafic vers un Service Azure (point de terminaison de stockage) doivent être catégorisée sous ce type de flux. 
1. ExternalPublic - une des adresses IP appartient à un réseau virtuel Azure alors que l’autre adresse IP est une adresse IP publique qui n’est pas dans Azure, n’est pas signalée comme étant malveillante dans les flux ASC qui consomme d’Analytique du trafic pour l’intervalle de traitement entre » FlowIntervalStartTime_t » et « FlowIntervalEndTime_t ». 
1. MaliciousFlow - une des adresses IP appartiennent à un réseau virtuel azure alors que l’autre adresse IP est une adresse IP publique qui n’est pas dans Azure et est signalée comme étant malveillante dans les flux ASC qui consomme d’Analytique du trafic pour l’intervalle de traitement entre » FlowIntervalStartTime_t » et « FlowIntervalEndTime_t ». 
1. UnknownPrivate - une des adresses IP appartiennent au réseau virtuel Azure pendant que l’autre adresse IP appartient à la plage d’adresses IP privée, tel que défini dans RFC 1918 et pas pu être mappé par Analytique du trafic à un client appartenant à site ou réseau virtuel Azure.
1. Inconnu : Impossible de mapper l’une de l’adresse IP adresses dans les flux avec la topologie de client dans Azure ainsi que sur site (site).

### <a name="next-steps"></a>Étapes suivantes
Pour obtenir des réponses aux questions fréquemment posées, consultez [analytique Forum aux questions du trafic](traffic-analytics-faq.md) pour obtenir plus d’informations sur les fonctionnalités, consultez [documentation d’analytique du trafic](traffic-analytics.md)
    


    


