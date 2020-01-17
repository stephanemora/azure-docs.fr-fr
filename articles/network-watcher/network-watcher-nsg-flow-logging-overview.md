---
title: Introduction à la journalisation des flux pour les groupes de sécurité réseau
titleSuffix: Azure Network Watcher
description: Cet article explique comment utiliser les journaux de flux de groupe de sécurité réseau, une fonctionnalité d’Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 2530c9b2f366bd64013c7125b4d7984ca2a69248
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454289"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Présentation de la journalisation des flux pour les groupes de sécurité réseau

Les journaux d’activité des flux de groupe de sécurité réseau désignent une fonctionnalité de Network Watcher qui vous permet de visualiser des informations sur le trafic IP d’entrée et de sortie par le biais d’un groupe de sécurité réseau. Les journaux de flux sont écrits au format JSON et affichent les flux entrants et sortants en fonction de la règle, la carte réseau à laquelle le flux s’applique, des informations à 5 tuples sur le flux (IP source/de destination, port source/de destination, protocole), l’autorisation ou le refus du trafic et, dans la version 2, des informations de débit (octets et paquets).


![vue d’ensemble des journaux de flux](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Même si les journaux de flux ciblent les groupes de sécurité réseau, ils ne sont pas affichés de la même façon que les autres journaux d’activité. Les journaux de flux sont uniquement stockés dans un compte de stockage et suivent le chemin de journalisation comme indiqué dans l’exemple ci-après :

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Vous pouvez analyser les journaux de flux et obtenir des informations sur votre trafic réseau à l’aide de l’[analytique du trafic](traffic-analytics.md).

Les mêmes stratégies de conservation que celles des autres journaux d’activité s’appliquent aux journaux de flux. Vous pouvez définir la stratégie de conservation de journal entre 1 jour et 365 jours. Si aucune stratégie de rétention n’est définie, les journaux d’activité sont conservés indéfiniment.

> [!NOTE] 
> L’utilisation de la fonctionnalité de stratégie de conservation avec la journalisation des flux de groupe de sécurité réseau peut entraîner un volume élevé d’opérations de stockage avec les coûts associés. Si vous n’avez pas besoin de la fonctionnalité de stratégie de conservation, nous vous recommandons de définir cette valeur sur 0.


## <a name="log-file"></a>Fichier journal

Les flux de journaux d’activité incluent les propriétés suivantes :

* **time** - L’heure à laquelle l’événement a été journalisé.
* **systemId** - L’ID de la ressource du groupe de sécurité réseau.
* **category** - La catégorie de l'événement. La catégorie est toujours **NetworkSecurityGroupFlowEvent**
* **resourceid** - L’ID de la ressource du groupe de sécurité réseau.
* **operationName** - Toujours NetworkSecurityGroupFlowEvents.
* **properties** - Une collection des propriétés du flux.
    * **Version** - Le numéro de version du schéma d’événement du journal de flux.
    * **flow** - Une collection de flux. Cette propriété a plusieurs entrées pour différentes règles.
        * **rule** - La règle pour laquelle les flux sont répertoriés.
            * **flows** - Une collection de flux.
                * **mac** - L’adresse MAC de la carte réseau de la machine virtuelle où le flux a été collecté.
                * **flowTuples** - Une chaîne qui contient plusieurs propriétés séparées par des virgules pour le tuple de flux.
                    * **Time Stamp** - Cette valeur indique la date et l’heure du flux au format UNIX EPOCH.
                    * **Source IP** - L’adresse IP source.
                    * **Destination IP** - L’adresse IP de destination.
                    * **Source Port** - Le port source.
                    * **Destination Port** - Le port de destination.
                    * **Protocol** - Le protocole du flux. Les valeurs valides sont **T** pour TCP et **U** pour UDP.
                    * **Traffic Flow** - La direction du flux de trafic. Les valeurs valides sont **I** pour le trafic entrant et **O** pour le trafic sortant.
                    * **Traffic Decision** - Indique si le trafic a été autorisé ou refusé. Les valeurs valides sont **A** pour autorisé et **D** pour refusé.
                    * **Flow State - Version 2 Only** - Capture l’état du flux. Les états possibles sont **B** : début, lors de la création d’un flux. Aucune statistique n’est fournie. **C** : continuation d’un flux en cours. Des statistiques sont fournies toutes les 5 minutes. **E** : fin, quand un flux est arrêté. Des statistiques sont fournies.
                    * **Packets - Source to destination - Version 2 Only** Nombre total de paquets TCP ou UDP envoyés de la source à la destination depuis la dernière mise à jour.
                    * **Bytes sent - Source to destination - Version 2 Only** Nombre total d’octets de paquets TCP ou UDP envoyés de la source à la destination depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet.
                    * **Packets - Destination to source - Version 2 Only** Nombre total de paquets TCP ou UDP envoyés de la destination à la source depuis la dernière mise à jour.
                    * **Bytes sent - Destination to source - Version 2 Only** Nombre total d’octets de paquets TCP et UDP envoyés de la destination à la source depuis la dernière mise à jour. Les octets de paquets incluent l’en-tête et la charge utile du paquet.

## <a name="nsg-flow-logs-version-2"></a>Journaux de flux NSG version 2

La version 2 des journaux présente l’état du flux. Vous pouvez configurer la version des journaux de flux que vous recevez. Pour savoir comment activer les journaux de flux, consultez [Enable flow logs](network-watcher-nsg-flow-logging-portal.md) (Activer les journaux de flux).

L’état du flux *B* est enregistré lorsqu’un flux est lancé. L’état du flux *C* et l’état du flux *E* sont les états qui marquent respectivement la continuation d’un flux et son arrêt. Les deux états *C* et *E* contiennent des informations sur la bande passante du trafic.

**Exemple** : tuples de flux à partir d’une conversation TCP entre 185.170.185.105:35370 and 10.2.0.4:23 :

« 1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,, » « 1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880 » « 1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072 »

Pour la continuation *C* et la fin *E* d’états de flux, le nombre d’octets et de paquets est le nombre cumulé depuis l’enregistrement de tuple de flux précédent. Si l’on se réfère à l’exemple de conversation précédent, le nombre total de paquets transférés est de 1 021 + 52 + 8 005 + 47 = 9 125. Le nombre total d’octets transférés est de 588 096+29 952+4 610 880+27 072 = 5 256 000.

Le texte ci-dessous est un exemple de journal de flux. Comme vous pouvez le voir, il existe plusieurs enregistrements qui suivent la liste des propriétés décrite dans la section précédente.

## <a name="nsg-flow-logging-considerations"></a>Considérations sur la journalisation de flux NSG

**Considérations relatives aux comptes de stockage** : 

1. Localisation : le compte de stockage doit se trouver dans la même région que le groupe de sécurité réseau.
2. Aucun Pare-feu : les journaux de flux de groupe de sécurité réseau ne sont pas intégrés en tant que [service Microsoft approuvé pour le stockage Azure](https://docs.microsoft.com/azure/storage/common/storage-network-security#trusted-microsoft-services). Pour désactiver le pare-feu, consultez [Comment désactiver le pare-feu sur mon compte de stockage ?](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#how-do-i-disable-the--firewall-on-my-storage-account). 
3. Aucun point de terminaison de service : en raison d’une limitation actuelle, les journaux peuvent uniquement être émis directement vers des comptes de stockage et non par le biais de points de terminaison de service. Pour obtenir de l’aide sur la suppression de points de terminaison de service existants, consultez [Comment utiliser les journaux de flux de groupe de sécurité réseau avec des point de terminaison de service ?](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#how-do-i-use-nsg-flow-logs-with-service-endpoints).
4. Rotation des clés auto-gérée : si vous modifiez (ou effectuez la rotation) des clés d’accès à votre compte de stockage, les journaux de flux de groupe de sécurité réseau cessent de fonctionner. Pour corriger ce problème, vous devez désactiver puis réactiver les journaux de flux de groupe de sécurité réseau.

**Activer la journalisation de flux NSG sur tous les groupes de sécurité réseau associés à une ressource** : la journalisation de flux dans Azure est configurée sur la ressource NSG. Un flux ne peut être associé qu’à une règle de groupe de sécurité réseau. Dans les scénarios où plusieurs groupes de sécurité réseau sont utilisés, nous recommandons que la journalisation de flux NSG soit activée sur tous les groupes de sécurité réseau auxquels le sous-réseau ou l’interface réseau d’une ressource est appliqué pour vous assurer que tout le trafic est enregistré. Pour en savoir plus, consultez [Évaluation du trafic](../virtual-network/security-overview.md#how-traffic-is-evaluated) dans les groupes de sécurité réseau.

**Coûts de la journalisation de flux** : la journalisation de flux NSG est facturée selon le volume de journaux d’activité produits. Un volume de trafic élevé peut entraîner un volume important de journaux de flux avec les coûts associés. Les tarifs des journaux de flux NSG n’incluent pas les coûts de stockage afférents. L’utilisation de la fonctionnalité de stratégie de conservation avec la journalisation des flux de groupe de sécurité réseau peut entraîner un volume élevé d’opérations de stockage avec les coûts associés. Si vous n’avez pas besoin de la fonctionnalité de stratégie de conservation, nous vous recommandons de définir cette valeur sur 0. Pour en savoir plus, consultez [Tarifs Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) et [Tarifs du stockage Azure](https://azure.microsoft.com/pricing/details/storage/) pour de plus amples informations.

**Flux entrants journalisés à partir d’adresses IP Internet dans des machines virtuelles sans IP publiques** : Les machines virtuelles qui n’ont pas d’IP publique attribuée via une IP publique associée à la carte d’interface réseau en tant qu’IP publique de niveau d’instance, ou qui font partie d’un pool principal équilibreur de charge de base, utilisent une [architecture de système en réseau par défaut](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) et ont une adresse IP affectée par Azure afin de faciliter la connectivité sortante. Par conséquent, vous pouvez observer des entrées de journal de flux pour les flux d’adresses IP Internet, si le flux est destiné à un port dans la plage de ports attribués à l’architecture de système en réseau. Bien qu’Azure n’autorise pas ces flux vers les machines virtuelles, la tentative est journalisée et apparaît par conception dans le journal de flux du Groupe de sécurité réseau Network Watcher. Nous recommandons que le trafic Internet entrant indésirable soit explicitement bloqué avec le Groupe de sécurité réseau.

**Nombre d’octets et de paquets incorrect pour les flux sans état** : [Les groupes de sécurité réseau (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) sont implémentés en tant que [pare-feu avec état](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Toutefois, de nombreuses règles internes/par défaut qui contrôlent le flux du trafic sont implémentées sans état. En raison des limitations de la plateforme, les nombres d’octets et de paquets ne sont pas enregistrés pour les flux sans état (autrement dit, les flux de trafic passant par les règles sans état), ils sont enregistrés uniquement pour les flux avec état. Par conséquent, le nombre d’octets et de paquets signalé dans les journaux de flux NSG (et Traffic Analytics) peut différer des flux réels. La résolution de cette limitation est prévue avant juin 2020.

## <a name="sample-log-records"></a>Exemples d’enregistrements de journal

Le texte ci-dessous est un exemple de journal de flux. Comme vous pouvez le voir, il existe plusieurs enregistrements qui suivent la liste des propriétés décrite dans la section précédente.


> [!NOTE]
> Les valeurs de la propriété **flowTuples* correspondent à une liste de valeurs séparées par des virgules.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Exemple de modèle de journal de flux NSG version 1
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```
### <a name="version-2-nsg-flow-log-format-sample"></a>Exemple de modèle de journal de flux NSG version 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Étapes suivantes

- Pour savoir comment activer les journaux de flux, consultez [Enable flow logs](network-watcher-nsg-flow-logging-portal.md) (Activer les journaux de flux).
- Pour savoir comment lire les journaux de flux, consultez [Lire des journaux de flux NSG](network-watcher-read-nsg-flow-logs.md).
- Pour en savoir plus sur la journalisation du Groupe de sécurité réseau, consultez [Journaux Azure Monitor des Groupes de sécurité réseau (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Pour déterminer si le trafic est autorisé ou refusé vers ou à partir d’une machine virtuelle, consultez [Diagnostiquer un problème de filtre de trafic réseau de machines virtuelles](diagnose-vm-network-traffic-filtering-problem.md)
