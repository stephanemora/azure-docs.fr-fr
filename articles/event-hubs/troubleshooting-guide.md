---
title: Résoudre les problèmes de connectivité - Azure Event Hubs | Microsoft Docs
description: Cet article fournit des informations sur la résolution des problèmes de connectivité avec Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b85c0895d1c8f165f494d29013adea014187dd23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87039325"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>Résoudre les problèmes de connectivité - Azure Event Hubs
Il existe plusieurs raisons pour lesquelles des applications clientes peuvent ne pas parvenir à se connecter à un Event Hub. Les problèmes de connectivité que vous rencontrez peuvent être permanents ou transitoires. Si le problème se produit tout le temps (permanent), vous souhaiterez peut-être vérifier la chaîne de connexion, les paramètres de pare-feu de votre organisation, les paramètres du pare-feu IP, les paramètres de sécurité réseau (points de terminaison de service, points de terminaison privés, etc.), entre autres. Pour les problèmes temporaires, la mise à niveau vers la dernière version du kit de développement logiciel (SDK), l’exécution de commandes pour vérifier les paquets ignorés et l’obtention de traces réseau peuvent vous aider à résoudre les problèmes. 

Cet article fournit des conseils sur la résolution des problèmes de connectivité avec Azure Event Hubs. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>Résoudre les problèmes de connectivité permanents
Si l’application n’est pas en mesure de se connecter à Event Hub du tout, suivez les étapes de cette section pour résoudre le problème. 

