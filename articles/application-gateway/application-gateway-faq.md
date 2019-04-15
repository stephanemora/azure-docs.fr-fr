---
title: Forum aux questions pour Azure Application Gateway
description: Cette page fournit des réponses aux questions les plus souvent posées sur Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/20/2019
ms.author: victorh
ms.openlocfilehash: c40f372d3574f940e475a6626f998adae37a6d61
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851152"
---
# <a name="frequently-asked-questions-for-application-gateway"></a>Forum aux questions pour Azure Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Généralités

### <a name="what-is-application-gateway"></a>Présentation de Application Gateway

Azure Application Gateway est un contrôleur de livraison d’applications (ADC) sous forme de service qui offre à vos applications plusieurs fonctionnalités d’équilibreur de charge de couche 7. Il fournit un service hautement disponible et évolutif, entièrement géré par Azure.

### <a name="what-features-does-application-gateway-support"></a>Quelles sont les fonctionnalités prises en charge par Application Gateway ?

Application Gateway prend en charge la mise à l’échelle automatique, le déchargement SSL et SSL de bout en bout, le pare-feu d’application web, l’affinité de session basée sur les cookies, le routage basé sur le chemin d’accès de l’URL, l’hébergement de plusieurs sites, et bien plus encore. Pour obtenir une liste complète des fonctionnalités prises en charge, voir [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

### <a name="what-is-the-difference-between-application-gateway-and-azure-load-balancer"></a>Quelle est la différence entre Application Gateway et Azure Load Balancer ?

Application Gateway est un équilibreur de charge de couche 7, ce qui signifie qu’il fonctionne avec uniquement le trafic web (HTTP/HTTPS/WebSocket/HTTP/2). Il prend en charge des fonctionnalités telles que la terminaison SSL, l’affinité de session basée sur les cookies et le tourniquet (round robin) pour le trafic d’équilibrage de charge. Load Balancer équilibre la charge du trafic au niveau de la couche 4 (TCP/UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quels sont les protocoles pris en charge par Application Gateway ?

Application Gateway prend en charge les protocoles HTTP, HTTPS, HTTP/2 et WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway prend-il en charge le protocole HTTP/2 ?

Consultez [prise en charge HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support) pour savoir comment Application gateway prend en charge le protocole HTTP/2.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quelles sont les ressources actuellement prises en charge dans le pool backend ?

Consultez [prise en charge des ressources principales](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool) pour savoir quelles ressources sont prises en charge par la passerelle d’Application.

### <a name="what-regions-is-the-service-available-in"></a>Dans quelles régions le service est-il disponible ?

Application Gateway est disponible dans toutes les régions de la version globale d’Azure. Il est également disponible dans [Azure - Chine 21Vianet](https://www.azure.cn/) et [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-a-dedicated-deployment-for-my-subscription-or-is-it-shared-across-customers"></a>S’agit-il d’un déploiement dédié à mon abonnement ou est-il partagé entre les clients ?

Application Gateway est un déploiement dédié dans votre réseau virtuel.

### <a name="is-http-https-redirection-supported"></a>La redirection HTTP->HTTPS est-elle prise en charge ?

La redirection est prise en charge. Pour en savoir plus, voir [Vue d’ensemble de la redirection Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Dans quel ordre les écouteurs sont-ils traités ?

Consultez [ordre de traitement des écouteurs](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-application-gateways-ip-and-dns"></a>Où puis-je trouver le DNS et l’adresse IP d’Application Gateway ?

Lorsque vous utilisez une adresse IP publique en tant que point de terminaison, ces informations sont disponibles dans la ressource d’adresse IP publique ou sur la page de présentation d’Application Gateway dans le portail. Si vous utilisez des adresses IP internes, les données se trouvent sur la page de présentation.

### <a name="what-is-keep-alive-timeout-and-tcp-idle-timeout-setting-on-application-gateway"></a>Qu’est le délai d’attente Keep-Alive et paramètre de délai d’inactivité TCP sur Application Gateway ?

Délai d’attente Keep-Alive sur la référence (SKU) v1 est 120 s. un délai d’attente Keep-Alive sur la référence (SKU) v2 est délai d’inactivité sec. 75 TCP correspond 4-min par défaut sur le serveur frontal d’adresse IP virtuelle de la passerelle d’Application.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>L’adresse IP ou le nom DNS changent-ils pendant la durée de vie d’Application Gateway ?

L’adresse IP virtuelle peut changer si la passerelle d’application est arrêtée et démarrée. Le nom DNS associé à la passerelle d’application ne change pas pendant toute la durée de vie de la passerelle. Pour cette raison, il est recommandé d’utiliser un alias CNAME et de le faire pointer vers l’adresse DNS de la passerelle d’application.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway prend-il en charge les adresses IP statiques ?

Oui. La référence SKU v2 d’Application Gateway prend en charge les adresses IP publiques statiques. La référence SKU v1 quant à elle prend en charge les adresses IP internes statiques.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway prend-il en charge plusieurs adresses IP publiques sur la passerelle ?

Une seule adresse IP publique est prise en charge sur une passerelle d’application.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quelle taille mon sous-réseau pour Application Gateway doit-il avoir ?

Consultez [considérations de taille de sous-réseau de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet) pour comprendre la taille de sous-réseau requise pour votre déploiement.

### <a name="q-can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Q. Puis-je déployer plusieurs ressources Application Gateway dans un seul sous-réseau ?

Oui, en plus d’avoir plusieurs instances d’un déploiement Application Gateway donné, vous pouvez configurer une autre ressource Application Gateway unique dans un sous-réseau existant qui contient une autre ressource Application Gateway.

La combinaison de Standard_v2 et Standard Application Gateway sur le même sous-réseau n’est pas prise en charge.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway prend-il en charge les en-têtes x-forwarded-for ?

Oui. Consultez [modifications apportées à la demande](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request) pour en savoir plus sur les en-têtes x-forwarded-for pris en charge par la passerelle d’Application.

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-does-my-application-gateway-still-work-when-being-updated"></a>Combien de temps faut-il pour déployer une Application Gateway ? Mon service Application Gateway continue-t-il de fonctionner lorsqu’il est mis à jour  ?

La configuration des déploiements de nouvelles références SKU v1 d’Application Gateway peut prendre jusqu’à 20 minutes. Les modifications apportées à la taille et au nombre des instances n’entraînent pas d’interruption, et la passerelle reste active pendant ce temps.

L’approvisionnement des déploiements de références SKU V2 peut quant à lui prendre de cinq à six minutes environ.

### <a name="can-exchange-server-be-used-as-backend-with-application-gateway"></a>Exchange server peut être utilisé en tant que serveur principal avec la passerelle d’Application ?

Non, Application Gateway ne prend pas en charge les protocoles de messagerie tels que SMTP, IMAP et POP3. 

## <a name="performance"></a>Performances

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Comment Application Gateway prend-il en charge la haute disponibilité et la scalabilité ?

La référence SKU v1 d’Application Gateway prend en charge les scénarios de haute disponibilité lorsque vous avez deux instances déployées ou plus. Azure distribue ces instances entre les domaines de mise à jour et d’erreur pour garantir que toutes les instances n’échouent pas en même temps. La référence SKU v1 prend en charge la scalabilité en ajoutant plusieurs instances de la même passerelle pour partager la charge.

La référence SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez une redondance de zone, les instances les plus récentes sont également réparties sur les zones de disponibilité pour offrir une résilience zonale en cas d’échec.

### <a name="how-do-i-achieve-dr-scenario-across-data-centers-with-application-gateway"></a>Comment puis-je obtenir le scénario de reprise d’activité après sinistre dans les centres de données avec Application Gateway ?

Les clients peuvent utiliser Traffic Manager pour répartir le trafic entre plusieurs Application Gateway dans différents centres de données.

### <a name="is-autoscaling-supported"></a>La mise à l’échelle automatique est-elle prise en charge ?

Oui. La référence SKU v2 d’Application Gateway prend en charge la mise à l’échelle automatique. Pour plus d’informations, voir [Passerelle d’application redondante interzone et avec mise à l’échelle automatique (préversion publique)](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-updown-cause-downtime"></a>Les opérations manuelles de scale up/down causent-elles des temps d’arrêt ?

Il n’y a pas de temps d’arrêt. Aucun temps d’arrêt n’a lieu, les instances sont réparties entre les domaines de mise à niveau et les domaines d’erreur.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway prend-il en charge le drainage de connexion ?

Oui. Vous pouvez configurer le drainage de connexion afin de modifier des membres au sein d’un pool principal sans interrompre le service. Ainsi, les connexions existantes continuent d’être envoyées à leur destination précédente jusqu'à ce que cette connexion soit fermée ou qu’un délai configurable expire. Ce drainage de connexion attend uniquement la fin des connexions actuellement en transit. Application Gateway ne connaît pas l’état de la session d’application.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Puis-je passer d’une taille moyenne à une grande taille d’instance sans interruption de service ?

Oui, Azure distribue les instances entre les domaines de mise à jour et d’erreur pour garantir que toutes les instances n’échouent pas en même temps. Application Gateway prend en charge la mise à l’échelle en ajoutant plusieurs instances de la même passerelle pour partager la charge.

## <a name="configuration"></a>Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway est-il toujours déployé dans un réseau virtuel ?

Oui, Application Gateway est toujours déployé dans un sous-réseau de réseau virtuel. Ce sous-réseau ne peut contenir que des Application Gateway. Consultez [les exigences réseau et sous-réseau virtuels](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet) pour comprendre les considérations de sous-réseau pour Application Gateway.

### <a name="can-application-gateway-communicate-with-instances-outside-of-the-virtual-network-it-is-in-or-outside-of-the-subscription-it-is-in"></a>Application Gateway peut communiquer avec des instances en dehors du réseau virtuel, dans qu'il se trouve ou en dehors de l’abonnement ?

Application Gateway peut communiquer avec les instances en dehors du réseau virtuel qu’il est ou en dehors de l’abonnement, qu'il se trouve dans, tant qu’il existe une connectivité de l’adresse IP. Si vous prévoyez d’utiliser des adresses IP internes en tant que membres du pool backend, il vous faudra utiliser [VNET Peering](../virtual-network/virtual-network-peering-overview.md) ou [une passerelle VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Puis-je déployer autre chose dans le sous-réseau de la passerelle d’application ?

Non, mais vous pouvez déployer d’autres passerelles d’application dans le sous-réseau.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Les groupes de sécurité réseau sont-ils pris en charge dans le sous-réseau de la passerelle d’application ?

Consultez [des restrictions de groupes de sécurité réseau sur le sous-réseau de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet) en savoir plus sur les groupes de sécurité réseau pris en charge sur le sous-réseau de passerelle d’application.

### <a name="are-user-defined-routes-supported-on-the-application-gateway-subnet"></a>Les routes définies par l’utilisateur sont-elles prises en charge sur le sous-réseau d’Application Gateway ?

Consultez [restrictions des itinéraires définis par l’utilisateur](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet) pour en savoir plus sur les itinéraires définis par l’utilisateur pris en charge sur le sous-réseau de passerelle d’application.

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quelles sont les limites d’Application Gateway ? Puis-je augmenter ces limites ?

Pour connaître les limites, voir [Limites d’Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-use-application-gateway-for-both-external-and-internal-traffic-simultaneously"></a>Puis-je utiliser Application Gateway pour le trafic externe et interne simultanément ?

Oui. Application Gateway peut avoir une adresse IP interne et une adresse IP externe par passerelle d’application.

### <a name="is-vnet-peering-supported"></a>VNET Peering est-il pris en charge ?

Oui, VNET Peering est pris en charge et est utile pour équilibrer la charge du trafic des autres réseaux virtuels.

### <a name="can-i-talk-to-on-premises-servers-when-they-are-connected-by-expressroute-or-vpn-tunnels"></a>Puis-je communiquer avec les serveurs locaux lorsqu’ils sont connectés via ExpressRoute ou des tunnels VPN ?

Oui, tant que le trafic est autorisé.

### <a name="can-i-have-one-backend-pool-serving-many-applications-on-different-ports"></a>Puis-je avoir un pool back-end servant plusieurs applications sur des ports différents ?

L’architecture orientée microservices est prise en charge. Plusieurs paramètres HTTP doivent être configurés pour une sonde sur différents ports.

### <a name="do-custom-probes-support-wildcardsregex-on-response-data"></a>Les sondes personnalisées prennent-elles en charge les caractères génériques/les expressions régulières sur les données de réponse ?

Les sondes personnalisées ne prennent pas en charge les caractères génériques/les expressions régulières sur les données de réponse.

### <a name="how-are-rules-processed"></a>Comment les règles sont-elles traitées ?

Consultez [ordre des règles de traitement](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules) pour comprendre le fonctionnement des règles de routage sont des processus dans Application Gateway.

### <a name="what-does-the-host-field-for-custom-probes-signify"></a>À quoi correspond le champ Hôte pour les sondes personnalisées ?

Le champ Hôte indique le nom auquel envoyer la sonde. S’applique uniquement lorsque plusieurs sites sont configurés sur Application Gateway, sinon utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de machine virtuelle et se trouve au format suivant : \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>.

### <a name="can-i-whitelist-application-gateway-access-to-a-few-source-ips"></a>Puis-je autoriser quelques adresses IP de sources à accéder à Application Gateway ?

Oui. Consultez [restreindre l’accès à des adresses IP sources spécifiques](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips) pour comprendre comment vous assurer que dans la liste approuvée uniquement les adresses IP sources peuvent accéder à la passerelle d’Application.

### <a name="can-the-same-port-be-used-for-both-public-and-private-facing-listeners"></a>Le même port peut-il être utilisé pour les écouteurs publics et privés ?

Non, ce n’est pas pris en charge.

## <a name="configuration---ssl"></a>Configuration - SSL

### <a name="what-certificates-are-supported-on-application-gateway"></a>Quels sont les certificats pris en charge sur Application Gateway ?

Les certificats auto-signés, les certificats d’autorité de certification, les certificats de validation étendue et les génériques sont pris en charge.

### <a name="what-are-the-current-cipher-suites-supported-by-application-gateway"></a>Quelles sont les suites de chiffrement actuellement prises en charge par Application Gateway ?

Vous trouverez ci-dessous les suites de chiffrement actuellement prises en charge par Application Gateway. Pour apprendre à personnaliser les options SSL, voir [Configurer les versions de stratégie SSL et les suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

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

### <a name="does-application-gateway-also-support-re-encryption-of-traffic-to-the-backend"></a>Application Gateway prend-il également en charge le nouveau chiffrement du trafic sur le back-end ?

Oui, Application Gateway prend en charge le déchargement SSL et SSL de bout en bout, qui chiffre à nouveau le trafic vers le serveur principal.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Puis-je configurer la stratégie SSL pour gérer les versions du protocole SSL ?

Oui, vous pouvez configurer Application Gateway pour refuser TLS1.0, TLS1.1 et TLS1.2. SSL 2.0 et 3.0 sont déjà désactivés par défaut et ne sont pas configurables.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Puis-je configurer les suites de chiffrement et l’ordre de la stratégie ?

Oui, la [configuration des suites de chiffrement](application-gateway-ssl-policy-overview.md) est prise en charge. Lorsque vous définissez une stratégie personnalisée, au moins une des suites de chiffrement suivantes doit être activée. Application Gateway utilise SHA256 pour la gestion des serveurs principaux.

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

### <a name="how-many-ssl-certificates-are-supported"></a>Quel est le nombre de certificats SSL pris en charge ?

Un maximum de 100 certificats SSL est pris en charge.

### <a name="how-many-authentication-certificates-for-backend-re-encryption-are-supported"></a>Quel est le nombre de certificats d’authentification pour le nouveau chiffrement du back-end pris en charge ?

10 certificats d’authentification maximum sont pris en charge, dont 5 par défaut.

### <a name="does-application-gateway-integrate-with-azure-key-vault-natively"></a>Application Gateway s’intègre-t-il à Azure Key Vault en mode natif ?

Non, il n’est pas intégré à Azure Key Vault.

### <a name="how-to-configure-https-listeners-for-com-and-net-sites"></a>Comment configurer les écouteurs HTTPS pour les sites .com et .net ? 

Pour plusieurs domaine routage basé sur (basée sur l’hôte), vous pouvez créer des écouteurs multisites, sélectionnez HTTPS comme protocole dans la configuration de l’écouteur et associer les écouteurs avec les règles de routage. Pour plus d’informations, consultez [hébergement de plusieurs sites avec Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuration - pare-feu d’applications Web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-with-the-standard-sku"></a>La référence SKU de pare-feu d’application web propose-t-elle toutes les fonctionnalités disponibles avec la référence SKU standard ?

Oui, le pare-feu d’application web prend en charge toutes les fonctionnalités de la référence Standard.

### <a name="what-is-the-crs-version-application-gateway-supports"></a>Quelle est la version CRS qu’Application Gateway prend en charge ?

Application Gateway prend en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Comment puis-je surveiller le pare-feu d’application web ?

Le pare-feu d’application web est surveillé via la journalisation des diagnostics. Vous trouverez plus d’informations sur la journalisation des diagnostics dans l’article [Intégrité du serveur principal, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Est-ce que le mode de détection bloque le trafic ?

Non, le mode de détection journalise uniquement le trafic, ce qui a déclenché une règle de pare-feu d’application web.

### <a name="can-i-customize-waf-rules"></a>Puis-je personnaliser les règles de pare-feu d’application web ?

Oui, les règles de pare-feu d’application web sont personnalisables. Pour plus d’informations, consultez [Personnaliser les règles et groupes de règles WAF](application-gateway-customize-waf-rules-portal.md)

### <a name="what-rules-are-currently-available"></a>Quelles sont les règles actuellement disponibles ?

Le pare-feu d’application web prend actuellement en charge CRS  [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et [3.0](application-gateway-crs-rulegroups-rules.md#owasp30), qui fournit la sécurité de ligne de base pour la plupart des 3.0 premières vulnérabilités identifiées par l’OWASP, présentées ici [OWASP top 10 Vulnerabilities](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013) (10 premières vulnérabilités identifiées par l’OWASP)

* Protection contre les injections de code SQL

* Protection de l’exécution de script de site à site

* Protection contre les attaques web courante comme l’injection de commande, les dissimulations de requêtes HTTP, la séparation de réponse HTTP et les attaques RFI (Remote File Inclusion)

* Protection contre les violations de protocole HTTP

* Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept

* Protection contre les robots, les crawlers et les scanneurs

* Détection des erreurs de configuration d’application courantes (par exemple, Apache, IIS, etc.)

### <a name="does-waf-also-support-ddos-prevention"></a>Le pare-feu d’application web prend-il également en charge la prévention DDoS ?

Oui. Vous pouvez activer le service de protection DDos sur le réseau virtuel sur lequel la passerelle d’application est déployée. Ainsi, l’adresse IP virtuelle de la passerelle d’application est également protégée à l’aide du service Azure DDoS Protection.

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-logs-are-available-with-application-gateway"></a>Quels sont les types de journaux d’activité disponibles avec Application Gateway ?

Trois journaux d’activité sont disponibles pour Application Gateway. Pour plus d’informations sur ces journaux d’activité et les autres fonctionnalités de diagnostic, voir [Intégrité backend, journaux d’activité des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

* **ApplicationGatewayAccessLog** : le journal d’accès contient toutes les requêtes envoyées au serveur frontal de la passerelle d’application. Les données incluent l’adresse IP de l’appelant, l’URL demandée, la latence de réponse, le code de retour, les octets d’entrée et de sortie. Le journal d’accès est collecté toutes les 300 secondes. Ce journal contient un enregistrement par instance d’une passerelle d’application.
* **ApplicationGatewayPerformanceLog** : le journal des performances capture des informations sur les performances par instance, notamment le nombre total de demandes traitées, le débit en octets, le nombre total de demandes présentées, le nombre de demandes ayant échoué, le nombre d’instances du serveur principal correctes et incorrectes.
* **ApplicationGatewayFirewallLog** : le journal des pare-feux contient les demandes consignées via le mode de détection ou de prévention d’une passerelle d’application configuré avec un pare-feu d’application web.

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Comment savoir si les membres de mon pool back-end sont intègres ?

Vous pouvez utiliser l’applet de commande PowerShell `Get-AzApplicationGatewayBackendHealth` ou vérifier l’intégrité via le portail en consultant l’article [Intégrité backend, journalisation des diagnostics et métriques pour la passerelle Application Gateway](application-gateway-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Quelle est la stratégie de conservation sur les journaux de diagnostic ?

Les journaux de diagnostic circulent vers le compte de stockage des clients, et les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un hub d’événements ou à des journaux d’activité Azure Monitor. Pour plus d’informations, voir [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Comment puis-je obtenir des journaux d’audit pour Application Gateway ?

Les journaux d’audit sont disponibles pour Application Gateway. Dans le portail, cliquez sur **journal d’activité** dans le panneau de menu d’une passerelle d’application pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Puis-je définir des alertes avec Application Gateway ?

Oui. Application Gateway prend en charge les alertes. Les alertes sont configurées d’après les métriques utilisées. Pour en savoir plus sur les métriques Application Gateway, voir [Métriques Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics). Pour en savoir plus sur les alertes, voir [Réception de notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Comment analyser les statistiques de trafic pour Application Gateway ?

Vous pouvez consulter et analyser les journaux d’activité d’accès à l’aide de nombreux outils tels que les journaux d’activité Azure Monitor, Excel ou Power BI.

Nous avons également publié un modèle Resource Manager qui installe et exécute le célèbre analyseur de journal d’activité [GoAccess](https://goaccess.io/) pour les journaux d’activité d’accès Application Gateway. GoAccess fournit des statistiques de trafic HTTP précieuses telles que les visiteurs uniques, les fichiers demandés, les hôtes, les systèmes d’exploitation, les navigateurs ou les codes d’état HTTP. Pour plus d’informations, consultez le [fichier Lisez-moi dans le dossier de modèles Resource Manager dans GitHub](https://aka.ms/appgwgoaccessreadme).

### <a name="backend-health-returns-unknown-status-what-could-be-causing-this-status"></a>L’intégrité du back-end retourne un état inconnu, à quoi est dû cet état ?

La raison la plus courante est l’accès pour le serveur principal est bloqué par un groupe de sécurité réseau, serveur DNS personnalisé, ou vous avez un UDR sur le sous-réseau de passerelle d’application. Pour plus d’informations, voir [Intégrité du serveur principal, journalisation des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Application Gateway, consultez [Qu’est-ce qu’Azure Application Gateway ?](overview.md)
