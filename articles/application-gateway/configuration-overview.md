---
title: Présentation de la configuration d’Azure Application Gateway
description: Cet article explique comment configurer les composants d’Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: absha
ms.openlocfilehash: 0245a23e46770840295904685c913826950c0642
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86517839"
---
# <a name="application-gateway-configuration-overview"></a>Présentation de la configuration d’Application Gateway

Azure Application Gateway comprend plusieurs composants que vous pouvez configurer de différentes manières pour différents scénarios. Cet article vous montre comment configurer chacun d’eux.

![Organigramme des composants d’Application Gateway](./media/configuration-overview/configuration-overview1.png)

Cette image illustre une application dotée de trois écouteurs. Les deux premiers écouteurs sont multisites pour `http://acme.com/*` et `http://fabrikam.com/*`, respectivement. Ils écoutent tous les deux le port 80. Le troisième est un écouteur de base qui dispose d’une terminaison de sécurité TLS (Transport Layer Security) de bout en bout, précédemment connue sous le nom de terminaison de protocole SSL.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prérequis

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Réseau virtuel Azure et sous-réseau dédié

Une passerelle d’application est un déploiement dédié dans votre réseau virtuel. Au sein de votre réseau virtuel, un sous-réseau dédié est nécessaire pour la passerelle d’application. Vous pouvez avoir plusieurs instances d’un déploiement de passerelle d’application donné dans un sous-réseau. Vous pouvez aussi déployer d’autres passerelles d’application dans le sous-réseau. Mais vous ne pouvez pas déployer une autre ressource dans le sous-réseau de la passerelle d’application.

> [!NOTE]
> Vous ne pouvez pas mélanger Azure Application Gateway Standard_v2 et Standard sur le même sous-réseau.

#### <a name="size-of-the-subnet"></a>Taille du sous-réseau

Application Gateway utilise une adresse IP privée par instance, ainsi qu’une autre adresse IP privée si une adresse IP front-end privée est configurée.

Azure réserve également cinq adresses IP dans chaque sous-réseau pour un usage interne : les quatre premières et la dernière. Prenons l’exemple de 15 instances de passerelle d’application sans aucune adresse IP front-end privée. Vous avez besoin d’au moins 20 adresses IP pour ce sous-réseau : cinq pour une utilisation interne et 15 pour les instances de passerelle d’application. Donc, vous avez besoin d’une taille de sous-réseau /27 ou plus.

Prenons l’exemple d’un sous-réseau disposant de 27 instances de passerelle d’application et d’une adresse front-end IP privée. Dans ce cas, vous avez besoin de 33 adresses IP : 27 pour les instances de passerelle d’application, une pour l’adresse front-end privée et cinq pour un usage interne. Donc, vous avez besoin d’une taille de sous-réseau /26 ou plus.

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

> [!IMPORTANT]
> L’utilisation de routes définies par l’utilisateur sur le sous-réseau Application Gateway est susceptible d’entraîner l’indication de l’état d’intégrité **Inconnu** dans l’[affichage de l’intégrité du back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health). Elle peut également entraîner l’échec de la génération des journaux et métriques Application Gateway. Nous vous recommandons de ne pas utiliser de routes définies par l’utilisateur sur le sous-réseau Application Gateway afin de pouvoir voir l’état d’intégrité, les journaux et les métriques du back-end.

- **v1**

   Pour la référence SKU v1, les routes définies par l’utilisateur sont prises en charge sur le sous-réseau Application Gateway, tant qu’elles n’altèrent pas la communication de demande/réponse de bout en bout. Par exemple, vous pouvez configurer une route définie par l’utilisateur dans le sous-réseau Application Gateway pour pointer vers une appliance de pare-feu afin d’inspecter un paquet. Mais vous devez vérifier que le paquet peut atteindre sa destination prévue après l’inspection. S’il n’y parvient pas, cela peut entraîner un comportement incorrect de la sonde d’intégrité ou du routage du trafic. Sont incluses les routes apprises ou 0.0.0.0/0 par défaut propagées par Azure ExpressRoute ou des passerelles VPN dans le réseau virtuel.

