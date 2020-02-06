---
title: Présentation de la configuration d’Azure Application Gateway
description: Cet article explique comment configurer les composants d’Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/15/2019
ms.author: absha
ms.openlocfilehash: 146dbdbf2f4e107e81515ce83188fa48c52aef36
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714857"
---
# <a name="application-gateway-configuration-overview"></a>Présentation de la configuration d’Application Gateway

Azure Application Gateway comprend plusieurs composants que vous pouvez configurer de différentes manières pour différents scénarios. Cet article vous montre comment configurer chacun d’eux.

![Organigramme des composants d’Application Gateway](./media/configuration-overview/configuration-overview1.png)

Cette image illustre une application dotée de trois écouteurs. Les deux premiers écouteurs sont multisites pour `http://acme.com/*` et `http://fabrikam.com/*`, respectivement. Ils écoutent tous les deux le port 80. Le troisième est un écouteur de base doté d’un arrêt SSL (Secure Sockets Layer) de bout en bout.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables requises

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Réseau virtuel Azure et sous-réseau dédié

Une passerelle d’application est un déploiement dédié dans votre réseau virtuel. Au sein de votre réseau virtuel, un sous-réseau dédié est nécessaire pour la passerelle d’application. Vous pouvez avoir plusieurs instances d’un déploiement de passerelle d’application donné dans un sous-réseau. Vous pouvez aussi déployer d’autres passerelles d’application dans le sous-réseau. Mais vous ne pouvez pas déployer une autre ressource dans le sous-réseau de la passerelle d’application.

> [!NOTE]
> Vous ne pouvez pas mélanger Azure Application Gateway Standard_v2 et Standard sur le même sous-réseau.

#### <a name="size-of-the-subnet"></a>Taille du sous-réseau

Application Gateway utilise une adresse IP privée par instance, ainsi qu’une autre adresse IP privée si une adresse IP front-end privée est configurée.

Azure réserve également 5 adresses IP dans chaque sous-réseau pour un usage interne : les 4 premières et la dernière. Prenons l’exemple de 15 instances de passerelle d’application sans aucune adresse IP front-end privée. Vous avez besoin d’au moins 20 adresses IP pour ce sous-réseau : 5 pour un usage interne et 15 pour les instances de passerelle d’application. Donc, vous avez besoin d’une taille de sous-réseau /27 ou plus.

Prenons l’exemple d’un sous-réseau disposant de 27 instances de passerelle d’application et d’une adresse front-end IP privée. Dans ce cas, vous avez besoin de 33 adresses IP : 27 pour les instances de passerelle d’application, 1 pour l’adresse front-end privée et 5 pour un usage interne. Donc, vous avez besoin d’une taille de sous-réseau /26 ou plus.

Nous vous recommandons d’utiliser une taille de sous-réseau d’au moins /28. Cette taille vous donne 11 adresses IP utilisables. Si la charge de votre application nécessite plus de 10 instances d’Application Gateway, envisagez une taille de sous-réseau de /27 ou /26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Groupes de sécurité réseau sur le sous-réseau Application Gateway

Les Groupes de sécurité réseau (NSG) sont pris en charge sur Application Gateway. Mais quelques restrictions s’appliquent :

- Vous devez autoriser le trafic Internet entrant sur les ports TCP 65503-65534 pour la référence (SKU) Application Gateway v1, et sur les ports TCP 65200-65535 pour la référence (SKU) v2 avec le sous-réseau de destination en tant que **Any** et la source en tant que balise de service **GatewayManager**. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par des certificats Azure. Les entités externes, y compris les clients de ces passerelles, ne peuvent pas communiquer avec ces points de terminaison.

- La connectivité Internet sortante ne peut pas être bloquée. Les règles de trafic sortant par défaut dans le groupe de sécurité réseau permettent une connectivité Internet. Nous vous recommandons :

  - De ne pas supprimer les règles de trafic sortant par défaut.
  - De ne pas créer d’autres règles de trafic sortant qui refusent toute connectivité sortante.

