---
title: Présentation de la configuration Azure Application Gateway
description: Cet article explique comment configurer les composants d’Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 40c5444a54f4e483a9dcacb958c18f66da45019a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58906121"
---
# <a name="application-gateway-configuration-overview"></a>Vue d’ensemble de configuration Application Gateway

Azure Application Gateway se compose de plusieurs composants que vous pouvez configurer de différentes manières pour différents scénarios. Cet article vous montre comment configurer chaque composant.

![Diagramme de flux de composants Application Gateway](./media/configuration-overview/configuration-overview1.png)

Cette image illustre une application qui a trois écouteurs. Les deux premières sont des écouteurs multisites pour `http://acme.com/*` et `http://fabrikam.com/*`, respectivement. À la fois écoutent sur le port 80. Le troisième est un écouteur de base qui a l’arrêt de Secure Sockets Layer (SSL) de bout en bout.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Conditions préalables

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Réseau virtuel Azure et le sous-réseau dédié

Une passerelle d’application est un déploiement dédié dans votre réseau virtuel. Au sein de votre réseau virtuel, un sous-réseau dédié est requis pour la passerelle d’application. Vous pouvez avoir plusieurs instances d’un déploiement de passerelle d’application donnée dans un sous-réseau. Vous pouvez également déployer d’autres passerelles d’application dans le sous-réseau. Mais vous ne pouvez pas déployer n’importe quelle autre ressource dans le sous-réseau de passerelle d’application.

> [!NOTE]
> Vous ne pouvez pas mélanger Standard_v2 et Azure Application Gateway Standard sur le même sous-réseau.

#### <a name="size-of-the-subnet"></a>Taille du sous-réseau

Passerelle d’application consomme 1 adresse IP privée par instance, ainsi qu’une autre adresse IP privée si une adresse IP de serveur frontal privé est configurée.

Azure réserve également 5 adresses IP dans chaque sous-réseau à un usage interne : le premier 4 et la dernière adresses IP. Par exemple, considérez les 15 instances application gateway avec aucune adresse IP frontale privée. Vous avez besoin d’au moins 20 adresses IP pour ce sous-réseau : 5 pour un usage interne et 15 pour les instances de passerelle d’application. Par conséquent, vous devez/27 sous-réseau, taille ou plus grande.

Envisagez un sous-réseau disposant 27 instances application gateway et une adresse IP pour une adresse IP de serveur frontal privée. Dans ce cas, vous devez les adresses IP 33 : 27 pour les instances de passerelle d’application, 1 pour le serveur frontal privé et 5 pour une utilisation interne. Par conséquent, vous avez besoin d’un /26 sous-réseau, taille ou plus grande.

Nous vous recommandons d’utiliser une taille de sous-réseau d’au moins/28. Cette taille donne 11 adresses IP utilisables. Si votre charge de l’application nécessite plus de 10 adresses IP, envisagez de/27 ou/26 taille du sous-réseau.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Groupes de sécurité réseau sur le sous-réseau de passerelle d’Application

Groupes de sécurité réseau (NSG) sont pris en charge sur Application Gateway. Mais il existe plusieurs restrictions :

- Vous devez inclure des exceptions pour le trafic entrant sur les ports 65503-65534 pour la référence SKU de passerelle d’Application v1 et les ports 65200-65535 pour la référence SKU v2. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par les certificats Azure. Les entités externes, notamment les clients de ces passerelles, Impossible d’initier les modifications sur ces points de terminaison sans les certificats appropriés en place.

- La connectivité Internet sortante ne peut pas être bloquée. Les règles de trafic sortant par défaut dans le groupe de sécurité réseau permettent une connectivité internet. Nous vous recommandons :

  - Ne supprimez pas les règles de trafic sortant par défaut.
  - Ne créez pas autres règles de trafic sortant qui refusent la connectivité internet sortante.

- Le trafic en provenance du **AzureLoadBalancer** balise doit être autorisée.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Accéder à des adresses IP sources quelques liste verte Application Gateway