- **v2**

   Pour la référence SKU v2, il existe des scénarios pris en charge et non pris en charge :

   **Scénarios pris en charge par la v2**
   > [!WARNING]
   > Une configuration incorrecte de la table de routage peut entraîner un routage asymétrique dans Application Gateway v2. Assurez-vous que tout le trafic de gestion/plan de contrôle est envoyé directement à Internet et non par le biais d’une appliance virtuelle. La journalisation et les métriques peuvent également être affectées.


  **Scenario 1** : UDR pour désactiver la propagation d’itinéraires du protocole de passerelle frontière (BGP) vers le sous-réseau d’Application Gateway

   Parfois, l’itinéraire de la passerelle par défaut (0.0.0.0/0) est publié via les passerelles VPN ou ExpressRoute associées au réseau virtuel Application Gateway. Cela interrompt le trafic du plan de gestion, qui nécessite un chemin d’accès direct à Internet. Dans de tels scénarios, un itinéraire défini par l’utilisateur (UDR) peut être utilisé pour désactiver la propagation d’itinéraires BGP. 

   Pour désactiver la propagation d’itinéraires BGP, procédez comme suit :

   1. Créez une ressource de table de routage dans Azure.
   2. Désactivez le paramètre **Propagation de la route de la passerelle de réseau virtuel**. 
   3. Associez la table de routage au sous-réseau approprié. 

   L’activation de l’UDR pour ce scénario ne doit pas perturber les configurations existantes.

  **Scénario 2** : UDR pour diriger 0.0.0.0/0 vers Internet

   Vous pouvez créer un UDR pour envoyer le trafic de 0.0.0.0/0 directement vers Internet. 

  **Scénario 3** : UDR pour Azure Kubernetes Service avec kubenet

  Si vous utilisez kubenet avec Azure Kubernetes Service (AKS) et Application Gateway Ingress Controller (AGIC), vous avez besoin d’une table de route pour permettre au trafic envoyé aux pods d’Application Gateway d’être acheminé vers le bon nœud. Cela n’est pas nécessaire si vous utilisez Azure CNI. 

  Pour configurer la table de route afin de permettre à kubenet de fonctionner, procédez comme suit :

  1. Accédez au groupe de ressources créé par AKS (le nom du groupe de ressources doit commencer par « MC_ »)
  2. Recherchez la table de route créée par AKS dans ce groupe de ressources. La table de route doit être remplie avec les informations suivantes :
     - Le préfixe d’adresse doit être la plage d’adresses IP des pods que vous souhaitez atteindre dans AKS. 
     - Le type de tronçon suivant doit être Appliance virtuelle. 
     - L’adresse du tronçon suivant doit être l’adresse IP du nœud qui héberge les pods.
  3. Associez cette table de route au sous-réseau Application Gateway. 
    
  **Scénarios non pris en charge par la v2**

  **Scenario 1** : UDR pour les appliances virtuelles

  Aucun scénario dans lequel 0.0.0.0/0 doit être redirigé via une appliance virtuelle, un réseau virtuel hub/spoke ou localement (tunneling forcé) n’est pas pris en charge pour V2.

## <a name="front-end-ip"></a>Adresse IP front-end

Vous pouvez configurer la passerelle d’application pour qu’elle ait une adresse IP publique, une adresse IP privée ou les deux. Une adresse IP publique est nécessaire quand vous hébergez un back-end auquel les clients doivent accéder par Internet par le biais d’une adresse IP virtuelle Internet. 

Une adresse IP publique n’est pas nécessaire pour un point de terminaison interne non exposé à Internet. Ce dernier est appelé point de terminaison d’*équilibreur de charge interne* ou adresse IP front-end privée. L’équilibreur de charge interne de la passerelle d’application s’avère utile pour les applications métier internes non exposées à Internet. Il s’avère également utile pour les services et niveaux inclus dans une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais qui a besoin d’une distribution de charge par tourniquet, de l’adhérence de session ou de la terminaison TLS.

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

- Si vous souhaitez transférer les requêtes HTTP vers différents pools principaux en fonction de l’en-tête d’*hôte* ou des noms d’hôte, choisissez le type d’écouteur « multisite » ; dans ce cas, vous devez également spécifier un nom d’hôte qui correspond à la demande entrante. En effet, Application Gateway s’appuie sur des en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites web sur la même adresse IP publique et le même port. Pour en savoir plus, consultez [Hébergement de plusieurs sites à l’aide d’Application Gateway](multiple-site-overview.md).

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

