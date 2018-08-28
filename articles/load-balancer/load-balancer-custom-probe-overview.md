---
title: Utilisez des sondes d’intégrité de l’équilibreur de charge pour protéger votre service | Microsoft Docs
description: Découvrez comment utiliser les sondes d’intégrité pour surveiller les instances derrière Load Balancer
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: 46b152c5-6a27-4bfc-bea3-05de9ce06a57
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: kumud
ms.openlocfilehash: 91c7d16296653aea2381793f2e52f2b33b831185
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2018
ms.locfileid: "42144206"
---
# <a name="load-balancer-health-probes"></a>Sondes d’intégrité Load Balancer

Azure Load Balancer utilise des sondes d’intégrité pour déterminer quelle instance de pool principale recevra de nouveaux flux. Vous pouvez utiliser des sondes d’intégrité pour détecter la défaillance d’une application sur une instance principale. Vous pouvez également générer une réponse adaptée à une sonde d’intégrité, et utiliser cette dernière pour contrôler les flux et pour signaler à Load Balancer s’il faut continuer d’envoyer de nouveaux flux ou arrêter l’envoi de nouveaux flux vers une instance principale. Ce peut être utilisé pour gérer la charge ou un temps d’arrêt planifié. Lors d’un échec d’une sonde d’intégrité, l’équilibreur de charge cesse d’envoyer de nouveaux flux à l’instance non intègre concernée.

Les types de sondes d’intégrité disponibles et la façon dont les sondes d’intégrité se comportent dépendent de la référence SKU de Load Balancer que vous utilisez. Par exemple, le comportement de flux nouveaux et existants dépend du caractère TCP ou UDP d’un flux, ainsi que du type de référence SKU de Load Balancer que vous utilisez.

