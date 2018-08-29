---
title: Présentation de l’équilibrage de charge Azure | Microsoft Docs
description: Présentation des fonctionnalités, de l'architecture et de l'implémentation de l'équilibrage de charge Azure. Découvrez comment fonctionne l’équilibrage de charge et l’exploiter dans le cloud.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer service and what I can use it for.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/20/2018
ms.author: kumud
ms.custom: mvc
ms.openlocfilehash: 47509cd0a9208f41a52bf1a07c460bcdda2cb479
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42145995"
---
# <a name="what-is-azure-load-balancer"></a>Qu’est-ce qu’Azure Load Balancer ?

Azure Load Balancer vous permet de mettre à l’échelle vos applications et de créer une haute disponibilité pour vos services. Load Balancer prend en charge les scénarios entrants et sortants, offre une latence faible et un débit élevé, et peut augmenter l’échelle jusqu’à des millions de flux pour toutes les applications TCP et UDP.  

Load Balancer distribue les nouveaux flux entrants arrivant sur le serveur frontal de l’équilibreur de charge sur des instances de pool du serveur principal en fonction des règles et des sondes d’intégrité définies. 

En outre, un équilibreur de charge public peut fournir des connexions sortantes pour des machines virtuelles à l’intérieur de votre réseau virtuel en traduisant leurs adresses IP privées en adresses IP publiques.

Azure Load Balancer se décline en deux références SKU : De base et Standard. Celles-ci présentent des différences en termes de mise à l’échelle, de fonctionnalités et de tarification. N’importe quel scénario possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard, même si l’approche à suivre peut varier légèrement. Pour bien comprendre le fonctionnement de Load Balancer, il est important de se familiariser avec ses capacités de base et les différences propres aux références SKU.

## <a name="why-use-load-balancer"></a>Pourquoi utiliser Load Balancer ? 

Vous pouvez utiliser Azure Load Balancer pour :

