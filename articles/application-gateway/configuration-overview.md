---
title: Présentation de la configuration Azure Application Gateway
description: Cet article fournit des informations sur la façon de configurer les différents composants d’Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: ca4f9bf00d70f327ff756558e25315762a9a77a8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519746"
---
# <a name="application-gateway-configuration-overview"></a>Vue d’ensemble de configuration Application Gateway

Passerelle d’application comprend plusieurs composants qui peuvent être configurées de différentes façons pour accomplir les différents scénarios. Cet article vous guide dans la manière dont chaque composant doit être configuré.

![application-gateway-components](./media/configuration-overview/configuration-overview1.png)

L’image de l’exemple ci-dessus illustre la configuration d’une application avec des écouteurs de 3. Tout d’abord deux sont des écouteurs multisites pour `http://acme.com/*` et `http://fabrikam.com/*`, respectivement. Les deux sont à l’écoute sur le port 80. Le troisième port d’écoute est un écouteur de base avec terminaison SSL de bout en bout. 

## <a name="prerequisites"></a>Conditions préalables

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Réseau virtuel Azure et le sous-réseau dédié

Application gateway est un déploiement dédié dans votre réseau virtuel. Au sein de votre réseau virtuel, un sous-réseau dédié est requis pour votre passerelle d’application. Vous pouvez avoir plusieurs instances d’un déploiement de passerelle d’application donnée dans ce sous-réseau. Vous pouvez également déployer d’autres passerelles d’application dans le sous-réseau, mais vous ne pouvez pas déployer n’importe quelle autre ressource dans le sous-réseau de passerelle d’application.  

> [!NOTE]   
> La combinaison de Standard_v2 et Standard Application Gateway sur le même sous-réseau n’est pas prise en charge.

#### <a name="size-of-the-subnet"></a>Taille du sous-réseau

Application Gateway utilise une adresse IP privée par instance, ainsi qu’une autre adresse IP privée si une configuration IP frontale privée est configurée. En outre, Azure réserve 5 adresses IP - les quatre premières et dernière adresse IP - dans chaque sous-réseau pour un usage interne. Par exemple, si une passerelle d’application est définie sur 15 instances et aucune adresse IP frontale privée, au moins 20 adresses IP requises dans le sous-réseau - cinq adresses IP pour un usage interne et 15 adresses IP pour les 15 instances de la passerelle d’application. Par conséquent, dans ce cas, / 27 sous-réseau, taille ou version ultérieure est nécessaire. Si vous avez des 27 instances et une configuration d’adresse IP pour le serveur frontal privé IP, puis les adresses IP 33 devront - 27 adresses IP pour les instances de 27 de la passerelle d’application, une adresse IP pour l’adresse IP frontale privée et l’adresse IP de cinq adresses pour un usage interne. Par conséquent, dans ce cas, un /26 sous-réseau, taille ou version ultérieure est nécessaire.

Il est recommandé d’utiliser au moins un sous-réseau/28 taille du sous-réseau. Cela vous donne 11 adresses utilisables. Si votre charge de l’application nécessite plus de 10 instances, vous devez envisager / 27 ou/26 taille du sous-réseau.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Groupes de sécurité réseau pris en charge sur le sous-réseau de passerelle d’Application

Groupes de sécurité réseau (NSG) sont pris en charge sur le sous-réseau de passerelle d’Application avec les restrictions suivantes : 

- Des exceptions doivent être définies pour le trafic entrant sur les ports 65503-65534 pour la référence SKU v1 d’Application Gateway et les ports 65200-65535 pour la référence SKU v2. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ils sont protégés (verrouillés) par des certificats Azure. Sans les certificats appropriés, les entités externes (notamment les clients de ces passerelles) ne peuvent initier aucun changement sur ces points de terminaison.

- La connectivité Internet sortante ne peut pas être bloquée. Les règles de trafic sortant par défaut dans le groupe de sécurité réseau déjà autorisent la connectivité internet. Nous vous recommandons de ne pas supprimer les règles de trafic sortant par défaut et que vous ne créez pas autres règles de trafic sortant qui refusent la connectivité internet sortante.