| | Référence SKU standard | Référence SKU De base |
| --- | --- | --- |
| [Types de sonde](#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [Comportement en cas de panne de sonde](#probedown) | Toutes les sondes sont en panne, tous les flux TCP continuent. | Toutes les sondes sont en panne, tous les flux TCP sont terminés. | 

> [!IMPORTANT]
> Les sondes d’intégrité d’un équilibreur de charge proviennent de l’adresse IP 168.63.129.16 et ne doivent pas être bloquées pour pouvoir annoter votre instance.  Consultez [adresse IP source de sonde](#probesource) pour plus d’informations.

## <a name="types"></a>Types de sonde d’intégrité

Les sondes d’intégrité peuvent observer n’importe quel port sur une instance de serveur principal, y compris le port sur lequel ledit service est fourni. Le protocole de la sonde d’intégrité peut être configuré pour trois types de sondes d’intégrité différents :

- [Écouteurs TCP](#tcpprobe)
- [Points de terminaison HTTP](#httpprobe)
- [Points de terminaison HTTPS](#httpsprobe)

Les types de sondes d’intégrité disponibles varient en fonction de la référence SKU de Load Balancer sélectionnée :

|| TCP | HTTP | HTTPS |
| --- | --- | --- | --- |
| Référence SKU standard |    &#9989; |   &#9989; |   &#9989; |
| Référence SKU De base |   &#9989; |   &#9989; | &#10060; |

Pour l’équilibrage de charge UDP, vous devez générer un signal de sonde d’intégrité personnalisé pour l’instance principale en utilisant une sonde d’intégrité TCP, HTTP ou HTTPS.

Lorsque vous utilisez des [règles d’équilibrage de charge de ports à haute disponibilité](load-balancer-ha-ports-overview.md) avec [Standard Load Balancer](load-balancer-standard-overview.md), tous les ports sont équilibrés en charge et une seule réponse de sonde d’intégrité doit refléter l’état de l’intégralité de l’instance.  

Vous ne devez pas utiliser de NAT ou de proxy pour une sonde d’intégrité via l’instance qui reçoit la sonde d’intégrité pour la transférer vers une autre instance de votre réseau virtuel, car cela peut entraîner des défaillances en cascade dans votre scénario.

Si vous souhaitez tester une défaillance de la sonde d’intégrité ou de marquer comme hors-service une instance individuelle, vous pouvez utiliser un groupe de sécurité pour bloquer ladite sonde d’intégrité (destination ou [source](#probesource)).

### <a name="tcpprobe"></a> Sonde TCP

Les sondes TCP établissent une connexion en effectuant une connexion TCP ouverte en trois temps au port défini.  S’ensuit alors une connexion TCP fermée en quatre temps.

L’intervalle minimal de sonde est de 5 secondes et le nombre minimal de réponses défaillantes est de 2.  La durée totale ne peut pas dépasser 120 secondes.

Une sonde TCP échoue quand :
* L’écouteur TCP sur l’instance ne répond pas durant toute la durée de l’opération.  Une sonde est marquée hors service en fonction du nombre de demandes d’analyse ayant échoué, et qui ont été configurées pour rester sans réponse avant que la sonde ne soit marquée négativement.
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

Les sondes HTTP et HTTPS établissent une connexion TCP et émettent un HTTP GET avec le chemin d’accès spécifié. Les deux sondes prennent en charge les chemins d’accès relatifs pour le HTTP GET. Les sondes HTTPS sont identiques aux sondes HTTP avec un wrapper Transport Layer Security (TLS, anciennement appelé SSL) supplémentaire. La sonde d’intégrité est marquée comme étant en fonctionnement lorsque l’instance répond avec un statut HTTP de 200 dans la période d’expiration.  Par défaut, ces sondes d’intégrité tentent de vérifier le port de sonde d’intégrité configuré toutes les 15 secondes. L’intervalle d’analyse de sonde minimal est de 5 secondes. La durée totale ne peut pas dépasser 120 secondes. 

Les sondes HTTP/HTTPS peuvent également être utiles pour implémenter votre propre logique, afin de supprimer des instances de la rotation de l’équilibreur de charge. Par exemple, vous pouvez décider de supprimer une instance si elle utilise plus de 90 % du processeur et retourne dans un état HTTP différent de 200. 

Si vous utilisez Cloud Services et que vos rôles web utilisent w3wp.exe, vous bénéficiez aussi d’une surveillance automatique de votre site web. Les défaillances de votre code de site web renvoient un état autre que 200 pour la sonde de l’équilibreur de charge.  Cette sonde HTTP écrase la sonde d’agent invitée par défaut. 

Une sonde HTTP/HTTPS échoue quand :
* Le point de terminaison de la sonde renvoie un code de réponse HTTP autre que 200 (par exemple, 403, 404 ou 500). Ceci marquera immédiatement la sonde d’intégrité négativement. 
* Le point de terminaison de la sonde ne répond pas du tout lors de la période d'expiration de 31 secondes. Selon la valeur définie pour le délai d’attente, il se peut que plusieurs demandes d’analyse ne reçoivent pas de réponse avant que celle-ci ne soit marquée comme n’étant pas en cours d’exécution (autrement dit, avant que les sondes SuccessFailCount soient envoyées).
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

Les rôles de service cloud (rôles de travail et rôles Web) utilisent par défaut un agent invité pour la surveillance par sonde.   Vous devez considérer cette option de dernier recours.  Vous devez toujours définir une sonde d’intégrité explicitement avec une sonde TCP ou HTTP. Une sonde d’agent invité n’est pas aussi efficace que les sondes définies explicitement pour la plupart des scénarios d’application.  

Une sonde d’agent invité procède à une vérification de l’agent invité situé à l’intérieur de la machine virtuelle. Ensuite, il écoute et répond HTTP 200 OK uniquement lorsque l’instance est prête (les autres états sont de type occupé, recyclage ou arrêt).

Pour plus d’informations, consultez les sections relatives à la [configuration du fichier de définition de service (csdef) pour les sondes d’intégrité](https://msdn.microsoft.com/library/azure/ee758710.aspx) ou à la [création d’un équilibreur de charge public pour les services cloud](load-balancer-get-started-internet-classic-cloud.md#check-load-balancer-health-status-for-cloud-services).

Si l’agent invité ne répond pas avec HTTP 200 OK, l’équilibreur de charge marque l’instance comme ne répondant pas. Il arrête ensuite d’envoyer des flux vers cette instance. L’équilibreur de charge continue de vérifier l’instance. 

Si l’agent invité répond avec un HTTP 200, l’équilibreur de charge renvoie de nouveaux flux vers cette instance.

Quand vous utilisez un rôle web, le code du site web s’exécute généralement dans w3wp.exe, qui n’est pas surveillé par l’agent de structure Azure ou l’agent invité. Les échecs dans w3wp.exe (par exemple, les réponses HTTP 500) ne sont pas signalés à l’agent invité. Par conséquent, l’équilibreur de charge n’accepte qu’une instance hors rotation.

## <a name="probehealth"></a>Sonde d’intégrité

Les sondes TCP, HTTP et HTTPS sont considérées comme saines et annotent l’instance de rôle comme saine dans les cas suivants :

* La sonde d’intégrité est réussie lors du premier démarrage de la machine virtuelle.
* Le nombre SuccessFailCount (décrit précédemment) définit la valeur des sondes ayant réussi nécessaires pour marquer l’instance de rôle comme étant saine. Si une instance de rôle a été supprimée, le nombre de sondes ayant réussi successives doit être égal ou supérieur à la valeur de SuccessFailCount pour marquer l’instance de rôle comme étant en cours d’exécution.

> [!NOTE]
> Si l’intégrité d’une instance de rôle fluctue, l’équilibreur de charge attend plus longtemps avant de remettre l’instance de rôle dans un état d’intégrité normal. Ce délai d’attente supplémentaire protège l’utilisateur et l’infrastructure. Il s’agit d’une stratégie intentionnelle.

## <a name="probe-count-and-timeout"></a>Nombre et délai d’expiration des sondes

Le comportement des sondes dépend des éléments suivants :

* Le nombre de sondes ayant réussi et qui permettent à une instance d’être étiquetée positivement.
* Le nombre de sondes ayant échoué et qui permettent à une instance d’être étiquetée comme étant hors service.

La valeur du délai d’expiration et de la fréquence définie dans SuccessFailCount détermine si une instance est confirmée comme étant en cours d’exécution ou pas. Dans le portail Azure, le délai d’expiration est défini sur deux fois la valeur de la fréquence.

Une règle d’équilibrage de charge possède une sonde d’intégrité unique définie par le pool principal lié.

## <a name="probedown"></a>Comportement en cas de panne de sonde

### <a name="tcp-connections"></a>Connexions TCP

De nouvelles connexions TCP parviennent à l’instance de serveur principal, qui est saine et qui dispose d’un système d’exploitation invité ainsi que d’une application capable d’accepter un nouveau flux.

Si la sonde d’intégrité d’une instance de serveur principal échoue, les connexions TCP établies à cette instance de serveur principal demeurent.

Si toutes les sondes vers toutes les instances d’un pool principal échouent, aucun nouveau flux ne sera envoyé au pool principal. L’équilibreur de charge standard autorise les flux TCP établis à continuer.  L’équilibreur de charge de base arrête tous les flux TCP existants vers le pool principal.
 
Étant donné que le flux est toujours entre le client et le système d’exploitation invité de la machine virtuelle, un pool avec toutes les sondes hors service entraînera une absence de réponse de la part d’un serveur frontal face aux tentatives d’ouverture de connexion TCP, car il n’existe aucune instance de serveur principal saine pour recevoir le flux.

### <a name="udp-datagrams"></a>Datagrammes UDP

Les datagrammes UDP seront remis aux instances du serveur principal saines.

UDP est sans connexion et il n’existe aucun état de flux suivi pour UDP. Si la sonde d’intégrité de n’importe quelle instance de serveur principal échoue, les flux UDP existants peuvent se déplacer vers une autre instance intègre dans le pool principal.

Si l’ensemble des sondes de l’ensemble des instances d’un pool principal échouent, les flux UDP existants prennent fin pour les équilibreurs de charge de base et standard.

## <a name="probesource"></a>Adresse IP source de sonde

Toutes les sondes d’intégrité de l’équilibreur de charge ont pour source l’adresse IP 168.63.129.16.  Lorsque vous importez vos propres adresses IP dans le Réseau Virtuel Azure, l’adresse IP de cette sonde intègre est garantie unique car elle est réservée pour Microsoft dans le monde entier.  Cette adresse est la même dans toutes les régions et ne change pas. Elle ne doit pas être considérée comme un risque de sécurité, car seule la plateforme Azure interne peut envoyer un message à partir de cette adresse IP. 

Pour marquer la sonde d’intégrité de l’équilibreur de charge de votre instance comme étant active, vous **devez** autoriser cette adresse IP tous les [Groupes de sécurité](../virtual-network/security-overview.md) et les stratégies de pare-feu local Azure.

Si vous n’autorisez pas cette adresse IP dans vos stratégies de pare-feu, la sonde d’intégrité échouera, car il lui est impossible d’atteindre votre instance.  L’équilibreur de charge marque à son tour votre instance comme étant hors service, en raison de l’échec de la sonde d’intégrité.  Cela peut entraîner l’échec de votre service équilibré en charge. 

Vous devez également éviter de configurer votre réseau virtuel avec la plage d’adresses IP Microsoft qui contient 168.63.129.16.  Cette action crée un conflit avec l’adresse IP de la sonde d’intégrité.

Si vous avez plusieurs interfaces sur votre machine virtuelle, vous devez vous assurer que vous répondez à la sonde sur l’interface à partir de laquelle l’avez reçue.  Cela peut nécessiter une source de traduction d'adresses réseau de cette adresse unique dans la machine virtuelle sur une base par interface.

## <a name="monitoring"></a>Surveillance

Les [Standard Load Balancer](load-balancer-standard-overview.md) publics et internes exposent l’état des sondes d’intégrité par point de terminaison et par instance principale comme des métriques multidimensionnelles via Azure Monitor. Cela peut ensuite être consommé par d’autres services Azure ou applications tierces. 

Un Load Balancer public de base expose l’état de la sonde d’intégrité résumé par pool principal via Log Analytics.  Ce n’est pas disponible pour les Load Balancer de base.  Vous pouvez utiliser [Log Analytics](load-balancer-monitor-log.md) pour vérifier le nombre et l’état d’intégrité des sondes d’équilibreurs de charge publics. La journalisation peut être utilisée avec Power BI ou Operational Insights pour fournir des statistiques sur l’état d’intégrité de l’équilibreur de charge.

## <a name="limitations"></a>Limites

-  Les sondes HTTPS ne prennent pas en charge l’authentification mutuelle avec un certificat client.
-  Le Kit de développement logiciel et PowerShell ne prennent pas en charge les sondes HTTPS pour l’instant.

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur la [référence Standard de Load Balancer](load-balancer-standard-overview.md)
- [Créez un équilibreur de charge public dans le Gestionnaire des ressources à l’aide de PowerShell](load-balancer-get-started-internet-arm-ps.md)
- [API REST pour les sondes d’intégrité](https://docs.microsoft.com/rest/api/load-balancer/loadbalancerprobes/)
- Demander de nouvelles capacités de sonde d’intégrité avec le [UserVoice de Load Balancer](https://aka.ms/lbuservoice)