* équilibrer la charge du trafic Internet entrant sur vos machines virtuelles. Cette configuration est appelée [équilibreur de charge public](#publicloadbalancer).
* équilibrer la charge du trafic entre les machines virtuelles à l’intérieur d’un réseau virtuel. Vous pouvez également communiquer avec un serveur frontal d’équilibreur de charge à partir d’un réseau local dans le cadre d’un scénario hybride. Ces deux scénarios s’appuient sur une configuration appelée [équilibreur de charge interne](#internalloadbalancer).
* réacheminer le trafic d’un port vers un port spécifique sur des machines virtuelles données avec des règles de traduction d’adresses réseau (NAT) entrantes.
* fournir une [connectivité sortante](load-balancer-outbound-connections.md) pour des machines virtuelles à l’intérieur de votre réseau virtuel à l’aide d’un équilibreur de charge public.


>[!NOTE]
> Azure offre une suite de solutions d’équilibrage de charge entièrement managées pour vos scénarios. Si vous recherchez une terminaison de protocole TLS (« déchargement SSL ») ou un traitement de couche d’application par requête HTTP/HTTPS, consultez [Application Gateway](../application-gateway/application-gateway-introduction.md). Si vous recherchez un équilibrage de charge DNS global, consultez [Traffic Manager](../traffic-manager/traffic-manager-overview.md). Vos scénarios de bout en bout peuvent tirer parti de la combinaison de ces solutions en fonction de vos besoins.

## <a name="what-are-load-balancer-resources"></a>Que sont les ressources d’équilibreur de charge ?

Une ressource d’équilibreur de charge peut exister sous la forme d’un équilibreur de charge public ou d’un équilibreur de charge interne. Les fonctions de la ressource d’équilibreur de charge sont exprimées en tant que définition de serveur frontal, de règle, de sonde d’intégrité et de pool de serveur principal. Vous placez des machines virtuelles dans le pool principal en spécifiant le pool principal à partir de la machine virtuelle.

Les ressources d’équilibreur de charge sont des objets dans lesquels vous pouvez exprimer la manière dont Azure doit programmer son infrastructure mutualisée pour obtenir le scénario que vous souhaitez créer. Il n’existe aucune relation directe entre les ressources d’équilibreur de charge et l’infrastructure réelle. La création d’un équilibreur de charge ne crée pas d’instance et la capacité demeure toujours disponible. 

## <a name="fundamental-load-balancer-features"></a>Fonctionnalités de base de Load Balancer

Load Balancer offre les fonctionnalités de base suivantes pour les applications TCP et UDP :

* **Équilibrage de charge**

    Azure Load Balancer permet de créer une règle pour distribuer le trafic arrivant sur un serveur frontal sur des instances de pool de serveur principal. Load Balancer utilise un algorithme de hachage pour la distribution des flux entrants et réécrit les en-têtes des flux sur les instances du pool du serveur principal en conséquence. Un serveur est disponible pour recevoir les nouveaux flux quand une sonde d’intégrité indique un point de terminaison de serveur principal sain.
    
    Par défaut, Load Balancer utilise un hachage à 5 tuples composé de l’adresse IP source, du port source, de l’adresse IP de destination, du port de destination et du numéro de protocole IP pour mapper les flux vers les serveurs disponibles. Vous pouvez choisir de créer une affinité avec une adresse IP source spécifique en utilisant un hachage à 2 ou 3 tuples pour une règle donnée. Tous les paquets d’un même flux arrivent sur la même instance derrière le serveur frontal soumis à l’équilibrage de charge. Quand le client démarre un nouveau flux à partir de la même adresse IP source, le port source est modifié. Par conséquent, la distribution à 5 tuples peut provoquer l’acheminement du trafic vers un autre point de terminaison de serveur principal.

    Pour plus d’informations, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md). L’image suivante montre la distribution basée sur le hachage :

    ![Distribution basée sur le hachage](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figure : distribution basée sur le hachage*

* **Réacheminement de port**

    Load Balancer vous permet de créer une règle NAT entrante pour réacheminer le trafic d’un port spécifique d’une adresse IP donnée du serveur frontal vers un port spécifique d’une instance donnée du serveur principal à l’intérieur du réseau virtuel. Cette opération est accomplie à l’aide de la même distribution basée sur le hachage que l’équilibrage de charge. Les scénarios courants pour cette fonctionnalité incluent les sessions de protocole RDP (Remote Desktop Protocol) ou SSH (Secure Shell) avec des instances de machines virtuelles individuelles à l’intérieur du réseau virtuel Azure. Vous pouvez mapper plusieurs points de terminaison internes à des ports différents sur la même adresse IP de serveur frontal. Vous pouvez les utiliser pour administrer à distance vos machines virtuelles via Internet sans avoir besoin de serveur de rebond supplémentaire.

* **Indépendance vis-à-vis des applications et transparence**

    Load Balancer n’interagit pas directement avec les protocoles TCP et UDP ou la couche Application et n’importe quel scénario d’application TCP ou UDP peut être pris en charge.  Load Balancer ne démarre pas et ne termine pas les flux ; il n’interagit pas avec la charge utile du flux ; il ne fournit aucune fonction de passerelle de couche Application et l’établissement de liaisons de protocole se produit toujours directement entre le client et l’instance de pool du serveur principal.  La réponse à un flux entrant provient toujours d’une machine virtuelle.  Lorsque le flux arrive sur la machine virtuelle, l’adresse IP source d’origine est également conservée.  Quelques exemples pour mieux illustrer la transparence :
    - Chaque point de terminaison obtient uniquement une réponse d’une machine virtuelle.  Par exemple, l’établissement d’une liaison TCP se fait toujours entre le client et la machine virtuelle du serveur principal sélectionnée.  La réponse à une demande d’un serveur principal est une réponse générée par la machine virtuelle du serveur principal. Lorsque vous validez correctement la connectivité à un serveur frontal, vous validez la connectivité de bout en bout à au moins une machine virtuelle du serveur principal.
    - Les charges utiles d’application sont transparentes pour Load Balancer et n’importe quelle application UDP ou TCP peut être prise en charge. Pour les charges de travail qui nécessitent un traitement par requête HTTP ou une manipulation des charges utiles de couche Application (par exemple, l’analyse des URL HTTP), vous devez utiliser un équilibreur de charge de couche 7 comme [Application Gateway](https://azure.microsoft.com/services/application-gateway).
    - Étant donné que Load Balancer est indépendant de la charge utile TCP et que le déchargement TLS (« SSL ») n’est pas fourni, vous pouvez générer des scénarios chiffrés de bout en bout à l’aide de Load Balancer et vous profitez d’une importante augmentation de la taille des instances pour les applications TLS en mettant fin à la connexion TLS sur la machine virtuelle elle-même.  Par exemple, la capacité de création de clés pour votre session TLS est uniquement limitée par le type et le nombre de machines virtuelles que vous ajoutez au pool du serveur principal.  Si vous avez besoin du « déchargement SSL », d’un traitement de couche Application ou si vous souhaitez déléguer la gestion des certificats à Azure, vous devez utiliser l’équilibreur de charge couche 7 Azure, à savoir [Application Gateway](https://azure.microsoft.com/services/application-gateway).
        

* **Reconfiguration automatique**

    Load Balancer se reconfigure instantanément lors de la mise à l’échelle d’instances vers le haut ou vers le bas. Quand vous ajoutez ou supprimez des machines virtuelles du pool du serveur principal, l’équilibreur de charge est reconfiguré sans opérations supplémentaires sur la ressource de l’équilibreur de charge.

* **Sondes d’intégrité**

    Pour déterminer l’intégrité des instances du pool du serveur principal, Load Balancer fait appel à des sondes d’intégrité définies par vos soins. Lorsqu’une sonde ne répond pas, l’équilibrage de charge n’envoie plus de nouvelles connexions aux instances défaillantes. Les connexions existantes ne sont pas affectées et restent actives jusqu’à ce que l’application termine le flux, qu’un délai d’inactivité soit atteint ou que la machine virtuelle soit arrêtée.
     
    Load Balancer fournit des [types de sonde d’intégrité différents](load-balancer-custom-probe-overview.md#types) pour les points de terminaison TCP, HTTP et HTTPS.

    En outre, lorsque vous utilisez les services de cloud classique, un type supplémentaire est autorisé : [agent invité](load-balancer-custom-probe-overview.md#guestagent).  Il s’agit d’une sonde d’intégrité de dernier recours qui n’est pas recommandée lorsque d’autres options sont viables.
    
* **Connexions sortantes (SNAT)**

    Tous les flux sortants circulant d’adresses IP privées à l’intérieur de votre réseau virtuel vers des adresses IP publiques sur Internet peuvent être traduites en une adresse IP de serveur frontal de l’équilibreur de charge. Quand un serveur frontal public est lié à une machine virtuelle de serveur principal par le biais d’une règle d’équilibrage de charge, Azure programme la traduction automatique des connexions sortantes en adresse IP du serveur frontal public.

    * Il permet une mise à niveau et une récupération d’urgence simples des services, étant donné que le serveur frontal peut être dynamiquement mappé à une autre instance du service.
    * Il facilite la gestion des listes de contrôle d’accès (ACL). Les ACL exprimées sous forme d’adresses IP de serveur frontal ne changent pas quand les services montent en puissance, descendent en puissance ou sont redéployés.  La traduction des connexions sortantes en un plus petit nombre d’adresses IP que de machines peut réduire la charge liée à la création d’une liste verte.

    Pour plus d’informations, consultez [Connexions sortantes](load-balancer-outbound-connections.md).

La référence SKU Standard de Load Balancer offre des fonctionnalités spécifiques en plus de ces capacités de base. Pour plus d’informations, poursuivez la lecture de cet article.

## <a name="skus"></a> Comparaison des références SKU de Load Balancer

Load Balancer prend en charge les références SKU De base et Standard, chaque type présentant des différences en termes d’échelle de scénario, de fonctionnalités et de tarification. N’importe quel scénario possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard. En fait, les API sont identiques pour ces deux références SKU et sont appelées en spécifiant une référence SKU. Les références SKU pour Load Balancer et les adresses IP publiques sont prises en charge à partir de la version d’API 2017-08-01. Les deux références SKU présentent les mêmes API et structure générales.

Toutefois, selon la référence SKU choisie, la configuration du scénario complet peut varier légèrement. Dans la documentation relative à Load Balancer, les articles applicables uniquement à une référence SKU spécifique sont signalés. Pour comparer les références SKU et comprendre leurs différences, consultez le tableau ci-dessous. Pour plus d’informations, consultez [Vue d’ensemble du niveau Standard de Load Balancer](load-balancer-standard-overview.md).

>[!NOTE]
> Les nouvelles conceptions doivent adopter Standard Load Balancer. 

Les machines virtuelles autonomes, les groupes à haute disponibilité et les groupes de machines virtuelles identiques peuvent uniquement être connectés à une référence SKU, jamais aux deux. Lorsque vous utilisez des adresses IP publiques, les références SKU de Load Balancer et des adresses IP publiques doivent correspondre. Les références SKU de Load Balancer et des adresses IP publiques ne sont pas mutables.

_Il est recommandé de spécifier les références SKU de manière explicite, même si cela n’est pas encore obligatoire._  Pour l’heure, les modifications requises sont réduites au minimum. Si aucune référence SKU n’est spécifiée, cela est interprété comme l’intention d’utiliser la version d’API 2017-08-01 de la référence SKU De base.

>[!IMPORTANT]
>La référence SKU Standard de Load Balancer est un nouveau produit qui constitue essentiellement une version plus avancée de la référence De base de Load Balancer. Il existe des différences importantes et délibérées entre ces deux produits. N’importe quel scénario complet possible avec la référence SKU De base de Load Balancer peut également être créé avec la référence SKU Standard. Si vous êtes déjà habitué à la référence SKU De base de Load Balancer, vous devez vous familiariser avec la référence SKU Standard de Load Balancer pour comprendre les changements les plus récents en termes de comportement entre ces deux références et leur impact. Lisez attentivement cette section.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Pour plus d’informations, voir [Limites de service pour Load Balancer](https://aka.ms/lblimits). Pour obtenir plus d’informations sur la référence SKU Standard de Load Balancer, consultez également la [présentation](load-balancer-standard-overview.md), la page relative à la [tarification](https://aka.ms/lbpricing) et la page relative au [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Concepts

### <a name = "publicloadbalancer"></a>Équilibreur de charge public

L’équilibreur de charge public mappe l’adresse IP publique et le numéro de port du trafic entrant à l’adresse IP privée et au numéro de port de la machine virtuelle et inversement pour le trafic provenant de la machine virtuelle. En appliquant des règles d’équilibrage de charge, vous pouvez distribuer des types de trafic donnés entre plusieurs machines virtuelles ou services. Par exemple, vous pouvez répartir la charge du trafic des requêtes web entre plusieurs serveurs web.

La figure suivante présente un point de terminaison à charge équilibrée pour le trafic web partagé entre trois machines virtuelles pour le port TCP 80 et public. Celles-ci sont incluses dans un jeu d’équilibrage de la charge.

![Exemple d’équilibrage de charge public](./media/load-balancer-overview/IC727496.png)

*Figure : équilibrage du trafic web à l’aide d’un équilibreur de charge public*

Quand les clients Internet envoient des requêtes de pages web à l’adresse IP publique d’une application web sur le port TCP 80, Azure Load Balancer distribue les requêtes entre les trois machines virtuelles du groupe soumis à l’équilibrage de charge. Des informations supplémentaires sur l’algorithme de l’équilibreur de charge sont disponibles dans la section[Fonctionnalités de l’équilibreur de charge](load-balancer-overview.md##fundamental-load-balancer-features) de cet article.

Par défaut, Azure Load Balancer répartit le trafic réseau équitablement sur plusieurs instances de machine virtuelle. Vous pouvez également configurer l’affinité de session. Pour plus d’informations, consultez [Mode de distribution de l’équilibrage de charge](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a> Équilibreur de charge interne

Un équilibreur de charge interne dirige le trafic uniquement vers les ressources qui se trouvent à l’intérieur d’un réseau virtuel ou qui utilisent un VPN pour accéder à l’infrastructure Azure. Un équilibreur de charge interne est en cela différent d’un équilibreur de charge public. L’infrastructure Azure limite l’accès aux adresses IP du serveur frontal soumis à l’équilibrage de charge au sein d’un réseau virtuel. Les adresses IP du serveur frontal et les réseaux virtuels ne sont jamais directement exposés à un point de terminaison Internet. Les applications métier internes s’exécutent dans Azure et sont accessibles à partir d’Azure ou à partir des ressources locales.

Un équilibreur de charge interne permet d’effectuer les types d’équilibrage de charge suivants :

* **Dans un réseau virtuel** : équilibrage de charge des machines virtuelles dans le réseau virtuel à un ensemble de machines virtuelles qui résident au sein du même réseau virtuel.
* **Pour un réseau virtuel intersites** : équilibrage de charge des ordinateurs locaux à un ensemble de machines virtuelles qui résident au sein du même réseau virtuel. 
* **Pour les applications à plusieurs niveaux** : équilibrage de charge pour les applications multiniveau accessibles sur Internet dans lesquelles les niveaux principaux ne sont pas accessibles sur Internet. Les niveaux principaux nécessitent un équilibrage de la charge du trafic du niveau accessible sur Internet (voir la figure suivante).
* **Pour les applications métier** : équilibrage de charge pour des applications métier hébergées dans Azure, sans matériel ou logiciel d’équilibrage de charge supplémentaire. Ce scénario inclut des serveurs locaux dans l’ensemble d’ordinateurs dont la charge du trafic est équilibrée.

![Exemple d’équilibreur de charge interne](./media/load-balancer-overview/IC744147.png)

*Figure : applications multiniveaux utilisant à la fois un équilibreur de charge public et un équilibreur de charge interne*

## <a name="pricing"></a>Tarifs
L’utilisation de la référence SKU Standard de Load Balancer est facturée en fonction du nombre de règles d’équilibrage de charge configurées et de la quantité de données traitées en entrée et en sortie. Pour plus d’informations sur la tarification de Load Balancer Standard, consultez la page [Tarification de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

La référence SKU De base de Load Balancer est proposée gratuitement.

## <a name="sla"></a>Contrat SLA

Pour plus d’informations sur le contrat de niveau de service (SLA) de la référence SKU Standard de Load Balancer, consultez la page [SLA pour Load Balancer](https://aka.ms/lbsla). 

## <a name="limitations"></a>Limites

- Load Balancer est un produit TCP ou UDP pour l’équilibrage de charge et de réacheminement de ports pour ces protocoles IP spécifiques.  Les règles d’équilibrage de charge et les règles NAT entrantes sont prises en charge pour les protocoles TCP et UDP, mais pas pour les autres protocoles IP, y compris ICMP. Load Balancer ne peut pas mettre fin à la charge utile d’un flux TCP ou UDP, ni y répondre ou interagir d’une quelconque autre façon avec cette charge utile. Il ne s’agit pas d’un proxy. La réussite de la validation de la connectivité à un serveur frontal doit avoir lieu dans la bande avec le même protocole que celui utilisé dans une règle d’équilibrage de charge ou de règle NAT entrante (TCP ou UDP) _et_ au moins une de vos machines virtuelles doit générer une réponse pour qu’un client puisse voir une réponse à partir d’un serveur frontal.  L’absence de réponse de la part du serveur frontal Load Balancer dans la bande indique qu’aucune machine virtuelle n’a été en mesure de répondre.  Il est impossible d’interagir avec un serveur frontal Load Balancer si aucune machine virtuelle ne peut répondre.  Cela vaut également pour les connexions sortantes où la [traduction d’adresses réseau avec masquage de port](load-balancer-outbound-connections.md#snat) est uniquement prise en charge pour les protocoles TCP et UDP ; tous les autres protocoles IP, y compris ICMP, échoueront également.  Assignez une adresse IP publique de niveau d’instance pour résoudre le problème.
- À la différence des équilibreurs de charge publics qui fournissent des [connexions sortantes](load-balancer-outbound-connections.md) lors de la transition à partir d’adresses IP privées à l’intérieur du réseau virtuel vers des adresses IP publiques, les équilibreurs de charge internes ne traduisent pas les connexions sortantes pour le serveur frontal d’un équilibreur de charge interne, dans la mesure où les deux sont dans l’espace d’adressage IP privé.  Cela évite le risque d’épuisement du port SNAT à l’intérieur de l’espace d’adressage IP interne unique lorsque la traduction n’est pas requise.  L’effet secondaire est que, si un flux sortant d’une machine virtuelle dans le pool principal tente un flux vers le serveur frontal de l’équilibreur de charge interne dans le pool duquel il réside _et_ est mappé sur lui-même, les deux aspects du flux ne correspondent pas et le flux échoue.  Si le flux n’es pas mappé sur la même machine virtuelle dans le pool principal qui a créé le flux vers le serveur frontal, le flux réussit.   Lorsque le flux est mappé sur lui-même, le flux sortant apparaît comme provenant de la machine virtuelle vers le serveur frontal et le flux entrant correspondant apparaît comme étant issu de la machine virtuelle vers elle-même. Du point de vue du système d’exploitation invité, les parties entrante et sortante d’un même flux ne correspondent pas à l’intérieur de la machine virtuelle. La pile TCP ne reconnaît pas ces parties du flux comme faisant partie du même flux étant donné que la source et la destination ne correspondent pas.  Quand le flux est mappé vers une autre machine virtuelle dans le pool backend, les parties du flux correspondent et la machine virtuelle peut répondre correctement au flux.  Le symptôme pour repérer ce scénario est la présence de délais d’expiration de connexion intermittents lorsque le flux revient au serveur principal qui est à l’origine du flux. Plusieurs solutions de contournement courantes permettent de parvenir à ce scénario de manière fiable (flux originaires d’un pool principal vers les pools du serveur principal ou vers le serveur frontal Load Balancer interne), notamment l’insertion d’une couche de proxy derrière le serveur Load Balancer interne ou [l’utilisation de règles de style DSR](load-balancer-multivip-overview.md).  Les clients peuvent combiner un serveur Load Balancer interne avec un proxy tiers ou remplacer une [Application Gateway](../application-gateway/application-gateway-introduction.md) interne pour les scénarios de proxy limités à HTTP/HTTPS. Vous pouvez utiliser un équilibreur de charge public pour résoudre le problème, mais le scénario qui en résulte est sujet aux [épuisements SNAT](load-balancer-outbound-connections.md#snat) et doit être évité, sauf s’il est soigneusement géré.

## <a name="next-steps"></a>Étapes suivantes

Vous avez à présent une vue d’ensemble d’Azure Load Balancer. Pour commencer à utiliser un équilibreur de charge, créez-en un, créez des machines virtuelles avec une extension IIS personnalisée installée et équilibrez la charge de l’application web entre les machines virtuelles. Pour en savoir plus, consultez le démarrage rapide [Créer un équilibreur de charge de base](quickstart-create-basic-load-balancer-portal.md).