- Le trafic en provenance de la balise AzureLoadBalancer doit être autorisé.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Accéder à des adresses IP sources quelques liste verte Application Gateway

Ce scénario est possible en utilisant des groupes de sécurité réseau dans le sous-réseau de la passerelle d’application. Les restrictions suivantes doivent être imposées au sous-réseau dans l’ordre de priorité indiqué :

1. Autoriser le trafic entrant à partir de l’adresse IP ou de la plage d’adresses IP sources.
2. Autoriser les demandes entrantes de toutes sources aux ports 65503-65 534 pour les [communications relatives à l’intégrité backend](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ils sont protégés (verrouillés) par des certificats Azure. Sans les certificats appropriés, les entités externes (notamment les clients de ces passerelles) ne pourront initier aucun changement sur ces points de terminaison.
3. Autoriser les sondes entrantes d’Azure Load Balancer (balise AzureLoadBalancer) et le trafic réseau virtuel entrant (balise VirtualNetwork) sur le [Groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquer tout autre trafic entrant avec une règle Refuser tout.
5. Autoriser le trafic sortant vers internet pour toutes les destinations.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Itinéraires définis par l’utilisateur pris en charge sur le sous-réseau de passerelle d’Application

En cas de référence (SKU) de v1, les itinéraires définis par l’utilisateur (UDR) sont pris en charge sur le sous-réseau de passerelle d’application, tant qu’elles ne modifient pas la communication de bout en bout demande/réponse. Par exemple, vous pouvez configurer un UDR dans le sous-réseau d’Application Gateway pour pointer vers une appliance de pare-feu pour l’inspection des paquets, mais vous devez vous assurer que le paquet peut atteindre l’inspection sa destination prévue après l’inspection. Cela peut entraîner un comportement de sonde d’intégrité ou de routage du trafic incorrect. Cela inclut les itinéraires appris ou les itinéraires 0.0.0.0/0 par défaut propagés par ExpressRoute ou des passerelles VPN dans le réseau virtuel.

En cas de v2 référence (SKU), UDR sur le sous-réseau de passerelle d’application ne sont pas pris en charge. Pour plus d’informations, voir [Passerelle d’application redondante interzone et avec mise à l’échelle automatique (préversion publique)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> L’utilisation d’UDR sur le sous-réseau de passerelle d’application entraîne l’état d’intégrité dans le [l’affichage intégrité backend](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) sous forme de **inconnu** et entraîne la failue de génération de journaux de passerelle d’application et métriques. Il est recommandé de que vous n’utilisez pas UDR sur le sous-réseau de passerelle d’application pour être en mesure d’afficher l’intégrité du serveur principal, journaux et mesures.

## <a name="frontend-ip"></a>Adresse IP frontale

Vous pouvez configurer la passerelle d’application pour avoir une adresse IP publique ou une adresse IP privée ou les deux. Adresse IP publique est requise lorsque vous hébergez un serveur principal qui est nécessaire pour être accessible par les clients sur internet via une adresse IP virtuelle accessible sur Internet. Adresse IP publique n’est pas obligatoire pour un point de terminaison interne qui n’est pas exposée à Internet, également appelée charge interne (ILB) d’équilibrage point de terminaison. La configuration de la passerelle avec un équilibrage de charge interne est utile pour les applications métier internes non exposées à Internet. Elle est également utile pour les services et niveaux au sein d’une application multiniveau qui se trouve dans une limite de sécurité non exposée à Internet, mais qui requiert tout de même une distribution de charge par tourniquet, une adhérence de session ou une terminaison SSL (Secure Sockets Layer).

Une seule adresse IP publique ou une adresse IP privée est pris en charge. Vous choisissez l’adresse IP frontend lors de la création de la passerelle d’Application. 

- Dans le cas d’une adresse IP publique, vous pouvez choisir créer une nouvelle adresse IP publique ou utiliser une adresse IP publique existante dans le même emplacement que la passerelle d’Application. Si vous créez une nouvelle adresse IP publique, le type d’adresse IP sélectionné (statique ou dynamique) ne peut pas être modifié ultérieurement. Pour plus d’informations, consultez [statiques et adresse IP publique dynamique](https://docs.microsoft.com/azure/application-gateway/application-gateway-components) 

- Dans le cas d’une adresse IP privée, vous pouvez choisir de spécifier une adresse IP privée à partir du sous-réseau dans lequel la passerelle d’Application est créée. Si non spécifié explicitement, une adresse IP arbitraire sera automatiquement sélectionnée à partir du sous-réseau. Pour plus d’informations, consultez [créer une passerelle d’application avec un point de terminaison d’équilibreur de charge interne.](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Un Frontend IP est associée à *écouteur* qui vérifie les demandes entrantes sur le Frontend IP.

## <a name="listeners"></a>Écouteurs

Un écouteur est une entité logique qui vérifie les demandes de connexion entrantes, à l’aide d’adresse IP, protocole, hôte et port. Par conséquent, lorsque vous configurez l’écouteur que vous devrez entrer ces valeurs de port, protocole, hôte et adresses IP qui sont les même que les valeurs correspondantes dans la requête entrante sur la passerelle. Lorsque vous créez une passerelle d’application à l’aide du portail Azure, vous créez également un écouteur par défaut en choisissant le protocole et le port de l’écouteur. Vous pouvez en outre choisir si vous souhaitez activer la prise en charge de HTTP2 sur l’écouteur. Une fois la passerelle d’Application est créée, vous pouvez modifier le paramètre de cet écouteur par défaut (*appGatewayHttpListener*/*appGatewayHttpsListener*) et/ou créer de nouveaux écouteurs.

### <a name="listener-type"></a>Type d’écouteur

Vous pouvez choisir entre [écouteur de base ou de plusieurs sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) lors de la création d’un nouvel écouteur. 

- Si vous hébergez un site unique derrière une passerelle d’Application, choisissez l’écouteur de base. En savoir plus [comment créer une passerelle d’application avec l’écouteur de base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Si vous configurez plusieurs applications web ou plusieurs sous-domaines du même domaine parent sur la même instance de passerelle d’application, puis choisissez écouteur multisite. Pour l’écouteur multisite, vous devez en outre à entrer un nom d’hôte. Il s’agit, car la passerelle d’Application s’appuie sur les en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites Web sur la même adresse IP publique et le port.

#### <a name="order-of-processing-listeners"></a>Ordre de traitement des écouteurs

En cas de références v1, les écouteurs sont traités dans l’ordre qu’ils sont affichés. Pour cette raison, si un écouteur de base correspond à une demande entrante, il la traite en premier. Par conséquent, les écouteurs multisites doivent être configurés avant un écouteur élémentaire pour garantir le trafic est acheminé vers le serveur principal approprié.

En cas de références v2, les écouteurs multisites sont traitées avant les écouteurs de base.

### <a name="frontend-ip"></a>Adresse IP frontale

Choisissez l’adresse IP de serveur frontal que vous souhaitez associer à cet écouteur. L’écouteur écoute à la requête entrante sur cette adresse IP.

### <a name="frontend-port"></a>Port frontal

Choisissez le port frontal. Vous pouvez choisir les ports existants ou créez-en un. Vous pouvez choisir n’importe quelle valeur à partir de la [autorisée plage de ports](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Cela permet de vous non seulement utiliser les ports bien connus tels que 80 et 443 mais les autorisée port personnalisé approprié pour votre usage. Un seul port peut servir pour écouteurs accessible sur public ou privés écouteurs exposés.

### <a name="protocol"></a>Protocole

Vous devez choisir entre le protocole HTTP et HTTPS. 

- Si vous choisissez HTTP, le trafic entre la passerelle client et application de flux non chiffrés.

- Sélectionnez HTTPS si vous êtes intéressé [arrêt de la couche SSL (Secure Sockets)](https://docs.microsoft.com/azure/application-gateway/overview) ou [chiffrement SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Si vous sélectionnez HTTPS, le trafic entre la passerelle client et l’application est chiffré et la connexion SSL va être interrompue au niveau de la passerelle d’application.  Si vous souhaitez que le chiffrement SSL de bout en bout, vous devez en outre choisir le protocole HTTPS lors de la configuration *paramètre principal HTTP*. Cela garantit que le trafic est rechiffrée lorsqu’elles transitent à partir de la passerelle d’Application vers le serveur principal.

  Pour configurer l’arrêt de la couche SSL (Secure Sockets) et le chiffrement SSL de bout en bout, un certificat est nécessaire pour être ajouté à l’écouteur afin de permettre à la passerelle d’Application dériver une clé symétrique selon la spécification du protocole SSL. La clé symétrique est ensuite utilisée pour chiffrer et déchiffrer le trafic envoyé à la passerelle. Le certificat de passerelle doit être partagé au format Personal Information Exchange (PFX). Ce format de fichier permet d’exporter la clé privée requise par la passerelle d’application pour effectuer le chiffrement et le déchiffrement du trafic. 

#### <a name="supported-certificates"></a>Certificats pris en charge

Consultez [certificats pris en charge pour un arrêt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Prise en charge de protocole supplémentaire

#### <a name="http2-support"></a>Prise en charge de HTTP2

La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant aux écouteurs Application Gateway uniquement. La communication avec les pools du serveur principal est sur HTTP/1.1. Par défaut, la prise en charge du protocole HTTP/2 est désactivée. L’exemple d’extrait de code Azure PowerShell suivant montre comment l’activer :

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Prise en charge de WebSocket

Prise en charge de WebSocket est activée par défaut. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. Vous pouvez utiliser les WebSockets avec des écouteurs HTTP et HTTPS. 

### <a name="custom-error-page"></a>Page d’erreur personnalisée

Pages d’erreurs personnalisées peuvent être définis au niveau global, ainsi que le niveau de l’écouteur, toutefois, la création de pages d’erreur personnalisés de niveau global à partir du portail Azure est actuellement pas pris en charge. Vous pouvez configurer une page d’erreur personnalisée pour une erreur 403 de pare-feu d’applications Web ou une page de maintenance 502 au niveau de l’écouteur. Vous devez également spécifier une URL de l’objet blob publiquement accessible pour le code d’état erreur donné. Pour plus d’informations, consultez [page d’erreur personnalisée créer](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Codes d’erreur Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pour configurer une page d’erreur personnalisée global, utilisez [Azure PowerShell pour la configuration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Stratégie SSL

Vous pouvez centraliser la gestion des certificats SSL et réduire les coûts liés au chiffrement et déchiffrement d’une batterie de serveurs back-end. Cette gestion SSL centralisée permet également de spécifier une stratégie SSL centrale adaptée aux besoins de sécurité de votre organisation.  Vous pouvez choisir entre la valeur par défaut, prédéfinis et stratégie SSL personnalisée. 

Vous pouvez configurer la stratégie SSL pour contrôler les versions du protocole SSL. Vous pouvez configurer la passerelle application gateway pour refuser TLS1.0, TLS1.1 et TLS 1.2. SSL 2.0 et 3.0 sont déjà désactivés par défaut et ne sont pas configurables. Pour plus d’informations, consultez [vue d’ensemble de la stratégie SSL de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Après avoir créé un écouteur, associez-la à une règle de routage de demande qui détermine comment la demande reçue sur l’écouteur doit être acheminé vers le serveur principal.

## <a name="request-routing-rule"></a>Règle de routage des demandes

Lors de la création de la passerelle d’application à l’aide du portail Azure, vous créez une règle par défaut (*rule1*), qui lie l’écouteur par défaut (*appGatewayHttpListener*) avec le pool de serveur principal par défaut (*appGatewayBackendPool*) et les paramètres du serveur principal HTTP par défaut (*appGatewayBackendHttpSettings*). Une fois la passerelle d’application est créée, vous pouvez modifier le paramètre de cette règle par défaut ou créer de nouvelles règles.

### <a name="rule-type"></a>Type de règle

Vous pouvez choisir entre [règle basée sur le chemin d’accès ou une base](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) lors de la création d’une nouvelle règle. 

- Si vous souhaitez transférer toutes les requêtes sur l’écouteur associé (par exemple : blog.contoso.com/*) à un pool principal unique, choisissez l’écouteur de base. 
- Choisissez l’écouteur basée sur le chemin d’accès si vous voulez acheminer les demandes avec l’URL du chemin d’accès spécifique des pools principaux spécifiques. Le modèle de chemin d’accès est appliqué uniquement pour le chemin d’accès de l’URL, pas à ses paramètres de requête.


#### <a name="order-of-processing-rules"></a>Ordre des règles de traitement

En cas de références v1, la mise en correspondance de modèle de la requête entrante est traitée dans l’ordre dans lequel les chemins d’accès sont répertoriés dans le mappage de chemin d’accès d’URL de la règle basée sur le chemin d’accès. Pour cette raison, si une requête correspond au modèle dans deux ou plusieurs des chemins d’accès dans le mappage de chemin d’accès d’URL, puis le chemin d’accès qui se trouve tout d’abord à rechercher et la requête est transférée vers le serveur principal associé à ce chemin d’accès.

En cas de références v2, une correspondance exacte maintient une priorité plus élevée sur l’ordre dans lequel les chemins d’accès sont répertoriés dans le mappage de chemin d’accès d’URL. Pour cette raison, si une requête correspond au modèle dans deux ou plusieurs chemins, puis la requête est transférée vers le serveur principal associé à ce chemin d’accès qui correspond exactement à la demande. Si le chemin d’accès dans la demande entrante ne correspond pas exactement à n’importe quel chemin d’accès dans le mappage de chemin d’accès d’URL, mise en correspondance de modèle de la requête entrante est traitée dans l’ordre dans lequel les chemins d’accès sont répertoriés dans le mappage de chemin d’accès d’URL de la règle basée sur le chemin d’accès.

### <a name="associated-listener"></a>Écouteur associé

Vous devez associer un écouteur à la règle afin que le *règle de routage des demandes* associé à la *écouteur* est évaluée pour déterminer le *pool principal* auquel le demande doit être acheminé.

### <a name="associated-backend-pool"></a>Associé principal Pool

Associer le pool principal contenant les cibles de back-end qui traitera les demandes reçues par l’écouteur. Dans le cas d’une règle de base, qu’un seul pool principal est autorisé dans la mesure où toutes les requêtes sur le port d’écoute associé seront transmises à ce pool principal. Dans le cas d’une règle basée sur le chemin d’accès, ajoutez plusieurs pools principaux correspondant à chaque chemin d’URL. Les requêtes qui correspondent au chemin URL entré ici, sont transmises au pool backend correspondant. En outre, ajoutez un pool de serveur principal par défaut dans la mesure où les requêtes qui ne correspondent pas à n’importe quel chemin d’accès entré dans cette règle sont transmises à ce dernier.

### <a name="associated-backend-http-setting"></a>Paramètre HTTP de back-end associé

Ajouter un paramètre HTTP de serveur principal pour chaque règle. Les demandes seront acheminées à partir de la passerelle d’Application vers les cibles de serveur principal en utilisant le numéro de port, protocole et autres paramètres spécifiés dans ce paramètre. Dans le cas d’une règle de base, qu’un seul paramètre de serveur principal HTTP est autorisé dans la mesure où toutes les requêtes sur l’écouteur associé sont transmises aux cibles de serveur principal correspondant à l’aide de ce paramètre HTTP. Dans le cas d’une règle basée sur le chemin d’accès, ajoutez plusieurs paramètres du serveur principal HTTP correspondant à chaque chemin d’URL. Les requêtes qui correspondent au chemin URL entré ici, sont transmises aux cibles back-end correspondante en utilisant les paramètres HTTP correspondant à chaque chemin d’URL. En outre, ajouter un paramètre de HTTP par défaut dans la mesure où les requêtes qui ne correspondent pas à n’importe quel chemin d’accès entré dans cette règle sont transmises au pool principal par défaut à l’aide du paramètre HTTP par défaut.

### <a name="redirection-setting"></a>Paramètre de redirection

Si la redirection est configurée pour une règle de base, toutes les demandes sur l’écouteur associé seront redirigés vers la cible de redirection, ce qui permet la redirection globale. Si la redirection est configurée pour une règle basée sur le chemin d’accès, les requêtes uniquement sur une zone de site spécifique, par exemple un achat au panier zone désignée par/cart / *, sera redirigé vers la cible de redirection, ce qui permet la redirection basée sur le chemin d’accès. 

Pour plus d’informations sur la fonctionnalité de redirection, consultez [présentation de la Redirection](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Type de redirection

  Choisissez le type de redirection requise à partir de : Other(303) permanent(301), Temporary(307), Found(302) ou consultez.

- #### <a name="redirection-target"></a>Cible de redirection

  Vous pouvez choisir entre un autre écouteur ou un site externe comme cible de redirection. 

  - ##### <a name="listener"></a>Écouteur

    En choisissant l’option d’écouteur comme cible de redirection permet de redirection à partir d’un seul écouteur vers un autre écouteur sur la passerelle. Ce paramètre est requis lorsque vous souhaitez activer le protocole HTTP pour la redirection de HTTPS, par exemple, le trafic de redirection à partir de l’écouteur source la vérification pour les demandes HTTP entrantes à l’écouteur de destination la vérification pour les demandes HTTPS entrantes. Vous pouvez également choisir la chaîne de requête et le chemin d’accès dans la demande d’origine à inclure dans la demande transmise à la cible de la redirection.![application-gateway-components](./media/configuration-overview/configure-redirection.png)

    Pour plus d’informations sur HTTP à la redirection de HTTPS, consultez [redirection HTTP vers HTTP à l’aide du portail](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [redirection de HTTP vers HTTP à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [redirection de HTTP vers HTTP à l’aide de CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Site externe

    Choisissez le site externe lorsque vous souhaitez rediriger le trafic sur l’écouteur associé règle donc à être redirigées vers un site externe. Vous pouvez choisir la chaîne de requête dans la demande d’origine à inclure dans la demande transmise à la cible de la redirection. Vous ne pouvez pas transférer le chemin d’accès à la demande d’origine pour le site externe.

    Pour plus d’informations sur la redirection vers un site externe, consultez [rediriger le trafic vers un site externe à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) et [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Le paramètre d’en-tête HTTP de réécriture

Cette fonctionnalité permet d’ajouter, supprimer ou mettre à jour des en-têtes de demande et de réponse HTTP lors de la demande et les paquets de réponse de se déplacent entre les pools de client et serveur principal.    Vous pouvez configurer cette fonctionnalité uniquement par le biais de PowerShell. Prise en charge de portail et l’interface CLI n’est pas encore disponible. Pour plus d’informations, consultez [en-têtes HTTP réécrire](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) vue d’ensemble et [réécriture d’en-tête HTTP configurer](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Paramètres HTTP

La passerelle application gateway achemine le trafic vers les serveurs principaux à l’aide de la configuration spécifiée dans ce composant. Une fois que vous créez un paramètre HTTP, vous devez l’associer avec l’un ou plus demander des règles de routage.

### <a name="cookie-based-affinity"></a>Affinité basée sur les cookies

Cette fonctionnalité est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur. En utilisant des cookies gérés de passerelle, la passerelle Application Gateway peut diriger le trafic pour traitement à partir d’une session utilisateur vers le même serveur. Ceci est important lorsque l’état de la session est enregistré localement sur le serveur pour une session utilisateur. Si l’application ne peut pas gérer l’affinité basée sur les cookies, vous ne pourrez pas utiliser cette fonctionnalité. Pour utiliser l’affinité de session basée sur les cookies, vous devez vous assurer que les clients doivent prendre en charge les cookies. 

### <a name="connection-draining"></a>Vidage des connexions

Le vidage des connexions permet d’éliminer délicatement les membres du pool principal lors des mises à jour planifiées de maintenance. Ce paramètre peut être appliqué à tous les membres d’un pool principal lors de la création de règle. Une fois activé, application gateway vérifie que toutes les instances de l’annulation d’inscription d’un pool principal ne reçoivent pas les nouvelles demandes tout en autorisant des requêtes existantes à s’effectuer dans un délai configuré. Cela s’applique à la fois aux instances principales explicitement supprimées du pool principal par un appel d’API et aux instances secondaires signalées comme étant non intègres d’après les résultats des sondes d’intégrité.

### <a name="protocol"></a>Protocole

Passerelle d’application prend en charge les protocoles HTTP et HTTPS pour router les demandes vers les serveurs principaux. Si le protocole HTTP est choisi, puis les flux de trafic non chiffrés aux serveurs principaux. Dans les cas où la communication non chiffrée vers les serveurs principaux n’est pas une option acceptable, vous devez choisir le protocole HTTPS. Ce paramètre combiné avec choix du protocole HTTPS dans l’écouteur vous permet d’activer [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Qui vous permet de transmettre en toute sécurité des données sensibles au backend. Chaque serveur principal du pool principal pour lequel un chiffrement SSL de bout en bout a été activé doit être configuré avec un certificat afin de permettre une communication sécurisée.

### <a name="port"></a>Port

C’est le port que les serveurs principaux sont à l’écoute le trafic entrant à partir de la passerelle d’Application. Vous pouvez configurer les ports compris entre 1 et 65535.

### <a name="request-timeout"></a>Délai d’expiration de la demande

Le nombre de secondes de qu'attente de la passerelle d’application pour recevoir la réponse à partir du pool principal avant de retourner une erreur « Connexion a expiré ».

### <a name="override-backend-path"></a>Remplacer le chemin backend

Ce paramètre vous permet de configurer un chemin d’accès de transfert personnalisé facultatif à utiliser lors de la demande est transmise au serveur principal. Cette commande copie n’importe quelle partie du chemin entrant qui correspond au chemin personnalisé spécifié dans le **remplacer le chemin d’accès du serveur principal** champ pour le chemin d’accès transmis. Consultez le tableau ci-dessous pour comprendre le fonctionne de la fonctionnalité.

- Lorsque le paramètre HTTP est attaché à une règle de routage de demande de base :

  | Demande d’origine  | Remplacer le chemin backend | Demande transmise au serveur principal |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Lorsque le paramètre HTTP est attaché à une règle de routage de demande basée sur le chemin d’accès :

  | Demande d’origine           | Règle de chemin d’accès       | Remplacer le chemin backend | Demande transmise au serveur principal |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /Home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | / pathrule/accueil * | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | / pathrule/accueil * | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>Utiliser pour App Service

Il s’agit d’un raccourci de l’interface utilisateur qui sélectionne les deux paramètres requis pour le serveur principal d’application service : permet de choisir le nom d’hôte à partir de l’adresse du serveur principal et crée une sonde personnalisée. La raison pour laquelle pourquoi l’ancienne base de données est effectuée est expliqué dans la section pour **choisir le nom d’hôte à partir de l’adresse du serveur principal** paramètre. Une nouvelle sonde est créée dans lequel l’en-tête de la sonde est également récupéré à partir de l’adresse du membre principal.

### <a name="use-custom-probe"></a>Utiliser une sonde personnalisée

Ce paramètre est utilisé pour associer un [sonde personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) avec ce paramètre HTTP. Vous pouvez associer qu’une sonde personnalisée à un paramètre HTTP. Si vous ne l’associez explicitement une sonde personnalisée, puis [sonde par défaut](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) permet de surveiller l’intégrité du serveur principal. Il est recommandé de créer une sonde personnalisée pour avoir un contrôle plus précis sur la surveillance de l’intégrité de vos serveurs principaux.

> [!NOTE]   
> Sonde personnalisée ne peut être lancée analyse l’intégrité du pool principal, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

### <a name="pick-host-name-from-backend-address"></a>Choisir un nom d'hôte à partir d'une adresse backend

Cette fonctionnalité définit dynamiquement le *hôte* en-tête dans la demande au nom d’hôte du pool principal à l’aide d’une adresse IP ou le nom de domaine complet (FQDN). Cela est utile dans les scénarios où le nom de domaine du serveur principal est différent du nom DNS de la passerelle d’application et le serveur principal s’appuie sur un en-tête d’hôte spécifique ou d’une extension SNI pour résoudre au point de terminaison correct, comme en cas de services d’architecture mutualisées comme le back-end. Dans la mesure où App service est un service mutualisé à l’aide d’un espace partagé avec une seule adresse IP, un service d’application sont accessibles uniquement avec les noms d’hôte configuré dans les paramètres de domaine personnalisé. Par défaut, le nom de domaine personnalisé est *example.azurewebsites.net*. Par conséquent, si vous souhaitez accéder à votre service d’application à l’aide de la passerelle d’application avec un nom d’hôte ne sont pas explicitement inscrite dans App service ou avec le nom de domaine complet de passerelle d’Application, vous devez remplacer le nom d’hôte dans la demande d’origine au nom d’hôte du service d’application, en l’activation de **choisir le nom d’hôte à partir de l’adresse du serveur principal** paramètre.

Si vous possédez un domaine personnalisé et que vous avez mappé le nom DNS personnalisé existant pour le service d’application, il est inutile d’activer ce paramètre.

> [!NOTE]   
> Ce paramètre n’est pas requis pour App Service Environment (ASE) dans la mesure où les ASE est un déploiement dédié. 

### <a name="host-name-override"></a>Remplacement de nom d’hôte

Cette fonction remplace la *hôte* en-tête dans la requête entrante sur la passerelle d’application au nom d’hôte que vous spécifiez ici. Par exemple, si www\.contoso.com est spécifié comme le **nom d’hôte** définition, de la demande d’origine https://appgw.eastus.cloudapp.net/path1 sera modifiée en https://www.contoso.com/path1 lorsque la demande est transférée vers le serveur principal. 

## <a name="backend-pool"></a>Pool principal

Un pool principal peut être pointé vers quatre types de membres back-end : un ordinateur virtuel spécifique, les machines virtuelles identiques, un adresse IP ou le FQDN ou un service d’application. Chaque pool principal peut pointer vers plusieurs membres du même type. Pointant vers des membres de types différents dans le même pool principal n’est pas pris en charge. 

Après avoir créé un pool principal, vous devez associer à une ou plusieurs règles de routage de demande. Vous devez également configurer les sondes d’intégrité pour chaque pool principal sur votre passerelle d’application. Quand une condition de règle de routage de demande est remplie, la passerelle d’application transfère le trafic vers les serveurs sains (tel que déterminé par les sondes d’intégrité) dans le pool principal correspondant.

## <a name="health-probes"></a>Sondes d’intégrité

Bien que la passerelle d’application surveille l’intégrité de toutes les ressources dans son serveur principal par défaut, il est hautement recommandé de que créer une sonde personnalisée pour chaque paramètre du principal de HTTP afin d’avoir un contrôle plus précis sur l’analyse d’intégrité. Pour savoir comment configurer la sonde d’intégrité personnalisées, consultez [paramètres de sonde d’intégrité personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Une fois que vous créez une sonde d’intégrité personnalisées, vous devez l’associer à un paramètre de serveur principal HTTP. Sonde personnalisée ne peut être lancée analyse l’intégrité du pool principal, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert les composants de la passerelle d’Application, vous pouvez :

- [Créer une passerelle d’Application dans le portail Azure](quick-create-portal.md)
- [Créer une passerelle d’Application à l’aide de PowerShell](quick-create-powershell.md)
- [Créer une passerelle d’Application à l’aide d’Azure CLI](quick-create-cli.md)
