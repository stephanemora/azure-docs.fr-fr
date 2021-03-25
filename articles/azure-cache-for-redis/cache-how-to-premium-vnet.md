---
title: Configurer un réseau virtuel – Instance Azure Cache pour Redis de niveau Premium
description: Apprenez à créer et gérer la prise en charge d'un réseau virtuel pour votre instance Azure Cache pour Redis de niveau Premium.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 94bbb9bb683f40d44d6649802b66bda6feeee218
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375270"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Configurer la prise en charge d'un réseau virtuel pour une instance Azure Cache pour Redis Premium

Le déploiement du [Réseau virtuel Azure](https://azure.microsoft.com/services/virtual-network/) fournit une sécurité et une isolation améliorées, ainsi que des sous-réseaux, des stratégies de contrôle d'accès et d'autres fonctionnalités permettant de restreindre davantage l’accès. Lorsqu’une instance Azure Cache pour Redis est configurée avec un réseau virtuel, elle n’est pas adressable publiquement et elle est accessible uniquement à partir de machines virtuelles et d’applications figurant dans le réseau virtuel. Cet article décrit comment configurer la prise en charge d’un réseau virtuel pour une instance Azure Cache pour Redis de niveau Premium.

> [!NOTE]
> Azure Cache pour Redis prend en charge le modèle de déploiement classique et les réseaux virtuels Azure Resource Manager.
> 

## <a name="set-up-virtual-network-support"></a>Configurer la prise en charge du réseau virtuel

La configuration de la prise en charge du réseau virtuel s’effectue dans le volet **Nouveau cache Azure pour Redis** lors de la création du cache.

1. Pour créer un cache de niveau Premium, connectez-vous au [portail Azure](https://portal.azure.com) et sélectionnez **Créer une ressource**. En plus de créer des caches dans le portail Azure, vous pouvez en créer à l’aide de modèles Resource Manager, de PowerShell ou d’Azure CLI. Pour plus d’informations sur la création d’une instance Azure Cache pour Redis, consultez [Créer un cache](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Capture d’écran montrant l’option Créer une ressource":::
   
1. Dans la page **Nouveau**, sélectionnez **Bases de données**. Ensuite, sélectionnez **Azure Cache pour Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Capture d’écran montrant la sélection d’Azure Cache pour Redis":::

1. Dans la page **Nouveau cache Redis**, configurez les paramètres du nouveau cache de niveau Premium.
   
   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nom DNS** | Entrez un nom globalement unique. | Le nom du cache doit être une chaîne de 1 à 63 caractères ne contenant que des chiffres, des lettres ou des traits d’union. Le nom doit commencer et se terminer par un chiffre ou une lettre, et ne doit pas contenir de traits d’union consécutifs. Le *nom d’hôte* de votre instance de cache sera *\<DNS name>.redis.cache.windows.net*. |
   | **Abonnement** | Sélectionnez votre abonnement dans la liste déroulante. | Abonnement sous lequel créer cette nouvelle instance d’Azure Cache pour Redis. |
   | **Groupe de ressources** | Sélectionnez un groupe de ressources dans la liste déroulante ou sélectionnez **Créer nouveau**, puis entrez un nouveau nom de groupe de ressources. | Nom du groupe de ressources dans lequel créer votre cache et d’autres ressources. En plaçant toutes les ressources de votre application dans un seul groupe de ressources, vous pouvez facilement les gérer ou les supprimer ensemble. |
   | **Lieu** | Sélectionnez un emplacement dans la liste déroulante. | Choisissez une [Région](https://azure.microsoft.com/regions/) proche d’autres services qui utiliseront votre cache. |
   | **Type de cache** |Sélectionnez un cache de niveau Premium dans la liste déroulante pour configurer les fonctionnalités de niveau Premium. Pour plus d’informations, consultez [Tarification du Cache Azure pour Redis](https://azure.microsoft.com/pricing/details/cache/). |  Le niveau tarifaire détermine la taille, les performances et les fonctionnalités disponibles pour le cache. Pour plus d’informations, consultez [Présentation d’Azure Cache pour Redis](cache-overview.md). |

1. Sélectionnez l’onglet **Réseau** ou sélectionnez le bouton **Réseau** au bas de la page.

1. Sous l’onglet **Réseau**, sélectionnez **Réseaux virtuels** comme méthode de connexion. Pour utiliser un nouveau réseau virtuel, vous devez tout d’abord le créer en suivant les étapes indiquées dans [Créer un réseau virtuel à l’aide du portail Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network) ou [Créer un réseau virtuel (classique) à l’aide du portail Azure](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Revenez ensuite au volet **Nouveau cache Azure pour Redis** pour créer et configurer votre cache de niveau Premium.

   > [!IMPORTANT]
   > Lorsque vous déployez Azure Cache pour Redis dans un réseau virtuel Resource Manager, le cache doit se trouver dans un sous-réseau dédié ne contenant pas de ressources autres que des instances Azure Cache pour Redis. Si vous tentez de déployer une instance Azure Cache pour Redis dans un sous-réseau de réseau virtuel Resource Manager contenant d’autres ressources, le déploiement échoue.
   > 
   > 

   | Paramètre      | Valeur suggérée  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Réseau virtuel** | Sélectionnez votre réseau virtuel dans la liste déroulante. | Sélectionnez un réseau virtuel figurant dans les mêmes abonnement et emplacement que votre cache. |
   | **Sous-réseau** | Sélectionnez votre sous-réseau dans la liste déroulante. | La plage d’adresses du sous-réseau doit utiliser la notation CIDR (par exemple, 192.168.1.0/24). Elle doit être contenue dans l’espace d’adressage du réseau virtuel. |
   | **Adresse IP statique** | (Facultatif) Entrez une adresse IP statique. | Si vous ne spécifiez pas d’adresse IP statique, une adresse IP est choisie automatiquement. |

   > [!IMPORTANT]
   > Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   > 
   > Outre les adresses IP utilisées par l’infrastructure de réseau virtuel Azure, chaque instance Azure Cache pour Redis dans le sous-réseau utilise deux adresses IP par partition et une adresse IP supplémentaire pour l’équilibreur de charge. Un cache non-cluster est considéré comme ayant une seule partition.
   > 

1. Sélectionnez l’onglet **Suivant : Avancé** ou sélectionnez le bouton **Suivant : Avancé** en bas de la page.

1. Sous l’onglet **Avancé** d’une instance de cache de niveau Premium, configurez les paramètres pour le port non-TLS, le clustering et la persistance des données.

1. Sélectionnez l’onglet **Suivant : Avancé** ou sélectionnez le bouton **Suivant : Étiquettes** au bas de la page.

1. Si vous le voulez, sous l’onglet **Étiquettes**, entrez le nom et la valeur si vous souhaitez catégoriser la ressource.

1. Sélectionnez **Revoir + créer**. Vous êtes redirigé vers l’onglet **Vérifier + créer** où Azure valide votre configuration.

1. Une fois que le message vert **Validation réussie** s’affiche, sélectionnez **Créer**.

La création du cache prend un certain temps. Vous pouvez surveiller la progression dans la page **Vue d’ensemble** du Azure Cache pour Redis. Lorsque **État** indique **En cours d’exécution**, le cache est prêt pour utilisation. Une fois le cache créé, vous pouvez afficher la configuration pour le réseau virtuel en sélectionnant **Réseau virtuel** dans le menu **Ressource**.

![Réseau virtuel][redis-cache-vnet-info]

Pour vous connecter à votre instance Azure Cache pour Redis lorsque vous utilisez un réseau virtuel, spécifiez le nom d’hôte de votre cache dans la chaîne de connexion, comme indiqué dans l’exemple suivant :

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Questions fréquentes (FAQ) sur le réseau virtuel Azure Cache pour Redis

La liste suivante présente différentes réponses aux questions les plus fréquemment posées sur la mise à l’échelle du cache Azure pour Redis.

* Quels sont les problèmes de configuration les plus courants avec Azure Cache pour Redis et des réseaux virtuels ?
* [Comment puis-je vérifier que mon cache fonctionne dans un réseau virtuel ?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Quand j’essaie de me connecter à mon instance Azure Cache pour Redis dans un réseau virtuel, pourquoi est-ce que je reçois un message d’erreur indiquant que le certificat distant n’est pas valide ?
* [Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* Pourquoi la création d’une instance Azure Cache pour Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?
* [Quelles sont les exigences d’espace d’adressage du sous-réseau ?](#what-are-the-subnet-address-space-requirements)
* [Toutes les fonctionnalités de cache fonctionnent-elles quand un cache est hébergé dans un réseau virtuel ?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>Quels sont les problèmes de configuration les plus courants avec Azure Cache pour Redis et des réseaux virtuels ?

Quand Azure Cache pour Redis est hébergé dans un réseau virtuel, les ports répertoriés dans les tableaux suivants sont utilisés.

>[!IMPORTANT]
>Si les ports répertoriés dans les tableaux suivants sont bloqués, le cache risque de ne pas fonctionner correctement. Le blocage d’un ou plusieurs de ces ports constitue le problème de configuration le plus courant lorsque vous utilisez Azure Cache pour Redis dans un réseau virtuel.
> 

- [Configuration requise de port sortant](#outbound-port-requirements)
- [Configuration requise de port entrant](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Configuration requise de port sortant

Il existe neuf configurations requises de port sortant. Les demandes sortantes dans ces plages sont soit sortantes vers d’autres services nécessaires au cache pour fonctionner, soit internes au sous-réseau Redis pour la communication entre les nœuds. Pour la géoréplication, il existe d’autres règles de trafic sortant pour la communication entre les sous-réseaux du cache principal et de réplica.

| Ports | Sens | Protocole de transfert | Objectif | Adresse IP locale | Adresse IP distante |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Règle de trafic sortant |TCP |Dépendances Redis sur Azure Storage/l’infrastructure à clé publique (Internet) | (sous-réseau Redis) |* <sup>4</sup> |
| 443 | Règle de trafic sortant | TCP | Dépendance Redis à Azure Key Vault et à Azure Monitor | (sous-réseau Redis) | AzureKeyVault, AzureMonitor<sup>1</sup> |
| 53 |Règle de trafic sortant |TCP/UDP |Dépendances Redis sur DNS (Internet/réseau virtuel) | (sous-réseau Redis) | 168.63.129.16 et 169.254.169.254 <sup>2</sup> et n’importe quel serveur DNS personnalisé pour le sous-réseau <sup>3</sup> |
| 8443 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) | (sous-réseau Redis) |
| 10221-10231 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) | (sous-réseau Redis) |
| 20226 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 13000-13999 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 15000-15999 |Règle de trafic sortant |TCP |Communications internes pour Redis et géoréplication | (sous-réseau Redis) |(Sous-réseau Redis) (Sous-réseau d’homologues géo-réplica) |
| 6379-6380 |Règle de trafic sortant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |

<sup>1</sup> Vous pouvez utiliser les étiquettes de service AzureKeyVault et AzureMonitor avec des groupes de sécurité réseau Resource Manager.

<sup>2</sup> Ces adresses IP appartenant à Microsoft sont utilisées pour adresser la machine virtuelle hôte qui sert Azure DNS.

<sup>3</sup> Ces informations ne sont pas nécessaires pour les sous-réseaux sans serveur DNS personnalisé ou pour des caches Redis plus récents qui ignorent le service DNS personnalisé.

<sup>4</sup> Pour plus d’informations, consultez [Conditions supplémentaires de connexion aux réseaux virtuels](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Exigences relatives aux ports homologues de géoréplication

Si vous utilisez la géoréplication entre caches dans des réseaux virtuels Azure, débloquez les ports 15000-15999 pour l’ensemble du sous-réseau dans les directions entrante *et* sortante vers les deux caches. Cette configuration permet à tous les composants de réplica du sous-réseau de communiquer directement entre eux, même en cas de basculement géographique.

#### <a name="inbound-port-requirements"></a>Configuration requise des ports entrants

Il existe huit configurations requises de port entrant. Les requêtes entrantes dans ces plages proviennent d’autres services hébergés dans le même réseau virtuel ou des communications sur sous-réseau Redis interne.

| Ports | Sens | Protocole de transfert | Objectif | Adresse IP locale | Adresse IP distante |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Trafic entrant |TCP |Communication client avec Redis, équilibrage de charge Azure | (sous-réseau Redis) | (sous-réseau Redis), (sous-réseau client), AzureLoadBalancer <sup>1</sup> |
| 8443 |Trafic entrant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |
| 8500 |Trafic entrant |TCP/UDP |Équilibrage de charge Azure | (sous-réseau Redis) | AzureLoadBalancer |
| 10221-10231 |Trafic entrant |TCP |Communication client avec les clusters Redis, communications internes pour Redis | (sous-réseau Redis) |(Sous-réseau Redis), AzureLoadBalancer, (Sous-réseau client) |
| 13000-13999 |Trafic entrant |TCP |Communication client avec les clusters Redis, équilibrage de charge Azure | (sous-réseau Redis) | (sous-réseau Redis), (sous-réseau client), AzureLoadBalancer |
| 15000-15999 |Trafic entrant |TCP |Communication client avec les clusters Redis, équilibrage de charge Azure et géoréplication | (sous-réseau Redis) | (sous-réseau Redis), (sous-réseau client), AzureLoadBalancer, (sous-réseau homologue avec géoréplication) |
| 16001 |Trafic entrant |TCP/UDP |Équilibrage de charge Azure | (sous-réseau Redis) | AzureLoadBalancer |
| 20226 |Trafic entrant |TCP |Communications internes pour Redis | (sous-réseau Redis) |(sous-réseau Redis) |

<sup>1</sup> Vous pouvez utiliser la balise de service AzureLoadBalancer pour Resource Manager ou AZURE_LOADBALANCER pour le modèle de déploiement classique dans le but de créer les règles de groupe de sécurité réseau.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Conditions supplémentaires de connexion aux réseaux virtuels

Il existe des conditions de connexion réseau pour Azure Cache pour Redis qui peuvent ne pas être initialement satisfaites dans un réseau virtuel. Azure Cache pour Redis nécessite tous les éléments suivants pour fonctionner correctement lorsqu’il est utilisé dans un réseau virtuel :

* Connectivité réseau sortante à des points de terminaison Azure Storage dans le monde entier. Cela inclut les points de terminaison situés dans la même région que l’instance Azure Cache pour Redis, ainsi que les points de terminaison de stockage situés dans d’*autres* régions Azure. Les points de terminaison du Stockage Azure se résolvent dans les domaines DNS suivants : *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* et *file.core.windows.net*.
* Connectivité réseau sortante à *ocsp.digicert.com*, *crl4.digicert.com*, *ocsp.msocsp.com*, *mscrl.microsoft.com*, *crl3.digicert.com*, *cacerts.digicert.com*, *oneocsp.microsoft.com* et *crl.microsoft.com*. Cette connectivité est nécessaire pour prendre en charge la fonctionnalité TSL/SSL.
* La configuration DNS pour le réseau virtuel doit être capable de résoudre tous les points de terminaison et les domaines mentionnés dans les points précédents. La configuration DNS requise peut être satisfaite en garantissant qu'une infrastructure DNS valide est configurée et gérée pour le réseau virtuel.
* Connectivité réseau sortante aux points de terminaison Azure Monitor suivants qui se résolvent dans les domaines DNS suivants : *shoebox2-black.shoebox2.metrics.nsatc.net*, *north-prod2.prod2.metrics.nsatc.net*, *azglobal-black.azglobal.metrics.nsatc.net*, *shoebox2-red.shoebox2.metrics.nsatc.net*, *east-prod2.prod2.metrics.nsatc.net*, *azglobal-red.azglobal.metrics.nsatc.net*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-red.prod.microsoftmetrics.com* et *shoebox3-black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>Comment puis-je vérifier que mon cache fonctionne dans un réseau virtuel ?

>[!IMPORTANT]
>Quand vous vous connectez à une instance Azure Cache pour Redis hébergée dans un réseau virtuel, vos clients de cache doivent figurer dans le même réseau virtuel ou dans un réseau virtuel pour lequel le peering de réseaux virtuels est activé dans la même région Azure. Le peering global de réseaux virtuels n’est pas pris en charge actuellement. Cette condition s’applique aux applications de test et aux outils de requête de diagnostic. Quel que soit l’endroit où l’application cliente est hébergée, les groupes de sécurité réseau ou d’autres couches réseau doivent être configurés de façon à ce que le trafic réseau du client soit autorisé à atteindre l’instance Azure Cache pour Redis.
>

Une fois les conditions de port configurées comme décrit dans la section précédente, vous pouvez vérifier que votre cache fonctionne en procédant comme suit :

- [Redémarrez](cache-administration.md#reboot) tous les nœuds de cache. Si toutes les dépendances de cache requises ne sont pas accessibles, telles qu’elles sont décrites dans [Configuration requise des ports entrants](cache-how-to-premium-vnet.md#inbound-port-requirements) et [Configuration requise des ports sortants](cache-how-to-premium-vnet.md#outbound-port-requirements), le cache ne pourra pas redémarrer correctement.
- Une fois que les nœuds de cache ont redémarré, comme indiqué par l’état du cache dans le portail Azure, vous pouvez effectuer les tests suivants :
  - Vous pouvez effectuer un test ping du point de terminaison du cache en utilisant le port 6380 à partir d’une machine figurant dans le même réseau virtuel que le cache, à l’aide de [tcping](https://www.elifulkerson.com/projects/tcping.php). Par exemple :
    
    `tcping.exe contosocache.redis.cache.windows.net 6380`
    
    Si l’outil `tcping` signale que le port est ouvert, le cache est disponible pour la connexion à partir de clients dans le réseau virtuel.

  - Un autre test consiste à créer un client de cache de test (qui peut être une simple application console utilisant StackExchange.Redis) qui se connecte au cache et ajoute et récupère des éléments de celui-ci. Installez l’exemple d’application cliente sur une machine virtuelle figurant dans le même réseau virtuel que le cache. Ensuite, exécutez-la pour vérifier la connectivité au cache.


### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Quand j’essaie de me connecter à mon instance Azure Cache pour Redis dans un réseau virtuel, pourquoi est-ce que je reçois un message d’erreur indiquant que le certificat distant n’est pas valide ?

Quand vous tentez de vous connecter à une instance Azure Cache pour Redis dans un réseau virtuel, vous voyez une erreur de validation de certificat comme celle-ci :

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

La cause tient peut-être au fait que vous vous connectez à l’hôte par le biais de l’adresse IP. Nous vous recommandons d’utiliser le nom d’hôte. En d’autres termes, utilisez la chaîne suivante :

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Évitez d’utiliser l’adresse IP similaire à la chaîne de connexion suivante :

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Si vous ne parvenez pas à résoudre le nom DNS, certaines bibliothèques clientes incluent des options de configuration comme `sslHost`, qui est fournie par le client StackExchange.Redis. Cette option vous permet de remplacer le nom d’hôte utilisé pour la validation du certificat. Par exemple :

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>Puis-je utiliser des réseaux virtuels avec un cache De base ou Standard ?

Les réseaux virtuels ne peuvent être utilisés qu’avec des caches de niveau Premium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>Pourquoi la création d’une instance Azure Cache pour Redis échoue-t-elle dans certains sous-réseaux mais pas d’autres ?

Si vous déployez une instance Azure Cache pour Redis dans un réseau virtuel, le cache doit se trouver dans un sous-réseau dédié qui ne contient aucun autre type de ressource. Si vous tentez de déployer une instance Azure Cache pour Redis dans un sous-réseau de réseau virtuel Resource Manager contenant d’autres ressources, telles que des instances Azure Application Gateway ou des règles NAT de trafic sortant, le déploiement échoue en général. Vous devez supprimer les autres types de ressources existantes avant de pouvoir créer une nouvelle instance Azure Cache pour Redis.

Vous devez également disposer de suffisamment d’adresses IP disponibles dans le sous-réseau.

### <a name="what-are-the-subnet-address-space-requirements"></a>Quelles sont les exigences d’espace d’adressage du sous-réseau ?

Azure réserve dans chaque sous-réseau des adresses IP qui ne peuvent pas être utilisées. Les première et dernière adresse IP des sous-réseaux sont réservées à la conformité du protocole, et 3 adresses supplémentaires sont utilisées pour les services Azure. Pour plus d’informations, consultez [Existe-t-il des restrictions sur l’utilisation des adresses IP au sein de ces sous-réseaux ?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Outre les adresses IP utilisées par l’infrastructure de réseau virtuel Azure, chaque instance Azure Cache pour Redis figurant dans le sous-réseau utilise deux adresses IP par partition de cluster, ainsi que des adresses IP supplémentaires pour les réplicas supplémentaires, le cas échéant. Une adresse IP supplémentaire est utilisée pour l’équilibreur de charge. Un cache non-cluster est considéré comme ayant une seule partition.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>Toutes les fonctionnalités de cache fonctionnent-elles quand un cache est hébergé dans un réseau virtuel ?

Quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Par conséquent, les fonctionnalités de gestion de cache suivantes ne fonctionnent pas pour l’instant :

* **Console Redis** : comme la console Redis s’exécute dans votre navigateur local, qui est généralement situé sur un ordinateur de développement qui n’est pas connecté au réseau virtuel, elle ne peut pas se connecter à votre cache.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Utiliser ExpressRoute avec le Cache Azure pour Redis

Les clients peuvent connecter un circuit [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) à leur infrastructure de réseau virtuel. De cette façon, ils étendent leur réseau local à Azure.

Par défaut, un circuit ExpressRoute nouvellement créé n’effectue pas de tunneling forcé (publication d’une route par défaut, 0.0.0.0/0) dans un réseau virtuel. Par conséquent, la connectivité Internet sortante est autorisée directement à partir du réseau virtuel. Les applications clientes peuvent se connecter à d’autres points de terminaison Azure, ce qui inclut une instance Azure Cache pour Redis.

Une configuration client courante consiste à utiliser un tunneling forcé (publication d’une route par défaut), ce qui force le trafic Internet sortant à circuler localement à la place. Ce flux de trafic interrompt la connectivité avec Azure Cache pour Redis si le trafic sortant est ensuite bloqué localement, de sorte que l’instance Azure Cache pour Redis ne puisse pas communiquer avec ses dépendances.

La solution consiste à définir une ou plusieurs routes définies par l’utilisateur (UDR) sur le sous-réseau qui contient l’instance Azure Cache pour Redis. Un itinéraire défini par l'utilisateur définit des itinéraires spécifiques au sous-réseau qui seront respectés au lieu de l'itinéraire par défaut.

Si possible, utilisez la configuration suivante :

* La configuration ExpressRoute publie 0.0.0.0/0 et, par défaut, utilise le tunneling forcé sur tout le trafic sortant local.
* La route définie par l’utilisateur appliquée au sous-réseau qui contient l’instance Azure Cache pour Redis définit 0.0.0.0/0 avec une route de travail pour le trafic TCP/IP vers le réseau Internet public. Par exemple, elle définit le type de tronçon suivant sur *Internet*.

L’effet combiné de ces étapes est que la route définie par l’utilisateur au niveau du sous-réseau a la priorité sur le tunneling forcé ExpressRoute, garantissant ainsi un accès Internet sortant à partir de l’instance Azure Cache pour Redis.

La connexion à une instance Azure Cache pour Redis à partir d’une application locale à l’aide d’ExpressRoute n’est pas un scénario d’utilisation standard pour des raisons de performances. Pour des performances optimales, les clients Azure Cache pour Redis doivent se trouver dans la même région que l’instance Azure Cache pour Redis.

>[!IMPORTANT]
>Les itinéraires définis dans un UDR *doivent* être suffisamment spécifiques pour avoir la priorité sur les itinéraires annoncés par la configuration ExpressRoute. L’exemple suivant utilise la plage d’adresses 0.0.0.0/0 étendue, qui peut de ce fait être remplacée accidentellement par des publications de routes utilisant des plages d’adresses plus spécifiques.

>[!WARNING]
>Azure Cache pour Redis n’est pas pris en charge avec les configurations ExpressRoute qui *publient incorrectement de façon croisée des routes à partir du chemin de peering public vers le chemin de peering privé*. Les configurations ExpressRoute ayant un peering public configuré reçoivent des annonces de routage en provenance de Microsoft pour un grand ensemble de plages d’adresses IP Microsoft Azure. Si ces plages d’adresses sont incorrectement publiées de façon croisée sur le chemin d’accès de peering privé, il en résulte que tous les paquets réseau sortants du sous-réseau de l’instance du Cache Azure pour Redis sont incorrectement acheminés de force vers l’infrastructure réseau locale d’un client. Ce flux réseau interrompt le Cache Azure pour Redis. La solution à ce problème consiste à arrêter les itinéraires croisés depuis le chemin d'accès de peering public vers le chemin d'accès de peering privé.

Des informations contextuelles sur les routes définies par l’utilisateur sont disponibles dans [Routage du trafic de réseau virtuel](../virtual-network/virtual-networks-udr-overview.md).

Pour plus d’informations sur ExpressRoute, voir [Aperçu technique d’ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur les fonctionnalités d’Azure Cache pour Redis.

* [Niveaux de service Premium Azure Cache pour Redis](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