- Le trafic en provenance de la balise **AzureLoadBalancer** doit être autorisé.

#### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Autoriser l’accès d’Application Gateway à quelques adresses IP sources

Pour ce scénario, utilisez des groupes de sécurité réseau sur le sous-réseau Application Gateway. Placez les restrictions suivantes sur le sous-réseau dans cet ordre de priorité :

1. Autorisez le trafic entrant à partir d’une adresse IP source ou d’une plage d’adresses IP avec la destination comme plage d’adresses de sous-réseau Application Gateway et le port de destination comme votre port d’accès entrant, par exemple, le port 80 pour l’accès HTTP.
2. Autorisez les demandes entrantes à partir de la source comme balise de service **GatewayManager** et la destination **Any** et les ports de destination 65503-65534 pour la référence SKU Application Gateway v1, et les ports 65200-65535 pour la référence SKU v2 pour les [communications de l’état d’intégrité du back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par des certificats Azure. Sans les certificats appropriés en place, les entités externes ne peuvent pas lancer des modifications sur ces points de terminaison.
3. Autorisez les sondes Azure Load Balancer entrantes (balise *AzureLoadBalancer*) et le trafic de réseau virtuel entrant (balise *VirtualNetwork*) sur le [Groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquez tout autre trafic entrant avec une règle Tout refuser.
5. Autoriser le trafic sortant vers internet pour toutes les destinations.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Routes définies par l’utilisateur prises en charge sur le sous-réseau Application Gateway

Pour la référence SKU v1, les routes définies par l’utilisateur sont prises en charge sur le sous-réseau Application Gateway, tant qu’elles n’altèrent pas la communication de demande/réponse de bout en bout. Par exemple, vous pouvez configurer une route définie par l’utilisateur dans le sous-réseau Application Gateway pour pointer vers une appliance de pare-feu afin d’inspecter un paquet. Mais vous devez vérifier que le paquet peut atteindre sa destination prévue après l’inspection. S’il n’y parvient pas, cela peut entraîner un comportement incorrect de la sonde d’intégrité ou du routage du trafic. Sont incluses les routes apprises ou 0.0.0.0/0 par défaut propagées par Azure ExpressRoute ou des passerelles VPN dans le réseau virtuel.

Pour la référence SKU v2, les routages définis par l’utilisateur (UDR) ne sont pas pris en charge sur le sous-réseau Application Gateway. Pour plus d’informations, consultez [Référence SKU v2 d’Azure Application Gateway](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> Les routages définis par l’utilisateur (UDR) ne sont pas pris en charge pour la référence SKU v2 à l’heure actuelle.

> [!NOTE]
> L’utilisation de routes définies par l’utilisateur sur le sous-réseau Application Gateway est susceptible d’entraîner l’indication de l’état d’intégrité « Inconnu » dans l’[affichage de l’intégrité du back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health). Elle peut également entraîner l’échec de la génération des journaux et métriques Application Gateway. Nous vous recommandons de ne pas utiliser de routes définies par l’utilisateur sur le sous-réseau Application Gateway afin de pouvoir voir l’état d’intégrité, les journaux et les métriques du back-end.

## <a name="front-end-ip"></a>Adresse IP front-end

Vous pouvez configurer la passerelle d’application pour qu’elle ait une adresse IP publique, une adresse IP privée ou les deux. Une adresse IP publique est nécessaire quand vous hébergez un back-end auquel les clients doivent accéder par Internet par le biais d’une adresse IP virtuelle Internet. 

Une adresse IP publique n’est pas nécessaire pour un point de terminaison interne non exposé à Internet. Ce dernier est appelé point de terminaison d’*équilibreur de charge interne* ou adresse IP front-end privée. L’équilibreur de charge interne de la passerelle d’application s’avère utile pour les applications métier internes non exposées à Internet. Il s’avère également utile pour les services et niveaux inclus dans une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais qui a besoin d’une distribution de charge par tourniquet, de l’adhérence de session ou de la terminaison SSL.

Une seule adresse IP publique ou une seule adresse IP privée est prise en charge. Vous choisissez l’adresse IP front-end quand vous créez la passerelle d’application.

- Concernant l’adresse IP publique, vous pouvez en créer une ou en utiliser une existante au même emplacement que la passerelle d’application. Pour plus d’informations, consultez [Adresse IP statique ou dynamique](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- Concernant l’adresse IP privée, vous pouvez spécifier une adresse IP privée du sous-réseau dans lequel la passerelle d’application est créée. Si vous n’en spécifiez aucune, une adresse IP arbitraire est automatiquement sélectionnée dans le sous-réseau. Le type d’adresse IP que vous sélectionnez (statique ou dynamique) n’est pas modifiable par la suite. Pour plus d’informations, consultez [Créer une passerelle d’application avec un équilibreur de charge interne](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Une adresse IP front-end est associée à un *écouteur*, qui vérifie les demandes entrantes sur l’adresse IP front-end.

## <a name="listeners"></a>Écouteurs

Un écouteur est une entité logique qui vérifie les demandes de connexion entrante en utilisant le port, le protocole, l’hôte et l’adresse IP. Quand vous configurez l’écouteur, vous devez entrer des valeurs qui correspondent aux valeurs indiquées dans la demande entrante sur la passerelle.

Quand vous créez une passerelle d’application à l’aide du portail Azure, vous créez également un écouteur par défaut en choisissant le protocole et le port pour l’écouteur. Vous pouvez choisir d’activer ou non la prise en charge du protocole HTTP2 sur l’écouteur. Une fois que vous avez créé la passerelle d’application, vous pouvez modifier les paramètres de cet écouteur par défaut (*appGatewayHttpListener*) ou créer des écouteurs.

### <a name="listener-type"></a>Type d’écouteur

Quand vous créez un écouteur, vous choisissez entre le type [*de base* et le type *multisite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Si vous souhaitez que toutes vos demandes (pour tous les domaines) soient acceptées et transmises aux pools back-end, choisissez le type « de base ». Découvrez [comment créer une passerelle d’application avec un écouteur de base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Si vous souhaitez transférer les demandes vers des pools back-end différents en fonction de l’en-tête d’*hôte* ou du nom d’hôte, choisissez le type d’écouteur « multisite » ; dans ce cas, vous devez également spécifier un nom d’hôte qui correspond à la demande entrante. En effet, Application Gateway s’appuie sur des en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites web sur la même adresse IP publique et le même port.

#### <a name="order-of-processing-listeners"></a>Ordre de traitement des

Pour la référence SKU v1, les demandes sont mises en correspondance en fonction de l’ordre des règles et du type d’écouteur. Si une règle avec un écouteur de base occupe la première place, elle est traitée en premier et accepte toutes les demandes pour ce port et cette combinaison d’adresses IP. Pour éviter cela, configurez d’abord les règles avec des écouteurs multisites et poussez la règle avec l’écouteur de base vers la dernière position de liste.

Pour la référence SKU v2, les écouteurs multisites sont traités avant les écouteurs de base.

### <a name="front-end-ip"></a>Adresse IP front-end

Choisissez l’adresse IP front-end que vous prévoyez d’associer à cet écouteur. L’écouteur écoute les demandes entrantes sur cette adresse IP.

### <a name="front-end-port"></a>Port front-end

Choisissez le port front-end. Sélectionnez un port existant ou créez-en un. Choisissez une valeur dans la [plage de ports autorisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Vous pouvez utiliser non seulement les ports connus, comme les ports 80 et 443, mais aussi tout port personnalisé autorisé qui convient. Un port peut être utilisé pour des écouteurs publics ou privés.

### <a name="protocol"></a>Protocol

Choisissez HTTP ou HTTPS :

- Si vous choisissez HTTP, le trafic entre le client et la passerelle d’application est non chiffré.

- Sélectionnez HTTPS si vous voulez [un arrêt SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) ou un [chiffrement SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Le trafic entre le client et la passerelle d’application est chiffré. Et la connexion SSL s’arrête à la passerelle d’application. Si vous voulez un chiffrement SSL de bout en bout, vous devez choisir le protocole HTTPS et configurer le paramètre **HTTP du back-end**. Ce dernier permet de veiller à ce que le trafic soit rechiffré quand il passe de la passerelle d’application au back-end.

Pour configurer l’arrêt SSL et le chiffrement SSL de bout en bout, vous devez ajouter un certificat à l’écouteur pour permettre à la passerelle d’application de dériver une clé symétrique. Il s’agit d’une exigence de la spécification du protocole SSL. La clé symétrique sert à chiffrer et déchiffrer le trafic envoyé à la passerelle. Le certificat de passerelle doit être au format Personal Information Exchange (PFX). Ce format vous permet d’exporter la clé privée que la passerelle utilise pour chiffrer et déchiffrer le trafic.

#### <a name="supported-certificates"></a>Certificats pris en charge

Consultez [Certificats pris en charge pour un arrêt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Prise en charge d’autres protocoles

#### <a name="http2-support"></a>Prise en charge de HTTP2

La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant aux écouteurs de passerelle d’application uniquement. La communication avec les pools de serveurs back-end s’effectue par le biais du portocole HTTP/1.1. Par défaut, la prise en charge du protocole HTTP/2 est désactivée. L’extrait de code Azure PowerShell suivant montre comment activer cette prise en charge :

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Prise en charge de WebSocket

La prise en charge de WebSocket est activée par défaut. Il n’existe aucun paramètre configurable par l’utilisateur pour l’activer ou la désactiver. Vous pouvez utiliser des WebSockets avec des écouteurs HTTP et HTTPS.

### <a name="custom-error-pages"></a>Pages d’erreur personnalisées

Vous pouvez définir une erreur personnalisée au niveau global ou au niveau de l’écouteur. Mais la création de pages d’erreur personnalisées au niveau global à partir du portail Azure n’est actuellement pas prise en charge. Vous pouvez configurer une page d’erreur personnalisée pour une erreur de pare-feu d’application web 403 ou une page de maintenance 502 au niveau de l’écouteur. Vous devez également spécifier une URL d’objet blob accessible publiquement pour le code d’état d’erreur donné. Pour plus d’informations, consultez [Créer des pages d’erreur personnalisées Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Codes d’erreur Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pour configurer une page d’erreur personnalisée globale, consultez [Configuration Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Stratégie SSL

Vous pouvez centraliser la gestion des certificats SSL et réduire la surcharge de chiffrement-déchiffrement d’une batterie de serveurs back-end. Cette gestion SSL centralisée permet également de spécifier une stratégie SSL centrale adaptée à vos besoins de sécurité. Vous pouvez choisir une stratégie SSL *par défaut*, *prédéfinie* ou *personnalisée*.

Vous configurez la stratégie SSL pour contrôler les versions du protocole SSL. Vous pouvez configurer une passerelle d’application afin qu’elle utilise une version de protocole minimale pour les négociations TLS à partir de TLS 1.0, TLS 1.1 et TLS 1.2. Par défaut, SSL 2.0 et 3.0 sont désactivés et ne sont pas configurables. Pour plus d’informations, consultez [Vue d’ensemble de la stratégie SSL Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Après avoir créé un écouteur, vous l’associez à une règle de routage des demandes. Cette règle détermine la manière dont les demandes reçues sur l’écouteur sont routées vers le back-end.

## <a name="request-routing-rules"></a>Règles de routage des requêtes

Quand vous créez une passerelle d’application à l’aide du portail Azure, vous créez une règle par défaut (*rule1*). Cette règle lie l’écouteur par défaut (*appGatewayHttpListener*) au pool de back-ends par défaut (*appGatewayBackendPool*) et aux paramètres HTTP du back-end par défaut ( *appGatewayBackendHttpSettings*). Après avoir créé la passerelle, vous pouvez modifier les paramètres de la règle par défaut ou créer des règles.

### <a name="rule-type"></a>Type de règle

Quand vous créez une règle, vous choisissez entre le type [*de base* et le type *basé sur un chemin*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Choisissez le type de base pour transférer toutes les demandes sur l’écouteur associé (par exemple, *blog<i></i>.contoso.com/\*)* à un seul pool de back-ends.
- Choisissez le type basé sur un chemin pour router les demandes provenant de chemins d’URL spécifiques vers des pools de back-ends spécifiques. Le modèle de chemin s’applique uniquement au chemin de l’URL, pas à ses paramètres de demande.

#### <a name="order-of-processing-rules"></a>Ordre de traitement des règles

Pour la référence SKU v1, les critères spéciaux des demandes entrantes sont traités dans l’ordre dans lequel les chemins sont listés dans le mappage du chemin d’URL de la règle basée sur un chemin. Si une demande correspond au modèle dans deux chemins ou plus du mappage du chemin, le chemin listé en premier est mappé. Et la demande est transférée au back-end associé à ce chemin.

Pour la référence SKU v2, une correspondance exacte est une priorité plus élevée que l’ordre des chemins dans le mappage du chemin d’URL. Si une demande correspond au modèle dans deux chemins ou plus, elle est transférée au back-end associé au chemin qui correspond exactement à la demande. Si le chemin dans la demande entrante ne correspond pas exactement à un chemin du mappage, les critères spéciaux de la demande sont traités dans l’ordre de la liste du mappage du chemin pour la règle basée sur un chemin.

### <a name="associated-listener"></a>Écouteur associé

Associez un écouteur à la règle de sorte que la *règle de routage des demandes* associée à l’écouteur soit évaluée pour déterminer le pool de back-ends vers lequel router la demande.

### <a name="associated-back-end-pool"></a>Pool de back-ends associé

Associez à la règle le pool de back-ends qui contient les cibles back-end qui servent les demandes que l’écouteur reçoit.

 - Pour une règle de base, un seul pool de back-ends est autorisé. Toutes les demandes sur l’écouteur associé sont transférées à ce pool de back-ends.

 - Pour une règle basée sur un chemin, ajoutez plusieurs pools de back-ends qui correspondent à chaque chemin d’URL. Les demandes correspondant à ce chemin d’URL entré sont transférées au pool de back-ends correspondant. De plus, ajoutez un pool de back-ends par défaut. Les demandes qui ne correspondent à aucun chemin d’URL dans la règle sont transférées à ce pool.

### <a name="associated-back-end-http-setting"></a>Paramètre HTTP du back-end associé

Ajoutez un paramètre HTTP de back-end pour chaque règle. Les demandes sont routées depuis la passerelle d’application vers les cibles back-end à l’aide du numéro de port, du protocole et d’autres informations spécifiées dans ce paramètre.

Pour une règle de base, un seul paramètre HTTP du back-end est autorisé. Toutes les demandes sur l’écouteur associé sont transférées aux cibles back-end correspondantes en utilisant ce paramètre HTTP.

Pour une règle basée sur un chemin, ajoutez plusieurs paramètres HTTP du back-end qui correspondent à chaque chemin d’URL. Les demandes correspondant à ce chemin d’URL dans ce paramètre sont transférées aux cibles back-end correspondantes en utilisant les paramètres HTTP qui correspondent à chaque chemin d’URL. En outre, ajoutez un paramètre HTTP par défaut. Les demandes qui ne correspondent à aucun chemin d’URL dans cette règle sont transférées au pool back-end par défaut en utilisant le paramètre HTTP par défaut.

### <a name="redirection-setting"></a>Paramètre de redirection

Si la redirection est configurée pour une règle de base, toutes les demandes sur l’écouteur associé sont redirigées vers la cible. Il s’agit d’une redirection *globale*. Si la redirection est configurée pour une règle basée sur un chemin, seules les demandes dans une zone de site spécifique sont redirigées. Prenons l’exemple d’une zone de panier d’achat indiquée par */cart/\** . Il s’agit d’une redirection *basée sur un chemin*.

Pour plus d’informations sur les redirections, consultez [Vue d’ensemble de la redirection Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Type de redirection

Choisissez le type de redirection nécessaire : *Permanent (301)* , *Temporaire (307)* , *Trouvé (302)* ou *Voir autre (303)* .

#### <a name="redirection-target"></a>Cible de redirection

Choisissez un autre écouteur ou un site externe comme cible de redirection.

##### <a name="listener"></a>Écouteur

Choisissez l’écouteur comme cible de redirection pour rediriger le trafic depuis un écouteur vers un autre sur la passerelle. Ce paramètre est obligatoire quand vous voulez activer la redirection HTTP vers HTTPS. Il redirige le trafic depuis l’écouteur source qui vérifie les demandes HTTP entrantes vers l’écouteur de destination qui vérifie les demandes HTTPS entrantes. Vous pouvez également choisir d’inclure la chaîne et le chemin de requête de la demande d’origine dans la demande transférée à la cible de redirection.

![Boîte de dialogue Composants d’Application Gateway](./media/configuration-overview/configure-redirection.png)

Pour plus d’informations sur la redirection HTTP vers HTTPS, consultez :
- [Redirection HTTP vers HTTPS à l’aide du portail Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Redirection HTTP vers HTTPS à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Redirection HTTP vers HTTPS à l’aide d’Azure CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Site externe

Choisissez un site externe quand vous voulez rediriger le trafic sur l’écouteur associé à cette règle vers un site externe. Vous pouvez choisir d’inclure la chaîne de requête de la demande d’origine dans la demande transférée à la cible de redirection. Vous ne pouvez pas transférer le chemin au site externe qui était dans la demande d’origine.

Pour plus d’informations sur la redirection, consultez :
- [Rediriger le trafic vers un site externe à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Rediriger le trafic vers un site externe à l’aide de l’interface de ligne de commande](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Réécrire le paramètre d’en-tête HTTP

Ce paramètre ajoute, supprime et met à jour les en-têtes de demande et réponse HTTP pendant le déplacement des paquets de demande et réponse entre le pool client et le pool back-end. Pour plus d'informations, consultez les pages suivantes :

 - [Vue d’ensemble de la réécriture des en-têtes HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Configurer la réécriture des en-têtes HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)

## <a name="http-settings"></a>Paramètres HTTP

La passerelle d’application route le trafic vers les serveurs back-end en utilisant la configuration que vous spécifiez ici. Après avoir créé un paramètre HTTP, vous devez l’associer à une ou plusieurs règles de routage des demandes.

### <a name="cookie-based-affinity"></a>Affinité basée sur les cookies

Cette fonctionnalité s’avère utile quand vous voulez garder une session utilisateur sur le même serveur. Les cookies gérés par la passerelle permettent à la passerelle d’application de diriger le trafic ultérieur d’une session utilisateur vers le même serveur à des fins de traitement. Ceci est important quand l’état de la session est enregistré localement sur le serveur pour une session utilisateur. Si l’application ne peut pas gérer l’affinité basée sur les cookies, vous ne pouvez pas utiliser cette fonctionnalité. Pour l’utiliser, assurez-vous que les clients prennent en charge les cookies.

### <a name="connection-draining"></a>Vidage des connexions

Le vidage des connexions vous permet de supprimer élégamment des membres du pool de back-ends pendant les mises à jour de service planifiées. Vous pouvez appliquer ce paramètre à tous les membres d’un pool de back-ends lors de la création d’une règle. Elle garantit que toutes les instances de désinscription d’un pool de back-ends continuent à gérer les connexions existantes et à traiter les demandes en cours pendant un délai d’expiration configurable, et ne reçoivent aucune nouvelle demande ou connexion. La seule exception concerne les demandes liées à la désinscription des instances en raison d’une affinité de session gérée par la passerelle et qui continueront d’être transmises par proxy aux instances de désinscription. Le vidage des connexions s’applique aux instances back-end qui sont explicitement supprimées du pool de back-ends.

### <a name="protocol"></a>Protocol

Application Gateway prend en charge les protocoles HTTP et HTTPS pour router les demandes vers les serveurs back-end. Si vous choisissez HTTP, le trafic vers les serveurs back-end est non chiffré. Si des communications non chiffrées ne sont pas acceptables, sélectionnez HTTPS.

Ce paramètre combiné avec HTTPS dans l’écouteur prend en charge le chiffrement [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Celui-ci vous permet de transmettre en toute sécurité des données sensibles chiffrées au serveur back-end. Chaque serveur back-end du pool de back-ends pour lequel un chiffrement SSL de bout en bout est activé doit être configuré avec un certificat pour permettre une communication sécurisée.

### <a name="port"></a>Port

Ce paramètre spécifie le port où les serveurs back-end écoutent le trafic provenant de la passerelle d’application. Vous pouvez configurer des ports allant de 1 à 65535.

### <a name="request-timeout"></a>Délai d’expiration de la demande

Ce paramètre correspond au nombre de secondes pendant lesquelles la passerelle d’application attend de recevoir une réponse de la part du serveur back-end.

### <a name="override-back-end-path"></a>Remplacer le chemin back-end

Ce paramètre vous permet de configurer un chemin de transfert personnalisé facultatif à utiliser quand la demande est transférée au back-end. Toute partie du chemin entrant qui correspond au chemin personnalisé dans le champ **Remplacer le chemin back-end** est copiée dans le chemin transféré. Le tableau suivant montre comment agit cette fonctionnalité :

- Quand le paramètre HTTP est attaché à une règle de routage des demandes de base :

  | Demande d’origine  | Remplacer le chemin back-end | Demande transférée au back-end |
  | ----------------- | --------------------- | ---------------------------- |
  | /home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Quand le paramètre HTTP est attaché à une règle de routage des demandes basée sur un chemin :

  | Demande d’origine           | Règle de chemin       | Remplacer le chemin back-end | Demande transférée au back-end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override/            | /override/secondhome/        |
  | /pathrule/                 | /pathrule/      | /override/            | /override/                   |

### <a name="use-for-app-service"></a>Utiliser pour App Service

Il s’agit d’un raccourci d’interface utilisateur uniquement qui sélectionne les deux paramètres nécessaires au back-end Azure App Service. Ce raccourci permet de **choisir un nom d’hôte à partir d’une adresse back-end**, puis il crée une sonde personnalisée si vous n’en avez pas déjà une. (Pour plus d’informations, consultez la section du paramètre [Choisir un nom d’hôte à partir d’une adresse back-end](#pick) dans cet article.) Une nouvelle sonde est créée, puis l’en-tête de la sonde est choisie à partir de l’adresse du membre back-end.

### <a name="use-custom-probe"></a>Utiliser une sonde personnalisée

Ce paramètre associe une [sonde personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) à un paramètre HTTP. Vous pouvez associer une seule sonde personnalisée à un paramètre HTTP. Si vous n’associez pas explicitement une sonde personnalisée, la [sonde par défaut](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) est utilisée pour superviser l’intégrité du back-end. Nous vous recommandons de créer une sonde personnalisée pour mieux contrôler la supervision de l’intégrité de vos back-ends.

> [!NOTE]
> La sonde personnalisée ne supervise pas l’intégrité du pool de back-ends, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

### <a id="pick"/></a>Choisir un nom d’hôte à partir d’une adresse back-end

Cette fonctionnalité définit dynamiquement l’en-tête de l’*hôte* dans la demande sur le nom d’hôte du pool de back-ends. Elle utilise une adresse IP ou un nom de domaine complet.

Cette fonctionnalité s’avère utile quand le nom de domaine du back end est différent du nom DNS de la passerelle d’application et que le back-end s’appuie sur un en-tête d’hôte spécifique pour se résoudre en point de terminaison correct.

Exemple : des services multilocataires en tant que back-end. Un service d’application est un service multilocataire qui utilise un espace partagé avec une seule adresse IP. Ainsi, un service d’application est uniquement accessible par le biais des noms d’hôte configurés dans les paramètres du domaine personnalisé.

Par défaut, le nom de domaine personnalisé est *example.azurewebsites.net*. Pour accéder à votre service d’application à l’aide d’une passerelle d’application par le biais d’un nom d’hôte qui n’est pas explicitement inscrit dans le service d’application ou par le biais du nom de domaine complet de la passerelle d’application, vous remplacez le nom d’hôte dans la demande d’origine par celui du service d’application. Pour cela, activez le paramètre **Choisir un nom d’hôte à partir d’une adresse back-end**.

Pour un domaine personnalisé dont le nom DNS personnalisé existant est mappé au service d’application, vous n’êtes pas obligé d’activer ce paramètre.

> [!NOTE]
> Il n’est pas nécessaire pour App Service Environment, qui est un déploiement dédié.

### <a name="host-name-override"></a>Remplacement du nom d’hôte

Cette fonctionnalité remplace l’en-tête de l’*hôte* dans la demande entrante sur la passerelle d’application par le nom d’hôte que vous spécifiez.

Par exemple, si *www.contoso.com* est spécifié dans le paramètre **Nom d’hôte**, la demande d’origine * https://appgw.eastus.cloudapp.azure.com/path1 est remplacée par * https://www.contoso.com/path1 quand la demande est transférée au back-end.

## <a name="back-end-pool"></a>Pool de back-ends

Vous pouvez pointer un pool de back-ends vers quatre types de membres de back-end : une machine virtuelle spécifique, un groupe de machines virtuelles identiques, une adresse IP/un nom de domaine complet ou un service d’application. Chaque pool de back-ends peut pointer vers plusieurs membres du même type. Pointer vers des membres de types différents dans le même pool de back-ends n’est pas pris en charge.

Après avoir créé un pool de back-ends, vous devez l’associer à une ou plusieurs règles de routage des demandes. Vous devez également configurer des sondes d’intégrité pour chaque pool de back-ends sur votre passerelle d’application. Quand une condition de règle de routage des demandes est remplie, la passerelle d’application transfère le trafic aux serveurs sains (comme déterminé par les sondes d’intégrité) dans le pool de back-ends correspondant.

## <a name="health-probes"></a>Sondes d’intégrité

Par défaut, une passerelle d’application supervise l’intégrité de toutes les ressources dans son back-end. Mais nous vous recommandons vivement de créer une sonde personnalisée pour chaque paramètre HTTP de back-end afin de mieux contrôler la supervision de l’intégrité. Pour savoir comment configurer une sonde personnalisée, consultez [Paramètres de sonde d’intégrité personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Une fois que vous avez créé une sonde d’intégrité personnalisée, vous avez besoin de l’associer à un paramètre HTTP de back-end. Une sonde personnalisée ne supervise pas l’intégrité du pool de back-ends, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur à l’aide d’une règle.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les composants d’Application Gateway, vous pouvez effectuer les opérations suivantes :

- [Créer une passerelle d’application dans le portail Azure](quick-create-portal.md)
- [Créer une passerelle d’application à l’aide de PowerShell](quick-create-powershell.md)
- [Créer une passerelle d’application à l’aide d’Azure CLI](quick-create-cli.md)
