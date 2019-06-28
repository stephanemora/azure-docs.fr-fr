---
title: Utiliser les sondes d’intégrité Azure Load Balancer pour mettre à l’échelle et fournir une haute disponibilité pour votre service
titlesuffix: Azure Load Balancer
description: Découvrez comment utiliser les sondes d’intégrité pour surveiller les instances derrière Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/07/2019
ms.author: kumud
ms.openlocfilehash: e488a4a6438279270f3d86dafa16c45eda184059
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65415710"
---
# <a name="load-balancer-health-probes"></a>Sondes d’intégrité Load Balancer

Azure Load Balancer fournit des sondes d’intégrité à utiliser avec les règles d’équilibrage de charge.  La configuration des sondes d’intégrité et les réponses des sondes déterminent quelle instance de pool back-end recevra de nouveaux flux. Vous pouvez utiliser des sondes d’intégrité pour détecter la défaillance d’une application sur une instance principale. Vous pouvez également générer une réponse personnalisée pour une sonde d’intégrité, et utiliser celle-ci pour contrôler le flux de façon à gérer la charge ou les temps d’arrêt planifiés. Lors d’un échec d’une sonde d’intégrité, l’équilibreur de charge cesse d’envoyer de nouveaux flux à l’instance non intègre concernée.

Les sondes d’intégrité prennent en charge plusieurs protocoles. La disponibilité d’un type spécifique de sonde d’intégrité pour prendre en charge un protocole spécifique varie selon la référence SKU de l’équilibreur de charge.  De plus, le comportement du service varie selon la référence SKU de l’équilibreur de charge.