### <a name="check-if-there-is-a-service-outage"></a>Vérifier s’il y a une panne de service
Vérifiez s’il y a une panne du service Azure Event Hubs sur le [Site d’état du service Azure](https://azure.microsoft.com/status/).

### <a name="verify-the-connection-string"></a>Vérifier la chaîne de connexion 
Vérifiez que la chaîne de connexion est que vous utilisez est correcte. Consultez [Obtenir la chaîne de connexion](event-hubs-get-connection-string.md) pour obtenir la chaîne de connexion à l’aide du portail Azure, de l’interface CLI ou de PowerShell. 

Pour les clients Kafka, vérifiez que les fichiers producer.config ou consumer.config files sont configurés correctement. Pour plus d’informations, consultez [Envoyer et recevoir des messages avec Kafka dans Event Hubs](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs).

### <a name="check-if-the-ports-required-to-communicate-with-event-hubs-are-blocked-by-organizations-firewall"></a>Vérifiez si les ports requis pour communiquer avec Event Hubs sont bloqués par le pare-feu de l’organisation
Vérifiez que les ports utilisés pour communiquer avec Azure Event Hubs ne sont pas bloqués sur le pare-feu de votre organisation. Consultez le tableau suivant pour savoir quels ports de sortie vous devez ouvrir dans le but de communiquer avec Azure Event Hubs. 

| Protocol | Ports | Détails | 
| -------- | ----- | ------- | 
| AMQP | 5671 et 5672 | Consultez le [Guide du protocole AMQP](../service-bus-messaging/service-bus-amqp-protocol-guide.md) | 
| HTTP, HTTPS | 80, 443 |  |
| Kafka | 9093 | Voir [Utiliser Azure Event Hubs à partir d’applications Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

Voici un exemple de commande qui vérifie si le port 5671 est bloqué.

```powershell
tnc <yournamespacename>.servicebus.windows.net -port 5671
```

Sur Linux :

```shell
telnet <yournamespacename>.servicebus.windows.net 5671
```

### <a name="verify-that-ip-addresses-are-allowed-in-your-corporate-firewall"></a>Vérifier que les adresses IP sont autorisées dans votre pare-feu d’entreprise
Lorsque vous utilisez Azure, vous devez parfois autoriser des plages d’adresses IP ou des URL spécifiques dans votre pare-feu ou proxy d’entreprise pour accéder à tous les services Azure que vous utilisez ou essayez d’utiliser. Vérifiez que le trafic est autorisé sur les adresses IP utilisées par Event Hubs. Pour les adresses IP utilisées par Azure Event Hubs : consultez le document [Plages d’adresses IP Azure et balises de service – Cloud public](https://www.microsoft.com/download/details.aspx?id=56519).

Vérifiez également que l’adresse IP de votre espace de noms est autorisée. Pour trouver les adresses IP à autoriser pour vos connexions, procédez comme suit :

1. Exécutez la commande suivante depuis une invite de commandes : 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Notez l’adresse IP renvoyée dans `Non-authoritative answer`. Elle est susceptible de changer dans un seul cas : si vous restaurez l’espace de noms sur un autre cluster.

Si vous utilisez la redondance de zone pour votre espace de noms, vous devez suivre quelques étapes supplémentaires : 

1. Tout d’abord, exécutez nslookup sur l’espace de noms.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Notez le nom dans la section **Réponse ne faisant pas autorité**, qui se présente dans l’un des formats suivants : 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Exécutez nslookup pour chacun d’eux avec des suffixes s1, s2 et s3 pour obtenir les adresses IP des 3 instances en cours d’exécution dans 3 zones de disponibilité. 

### <a name="verify-that-azureeventgrid-service-tag-is-allowed-in-your-network-security-groups"></a>Vérifier que la balise de service AzureEventGrid est autorisée dans vos groupes de sécurité réseau
Si votre application s’exécute à l’intérieur d’un sous-réseau et qu’il existe un groupe de sécurité réseau associé, vérifiez si le trafic sortant Internet est autorisé ou si la balise de service AzureEventGrid est autorisée. Consultez [Balises de service du réseau virtuel](../virtual-network/service-tags-overview.md) et recherchez `EventHub`.

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>Vérifier si l’application doit être en exécutée sur un sous-réseau spécifique d’un réseau virtuel
Vérifiez que votre application s’exécute sur un sous-réseau de réseau virtuel qui a accès à l’espace de noms. Si ce n’est pas le cas, exécutez l’application dans le sous-réseau qui a accès à l’espace de noms ou ajoutez l’adresse IP de la machine sur laquelle l’application s’exécute au [pare-feu IP](event-hubs-ip-filtering.md). 

Lorsque vous créez un point de terminaison de service de réseau virtuel pour un espace de noms Event Hub, l’espace de noms accepte le trafic uniquement à partir du sous-réseau qui est lié au point de terminaison du service. Toutefois, il existe une exception à ce comportement. Vous pouvez ajouter des adresses IP spécifiques dans le pare-feu IP pour permettre l’accès au point de terminaison public Event Hub. Pour plus d’informations, consultez [Points de terminaison de service réseau](event-hubs-service-endpoints.md).

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>Vérifiez les paramètres de pare-feu IP pour votre espace de noms
Vérifiez que l’IP publique de la machine sur laquelle l’application s’exécute n’est pas bloquée par le pare-feu IP.  

Par défaut, les espaces de noms Event Hubs sont accessibles sur Internet tant que la demande s’accompagne d’une authentification et d’une autorisation valides. Avec le pare-feu IP, vous pouvez les limiter à un ensemble d’adresses IPv4 ou de plages d’adresses IPv4 dans la notation [CIDR (Classless InterDomain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing).

Les règles de pare-feu IP sont appliquées au niveau de l’espace de noms Event Hubs. Par conséquent, les règles s’appliquent à toutes les connexions de clients utilisant un protocole pris en charge. Toute tentative de connexion à partir d’une adresse IP qui ne correspond pas à une règle IP autorisée dans l’espace de noms Event Hubs est rejetée comme étant non autorisée. La réponse ne mentionne pas la règle IP. Les règles de filtre IP sont appliquées dans l’ordre et la première règle qui correspond à l’adresse IP détermine l’action d’acceptation ou de rejet.

Pour plus d'informations, consultez [Configuration des règles de pare-feu IP pour un espace de noms Azure Event Hubs](event-hubs-ip-filtering.md). Pour vérifier si vous avez des problèmes de filtrage IP, de réseau virtuel ou de chaîne de certificats, consultez [Résoudre les problèmes liés au réseau](#troubleshoot-network-related-issues).

#### <a name="find-the-ip-addresses-blocked-by-ip-firewall"></a>Rechercher les adresses IP bloquées par le pare-feu IP
Activez les journaux de diagnostic pour [Événements de connexion au réseau virtuel Event Hubs](event-hubs-diagnostic-logs.md#event-hubs-virtual-network-connection-event-schema) en suivant les instructions dans [Activer les journaux de diagnostic](event-hubs-diagnostic-logs.md#enable-diagnostic-logs). Vous verrez l’adresse IP de la connexion qui est refusée.

```json
{
    "SubscriptionId": "0000000-0000-0000-0000-000000000000",
    "NamespaceName": "namespace-name",
    "IPAddress": "1.2.3.4",
    "Action": "Deny Connection",
    "Reason": "IPAddress doesn't belong to a subnet with Service Endpoint enabled.",
    "Count": "65",
    "ResourceId": "/subscriptions/0000000-0000-0000-0000-000000000000/resourcegroups/testrg/providers/microsoft.eventhub/namespaces/namespace-name",
    "Category": "EventHubVNetConnectionEvent"
}
```

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>Vérifier si l’espace de noms est accessible uniquement à l’aide d’un point de terminaison privé
Si l’espace de noms Event Hubs est configuré pour être accessible uniquement par le biais d’un point de terminaison privé, vérifiez que l’application cliente accède à l’espace de noms sur le point de terminaison privé. 

[Le service Azure Private Link](../private-link/private-link-overview.md) vous permet d’accéder à Azure Event Hubs sur un **point de terminaison privé** de votre réseau virtuel. Un point de terminaison privé est une interface réseau qui vous permet de vous connecter de façon privée et sécurisée à un service basé sur Azure Private Link. Le point de terminaison privé utilise une adresse IP privée de votre réseau virtuel, plaçant de fait le service dans votre réseau virtuel. Sachant que l’ensemble du trafic à destination du service peut être routé via le point de terminaison privé, il n’y a aucun besoin de passerelles, d’appareils NAT, de connexions ExpressRoute ou VPN ou d’adresses IP publiques. Le trafic entre votre réseau virtuel et le service transite par le réseau principal de Microsoft, éliminant ainsi toute exposition à l’Internet public. Vous pouvez vous connecter à une instance d’une ressource Azure, ce qui vous donne le plus haut niveau de granularité en matière de contrôle d’accès.

Pour plus d’informations, consultez [Configurer des points de terminaison privés](private-link-service.md). Reportez-vous à la section **Valider le fonctionnement de la connexion au point de terminaison privé** pour confirmer qu’un point de terminaison privé est utilisé. 

### <a name="troubleshoot-network-related-issues"></a>Résoudre les problèmes liés au réseau
Pour résoudre les problèmes liés au réseau avec Event Hubs, procédez comme suit : 

Accédez à `https://<yournamespacename>.servicebus.windows.net/` ou utilisez [wget](https://www.gnu.org/software/wget/). Cet outil facilite les vérifications quand vous rencontrez des problèmes avec le filtrage des adresses IP, le réseau virtuel ou les chaînes de certificats (problème fréquent avec le SDK Java).

Voici un exemple de **message de réussite** :

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

Voici un exemple de **message d’échec** :

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>Résoudre les problèmes de connectivité temporaires
Si vous rencontrez des problèmes de connectivité intermittents, consultez les sections suivantes pour obtenir des conseils de dépannage. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>Utiliser la dernière version du kit de développement logiciel (SDK) client
Certains problèmes de connectivité temporaires ont peut-être été résolus dans les versions ultérieures du kit de développement logiciel (SDK) par rapport à celui que vous utilisez. Vérifiez que vous utilisez la dernière version des kits de développement logiciel (SDK) clients dans vos applications. Les kits de développement logiciel (SDK) sont améliorés en permanence avec des fonctionnalités nouvelles ou mises à jour et des correctifs de bogues. Consultez les notes de publication concernant les problèmes résolus et les fonctionnalités ajoutées/mises à jour. 

Pour plus d’informations sur les kits de développement logiciel (SDK) clients, consultez l’article [Azure Event Hubs - Kits de développement logiciel (SDK)](sdks.md). 

### <a name="run-the-command-to-check-dropped-packets"></a>Exécutez la commande pour vérifier les paquets ignorés
Si vous constatez des problèmes de connectivité intermittents, exécutez la commande suivante pour détecter les paquets supprimés. Cette commande essaiera d’établir 25 connexions TCP différentes toutes les 1 seconde avec le service. Ensuite, vous pouvez vérifier le nombre d’entre elles ayant réussi ou échoué, ainsi que la latence de connexion TCP. Vous pouvez télécharger l’outil `psping` à partir d’[ici](/sysinternals/downloads/psping).

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
Vous pouvez utiliser des commandes équivalentes dans d’autres outils, par exemple `tnc`, `ping`, etc. 

Si les étapes précédentes n’ont pas résolu le problème, obtenez une trace réseau et analysez-la à l’aide d’un outil tel que [Wireshark](https://www.wireshark.org/). Contactez le [support Microsoft](https://support.microsoft.com/) si nécessaire. 

### <a name="service-upgradesrestarts"></a>Mises à niveau/redémarrages du service
Des problèmes de connectivité temporaires peuvent se produire en raison de mises à niveau et redémarrages du service principal. Lorsqu’ils se produisent, vous pouvez observer les symptômes suivants : 

- Il peut y avoir une chute des messages/demandes entrants.
- Le fichier journal peut contenir des messages d’erreur.
- Les applications peuvent être déconnectées du service pendant quelques secondes.
- Les demandes peuvent être momentanément limitées.

Si le code d’application utilise le kit de développement logiciel (SDK), la stratégie de nouvelle tentative est déjà intégrée et active. L’application se reconnectera sans que cela ait un impact significatif sur l’application/le flux de travail. Le fait d’intercepter ces erreurs temporaires, d’effectuer une sauvegarde et une nouvelle tentative d’appel permet de garantir la résilience de votre code à ces problèmes temporaires.

## <a name="next-steps"></a>Étapes suivantes
Voir les articles suivants :

* [Résoudre les problèmes d’authentification et d’autorisation](troubleshoot-authentication-authorization.md)
