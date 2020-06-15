---
title: Forum aux questions sur Azure Application Gateway
description: Trouvez des réponses aux questions les plus fréquentes sur Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 05/26/2020
ms.author: victorh
ms.custom: references_regions
ms.openlocfilehash: e61ce629e723f56524ee22d8b127243f9568a835
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84196501"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Forum aux questions sur Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vous trouverez ci-dessous les questions fréquentes sur Azure Application Gateway.

## <a name="general"></a>Général

### <a name="what-is-application-gateway"></a>Présentation de Application Gateway

Le service Azure Application Gateway fournit un contrôleur de livraison d'applications (ADC) sous forme de service. Il offre diverses fonctionnalités d’équilibrage de charge de couche 7 pour vos applications. Ce service est hautement disponible, évolutif et complètement managé par Azure.

### <a name="what-features-does-application-gateway-support"></a>Quelles sont les fonctionnalités prises en charge par Application Gateway ?

Application Gateway prend en charge la mise à l’échelle automatique, le déchargement TLS et TLS de bout en bout, un pare-feu d’application web (WAF), l’affinité de session basée sur les cookies, le routage basé sur le chemin d’accès de l’URL, l’hébergement de plusieurs sites, et bien plus encore. Pour obtenir une liste complète des fonctionnalités prises en charge, voir [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>En quoi Application Gateway et Azure Load Balancer diffère-t-il ?

Application Gateway est un équilibreur de charge de couche 7, ce qui signifie qu’il fonctionne uniquement avec le trafic web (HTTP/HTTPS/WebSocket etHTTP/2). Il prend en charge des fonctionnalités telles que la terminaison TLS, l’affinité de session basée sur les cookies et le tourniquet (round robin) pour le trafic d’équilibrage de charge. Load Balancer équilibre la charge du trafic au niveau de la couche 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quels sont les protocoles pris en charge par Application Gateway ?

Application Gateway prend en charge les protocoles HTTP, HTTPS, HTTP/2 et WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway prend-il en charge le protocole HTTP/2 ?

Consultez [HTTP/2 support](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quelles sont les ressources prises en charge dans un pool backend ?

Consultez [Ressources principales prises en charge](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pools).

### <a name="in-what-regions-is-application-gateway-available"></a>Dans quelles régions Application Gateway est-il disponible ?

Application Gateway est disponible dans toutes les régions de la version globale d’Azure. Il est également disponible dans [Azure - Chine 21Vianet](https://www.azure.cn/) et [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>S’agit-il d’un déploiement dédié à mon abonnement ou est-il partagé entre les clients ?

Application Gateway est un déploiement dédié dans votre réseau virtuel.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway prend-il en charge la redirection HTTP vers HTTPS ?

La redirection est prise en charge. Consultez [Vue d’ensemble de la redirection Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Dans quel ordre les écouteurs sont-ils traités ?

Consultez l'[ordre de traitement des écouteurs](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Où puis-je trouver l’adresse IP et le DNS d’Application Gateway ?

Si vous utilisez une adresse IP publique en tant que point de terminaison, vous trouverez les informations d’adresse IP et de DNS dans la ressource d’adresse IP publique. Elles sont également disponibles sur la page Vue d'ensemble de la passerelle d’application. Si vous utilisez des adresses IP internes, ces informations se trouvent dans la page Vue d'ensemble.

Pour la référence SKU v2, ouvrez la ressource IP publique et sélectionnez **Configuration**. Le champ **Étiquette du nom DNS (facultatif)** est disponible pour configurer le nom DNS.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quels sont les paramètres du délai de maintien de connexion et du délai d’inactivité TCP ?

Le *délai d’expiration Keep-Alive* régit la durée pendant laquelle Application Gateway attend qu’un client envoie une autre requête HTTP sur une connexion permanente avant de la réutiliser ou de la fermer. Le *délai d’inactivité TCP* régit la durée pendant laquelle une connexion TCP est maintenue ouverte en cas d’absence d’activité. 

Le *délai d’expiration Keep-Alive* est de 120 secondes dans la référence SKU Application Gateway v1 et de 75 secondes dans la référence SKU v2. Par défaut, le *délai d’inactivité TCP* est de 4 minutes sur l’adresse IP virtuelle du serveur frontal des SKU v1 et v2 d’Application Gateway. Vous ne pouvez pas modifier ces valeurs.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L’adresse IP ou le nom DNS changent-ils pendant la durée de vie d’Application Gateway ?

Dans la référence SKU Application Gateway V1, l’adresse IP virtuelle peut changer si vous arrêtez et démarrez la passerelle d’application. Le nom DNS associé à la passerelle d’application, quant à lui, ne change pas pendant toute la durée de vie de la passerelle. Le nom DNS ne changeant pas, vous devez utiliser un alias CNAME et le faire pointer vers l’adresse DNS de la passerelle d’application. Dans la référence SKU Application Gateway V2, vous pouvez définir l’adresse IP comme étant statique, de sorte que l’adresse IP et le nom DNS ne changent pas pendant la durée de vie de la passerelle d’application. 

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway prend-il en charge les adresses IP statiques ?

Oui. La référence SKU v2 d’Application Gateway prend en charge les adresses IP publiques statiques. La référence SKU v1 quant à elle prend en charge les adresses IP internes statiques.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway prend-il en charge plusieurs adresses IP publiques sur la passerelle ?

Une seule adresse IP publique est prise en charge sur une passerelle d’application.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quelle taille mon sous-réseau pour Application Gateway doit-il avoir ?

Consultez [Considérations relatives à la taille du sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Puis-je déployer plusieurs ressources Application Gateway dans un seul sous-réseau ?

Oui. En plus de plusieurs instances d’un déploiement Application Gateway donné, vous pouvez configurer une autre ressource Application Gateway unique dans un sous-réseau existant qui contient une autre ressource Application Gateway.

Un seul sous-réseau ne peut pas prendre en charge les références (SKU) v2 et v1 d’Application Gateway.

### <a name="does-application-gateway-v2-support-user-defined-routes-udr"></a>Application Gateway v2 prend-il en charge les itinéraires définis par l’utilisateur (UDR) ?

Oui, mais uniquement dans des scénarios spécifiques. Pour plus d’informations, consultez [Présentation de la configuration d’Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway prend-il en charge les en-têtes x-forwarded-for ?

Oui. Consultez [Modifications apportées à une requête](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Combien de temps faut-il pour déployer une instance Application Gateway ? Mon instance Application Gateway continue-t-elle de fonctionner lorsqu’elle est mise à jour ?

La configuration des déploiements de nouvelles références SKU v1 d’Application Gateway peut prendre jusqu’à 20 minutes. Les modifications apportées à la taille ou au nombre des instances n’entraînent pas d’interruption, et la passerelle reste active pendant ce temps.

Pour la plupart des déploiements qui utilisent la référence SKU v2, le provisionnement prend environ 6 minutes. Toutefois, cette opération peut prendre plus de temps en fonction du type de déploiement. Par exemple, les déploiements sur plusieurs zones de disponibilité avec de nombreuses instances peuvent prendre plus de 6 minutes. 

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Puis-je utiliser Exchange Server en tant que serveur principal avec Application Gateway ?

Non. Application Gateway ne prend pas en charge les protocoles de messagerie tels que SMTP, IMAP et POP3.

### <a name="is-there-guidance-available-to-migrate-from-the-v1-sku-to-the-v2-sku"></a>Existe-t-il des instructions pour migrer de la référence SKU v1 vers la référence SKU v2 ?

Oui. Pour plus d'informations, consultez [Migrer Azure Application Gateway et le pare-feu d’applications web de v1 à v2](migrate-v1-v2.md).

### <a name="will-the-application-gateway-v1-sku-continue-to-be-supported"></a>Le support de la référence SKU Application Gateway v1 va-t-il continuer ?

Oui. Le support de la référence SKU Application Gateway v1 continue d’être assuré. Cependant, il est fortement recommandé de passer à v2 pour tirer parti des mises à jour des fonctionnalités de cette référence SKU. Pour plus d’informations, consultez [Application Gateway v2 avec mise à l’échelle automatique et redondance interzone](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-application-gateway-v2-support-proxying-requests-with-ntlm-authentication"></a>Application Gateway v2 prend-il en charge les demandes de proxy avec l’authentification NTLM ?

Non. Application Gateway v2 ne prend pas encore en charge les demandes de proxy avec l’authentification NTLM.

### <a name="does-application-gateway-affinity-cookie-support-samesite-attribute"></a>Le cookie d’affinité d’Application Gateway prend-il en charge l’attribut SameSite ?
Oui, la [mise à jour v80](https://chromiumdash.appspot.com/schedule) du [navigateur Chromium](https://www.chromium.org/Home) a permis que les cookies HTTP sans attribut SameSite soient traités comme SameSite=Lax. Cela signifie que le cookie d’affinité d’Application Gateway ne sera pas envoyé par le navigateur dans un contexte tiers. 

Pour prendre en charge ce scénario, Application Gateway injecte un autre cookie appelé *ApplicationGatewayAffinityCORS* en plus du cookie *ApplicationGatewayAffinity* existant.  Ces cookies sont similaires, mais le cookie *ApplicationGatewayAffinityCORS* a deux attributs supplémentaires qui lui sont ajoutés : *SameSite=None; Secure*. Ces attributs maintiennent les sessions rémanentes même pour les requêtes cross-origin. Pour plus d’informations, consultez la [section Affinité basée sur les cookies](configuration-overview.md#cookie-based-affinity).

## <a name="performance"></a>Performances

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Comment Application Gateway prend-il en charge la haute disponibilité et la scalabilité ?

La référence SKU v1 d’Application Gateway prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux instances ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir qu'elles n’échouent pas toutes en même temps. La référence SKU v1 prend en charge la scalabilité en ajoutant plusieurs instances de la même passerelle pour partager la charge.

La référence SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Comment puis-je obtenir un scénario DR dans les centres de données à l'aide d'Application Gateway ?

Utilisez Traffic Manager pour répartir le trafic entre plusieurs instances Application Gateway dans différents centres de données.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway prend-il en charge la mise à l'échelle automatique ?

Oui. La référence SKU v2 d’Application Gateway prend en charge la mise à l’échelle automatique. Pour plus d’informations, consultez [Application Gateway avec mise à l’échelle automatique et redondance interzone](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-or-automatic-scale-up-or-scale-down-cause-downtime"></a>L’opération de scale-up ou scale-down manuelle ou automatique entraîne-t-elle un temps d’arrêt ?

Non. Aucun temps d’arrêt n’a lieu, les instances sont réparties entre les domaines de mise à niveau et les domaines d’erreur.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway prend-il en charge le drainage de connexion ?

Oui. Vous pouvez configurer le drainage de connexion afin de modifier des membres au sein d’un pool principal sans interrompre le service. Pour plus d’informations, consultez la [section relative au drainage des connexions d’Application Gateway](features.md#connection-draining).

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Puis-je passer d’une taille moyenne à une grande taille d’instance sans interruption de service ?

Oui.

## <a name="configuration"></a>Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway est-il toujours déployé dans un réseau virtuel ?

Oui. Application Gateway est toujours déployé dans un sous-réseau de réseau virtuel. Ce sous-réseau ne peut contenir que des instances Application Gateway. Pour plus d’informations, consultez [Exigences de réseau virtuel et de sous-réseau](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway peut-il communiquer avec des instances en dehors de son réseau virtuel ou de son abonnement ?

Sous réserve de disposer d'une connectivité IP, Application Gateway peut communiquer avec des instances en dehors du réseau virtuel dans lequel il se trouve. Application Gateway peut également communiquer avec des instances extérieures à l'abonnement dans lequel il se trouve. Si vous envisagez d'utiliser des adresses IP internes en tant que membres de pool de back-ends, utilisez le [Peering de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) ou la [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Puis-je déployer autre chose dans le sous-réseau de la passerelle d’application ?

Non. Mais vous pouvez déployer d’autres passerelles d’application dans le sous-réseau.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Les groupes de sécurité réseau sont-ils pris en charge dans le sous-réseau de la passerelle d’application ?

Consultez [Groupes de sécurité réseau dans le sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Le sous-réseau de la passerelle d'application prend-il en charge les routes définies par l’utilisateur ?

Consultez [Les routes définies par l’utilisateur prises en charge dans le sous-réseau Application Gateway](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quelles sont les limites d’Application Gateway ? Puis-je augmenter ces limites ?

Consultez [Limites d’Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Puis-je utiliser simultanément Application Gateway pour le trafic externe et interne ?

Oui. Application Gateway peut avoir une adresse IP interne et une adresse IP externe par passerelle d’application.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway prend-il en charge le peering de réseaux virtuels ?

Oui. Le peering de réseaux virtuels permet d'équilibrer la charge du trafic dans d'autres réseaux virtuels.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Puis-je communiquer avec les serveurs locaux lorsqu’ils sont connectés via ExpressRoute ou des tunnels VPN ?

Oui, tant que le trafic est autorisé.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool back-end peut-il servir plusieurs applications sur des ports différents ?

L’architecture orientée microservices est prise en charge. Pour sonder différents ports, vous devez configurer plusieurs paramètres HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Les sondes personnalisées prennent-elles en charge les caractères génériques ou les expressions régulières sur les données de réponse ?

Non. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Comment les règles d'acheminement sont-elles traitées dans Application Gateway ?

Consultez [Ordre de traitement des règles](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>À quoi correspond le champ Hôte pour les sondes personnalisées ?

Le champ hôte Spécifie le nom auquel envoyer la sonde lorsque vous avez configuré plusieurs sites sur Application Gateway. Sinon, utilisez « 127.0.0.1 ». Cette valeur est différente du nom d’hôte de la machine virtuelle. Son format est \<protocol\>://\<host\>:\<port\>\<path\>.

### <a name="can-i-allow-application-gateway-access-to-only-a-few-source-ip-addresses"></a>Puis-je autoriser l’accès d’Application Gateway à quelques adresses IP sources uniquement ?

Oui. Consultez [Restreindre l’accès à des adresses IP sources spécifiques](https://docs.microsoft.com/azure/application-gateway/configuration-overview#allow-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Puis-je utiliser le même port pour les écouteurs publics et privés ?

Non.

### <a name="does-application-gateway-support-ipv6"></a>Application Gateway prend-il en charge IPv6 ?

Application Gateway v2 ne prend pas en charge IPv6 pour le moment. Il peut fonctionner dans un réseau virtuel à double pile à l’aide de IPv4 uniquement, mais le sous-réseau de passerelle doit être uniquement en IPv4. Application Gateway v1 ne prend pas en charge la double pile Réseaux virtuels. 

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Comment utiliser Application Gateway v2 avec uniquement une adresse IP frontend privée ?

Application Gateway v2 ne prend actuellement pas en charge le mode IP privé. Cette méthode prend en charge les combinaisons suivantes :
* adresse IP privée et adresse IP publique
* adresse IP publique uniquement

Toutefois, si vous souhaitez utiliser Application Gateway v2 avec uniquement une adresse IP privée, vous pouvez suivre le processus ci-dessous :
1. Créer une Application Gateway avec une adresse IP frontend publique et privée
2. Ne créez aucun écouteur pour l’adresse IP de serveur frontend public. Application Gateway n’écoute aucun trafic sur l’adresse IP publique si aucun écouteur n’est créé pour celui-ci.
3. Créez et associez un [groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview) pour le sous-réseau Application Gateway avec la configuration suivante dans l’ordre de priorité :
    
    a. Autorisez le trafic de la source en tant que balise de service **GatewayManager** et de la destination en tant que **N’importe laquelle** et du port de destination en tant que **65200-65535**. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par l’authentification par certificat. Les entités externes, y compris les administrateurs d’utilisateurs de la passerelle, ne peuvent pas initier de modifications sur ces points de terminaison sans que les certificats appropriés soient en place.
    
    b. Autorisez le trafic de la source en tant que balise de service **AzureLoadBalancer** et de la destination en tant que **N’importe laquelle**.
    
    c. Refusez tout trafic entrant à partir de la source en tant que balise de service **Internet** et la destination et port de destination en tant que **N’importe laquelle**. Donnez à cette règle la *priorité la plus faible* dans les règles de trafic entrant
    
    d. Conserver les règles par défaut, comme autoriser les VirtualNetwork entrants, afin que l’accès à l’adresse IP privée ne soit pas bloqué
    
    e. La connectivité Internet sortante ne peut pas être bloquée. Dans le cas contraire, vous rencontrerez des problèmes avec la journalisation, les mesures, etc.

Exemple de configuration de groupe de sécurité réseau pour un accès d’adresse IP privée uniquement : ![Configuration Application Gateway v2 NSG pour l’accès IP privé uniquement](./media/application-gateway-faq/appgw-privip-nsg.png)

## <a name="configuration---tls"></a>Configuration - TLS

### <a name="what-certificates-does-application-gateway-support"></a>Quels sont les certificats pris en charge par Application Gateway ?

Application Gateway prend en charge les certificats auto-signés, les certificats d’autorité de certification (CA), les certificats de validation étendue (EV) et les certificats génériques.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Quelles sont les suites de chiffrement prises en charge par Application Gateway ?

Application Gateway prend en charge les suites de chiffrement ci-dessous. 

- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

Pour savoir comment personnaliser les options TLS, consultez [Configurer les versions de stratégie TLS et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway prend-il en charge le nouveau chiffrement du trafic sur le back-end ?

Oui. Application Gateway prend en charge le déchargement TLS et TLS de bout en bout, qui chiffre à nouveau le trafic vers le serveur principal.

### <a name="can-i-configure-tls-policy-to-control-tls-protocol-versions"></a>Puis-je configurer la stratégie TLS de manière à contrôler les versions du protocole TLS ?

Oui. Vous pouvez configurer Application Gateway pour refuser TLS1.0, TLS1.1 et TLS1.2. Par défaut, SSL 2.0 et 3.0 sont déjà désactivés et ne sont pas configurables.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Puis-je configurer les suites de chiffrement et l’ordre de la stratégie ?

Oui. Dans Application Gateway, vous pouvez [configurer des suites de chiffrement](application-gateway-ssl-policy-overview.md). Pour définir une stratégie personnalisée, activez au moins une des suites de chiffrement suivantes. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway utilise SHA256 pour la gestion des serveurs principaux.

### <a name="how-many-tlsssl-certificates-does-application-gateway-support"></a>Combien de certificats TLS/SSL Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu'à 100 certificats TLS/SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Combien de certificats d’authentification pour le nouveau chiffrement du back-end Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu’à 100 certificats d’authentification.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway s’intègre-t-il en mode natif à Azure Key Vault ?

La référence SKU v2 d’Application Gateway prend en charge Key Vault. Pour plus d'informations, consultez [Arrêt de TLS avec des certificats Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Comment configurer des écouteurs HTTPS pour les sites .com et .net ? 

Pour un acheminement (basé sur l'hôte) sur plusieurs domaines, vous pouvez créer des écouteurs multisites, configurer des écouteurs utilisant le protocole HTTPS et associer les écouteurs aux règles d'acheminement. Pour plus d’informations, consultez [Hébergement de plusieurs sites à l'aide d'Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

### <a name="can-i-use-special-characters-in-my-pfx-file-password"></a>Puis-je utiliser des caractères spéciaux dans le mot de passe de mon fichier. pfx ?

Non, utilisez uniquement des caractères alphanumériques dans le mot de passe de votre fichier. pfx.

## <a name="configuration---ingress-controller-for-aks"></a>Configuration - Contrôleur d’entrée pour AKS

### <a name="what-is-an-ingress-controller"></a>Qu’est-ce qu’un contrôleur d’entrée ?

Kubernetes permet de créer des ressources `deployment` et `service` pour exposer en interne un groupe de pods dans le cluster. Pour exposer le même service en externe, une ressource [`Ingress`](https://kubernetes.io/docs/concepts/services-networking/ingress/) est définie, ce qui permet l’équilibrage de charge, l’arrêt TLS et l’hébergement virtuel basé sur le nom.
Pour satisfaire cette ressource `Ingress`, un contrôleur d’entrée est nécessaire, qui écoute les modifications apportées aux ressources `Ingress` et configure les stratégies d’équilibreur de charge.

Le contrôleur d’entrée Application Gateway permet l’utilisation d’[Azure Application Gateway](https://azure.microsoft.com/services/application-gateway/) en tant qu’entrée pour un service [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/), également appelé un cluster AKS.

### <a name="can-a-single-ingress-controller-instance-manage-multiple-application-gateways"></a>Une seule instance de contrôleur d’entrée peut-elle gérer plusieurs Application Gateways ?

Actuellement, une instance de contrôleur d’entrée ne peut être associée qu’à une seule Application Gateway.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quels types de journaux Application Gateway fournit-il ?

Application Gateway fournit trois types de journaux : 

* **ApplicationGatewayAccessLog** : le journal d’accès contient toutes les requêtes envoyées au serveur frontal de la passerelle d’application. Les données incluent l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, et les octets d’entrée et de sortie. Il contient un enregistrement par instance Application Gateway.
* **ApplicationGatewayPerformanceLog** : Le journal des performances capture les informations de performances de chaque instance d'Application Gateway. Parmi les informations consignées figurent notamment le débit en octets, le nombre total de requêtes traitées, le nombre de requêtes ayant échoué, ainsi que le nombre d'instances de serveur principal saines ou non saines.
* **ApplicationGatewayFirewallLog** : Pour les passerelles d’application que vous configurez avec le pare-feu d'applications web, le journal du pare-feu contient les requêtes consignées via le mode de détection ou le mode de prévention.

Tous les journaux sont collectés toutes les 60 secondes. Pour plus d’informations, consultez [Intégrité du serveur principal, journaux de diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Comment savoir si les membres de mon pool back-end sont intègres ?

Pour vérifier leur intégrité, utilisez la cmdlet PowerShell `Get-AzApplicationGatewayBackendHealth` ou le portail. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Quelle est la stratégie de rétention des journaux de diagnostic ?

Les journaux de diagnostic sont envoyés au compte de stockage du client. Les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un Event Hub ou à des journaux d’activité Azure Monitor. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Comment puis-je obtenir des journaux d’audit pour Application Gateway ?

Dans le portail, dans le panneau du menu de la passerelle d’application, sélectionnez **Journal d’activité** pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Puis-je définir des alertes avec Application Gateway ?

Oui. Dans Application Gateway, les alertes sont configurées sur les métriques. Pour plus d’informations, consultez [Métriques Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics) et [Recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Comment analyser les statistiques de trafic pour Application Gateway ?

Vous pouvez afficher et analyser les journaux d’accès de plusieurs façons. Utilisez les journaux d'activité Azure Monitor, Excel, Power BI, etc.

Vous pouvez également utiliser un modèle Resource Manager qui installe et exécute le célèbre analyseur de journal d’activité [GoAccess](https://goaccess.io/) pour les journaux d’accès Application Gateway. GoAccess fournit des statistiques de trafic HTTP précieuses telles que les visiteurs uniques, les fichiers demandés, les hôtes, les systèmes d’exploitation, les navigateurs ou les codes d’état HTTP. Pour plus d’informations, consultez le [fichier Lisez-moi dans le dossier de modèles Resource Manager dans GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Dans quelles circonstances, l'intégrité d'un serveur principal peut-elle indiquer un état inconnu ?

En règle générale, vous constatez un état inconnu lorsque l'accès au serveur principal est bloqué par un groupe de sécurité réseau (NSG), un DNS personnalisé ou un routage défini par l'utilisateur (UDR) sur le sous-réseau d'Application Gateway. Pour plus d’informations, consultez [Intégrité du serveur principal, journalisation des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

### <a name="is-there-any-case-where-nsg-flow-logs-wont-show-allowed-traffic"></a>Existe-t-il un cas où les journaux de flux NSG n’affichent pas le trafic autorisé ?

Oui. Si votre configuration correspond au scénario suivant, vous ne verrez pas le trafic autorisé dans vos journaux de flux NSG :
- Vous avez déployé Application Gateway v2
- Vous avez un NSG sur le sous-réseau Application Gateway
- Vous avez activé les journaux de flux NSG sur ce groupe de sécurité réseau

### <a name="how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address"></a>Comment utiliser Application Gateway v2 avec uniquement une adresse IP frontend privée ?

Application Gateway v2 ne prend actuellement pas en charge le mode IP privé. Cette méthode prend en charge les combinaisons suivantes :
* adresse IP privée et adresse IP publique
* adresse IP publique uniquement

Toutefois, si vous souhaitez utiliser Application Gateway v2 avec uniquement une adresse IP privée, vous pouvez suivre le processus ci-dessous :
1. Créer une Application Gateway avec une adresse IP frontend publique et privée
2. Ne créez aucun écouteur pour l’adresse IP de serveur frontend public. Application Gateway n’écoute aucun trafic sur l’adresse IP publique si aucun écouteur n’est créé pour celui-ci.
3. Créez et associez un [groupe de sécurité réseau](https://docs.microsoft.com/azure/virtual-network/security-overview) pour le sous-réseau Application Gateway avec la configuration suivante dans l’ordre de priorité :
    
    a. Autorisez le trafic de la source en tant que balise de service **GatewayManager** et de la destination en tant que **N’importe laquelle** et du port de destination en tant que **65200-65535**. Cette plage de ports est nécessaire pour la communication avec l’infrastructure Azure. Ces ports sont protégés (verrouillés) par l’authentification par certificat. Les entités externes, y compris les administrateurs d’utilisateurs de la passerelle, ne peuvent pas initier de modifications sur ces points de terminaison sans que les certificats appropriés soient en place.
    
    b. Autorisez le trafic de la source en tant que balise de service **AzureLoadBalancer** et le port destination en tant que **N’importe lequel**
    
    c. Refusez tout trafic entrant à partir de la source en tant que balise de service **Internet** et le port de destination en tant que **N’importe lequel**. Donnez à cette règle la *priorité la plus faible* dans les règles de trafic entrant
    
    d. Conserver les règles par défaut, comme autoriser les VirtualNetwork entrants, afin que l’accès à l’adresse IP privée ne soit pas bloqué
    
    e. La connectivité Internet sortante ne peut pas être bloquée. Dans le cas contraire, vous rencontrerez des problèmes avec la journalisation, les mesures, et ainsi de suite.

Exemple de configuration de groupe de sécurité réseau pour un accès d’adresse IP privée uniquement : ![Configuration Application Gateway v2 NSG pour l’accès IP privé uniquement](./media/application-gateway-faq/appgw-privip-nsg.png)


## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Application Gateway, consultez [Qu’est-ce qu’Azure Application Gateway ?](overview.md)