| | Référence SKU standard | Référence SKU De base |
| --- | --- | --- |
| [Types de sonde](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde](#probedown) | Toutes les sondes sont en panne, tous les flux TCP continuent. | Toutes les sondes sont en panne, tous les flux TCP arrivent à expiration. | 

> [!IMPORTANT]
> Les sondes d’intégrité d’un équilibreur de charge proviennent de l’adresse IP 168.63.129.16 et ne doivent pas être bloquées pour pouvoir annoter votre instance.  Consultez [adresse IP source de sonde](#probesource) pour plus d’informations.

## <a name="types"></a>Types de sonde

La sonde d’intégrité peut être configurée pour les écouteurs avec les trois protocoles suivants :

- [Écouteurs TCP](#tcpprobe)
- [Points de terminaison HTTP](#httpprobe)
- [Points de terminaison HTTPS](#httpsprobe)

Les types de sondes d’intégrité disponibles varient en fonction de la référence SKU de Load Balancer sélectionnée :

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Référence SKU standard |    &#9989; |   &#9989; |   &#9989; |
| Référence SKU De base |   &#9989; |   &#9989; | &#10060; |

Quel que soit le type de sonde que vous choisissez, les sondes d’intégrité peuvent observer n’importe quel port sur une instance de back-end, notamment le port sur lequel le service est fourni.

### <a name="tcpprobe"></a> Sonde TCP

Les sondes TCP établissent une connexion en effectuant une connexion TCP ouverte en trois temps au port défini.  Les sondes TCP mettent fin à une connexion avec une négociation TCP de fermeture dans quatre directions.

L’intervalle minimal de sonde est de 5 secondes et le nombre minimal de réponses défaillantes est de 2.  La durée totale de tous les intervalles ne peut pas dépasser 120 secondes.

Une sonde TCP échoue quand :
* L’écouteur TCP sur l’instance ne répond pas durant toute la durée de l’opération.  Une sonde est marquée hors service en fonction du nombre de demandes ayant échoué, et qui ont été configurées pour rester sans réponse avant que la sonde ne soit marquée hors service.
* La sonde reçoit une réinitialisation TCP depuis l’instance.

#### <a name="resource-manager-template"></a>Modèle Resource Manager

```json
    {
      "name": "tcp",
      "properties": {
        "protocol": "Tcp",
        "port": 1234,
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="httpprobe"></a> <a name="httpsprobe"></a> Sonde HTTP/HTTPS

> [!NOTE]
> La sonde HTTPS est disponible uniquement pour [Standard Load Balancer](load-balancer-standard-overview.md).

Les sondes HTTP et HTTPS sont basées sur la sonde TCP, et émettent un HTTP GET avec le chemin spécifié. Les deux sondes prennent en charge les chemins d’accès relatifs pour le HTTP GET. Les sondes HTTPS sont identiques aux sondes HTTP avec un wrapper Transport Layer Security (TLS, anciennement appelé SSL) supplémentaire. La sonde d’intégrité est marquée comme étant en fonctionnement lorsque l’instance répond avec un statut HTTP de 200 dans la période d’expiration.  Par défaut, la sonde d’intégrité tente de vérifier le port de sonde d’intégrité configuré toutes les 15 secondes. L’intervalle d’analyse de sonde minimal est de 5 secondes. La durée totale de tous les intervalles ne peut pas dépasser 120 secondes.

Les sondes HTTP / HTTPS peuvent également être pratiques si vous voulez qu’une sonde d’intégrité  implémente votre propre logique pour supprimer des instances de la rotation de l’équilibrage de charge si le port de la sonde est également l’écouteur pour le service lui-même. Par exemple, vous pouvez décider de supprimer une instance si elle utilise plus de 90 % du processeur et retourne dans un état HTTP différent de 200. 

Si vous utilisez Cloud Services et que vos rôles web utilisent w3wp.exe, vous bénéficiez aussi d’une surveillance automatique de votre site web. Les défaillances de votre code de site web renvoient un état autre que 200 pour la sonde de l’équilibreur de charge.

Une sonde HTTP/HTTPS échoue quand :
* Le point de terminaison de la sonde renvoie un code de réponse HTTP autre que 200 (par exemple, 403, 404 ou 500). Ceci marquera immédiatement la sonde d’intégrité comme étant hors service. 
* Le point de terminaison de la sonde ne répond pas du tout pendant la période d’expiration de 31 secondes. Plusieurs demandes de sondage peuvent rester sans réponse avant que la sonde soit marquée comme inactive et jusqu’à ce que la somme de tous les intervalles de délai d’attente ait été atteinte.
* Le point de terminaison de la sonde ferme la connexion via une réinitialisation TCP.

#### <a name="resource-manager-templates"></a>Modèles Resource Manager

```json
    {
      "name": "http",
      "properties": {
        "protocol": "Http",
        "port": 80,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

```json
    {
      "name": "https",
      "properties": {
        "protocol": "Https",
        "port": 443,
        "requestPath": "/",
        "intervalInSeconds": 5,
        "numberOfProbes": 2
      },
```

### <a name="guestagent"></a>Sonde d’agent invité (Classique uniquement)

Les rôles de service cloud (rôles de travail et rôles Web) utilisent par défaut un agent invité pour la surveillance par sonde.  Une sonde d’agent invité est une configuration de dernier recours.  Utilisez toujours une sonde d’intégrité explicitement avec une sonde TCP ou HTTP. Une sonde d’agent invité n’est pas aussi efficace que les sondes définies explicitement pour la plupart des scénarios d’application.

Une sonde d’agent invité procède à une vérification de l’agent invité situé à l’intérieur de la machine virtuelle. Ensuite, il écoute et répond HTTP 200 OK uniquement lorsque l’instance est prête (les autres états sont de type occupé, recyclage ou arrêt).

Pour plus d’informations, consultez les sections relatives à la [configuration du fichier de définition de service (csdef) pour les sondes d’intégrité](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou à la [création d’un équilibreur de charge public pour les services cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas. Il arrête ensuite d’envoyer des flux vers cette instance. L’équilibreur de charge continue de vérifier l’instance. 

Si l’agent invité répond avec un HTTP 200, l’équilibreur de charge renvoie de nouveaux flux vers cette instance.

Quand vous utilisez un rôle web, le code du site web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par l’agent de structure Azure ou l’agent invité. Les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent invité. Par conséquent, l’équilibreur de charge n’accepte qu’une instance hors rotation.

<a name="health"></a>
## <a name="probehealth"></a>Sonde d’intégrité

Les sondes TCP, HTTP et HTTPS sont considérées comme saines et annotent l’instance de rôle comme saine dans les cas suivants :

* La sonde d’intégrité fonctionne correctement après le démarrage de la machine virtuelle.
* Le nombre spécifié de sondes nécessaires pour marquer l’instance de rôle comme étant saine spécifié a été atteint.

> [!NOTE]
> Si la sonde d’intégrité fluctue, l’équilibreur de charge attend plus longtemps avant de replacer l’instance de rôle dans un état d’intégrité normal. Ce délai d’attente supplémentaire protège l’utilisateur et l’infrastructure. Il s’agit d’une stratégie intentionnelle.

## <a name="probe-count-and-timeout"></a>Nombre et délai d’expiration des sondes

Le comportement des sondes dépend des éléments suivants :

* Le nombre de sondes ayant réussi et qui permettent à une instance d’être étiquetée positivement.
* Le nombre de sondes ayant échoué et qui permettent à une instance d’être étiquetée comme étant hors service.

Les valeurs de délai d’expiration et d’intervalle spécifiées déterminent si une instance est marquée comme étant saine ou hors service.

## <a name="probedown"></a>Comportement en cas de panne de sonde

### <a name="tcp-connections"></a>Connexions TCP

Les nouvelles connexions TCP avec les instances de back-end saines restantes réussissent.

Si la sonde d’intégrité d’une instance de serveur principal échoue, les connexions TCP établies à cette instance de serveur principal demeurent.

Si toutes les sondes vers toutes les instances d’un pool principal échouent, aucun nouveau flux ne sera envoyé au pool principal. L’équilibreur de charge standard autorise les flux TCP établis à continuer.  L’équilibreur de charge de base arrête tous les flux TCP existants vers le pool principal.
 
Load Balancer est un service pass-through (il ne met pas fin aux connexions TCP), et le flux est toujours entre le client et le système d’exploitation invité et l’application de la machine virtuelle. Un pool avec toutes les sondes hors service entraînera une absence de réponse de la part d’un front-end face aux tentatives d’ouverture de connexion TCP (SYN), car il n’existe aucune instance de back-end saine pour recevoir le flux et répondre avec un SYN-ACK.

### <a name="udp-datagrams"></a>Datagrammes UDP

Les datagrammes UDP seront remis aux instances du serveur principal saines.

UDP est sans connexion et il n’existe aucun état de flux suivi pour UDP. Si la sonde d’intégrité de n’importe quelle instance de serveur principal échoue, les flux UDP existants peuvent se déplacer vers une autre instance intègre dans le pool principal.

Si l’ensemble des sondes de l’ensemble des instances d’un pool principal échouent, les flux UDP existants prennent fin pour les équilibreurs de charge de base et standard.

<a name="source"></a>
## <a name="probesource"></a>Adresse IP source de sonde

Load Balancer utilise un service de détection distribué pour son modèle de contrôle d’intégrité interne. Le service de sondage se trouve sur chaque hôte où il y a des machines virtuelles, et il peut être programmé pour générer des sondes d’intégrité à la demande en fonction de la configuration du client. Le trafic de la sonde d’intégrité se fait directement entre le service de sondage qui génère la sonde d’intégrité et la machine virtuelle du client. Toutes les sondes d’intégrité de l’équilibreur de charge ont pour source l’adresse IP 168.63.129.16.  Vous pouvez utiliser l’espace d’adressage IP à l’intérieur d’un réseau virtuel qui n’est pas un espace RFC1918.  L’utilisation d’une adresse IP réservée au niveau mondial et détenue par Microsoft réduit le risque d’un conflit d’adresse IP avec l’espace d’adressage IP que vous utilisez au sein du réseau virtuel.  Cette adresse IP est la même dans toutes les régions et ne change pas : elle ne constitue pas un risque de sécurité, car seul le composant de la plateforme Azure interne peut émettre un paquet depuis cette adresse IP. 

L’étiquette du service AzureLoadBalancer identifie cette adresse IP source dans vos [groupes de sécurité réseau](../virtual-network/security-overview.md) et autorise par défaut le trafic de la sonde d’intégrité.

En plus des sondes d’intégrité Load Balancer, les [opérations suivantes utilisent cette adresse IP](../virtual-network/what-is-ip-address-168-63-129-16.md):

- Permet à l’agent de machine virtuelle de communiquer avec la plateforme pour signaler qu’il est dans un état « Prêt »
- Permet la communication avec le serveur virtuel DNS pour fournir la résolution de nom filtré aux clients qui ne définissent pas de serveurs DNS personnalisés.  Ce filtrage permet de s’assurer que les clients peuvent uniquement résoudre les noms d’hôte de leur déploiement.
- Permet à la machine virtuelle d’obtenir une adresse IP dynamique auprès du service DHCP dans Azure.

## <a name="design"></a> Guide de conception

Les sondes d’intégrité sont utilisées pour améliorer la résilience de votre service et pour lui permettre d’être mis à l’échelle. Une configuration incorrecte ou un mauvais modèle de conception peut avoir un impact sur la disponibilité et la scalabilité de votre service. Passez en revue la totalité de ce document et considérez l’impact sur votre scénario quand la réponse de cette sonde est négative ou positive, et comment elle impacte la disponibilité du scénario de votre application.

Quand vous concevez le modèle d’intégrité pour votre application, vous devez sonder un port sur une instance de back-end qui reflète l’intégrité de cette instance de __et__ du service d’application que vous fournissez.  Le port de l’application et le port de la sonde ne doivent pas nécessairement être les mêmes.  Dans certains scénarios, il peut être souhaitable que le port de la sonde soit différent de celui sur lequel votre application fournit le service.  

Il peut parfois être utile pour votre application de générer une réponse de sonde d’intégrité non seulement pour détecter l’intégrité de votre application, mais aussi pour signaler directement à l’équilibreur de charge si votre instance doit ou non recevoir de nouveaux flux.  Vous pouvez manipuler la réponse de la sonde pour permettre à votre application de créer une contre-pression et de limiter la remise de nouveaux flux à une instance en mettant en échec la sonde d’intégrité, ou de préparer la maintenance de votre application et de commencer à simplifier votre scénario.  Lors de l’utilisation de l’équilibrage de charge standard, un signal de [sonde négative](#probedown) autorise toujours la continuation des flux TCP jusqu’au délai d’expiration pour inactivité ou jusqu’à la fermeture de la connexion. 

Pour l’équilibrage de charge UDP, vous devez générer un signal de sonde d’intégrité personnalisé à partir de l’instance de back-end, et utiliser une sonde d’intégrité TCP, HTTP ou HTTPS ciblant l’écouteur correspondant, afin de refléter l’intégrité de votre application UDP.

Quand vous utilisez des [règles d’équilibrage de charge de ports à haute disponibilité](load-balancer-ha-ports-overview.md) avec [Standard Load Balancer](load-balancer-standard-overview.md), tous les ports font l’objet de l’équilibrage de charge et une même réponse de sonde d’intégrité doit refléter l’état de l’intégralité de l’instance.

Ne traduisez pas ou n’utilisez pas de proxy pour une sonde d’intégrité via l’instance qui reçoit la sonde d’intégrité pour la transférer vers une autre instance de votre réseau virtuel, car cette configuration peut entraîner des défaillances en cascade dans votre scénario.  Considérez le scénario suivant : un ensemble d’appliances de tiers est déployé dans le pool de back-ends d’une ressource d’équilibreur de charge pour fournir une mise à l’échelle et une redondance pour les appliances, et la sonde d’intégrité est configurée pour sonder un port que l’appliance de tiers met en proxy ou traduit vers les autres machines virtuelles derrière l’appliance.  Si vous sondez le même port que celui que vous utilisez pour traduire ou mettre en proxy les demandes vers les autres machines virtuelles derrière l’appliance, toute réponse de la sonde provenant d’une même machine virtuelle derrière l’appliance marque l’appliance elle-même comme étant hors service. Cette configuration peut entraîner un échec en cascade du scénario d’application dans son intégralité en raison de la présence d’une instance de back-end derrière l’appliance.  Le déclencheur peut être un échec intermittent de la sonde, qui fait que l’équilibreur de charge marque comme étant hors service la destination d’origine (l’instance de l’appliance), qui à son tour peut rendre inopérant le scénario de toute votre application. Au lieu de cela, sondez l’intégrité de l’appliance elle-même. La sélection de la sonde pour déterminer le signal d’intégrité est une considération importante pour les scénarios d’appliances virtuelles réseau, et vous devez consulter le fournisseur de votre application pour savoir quel est le signal d’intégrité approprié pour de tels scénarios.

Si vous n’autorisez pas l’[adresse IP source](#probesource) de la sonde dans vos stratégies de pare-feu, la sonde d’intégrité échoue, car il lui est impossible d’atteindre votre instance.  L’équilibreur de charge marque à son tour votre instance comme étant hors service, en raison de l’échec de la sonde d’intégrité.  Une configuration incorrecte peut entraîner l’échec du scénario de votre application avec équilibrage de charge.

Pour que la sonde d’intégrité de l’équilibreur de charge marque positivement votre instance, vous **devez** autoriser cette adresse IP dans tous les [groupes de sécurité réseau](../virtual-network/security-overview.md) Azure et dans les stratégies de pare-feu local.  Par défaut, chaque groupe de sécurité réseau inclut l’[étiquette du service](../virtual-network/security-overview.md#service-tags) AzureLoadBalancer pour autoriser le trafic de la sonde d’intégrité.

Si vous voulez tester une défaillance de la sonde d’intégrité ou marquer négativement une instance individuelle, vous pouvez utiliser un [groupe de sécurité réseau ](../virtual-network/security-overview.md) pour bloquer explicitement la sonde d’intégrité (port de destination ou [adresse IP source](#probesource)) et simuler la défaillance d’une sonde.

Ne configurez pas votre réseau virtuel avec la plage d’adresses IP détenue par Microsoft qui contient 168.63.129.16.  Ces configurations vont entrer en conflit avec l’adresse IP de la sonde d’intégrité et risquent de faire échouer votre scénario.

Si vous avez plusieurs interfaces sur votre machine virtuelle, vous devez vous assurer que vous répondez à la sonde sur l’interface à partir de laquelle l’avez reçue.  Il peut être nécessaire de traduire l’adresse réseau source de cette adresse dans la machine virtuelle pour chaque interface.

N’activez pas les [horodatages TCP](https://tools.ietf.org/html/rfc1323).  L’activation des horodatages TCP entraîne l’échec des sondes d’intégrité à cause de la suppression des paquets TCP par la pile TCP du système d’exploitation invité de la machine virtuelle, ce qui fait que l’équilibreur de charge marque négativement le point de terminaison correspondant.  Les horodatages TCP sont normalement activés par défaut sur les images de machine virtuelle avec sécurité renforcée, et ils doivent être désactivés.

## <a name="monitoring"></a>Surveillance

Les [Standard Load Balancer](load-balancer-standard-overview.md) publics et internes exposent l’état des sondes d’intégrité par point de terminaison et par instance principale comme des métriques multidimensionnelles via Azure Monitor. Ces métriques peuvent être utilisées par d’autres services Azure ou par des applications partenaires. 

Une instance Load Balancer publique de base expose l’état de la sonde d’intégrité résumé, par pool principal, via les journaux Azure Monitor.  Les journaux Azure Monitor ne sont pas disponibles pour les instances Load Balancer de base internes.  Vous pouvez utiliser les [journaux Azure Monitor](load-balancer-monitor-log.md) pour vérifier le nombre et l’état d’intégrité des sondes d’équilibreurs de charge publics. La journalisation peut être utilisée avec Power BI ou Operational Insights pour fournir des statistiques sur l’état d’intégrité de l’équilibreur de charge.

## <a name="limitations"></a>Limites

- Les sondes HTTPS ne prennent pas en charge l’authentification mutuelle avec un certificat client.
- Les sondes d’intégrité échouent quand les horodatages TCP sont activés.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [référence Standard de Load Balancer](load-balancer-standard-overview.md)
- [Créez un équilibreur de charge public dans le Gestionnaire des ressources à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST pour les sondes d’intégrité](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Demander de nouvelles capacités de sonde d’intégrité avec le [UserVoice de Load Balancer](https://aka.ms/lbuservoice)