Pour ce scénario, utilisez les groupes de sécurité réseau sur le sous-réseau de passerelle d’Application. Placez les restrictions suivantes sur le sous-réseau dans l’ordre de priorité :

1. Autoriser le trafic entrant à partir d’une plage d’adresses IP/IP source.
2. Autoriser les demandes entrantes à partir de toutes les sources aux ports 65503-65534 pour [communication contrôle d’intégrité du serveur principal](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par les certificats Azure. Sans les certificats appropriés en place, les entités externes ne peuvent pas initier des changements sur ces points de terminaison.
3. Autoriser les sondes d’équilibreur de charge Azure entrant (*AzureLoadBalancer* balise) et le trafic de réseau virtuel entrant (*VirtualNetwork* balise) sur le [groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Bloquer tout le trafic entrant à l’aide d’une règle de tout refuser.
5. Autoriser le trafic sortant vers internet pour toutes les destinations.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Itinéraires définis par l’utilisateur pris en charge sur le sous-réseau de passerelle d’Application

La référence (SKU) v1, itinéraires définis par l’utilisateur (UDR) sont prises en charge sur le sous-réseau de passerelle d’Application, tant qu’altérer communication demande/réponse de bout en bout. Par exemple, vous pouvez configurer un UDR dans le sous-réseau de passerelle d’Application pour pointer vers une appliance de pare-feu pour l’inspection de paquets. Mais vous devez vous assurer que le paquet peut atteindre sa destination prévue après inspection. Cela peut entraîner une sonde d’intégrité incorrect ou le comportement de routage du trafic. Cela inclut les itinéraires appris ou des itinéraires de 0.0.0.0/0 par défaut qui sont propagés par Azure ExpressRoute ou passerelles VPN dans le réseau virtuel.

La référence (SKU) v2, UDR ne sont pas pris en charge sur le sous-réseau de passerelle d’Application. Pour plus d’informations, consultez [mise à l’échelle et la redondance dans une zone de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> À l’aide d’UDR sur le sous-réseau de passerelle d’Application entraîne l’état d’intégrité dans le [vue de contrôle d’intégrité du serveur principal](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) apparaisse comme « Inconnu ». Elle entraîne également la génération de journaux de passerelle d’Application et des métriques à échouer. Nous recommandons que vous n’utilisez pas UDR sur le sous-réseau de passerelle d’Application afin que vous puissiez afficher le contrôle d’intégrité du serveur principal, les journaux et les mesures.

## <a name="front-end-ip"></a>Adresse IP frontale

Vous pouvez configurer la passerelle d’application pour avoir une adresse IP publique, une adresse IP privée ou les deux. Une adresse IP publique est nécessaire lorsque vous hébergez un serveur principal qui les clients doivent accéder à Internet via une adresse IP virtuelle accessible sur internet (VIP). 

Une adresse IP publique n’est pas requise pour un point de terminaison interne qui n’est pas exposée à internet. Qui est appelé un *équilibreur de charge interne* point de terminaison (ILB). Une passerelle d’application équilibreur de charge interne est utile pour les applications line of business internes qui ne sont pas exposées à internet. Il est également utile pour les services et niveaux dans une application à plusieurs niveaux au sein d’une limite de sécurité qui ne sont pas exposés à internet mais qui nécessitent le tourniquet (round-robin) chargement distribution, adhérence de session ou une terminaison SSL.

1 seule adresse IP publique ou 1 adresse IP privée est pris en charge. Vous choisissez l’adresse IP frontale lorsque vous créez la passerelle d’application.

- Pour une adresse IP publique, vous pouvez créer une nouvelle adresse IP publique ou utiliser une adresse IP publique existante dans le même emplacement que la passerelle d’application. Si vous créez une adresse IP publique, le type d’adresse IP que vous sélectionnez (statique ou dynamique) ne peut pas être modifié ultérieurement. Pour plus d’informations, consultez [statique et l’adresse IP publique dynamique](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Pour une adresse IP privée, vous pouvez spécifier une adresse IP privée à partir du sous-réseau dans lequel la passerelle d’application est créée. Si vous ne spécifiez pas, une adresse IP arbitraire est automatiquement sélectionnée à partir du sous-réseau. Pour plus d’informations, consultez [créer une passerelle d’application avec un équilibreur de charge interne](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Une adresse IP frontale est associée à un *écouteur*, qui vérifie les demandes entrantes sur l’adresse IP frontale.

## <a name="listeners"></a>Écouteurs

Un écouteur est une entité logique qui vérifie les demandes de connexion entrantes en utilisant le port, un protocole, un hôte et une adresse IP. Lorsque vous configurez l’écouteur, vous devez entrer des valeurs pour ces qui correspondent aux valeurs correspondantes dans la requête entrante sur la passerelle.

Lorsque vous créez une passerelle d’application à l’aide du portail Azure, vous créez également un écouteur par défaut en choisissant le protocole et le port de l’écouteur. Vous pouvez choisir s’il faut activer la prise en charge de HTTP2 sur l’écouteur. Après avoir créé la passerelle d’application, vous pouvez modifier les paramètres de cet écouteur par défaut (*appGatewayHttpListener*/*appGatewayHttpsListener*) ou créer de nouveaux écouteurs.

### <a name="listener-type"></a>Type d’écouteur

Lorsque vous créez un nouvel écouteur, vous choisissez entre [ *base* et *multisite*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Si vous hébergez un site unique derrière une passerelle d’application, choisissez base. En savoir plus [comment créer une passerelle d’application avec un écouteur de base](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Si vous configurez plusieurs applications web ou plusieurs sous-domaines du même domaine parent sur la même instance de passerelle d’application, choisissez l’écouteur multisite. Pour un écouteur multisite, vous devez également entrer un nom d’hôte. Il s’agit, car la passerelle d’Application s’appuie sur les en-têtes d’hôte HTTP 1.1 pour héberger plusieurs sites Web sur la même adresse IP publique et le port.

#### <a name="order-of-processing-listeners"></a>Ordre de traitement des écouteurs

Pour la référence (SKU) v1, les écouteurs sont traités dans l’ordre d’apparition. Si un écouteur de base correspond à une demande entrante, l’écouteur traite cette demande tout d’abord. Par conséquent, configurez les écouteurs multisites avant les écouteurs de base pour vous assurer que le trafic est acheminé vers le serveur principal approprié.

Pour la référence (SKU) v2, les écouteurs multisites sont traitées avant les écouteurs de base.

### <a name="front-end-ip"></a>Adresse IP frontale

Choisissez l’adresse IP frontale que vous souhaitez associer à cet écouteur. L’écouteur écoute les demandes entrantes sur cette adresse IP.

### <a name="front-end-port"></a>Port frontal

Choisissez le port frontal. Sélectionner un port existant ou créez-en un. Choisissez n’importe quelle valeur à partir de la [autorisée plage de ports](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Vous pouvez utiliser non seulement les ports bien connus, tels que 80 et 443, mais n’importe quel port personnalisé autorisé qui convient. Un port peut être utilisé pour les écouteurs destinées au public ou privé orientés écouteurs.

### <a name="protocol"></a>Protocole

Choisissez HTTP ou HTTPS :

- Si vous choisissez HTTP, le trafic entre le client et la passerelle d’application est non chiffré.

- Sélectionnez HTTPS si vous souhaitez [une terminaison SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) ou [chiffrement SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Le trafic entre le client et la passerelle d’application est chiffré. Et la connexion SSL se termine à la passerelle d’application. Si vous souhaitez que le chiffrement SSL de bout en bout, vous devez choisir le protocole HTTPS et configurer le **HTTP du serveur principal** paramètre. Cela garantit que le trafic est rechiffrée lorsqu’elles transitent à partir de la passerelle d’application vers le serveur principal.

Pour configurer l’arrêt SSL et le chiffrement SSL de bout en bout, vous devez ajouter un certificat à l’écouteur pour activer la passerelle d’application dériver une clé symétrique. Cela est déterminé par la spécification du protocole SSL. La clé symétrique est utilisée pour chiffrer et déchiffrer le trafic est envoyé à la passerelle. Le certificat de passerelle doit être au format PFX Personal Information Exchange (). Ce format permet d’exporter la clé privée qu’utilise la passerelle pour chiffrer et déchiffrer le trafic.

#### <a name="supported-certificates"></a>Certificats pris en charge

Consultez [certificats pris en charge pour un arrêt SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Prise en charge de protocole supplémentaire

#### <a name="http2-support"></a>Prise en charge de HTTP2

Prise en charge du protocole HTTP/2 est disponible pour les clients qui se connectent à uniquement les écouteurs de la passerelle de l’application. La communication aux pools de serveurs back-end est sur HTTP/1.1. Par défaut, la prise en charge du protocole HTTP/2 est désactivée. L’extrait de code Azure PowerShell suivant montre comment activer cette option :

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Prise en charge de WebSocket

Prise en charge de WebSocket est activée par défaut. Il n’existe aucun paramètre configurable par l’utilisateur pour activer ou désactiver. Vous pouvez utiliser les WebSockets avec des écouteurs HTTP et HTTPS.

### <a name="custom-error-pages"></a>Pages d’erreur personnalisées

Vous pouvez définir des erreurs personnalisées au niveau global ou au niveau de l’écouteur. Mais la création de pages d’erreurs personnalisées de niveau global à partir du portail Azure est actuellement pas pris en charge. Vous pouvez configurer une page d’erreur personnalisée pour une erreur de pare-feu d’application 403 web ou une page de 502 maintenance au niveau de l’écouteur. Vous devez également spécifier une URL de l’objet blob publiquement accessible pour le code d’état erreur donné. Pour plus d’informations, consultez [Créer des pages d’erreur personnalisées Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Codes d’erreur Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pour configurer une page d’erreur personnalisée global, consultez [configuration d’Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Stratégie SSL

Vous pouvez centraliser la gestion des certificats SSL et réduire le chiffrement-déchiffrement surcharge pour une batterie de serveurs back-end. Gestion SSL centralisée permet également de spécifier une stratégie SSL centrale adaptée à vos exigences de sécurité. Vous pouvez choisir *par défaut*, *prédéfinis*, ou *personnalisé* stratégie SSL.

Vous pouvez configurer SSL pour le contrôle des versions de protocole SSL. Vous pouvez configurer une passerelle application gateway pour refuser TLS1.0, TLS1.1 et TLS 1.2. Par défaut, SSL 2.0 et 3.0 sont désactivés et ne sont pas configurables. Pour plus d’informations, consultez [vue d’ensemble de la stratégie SSL de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Après avoir créé un écouteur, associez-la à une règle de routage des demandes. Cette règle détermine comment les requêtes qui sont reçus sur l’écouteur sont acheminées vers le serveur principal.

## <a name="request-routing-rules"></a>Règles de routage de demande

Lorsque vous créez une passerelle d’application à l’aide du portail Azure, vous créez une règle par défaut (*rule1*). Cette règle lie l’écouteur par défaut (*appGatewayHttpListener*) avec le pool principal par défaut (*appGatewayBackendPool*) et les paramètres HTTP du serveur principal par défaut ( *appGatewayBackendHttpSettings*). Après avoir créé la passerelle, vous pouvez modifier les paramètres de la règle par défaut ou créer de nouvelles règles.

### <a name="rule-type"></a>Type de règle

Lorsque vous créez une règle, vous choisissez entre [ *base* et *basé sur le chemin*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Choisissez si vous souhaitez transférer toutes les requêtes sur l’écouteur associé de base (par exemple, *blog<i></i>.contoso.com/\*)* à un seul pool back-end.
- Choisissez par chemin d’accès-si vous souhaitez acheminer les demandes à partir de chemins d’accès des URL spécifiques pour les pools principaux spécifiques. Le modèle de chemin d’accès est appliqué uniquement pour le chemin d’accès de l’URL, pas à ses paramètres de requête.

#### <a name="order-of-processing-rules"></a>Ordre des règles de traitement

Pour la référence (SKU) v1, critères spéciaux de demandes entrantes est traité dans l’ordre que les chemins d’accès sont répertoriés dans le mappage de chemin d’accès d’URL de la règle basée sur le chemin d’accès. Si une requête correspond au modèle dans deux ou plusieurs des chemins d’accès dans le mappage de chemin d’accès, le chemin d’accès qui est répertorié est tout d’abord mis en correspondance. Et la demande est transférée vers le serveur principal qui est associé à ce chemin d’accès.

Pour la référence (SKU) v2, une correspondance exacte est une priorité plus élevée que l’ordre de chemin d’accès dans le mappage de chemin d’accès d’URL. Si une requête correspond au modèle dans deux ou plusieurs chemins d’accès, la demande est transférée vers le serveur principal qui est associé le chemin d’accès qui correspond exactement à la demande. Si le chemin d’accès dans la demande entrante ne correspond pas exactement à n’importe quel chemin d’accès dans le mappage, critères spéciaux de la demande est traitée dans la liste des commandes carte chemin d’accès pour la règle basée sur le chemin d’accès.

### <a name="associated-listener"></a>Écouteur associé

Associer un écouteur à la règle afin que le *règle de routage des demandes* qui est associé à l’écouteur est évalué pour déterminer le pool back-end pour router la demande à.

### <a name="associated-back-end-pool"></a>Pool back-end associé

Associer à la règle le pool principal qui contient les cibles de back-end qui traite les requêtes qui reçoit l’écouteur.

 - Pour une règle de base, qu’un seul pool back-end est autorisé. Toutes les demandes sur l’écouteur associé sont transmis à ce pool back-end.

 - Pour une règle basée sur le chemin d’accès, ajoutez plusieurs pools principaux qui correspondent à chaque chemin d’URL. Les demandes correspondant à ce chemin d’URL qui est entré sont transmises au pool principal correspondant. En outre, ajouter un pool de serveur principal par défaut. Les demandes qui ne correspondent pas à n’importe quel chemin d’URL dans la règle sont transmises à ce pool.

### <a name="associated-back-end-http-setting"></a>Paramètre de HTTP principal associé

Ajouter un paramètre HTTP de serveur principal pour chaque règle. En utilisant le numéro de port, protocole et autres informations qui sont spécifiées dans ce paramètre, les demandes sont routées à partir de la passerelle d’application pour les cibles de back-end.

Pour une règle de base, qu’un seul paramètre HTTP de serveur principal est autorisé. Toutes les demandes sur le port d’écoute associé sont transférés vers les cibles de serveur principal correspondants à l’aide de ce paramètre HTTP.

Ajouter un paramètre HTTP de serveur principal pour chaque règle. En utilisant le numéro de port, protocole et autres informations qui sont spécifiée dans ce paramètre, les demandes sont routées à partir de la passerelle d’application pour les cibles de back-end.

Pour une règle de base, qu’un seul paramètre HTTP de serveur principal est autorisé. Toutes les demandes sur le port d’écoute associé sont transférés vers les cibles de serveur principal correspondants à l’aide de ce paramètre HTTP.

Pour une règle basée sur le chemin d’accès, ajoutez plusieurs paramètres HTTP du serveur principal qui correspondent à chaque chemin d’URL. Les demandes correspondant à ce chemin d’URL dans ce paramètre sont transférées vers les cibles de back-end correspondantes en utilisant les paramètres HTTP qui correspondent à chaque chemin d’URL. En outre, ajoutez un paramètre de HTTP par défaut. Les demandes qui ne correspondent pas à n’importe quel chemin d’URL dans cette règle sont transférées vers le pool principal par défaut à l’aide du paramètre HTTP par défaut.

### <a name="redirection-setting"></a>Paramètre de redirection

Si la redirection est configurée pour une règle de base, toutes les demandes sur l’écouteur associé sont redirigées vers la cible. Il s’agit de *global* la redirection. Si la redirection est configurée pour une règle basée sur le chemin d’accès, seules les requêtes dans une zone spécifique de site sont redirigées. Un exemple est une zone de panier d’achat qui est indiquée par */cart/\**. Il s’agit de *basée sur le chemin d’accès* la redirection.

Pour plus d’informations sur les redirections, consultez [vue d’ensemble de la redirection Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Type de redirection

Choisissez le type de redirection requis : *Permanent(301)*, *Temporary(307)*, *Found(302)*, ou *consultez other(303)*.

#### <a name="redirection-target"></a>Cible de redirection

Choisissez un autre écouteur ou un site externe comme la cible de la redirection.

##### <a name="listener"></a>Écouteur

Choisir l’écouteur en tant que la cible de redirection pour rediriger le trafic à partir d’un écouteur à l’autre sur la passerelle. Ce paramètre est requis lorsque vous souhaitez activer la redirection HTTP vers HTTPS. Il redirige le trafic à partir de l’écouteur source qui vérifie les requêtes HTTP entrantes à l’écouteur de destination qui vérifie les requêtes HTTPS entrantes. Vous pouvez également choisir d’inclure la chaîne de requête et le chemin d’accès à partir de la demande d’origine dans la demande est transmise à la cible de la redirection.

![Boîte de dialogue composants Application Gateway](./media/configuration-overview/configure-redirection.png)

Pour plus d’informations sur la redirection HTTP vers HTTPS, consultez :
- [Redirection HTTP-HTTP à l’aide du portail Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Redirection HTTP-HTTP à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Redirection HTTP vers HTTP à l’aide de l’interface CLI Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Site externe

Choisissez le site externe lorsque vous souhaitez rediriger le trafic sur le port d’écoute est associé à cette règle à un site externe. Vous pouvez choisir d’inclure la chaîne de requête à partir de la demande d’origine dans la demande est transmise à la cible de la redirection. Vous ne pouvez pas transférer le chemin d’accès au site externe qui se trouvait dans la demande d’origine.

Pour plus d’informations sur la redirection, consultez :
- [Rediriger le trafic vers un site externe à l’aide de PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Rediriger le trafic vers un site externe à l’aide de l’interface CLI](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Le paramètre d’en-tête HTTP de réécriture

Ce paramètre ajoute, supprime ou met à jour des en-têtes de demande et de réponse HTTP lors de la demande et les paquets de réponse de se déplacent entre le client et les pools principaux. Vous ne pouvez configurer cette fonctionnalité via PowerShell. Portail Azure et la prise en charge de l’interface CLI ne sont pas encore disponibles. Pour plus d'informations, consultez les pages suivantes :

 - [Vue d’ensemble des en-têtes HTTP de réécriture](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Configurer la réécriture d’en-tête HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Paramètres HTTP

La passerelle application gateway achemine le trafic vers les serveurs principaux à l’aide de la configuration que vous spécifiez ici. Après avoir créé un paramètre HTTP, vous devez l’associer à une ou plusieurs règles de routage des demandes.

### <a name="cookie-based-affinity"></a>Affinité basée sur les cookies

Cette fonctionnalité est utile lorsque vous souhaitez conserver une session utilisateur sur le même serveur. Cookies gérés de passerelle permettent l’application directe suivantes le trafic de passerelle à partir d’une session utilisateur sur le même serveur pour traitement. Ceci est important lorsque l’état de session est enregistré localement sur le serveur pour une session utilisateur. Si l’application ne peut pas gérer l’affinité basée sur les cookies, vous ne pouvez pas utiliser cette fonctionnalité. Pour l’utiliser, assurez-vous que les clients prennent en charge les cookies.

### <a name="connection-draining"></a>Vidage des connexions

Drainage de connexion vous permet de supprimer correctement les membres du pool back-end pendant les mises à jour de service planifiée. Vous pouvez appliquer ce paramètre à tous les membres d’un pool back-end lors de la création de règle. Elle garantit que toutes les instances de l’annulation d’inscription d’un pool back-end ne reçoivent toutes les nouvelles requêtes. Pendant ce temps, les requêtes existantes sont autorisés à se terminer dans une limite de temps configurée. Drainage de connexion s’applique aux instances de serveur principal qui sont explicitement supprimés à partir du pool principal par un appel d’API. Il s’applique également aux instances de serveur principal qui sont signalés comme étant *défectueux* par l’intégrité des sondes.

### <a name="protocol"></a>Protocole

Application Gateway prend en charge les protocoles HTTP et HTTPS pour router les demandes vers les serveurs principaux. Si vous choisissez HTTP, le trafic vers les serveurs principaux est non chiffré. Si la communication non chiffrée n’est pas acceptable, sélectionnez HTTPS.

Ce paramètre est combiné avec HTTPS dans le prend en charge de l’écouteur [SSL de bout en bout](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Cela vous permet de transmettre en toute sécurité des données sensibles chiffrées au serveur principal. Chaque serveur principal dans le pool back-end qui SSL de bout en bout est activé doit être configuré avec un certificat pour permettre la communication sécurisée.

### <a name="port"></a>Port

Ce paramètre spécifie le port où les serveurs principaux écoutent le trafic à partir de la passerelle d’application. Vous pouvez configurer les ports compris entre 1 et 65535.

### <a name="request-timeout"></a>Délai d’expiration de la demande

Ce paramètre est le nombre de secondes pendant lesquelles la passerelle d’application attend de recevoir une réponse à partir du pool principal avant de retourner un message d’erreur « la connexion a expiré ».

### <a name="override-back-end-path"></a>Remplacer le chemin d’accès du serveur principal

Ce paramètre vous permet de configurer un chemin d’accès de transfert personnalisé facultatif à utiliser lors de la demande est transférée vers le serveur principal. N’importe quelle partie du chemin entrant qui correspond au chemin personnalisé dans le **remplacer le chemin d’accès du serveur principal** champ est copié dans le chemin d’accès transmis. Le tableau suivant montre comment fonctionne cette fonctionnalité :

- Lorsque le paramètre HTTP est attaché à une règle de routage de demande de base :

  | Demande d’origine  | Remplacer le chemin d’accès du serveur principal | Demande transmise à un back end |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | /override/home/              |
  | /home/secondhome/ | /override/            | /override/home/secondhome/   |

- Lorsque le paramètre HTTP est attaché à une règle de routage des demandes de basée sur le chemin d’accès :

  | Demande d’origine           | Règle de chemin d’accès       | Remplacer le chemin d’accès du serveur principal | Demande transmise à un back end |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | /pathrule*      | /override/            | /override/home/              |
  | /pathrule/home/secondhome/ | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /Home/                     | /pathrule*      | /override/            | /override/home/              |
  | /home/secondhome/          | /pathrule*      | /override/            | /override/home/secondhome/   |
  | /pathrule/home/            | / pathrule/accueil * | /override/            | /override/                   |
  | /pathrule/home/secondhome/ | / pathrule/accueil * | /override/            | /override/secondhome/        |

### <a name="use-for-app-service"></a>Utilisation pour app service

Il s’agit d’un raccourci de l’interface utilisateur qui sélectionne les deux paramètres requis pour le backend Azure App Service. Elle permet de **choisir le nom d’hôte à partir d’adresses back-end**, et il crée une sonde personnalisée. (Pour plus d’informations, consultez le [nom d’hôte de choix à partir d’adresses back-end](#pick) définissant la section de cet article.) Une nouvelle sonde est créée, et l’en-tête de la sonde est récupéré à partir de l’adresse de serveur principal.

### <a name="use-custom-probe"></a>Utiliser une sonde personnalisée

Ce paramètre associe un [sonde personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) avec un paramètre HTTP. Vous pouvez associer qu’une sonde personnalisée à un paramètre HTTP. Si vous n’associez pas explicitement une sonde personnalisée, le [sonde par défaut](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) est utilisé pour surveiller l’intégrité du back end. Nous vous recommandons de créer une sonde personnalisée pour mieux contrôler la surveillance de l’intégrité de vos serveurs principaux.

> [!NOTE]
> La sonde personnalisée ne surveille l’intégrité du pool principal, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

### <a id="pick"/></a>Choisir le nom d’hôte à partir d’adresses back-end

Cette fonctionnalité définit dynamiquement le *hôte* en-tête dans la demande au nom d’hôte du pool principal. Il utilise une adresse IP ou le nom de domaine complet.

Cette fonctionnalité vous aide à lorsque le nom de domaine du back end est différent du nom DNS de la passerelle d’application et le serveur principal s’appuie sur un en-tête d’hôte spécifique ou d’une extension d’Indication de nom de serveur (SNI) pour résoudre le point de terminaison correct.

Un exemple de cas est services multilocataires en tant que le serveur principal. Un service d’application est un service mutualisé qui utilise un espace partagé avec une seule adresse IP. Par conséquent, un service d’application est uniquement accessibles via les noms d’hôtes sont configurés dans les paramètres de domaine personnalisé.

Par défaut, le nom de domaine personnalisé est *example.azurewebsites.<i> </i>net*. Pour accéder à votre service d’application à l’aide d’une passerelle d’application via un nom d’hôte qui n’est pas explicitement inscrite dans le service d’application ou nom de domaine complet de la passerelle d’application, vous remplacez le nom d’hôte dans la demande d’origine au nom d’hôte du service de l’application. Pour ce faire, activez la **choisir le nom d’hôte à partir de l’adresse du serveur principal** paramètre.

Pour un domaine personnalisé dont le nom DNS personnalisé existant est mappé sur app service, vous n’êtes pas obligé d’activer ce paramètre.

> [!NOTE]
> Ce paramètre n’est pas requis pour l’environnement App Service pour PowerApps, qui est un déploiement dédié.

### <a name="host-name-override"></a>Remplacement de nom d’hôte

Cette fonction remplace la *hôte* en-tête dans la requête entrante sur la passerelle d’application avec le nom d’hôte que vous spécifiez.

Par exemple, si *www.contoso<i></i>.com* est spécifié dans le **nom d’hôte** définition, de la demande d’origine *https:/<i></i>/appgw.eastus.cloudapp.net/path1* est remplacée par *https:/<i></i>/www.contoso.com/path1* lorsque la demande est transmise au serveur principal.

## <a name="back-end-pool"></a>Pool back-end

Vous pouvez faire pointer un pool back-end pour les quatre types de membres back-end : un ordinateur virtuel spécifique, un jeu de mise à l’échelle de machine virtuelle, un adresse IP ou le FQDN ou un service d’application. Chaque pool back-end peut pointer vers plusieurs membres du même type. Qui pointe vers les membres de types différents dans le même pool principal n’est pas pris en charge.

Après avoir créé un pool back-end, vous devez l’associer à une ou plusieurs règles de routage des demandes. Vous devez également configurer les sondes d’intégrité pour chaque pool back-end sur votre passerelle d’application. Lorsqu’une condition de règle de routage des demandes est remplie, la passerelle d’application transfère le trafic vers les serveurs sains (tel que déterminé par les sondes d’intégrité) dans le pool principal correspondant.

## <a name="health-probes"></a>Sondes d’intégrité

Une passerelle d’application surveille l’intégrité de toutes les ressources dans son serveur principal par défaut. Mais nous vous recommandons vivement de vous créez une sonde personnalisée pour chaque paramètre de HTTP principal obtenir le plus grand contrôle sur la surveillance de l’intégrité. Pour savoir comment configurer une sonde personnalisée, consultez [paramètres de sonde d’intégrité personnalisée](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Une fois que vous créez une sonde d’intégrité personnalisées, vous devez l’associer à un paramètre de HTTP du serveur principal. Une sonde personnalisée ne surveiller l’intégrité du pool principal, sauf si le paramètre HTTP correspondant est explicitement associé à un écouteur.

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous connaissez les composants de la passerelle d’Application, vous pouvez :

- [Créer une passerelle d’application dans le portail Azure](quick-create-portal.md)
- [Créer une passerelle d’application à l’aide de PowerShell](quick-create-powershell.md)
- [Créer une passerelle d’application à l’aide de l’interface CLI Azure](quick-create-cli.md)