- Sélectionnez HTTPS si vous voulez [un arrêt TLS](features.md#secure-sockets-layer-ssltls-termination) ou un [chiffrement TLS de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Le trafic entre le client et la passerelle d’application est chiffré. Et la connexion TLS s’arrête à la passerelle d’application. Si vous voulez un chiffrement TLS de bout en bout, vous devez choisir le protocole HTTPS et configurer le paramètre **HTTP du back-end**. Ce dernier permet de veiller à ce que le trafic soit rechiffré quand il passe de la passerelle d’application au back-end.


Pour configurer l’arrêt TLS et le chiffrement TLS de bout en bout, vous devez ajouter un certificat à l’écouteur pour permettre à la passerelle d’application de dériver une clé symétrique. Il s’agit d’une exigence de la spécification du protocole TLS. La clé symétrique sert à chiffrer et déchiffrer le trafic envoyé à la passerelle. Le certificat de passerelle doit être au format Personal Information Exchange (PFX). Ce format vous permet d’exporter la clé privée que la passerelle utilise pour chiffrer et déchiffrer le trafic.

#### <a name="supported-certificates"></a>Certificats pris en charge

Consultez [Certificats pris en charge pour un arrêt TLS](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

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

### <a name="tls-policy"></a>Stratégie de protocole TLS

Vous pouvez centraliser la gestion des certificats TLS/SSL et réduire la surcharge de chiffrement-déchiffrement d’une batterie de serveurs back-end. Cette gestion TLS centralisée permet également de spécifier une stratégie TLS centrale adaptée à vos besoins de sécurité. Vous pouvez choisir une stratégie TLS *par défaut*, *prédéfinie* ou *personnalisée*.

Vous configurez la stratégie TLS pour contrôler les versions du protocole TLS. Vous pouvez configurer une passerelle d’application afin qu’elle utilise une version de protocole minimale pour les négociations TLS à partir de TLS 1.0, TLS 1.1 et TLS 1.2. Par défaut, SSL 2.0 et 3.0 sont désactivés et ne sont pas configurables. Pour plus d’informations, consultez [Vue d’ensemble de la stratégie TLS Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Après avoir créé un écouteur, vous l’associez à une règle de routage des demandes. Cette règle détermine la manière dont les demandes reçues sur l’écouteur sont routées vers le back-end.

## <a name="request-routing-rules"></a>Règles de routage des requêtes

Quand vous créez une passerelle d’application à l’aide du portail Azure, vous créez une règle par défaut (*rule1*). Cette règle lie l’écouteur par défaut (*appGatewayHttpListener*) au pool de back-ends par défaut (*appGatewayBackendPool*) et aux paramètres HTTP du back-end par défaut ( *appGatewayBackendHttpSettings*). Après avoir créé la passerelle, vous pouvez modifier les paramètres de la règle par défaut ou créer des règles.

### <a name="rule-type"></a>Type de règle

Quand vous créez une règle, vous choisissez entre le type [*de base* et le type *basé sur un chemin*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Choisissez le type de base pour transférer toutes les demandes sur l’écouteur associé (par exemple, *blog<i></i>.contoso.com/\*)* à un seul pool de back-ends.
- Choisissez le type basé sur un chemin pour router les demandes provenant de chemins d’URL spécifiques vers des pools de back-ends spécifiques. Le modèle de chemin s’applique uniquement au chemin de l’URL, pas à ses paramètres de demande.

#### <a name="order-of-processing-rules"></a>Ordre de traitement des règles

Pour la référence SKU v1 et v2, les critères spéciaux des demandes entrantes sont traités dans l’ordre dans lequel les chemins sont listés dans le mappage du chemin d’URL de la règle basée sur un chemin. Si une demande correspond au modèle dans deux chemins ou plus du mappage du chemin, le chemin listé en premier est mappé. Et la demande est transférée au back-end associé à ce chemin.

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

Pour plus d’informations sur les redirections, consultez [Vue d’ensemble de la redirection Application Gateway](redirect-overview.md).

#### <a name="redirection-type"></a>Type de redirection

Choisissez le type de redirection nécessaire : *Permanent (301)* , *Temporaire (307)* , *Trouvé (302)* ou *Voir autre (303)* .

#### <a name="redirection-target"></a>Cible de redirection

Choisissez un autre écouteur ou un site externe comme cible de redirection.

##### <a name="listener"></a>Écouteur

Choisissez l’écouteur comme cible de redirection pour rediriger le trafic depuis un écouteur vers un autre sur la passerelle. Ce paramètre est obligatoire quand vous voulez activer la redirection HTTP vers HTTPS. Il redirige le trafic depuis l’écouteur source qui vérifie les demandes HTTP entrantes vers l’écouteur de destination qui vérifie les demandes HTTPS entrantes. Vous pouvez également choisir d’inclure la chaîne et le chemin de requête de la demande d’origine dans la demande transférée à la cible de redirection.

![Boîte de dialogue Composants d’Application Gateway](./media/configuration-overview/configure-redirection.png)

Pour plus d’informations sur la redirection HTTP vers HTTPS, consultez :
- [Redirection HTTP vers HTTPS à l’aide du portail Azure](redirect-http-to-https-portal.md)
- [Redirection HTTP vers HTTPS à l’aide de PowerShell](redirect-http-to-https-powershell.md)
- [Redirection HTTP vers HTTPS à l’aide d’Azure CLI](redirect-http-to-https-cli.md)

##### <a name="external-site"></a>Site externe

Choisissez un site externe quand vous voulez rediriger le trafic sur l’écouteur associé à cette règle vers un site externe. Vous pouvez choisir d’inclure la chaîne de requête de la demande d’origine dans la demande transférée à la cible de redirection. Vous ne pouvez pas transférer le chemin au site externe qui était dans la demande d’origine.

Pour plus d’informations sur la redirection, consultez :
- [Rediriger le trafic vers un site externe à l’aide de PowerShell](redirect-external-site-powershell.md)
- [Rediriger le trafic vers un site externe à l’aide de l’interface de ligne de commande](redirect-external-site-cli.md)

### <a name="rewrite-http-headers-and-url"></a>Réécrire les en-têtes et les URL HTTP

En utilisant des règles de réécriture, vous pouvez ajouter, supprimer ou mettre à jour les en-têtes de requête et de réponse HTTP(S) ainsi que les paramètres du chemin URL et de la chaîne de requête lorsque les paquets de requête et de réponse se déplacent entre le client et des pools principaux via la passerelle d’application.

Vous pouvez régler les en-têtes et les paramètres d’URL sur des valeurs statiques ou d’autres en-têtes et variables de serveur. Cela facilite les cas d’usage importants, tels que l’extraction d’adresses IP des clients, la suppression d’informations sensibles relatives au back-end, le renforcement de la sécurité, etc.
Pour plus d'informations, consultez les pages suivantes :

 - [Vue d’ensemble de la réécriture des en-têtes HTTP](rewrite-http-headers-url.md)
 - [Configurer la réécriture des en-têtes HTTP](rewrite-http-headers-portal.md)
 - [Configurer la réécriture d’URL](rewrite-url-portal.md)

## <a name="http-settings"></a>Paramètres HTTP

La passerelle d’application route le trafic vers les serveurs back-end en utilisant la configuration que vous spécifiez ici. Après avoir créé un paramètre HTTP, vous devez l’associer à une ou plusieurs règles de routage des demandes.

### <a name="cookie-based-affinity"></a>Affinité basée sur les cookies

Azure Application Gateway utilise des cookies gérés par passerelle pour gérer les sessions utilisateur. Quand un utilisateur envoie la première demande à Application Gateway, il définit un cookie d’affinité dans la réponse avec une valeur de hachage qui contient les détails de la session, afin que les demandes suivantes incluant le cookie d’affinité soient routées vers le même serveur back-end pour conserver l’adhérence. 

Cette fonctionnalité est pratique quand vous voulez conserver une session utilisateur sur le même serveur et quand l’état de session est enregistré localement sur le serveur pour une session utilisateur. Si l’application ne peut pas gérer l’affinité basée sur les cookies, vous ne pouvez pas utiliser cette fonctionnalité. Pour l’utiliser, assurez-vous que les clients prennent en charge les cookies.

La [mise à jour v80](https://chromiumdash.appspot.com/schedule) du [navigateur Chromium](https://www.chromium.org/Home) a permis que les cookies HTTP sans attribut [SameSite](https://tools.ietf.org/id/draft-ietf-httpbis-rfc6265bis-03.html#rfc.section.5.3.7) soient traités comme SameSite=Lax. Dans le cas de requêtes CORS (Cross-Origin Resource Sharing), si le cookie doit être envoyé dans un contexte tiers, il doit utiliser les attributs *SameSite=None; Secure* et être envoyé seulement via HTTPS. Dans le cas contraire, dans un scénario HTTP uniquement, le navigateur n’envoie pas les cookies dans le contexte tiers. L’objectif de cette mise à jour à partir de Chrome est d’améliorer la sécurité et d’éviter les attaques par falsification de requête intersites (CSRF). 

Pour prendre en charge ce changement, à partir du 17 février 2020, Application Gateway (tous les types de références SKU) injecte un autre cookie appelé *ApplicationGatewayAffinityCORS* en plus du cookie *ApplicationGatewayAffinity* existant. Le cookie *ApplicationGatewayAffinityCORS* a deux attributs supplémentaires qui lui sont ajoutés ( *« SameSite=None; Secure »* ), afin que les sessions persistantes soient conservées même pour les requêtes cross-origin.

Notez que le nom du cookie d’affinité par défaut est *ApplicationGatewayAffinity* et que vous pouvez changer ce nom. Si vous utilisez un nom de cookie d’affinité personnalisé, un cookie supplémentaire est ajouté avec le suffixe CORS. Par exemple, *CustomCookieNameCORS*.

> [!NOTE]
> Si l’attribut *SameSite=None* est défini, le cookie doit obligatoirement contenir aussi l’indicateur *Secure* et être envoyé via HTTPS.  Si l’affinité de session est exigée sur CORS, vous devez migrer votre charge de travail vers HTTPS. Reportez-vous à la documentation sur le déchargement TLS et le protocole TLS de bout en bout pour Application Gateway ici : [Vue d’ensemble](ssl-overview.md), [Configurer une passerelle Application Gateway avec terminaison TLS à l’aide du portail Azure](create-ssl-portal.md), [Configurer le protocole TLS de bout en bout à l’aide d’Application Gateway avec le portail](end-to-end-ssl-portal.md).

### <a name="connection-draining"></a>Vidage des connexions

Le vidage des connexions vous permet de supprimer élégamment des membres du pool de back-ends pendant les mises à jour de service planifiées. Vous pouvez appliquer ce paramètre à tous les membres d’un pool principal en activant le drainage de la connexion sur le paramètre HTTP. Elle garantit que toutes les instances de désinscription d’un pool de back-ends continuent à gérer les connexions existantes et à traiter les demandes en cours pendant un délai d’expiration configurable, et ne reçoivent aucune nouvelle demande ou connexion. La seule exception concerne les demandes liées à la désinscription des instances en raison d’une affinité de session gérée par la passerelle et qui continueront d’être transmises par proxy aux instances de désinscription. Le vidage des connexions s’applique aux instances back-end qui sont explicitement supprimées du pool de back-ends.

### <a name="protocol"></a>Protocol

Application Gateway prend en charge les protocoles HTTP et HTTPS pour router les demandes vers les serveurs back-end. Si vous choisissez HTTP, le trafic vers les serveurs back-end est non chiffré. Si des communications non chiffrées ne sont pas acceptables, sélectionnez HTTPS.

Ce paramètre combiné avec HTTPS dans l’écouteur prend en charge le chiffrement [TLS de bout en bout](ssl-overview.md). Celui-ci vous permet de transmettre en toute sécurité des données sensibles chiffrées au serveur back-end. Chaque serveur back-end du pool de back-ends pour lequel un chiffrement TLS de bout en bout est activé doit être configuré avec un certificat pour permettre une communication sécurisée.

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

Ce paramètre associe une [sonde personnalisée](application-gateway-probe-overview.md#custom-health-probe) à un paramètre HTTP. Vous pouvez associer une seule sonde personnalisée à un paramètre HTTP. Si vous n’associez pas explicitement une sonde personnalisée, la [sonde par défaut](application-gateway-probe-overview.md#default-health-probe-settings) est utilisée pour superviser l’intégrité du back-end. Nous vous recommandons de créer une sonde personnalisée pour mieux contrôler la supervision de l’intégrité de vos back-ends.

> [!NOTE]
> La sonde personnalisée ne supervise pas l’intégrité du pool de back-ends, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

### <a name="pick-host-name-from-back-end-address"></a><a name="pick"></a>Choisir un nom d’hôte à partir d’une adresse back-end

Cette fonctionnalité définit dynamiquement l’en-tête de l’*hôte* dans la demande sur le nom d’hôte du pool de back-ends. Elle utilise une adresse IP ou un nom de domaine complet.

Cette fonctionnalité s’avère utile quand le nom de domaine du back end est différent du nom DNS de la passerelle d’application et que le back-end s’appuie sur un en-tête d’hôte spécifique pour se résoudre en point de terminaison correct.

Exemple : des services multilocataires en tant que back-end. Un service d’application est un service multilocataire qui utilise un espace partagé avec une seule adresse IP. Ainsi, un service d’application est uniquement accessible par le biais des noms d’hôte configurés dans les paramètres du domaine personnalisé.

Par défaut, le nom de domaine personnalisé est *example.azurewebsites.net*. Pour accéder à votre service d’application à l’aide d’une passerelle d’application par le biais d’un nom d’hôte qui n’est pas explicitement inscrit dans le service d’application ou par le biais du nom de domaine complet de la passerelle d’application, vous remplacez le nom d’hôte dans la demande d’origine par celui du service d’application. Pour cela, activez le paramètre **Choisir un nom d’hôte à partir d’une adresse back-end**.

Pour un domaine personnalisé dont le nom DNS personnalisé existant est mappé au service d’application, vous n’êtes pas obligé d’activer ce paramètre.

> [!NOTE]
> Il n’est pas nécessaire pour App Service Environment, qui est un déploiement dédié.

### <a name="host-name-override"></a>Remplacement du nom d’hôte

Cette fonctionnalité remplace l’en-tête de l’*hôte* dans la demande entrante sur la passerelle d’application par le nom d’hôte que vous spécifiez.

Par exemple, si *www.contoso.com* est spécifié dans le paramètre **Nom d’hôte**, la demande d’origine *`https://appgw.eastus.cloudapp.azure.com/path1` est remplacée par *`https://www.contoso.com/path1` quand la demande est transférée au back-end.

## <a name="back-end-pool"></a>Pool de back-ends

Vous pouvez pointer un pool de back-ends vers quatre types de membres de back-end : une machine virtuelle spécifique, un groupe de machines virtuelles identiques, une adresse IP/un nom de domaine complet ou un service d’application. 

Après avoir créé un pool de back-ends, vous devez l’associer à une ou plusieurs règles de routage des demandes. Vous devez également configurer des sondes d’intégrité pour chaque pool de back-ends sur votre passerelle d’application. Quand une condition de règle de routage des demandes est remplie, la passerelle d’application transfère le trafic aux serveurs sains (comme déterminé par les sondes d’intégrité) dans le pool de back-ends correspondant.

## <a name="health-probes"></a>Sondes d’intégrité

Par défaut, une passerelle d’application supervise l’intégrité de toutes les ressources dans son back-end. Mais nous vous recommandons vivement de créer une sonde personnalisée pour chaque paramètre HTTP de back-end afin de mieux contrôler la supervision de l’intégrité. Pour savoir comment configurer une sonde personnalisée, consultez [Paramètres de sonde d’intégrité personnalisée](application-gateway-probe-overview.md#custom-health-probe-settings).

> [!NOTE]
> Une fois que vous avez créé une sonde d’intégrité personnalisée, vous avez besoin de l’associer à un paramètre HTTP de back-end. Une sonde personnalisée ne supervise pas l’intégrité du pool de back-ends, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur à l’aide d’une règle.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les composants d’Application Gateway, vous pouvez effectuer les opérations suivantes :

- [Créer une passerelle d’application dans le portail Azure](quick-create-portal.md)
- [Créer une passerelle d’application à l’aide de PowerShell](quick-create-powershell.md)
- [Créer une passerelle d’application à l’aide d’Azure CLI](quick-create-cli.md)
