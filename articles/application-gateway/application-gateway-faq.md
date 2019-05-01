---
title: Forum aux questions sur Azure Application Gateway
description: Trouvez des réponses aux questions fréquemment posées sur Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 72eaa18d570e85274efbea80f12e9a7301eecbfa
ms.sourcegitcommit: ed66a704d8e2990df8aa160921b9b69d65c1d887
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64947082"
---
# <a name="frequently-asked-questions-about-application-gateway"></a>Forum aux questions sur la passerelle d’Application

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general"></a>Généralités

### <a name="what-is-application-gateway"></a>Présentation de Application Gateway

Azure Application Gateway fournit une intègre application delivery controller (ADC) en tant que service. Il offre diverses fonctionnalités d’équilibrage de charge layer 7 pour vos applications. Ce service est hautement disponible, évolutif et entièrement géré par Azure.

### <a name="what-features-does-application-gateway-support"></a>Quelles sont les fonctionnalités prises en charge par Application Gateway ?

Application Gateway prend en charge la mise à l’échelle, le déchargement SSL et SSL de bout en bout, un pare-feu d’applications web (WAF), affinité de session basée sur les cookies, URL le routage basé sur le chemin d’accès, hébergement multisite et autres fonctionnalités. Pour obtenir une liste complète des fonctionnalités prises en charge, voir [Vue d’ensemble d’Application Gateway](application-gateway-introduction.md).

### <a name="how-do-application-gateway-and-azure-load-balancer-differ"></a>Comment diffèrent une passerelle d’Application et Azure Load Balancer ?

Application Gateway est un équilibreur de charge de couche 7, ce qui signifie qu’il fonctionne uniquement avec le trafic web (HTTP, HTTPS, WebSocket et HTTP/2). Il prend en charge des fonctionnalités telles que la terminaison SSL, affinité de session basée sur les cookies et tourniquet (Round Robin) pour l’équilibrage de charge le trafic. Charger un équilibreur de charge-équilibre le trafic au niveau de la couche 4 (TCP ou UDP).

### <a name="what-protocols-does-application-gateway-support"></a>Quels sont les protocoles pris en charge par Application Gateway ?

Application Gateway prend en charge les protocoles HTTP, HTTPS, HTTP/2 et WebSocket.

### <a name="how-does-application-gateway-support-http2"></a>Application Gateway prend-il en charge le protocole HTTP/2 ?

Consultez [prise en charge HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

### <a name="what-resources-are-supported-as-part-of-a-backend-pool"></a>Quelles ressources sont prises en charge dans le cadre d’un pool principal ?

Consultez [prise en charge des ressources principales](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#backend-pool).

### <a name="in-what-regions-is-application-gateway-available"></a>Application Gateway dans quelles régions est-il disponible ?

Application Gateway est disponible dans toutes les régions de la version globale d’Azure. Il est également disponible dans [Azure China 21Vianet](https://www.azure.cn/) et [Azure Government](https://azure.microsoft.com/overview/clouds/government/).

### <a name="is-this-deployment-dedicated-for-my-subscription-or-is-it-shared-across-customers"></a>Ce déploiement n’est dédié pour mon abonnement ou est-il partagé entre les clients ?

Application Gateway est un déploiement dédié dans votre réseau virtuel.

### <a name="does-application-gateway-support-http-to-https-redirection"></a>Application Gateway prend-il en charge la redirection HTTP vers HTTPS ?

La redirection est prise en charge. Consultez [vue d’ensemble de la redirection Application Gateway](application-gateway-redirect-overview.md).

### <a name="in-what-order-are-listeners-processed"></a>Dans quel ordre les écouteurs sont-ils traités ?

Consultez le [ordre de traitement d’écouteur](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-listeners).

### <a name="where-do-i-find-the-application-gateway-ip-and-dns"></a>Où trouver le DNS et IP de passerelle d’Application ?

Si vous utilisez une adresse IP publique comme point de terminaison, vous trouverez les informations d’adresse IP et DNS sur la ressource d’adresse IP publique. Ou la rechercher dans le portail, dans la page de vue d’ensemble de la passerelle d’application. Si vous utilisez des adresses IP internes, trouvez les informations sur la page de présentation.

### <a name="what-are-the-settings-for-keep-alive-timeout-and-tcp-idle-timeout"></a>Quels sont les paramètres de délai d’attente Keep-Alive et délai d’inactivité TCP ?

 Dans la référence (SKU) v1 de la passerelle d’Application, le délai d’attente Keep-Alive est de 120 secondes. Le délai d’attente Keep-Alive pour la référence SKU v2 est 75 secondes. Délai d’inactivité TCP est une valeur par défaut de 4 minutes sur le serveur frontal adresse IP virtuelle (VIP) de passerelle d’Application.

### <a name="does-the-ip-or-dns-name-change-over-the-lifetime-of-the-application-gateway"></a>Le nom DNS ou adresse IP change pendant la durée de vie de la passerelle d’application ?

L’adresse IP virtuelle peut changer si vous arrêtez et démarrez la passerelle d’application. Mais le nom DNS associé à la passerelle d’application ne change pas pendant la durée de vie de la passerelle. Étant donné que le nom DNS ne change pas, vous devez utiliser un alias CNAME et pointer vers l’adresse DNS de la passerelle d’application.

### <a name="does-application-gateway-support-static-ip"></a>Application Gateway prend-il en charge les adresses IP statiques ?

Oui, la référence SKU de passerelle d’Application v2 prend en charge les adresses IP publiques statiques. La référence SKU v1 quant à elle prend en charge les adresses IP internes statiques.

### <a name="does-application-gateway-support-multiple-public-ips-on-the-gateway"></a>Application Gateway prend-il en charge plusieurs adresses IP publiques sur la passerelle ?

Une passerelle d’application prend en charge qu’une seule adresse IP publique.

### <a name="how-large-should-i-make-my-subnet-for-application-gateway"></a>Quelle taille mon sous-réseau pour Application Gateway doit-il avoir ?

Consultez [considérations de taille de sous-réseau de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/configuration-overview#size-of-the-subnet).

### <a name="can-i-deploy-more-than-one-application-gateway-resource-to-a-single-subnet"></a>Puis-je déployer plusieurs ressources Application Gateway dans un seul sous-réseau ?

Oui. En plus de plusieurs instances d’un déploiement de passerelle d’Application donné, vous pouvez configurer une autre ressource de passerelle d’Application unique à un sous-réseau existant qui contient une autre ressource de passerelle d’Application.

Un seul sous-réseau ne peut pas prendre en charge Standard_v2 et passerelle d’Application Standard ensemble.

### <a name="does-application-gateway-support-x-forwarded-for-headers"></a>Application Gateway prend-il en charge les en-têtes x-forwarded-for ?

Oui. Consultez [Modifications à une demande](https://docs.microsoft.com/azure/application-gateway/how-application-gateway-works#modifications-to-the-request).

### <a name="how-long-does-it-take-to-deploy-an-application-gateway-will-my-application-gateway-work-while-its-being-updated"></a>Combien de temps faut-il pour déployer une passerelle d’application ? Mon application gateway fonctionnera, mais elle est en cours de mise à jour ?

La configuration des déploiements de nouvelles références SKU v1 d’Application Gateway peut prendre jusqu’à 20 minutes. Modifications apportées à la taille d’instance ou de nombre ne sont pas sans interruption, et la passerelle reste active pendant ce temps.

Mise en, les déploiements qui utilisent la référence (SKU) v2 peuvent prendre jusqu'à 6 minutes.

### <a name="can-i-use-exchange-server-as-a-backend-with-application-gateway"></a>Puis-je utiliser Exchange Server comme serveur principal avec Application Gateway ?

Non. Application Gateway ne prend pas en charge les protocoles de messagerie tels que SMTP, IMAP et POP3. 

## <a name="performance"></a>Performances

### <a name="how-does-application-gateway-support-high-availability-and-scalability"></a>Comment Application Gateway prend-il en charge la haute disponibilité et la scalabilité ?

La référence SKU de passerelle d’Application v1 prend en charge les scénarios de haute disponibilité lorsque vous avez déployé deux ou plusieurs instances. Azure distribue ces instances entre des domaines d’erreur et de mise à jour pour vous assurer que les instances n’échouent en même temps. La référence SKU v1 prend en charge la scalabilité en ajoutant plusieurs instances de la même passerelle pour partager la charge.

La référence SKU v2 garantit automatiquement que les nouvelles instances sont réparties sur les domaines d’erreur et les domaines de mise à jour. Si vous choisissez de redondance de zone, les instances plus récents sont également réparties sur les zones de disponibilité pour offrir une résilience zonale échec.

### <a name="how-do-i-achieve-a-dr-scenario-across-datacenters-by-using-application-gateway"></a>Comment obtenir un scénario de récupération d’urgence entre centres de données à l’aide d’Application Gateway ?

Traffic Manager permet de répartir le trafic entre plusieurs passerelles d’application dans différents centres de données.

### <a name="does-application-gateway-support-autoscaling"></a>Application Gateway prend-il en charge la mise à l’échelle ?

Oui. La référence SKU v2 d’Application Gateway prend en charge la mise à l’échelle automatique. Pour plus d’informations, consultez [mise à l’échelle et passerelle d’Application redondant](application-gateway-autoscaling-zone-redundant.md).

### <a name="does-manual-scale-up-or-scale-down-cause-downtime"></a>Est mise à l’échelle manuelle ou réduire le temps d’arrêt de la cause ?

Non. Aucun temps d’arrêt n’a lieu, les instances sont réparties entre les domaines de mise à niveau et les domaines d’erreur.

### <a name="does-application-gateway-support-connection-draining"></a>Application Gateway prend-il en charge le drainage de connexion ?

Oui. Vous pouvez configurer la connexion drainage pour modifier des membres au sein d’un pool principal sans interruption de service. Ce paramétrage permet de vous permettent de continuer à envoyer les connexions existantes vers leur destination précédente jusqu'à ce que cette connexion se ferme ou l’expiration d’un délai configurable. Drainage de la connexion attend uniquement les connexions en cours d’exécution en cours se termine. Application Gateway ne tient pas compte de l’état de session d’application.

### <a name="can-i-change-instance-size-from-medium-to-large-without-disruption"></a>Puis-je passer d’une taille moyenne à une grande taille d’instance sans interruption de service ?

Oui. Azure distribue les instances entre des domaines d’erreur et de mise à jour pour vous assurer que les instances n’échouent en même temps. Application Gateway prend en charge la mise à l’échelle en ajoutant plusieurs instances de la même passerelle pour partager la charge.

## <a name="configuration"></a>Configuration

### <a name="is-application-gateway-always-deployed-in-a-virtual-network"></a>Application Gateway est-il toujours déployé dans un réseau virtuel ?

Oui. Application Gateway est toujours déployé dans un sous-réseau de réseau virtuel. Ce sous-réseau peut contenir uniquement des passerelles d’application. Pour plus d’informations, consultez [les exigences réseau et sous-réseau virtuels](https://docs.microsoft.com/azure/application-gateway/configuration-overview#azure-virtual-network-and-dedicated-subnet).

### <a name="can-application-gateway-communicate-with-instances-outside-of-its-virtual-network-or-outside-of-its-subscription"></a>Application Gateway peut communiquer avec des instances en dehors de son réseau virtuel ou en dehors de son abonnement ?

Tant que vous avez la connectivité IP, passerelle d’Application peut communiquer avec des instances en dehors du réseau virtuel qu’il est. Application Gateway peut également communiquer avec les instances en dehors de l’abonnement. Si vous envisagez d’utiliser des adresses IP internes en tant que membres du pool principal, utilisez [homologation de réseaux virtuels](../virtual-network/virtual-network-peering-overview.md) ou [passerelle VPN Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="can-i-deploy-anything-else-in-the-application-gateway-subnet"></a>Puis-je déployer autre chose dans le sous-réseau de la passerelle d’application ?

Non. Mais vous pouvez déployer d’autres passerelles d’application dans le sous-réseau.

### <a name="are-network-security-groups-supported-on-the-application-gateway-subnet"></a>Groupes de sécurité réseau sont pris en charge sur le sous-réseau de passerelle d’application ?

Consultez [des groupes de sécurité dans le sous-réseau de passerelle d’Application réseau](https://docs.microsoft.com/azure/application-gateway/configuration-overview#network-security-groups-on-the-application-gateway-subnet).

### <a name="does-the-application-gateway-subnet-support-user-defined-routes"></a>Le sous-réseau de passerelle d’application prend en charge les itinéraires définis par l’utilisateur ?

Consultez [les itinéraires définis par l’utilisateur pris en charge dans le sous-réseau de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/configuration-overview#user-defined-routes-supported-on-the-application-gateway-subnet).

### <a name="what-are-the-limits-on-application-gateway-can-i-increase-these-limits"></a>Quelles sont les limites d’Application Gateway ? Puis-je augmenter ces limites ?

Consultez [limites d’Application Gateway](../azure-subscription-service-limits.md#application-gateway-limits).

### <a name="can-i-simultaneously-use-application-gateway-for-both-external-and-internal-traffic"></a>Puis-je utiliser simultanément des Application Gateway pour le trafic externe et interne ?

Oui. Application Gateway prend en charge une adresse IP interne et une adresse IP externe par application gateway.

### <a name="does-application-gateway-support-virtual-network-peering"></a>Application Gateway prend-il en charge l’homologation de réseau virtuel ?

Oui. Homologation de réseaux virtuels vous aide à équilibrer la charge du trafic dans d’autres réseaux virtuels.

### <a name="can-i-talk-to-on-premises-servers-when-theyre-connected-by-expressroute-or-vpn-tunnels"></a>Puis-je communiquer avec les serveurs locaux lorsqu’ils sont connectés par ExpressRoute ou des tunnels VPN ?

Oui, tant que le trafic est autorisé.

### <a name="can-one-backend-pool-serve-many-applications-on-different-ports"></a>Un pool principal peut être utilisé de nombreuses applications sur des ports différents ?

Architecture de Microservices est prise en charge. Pour une sonde sur des ports différents, vous devez configurer plusieurs paramètres HTTP.

### <a name="do-custom-probes-support-wildcards-or-regex-on-response-data"></a>Les sondes personnalisées prennent en charge des caractères génériques ou des expressions régulières sur les données de réponse ?

Non. 

### <a name="how-are-routing-rules-processed-in-application-gateway"></a>Comment les règles de routage sont traités dans Application Gateway ?

Consultez [ordre des règles de traitement](https://docs.microsoft.com/azure/application-gateway/configuration-overview#order-of-processing-rules).

### <a name="for-custom-probes-what-does-the-host-field-signify"></a>Pour les sondes personnalisées, ce qui le champ hôte signifie ?

Le champ hôte Spécifie le nom auquel envoyer la sonde lorsque vous avez configuré multisite sur Application Gateway. Sinon, utilisez '127.0.0.1'. Cette valeur est différente du nom d’hôte de machine virtuelle. Son format est \<protocole\>://\<hôte\>:\<port\>\<chemin d’accès\>.

### <a name="can-i-whitelist-application-gateway-access-to-only-a-few-source-ips"></a>Faire la liste blanche d’accéder à seulement quelques adresses IP sources Application Gateway ?

Oui. Consultez [restreindre l’accès à des adresses IP sources spécifiques](https://docs.microsoft.com/azure/application-gateway/configuration-overview#whitelist-application-gateway-access-to-a-few-source-ips).

### <a name="can-i-use-the-same-port-for-both-public-facing-and-private-facing-listeners"></a>Puis-je utiliser le même port pour les écouteurs destinées au public et privé orientés ?

Non.

## <a name="configuration---ssl"></a>Configuration - SSL

### <a name="what-certificates-does-application-gateway-support"></a>Les certificats prennent en charge par la passerelle d’Application ?

Application Gateway prend en charge des certificats auto-signés, les certificats d’autorité de certification, les certificats de Validation étendue (EV) et les certificats avec caractères génériques.

### <a name="what-cipher-suites-does-application-gateway-support"></a>Les suites de chiffrement est la passerelle Application Gateway prend en charge ?

Application Gateway prend en charge les suites de chiffrement suivantes. 

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

Pour plus d’informations sur la façon de personnaliser les options SSL, consultez [versions de stratégie de configuration de SSL et suites de chiffrement sur Application Gateway](application-gateway-configure-ssl-policy-powershell.md).

### <a name="does-application-gateway-support-reencryption-of-traffic-to-the-backend"></a>Application Gateway prend-il en charge le renouvellement du chiffrement du trafic vers le serveur principal ?

Oui. Application Gateway prend en charge le déchargement SSL et SSL de bout en bout, qui rechiffrer le trafic vers le serveur principal.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Puis-je configurer la stratégie SSL pour les versions du protocole SSL contrôle ?

Oui. Vous pouvez configurer la passerelle Application Gateway pour refuser TLS1.0, TLS1.1 et TLS 1.2. Par défaut, SSL 2.0 et 3.0 sont déjà désactivés et ne sont pas configurables.

### <a name="can-i-configure-cipher-suites-and-policy-order"></a>Puis-je configurer les suites de chiffrement et l’ordre de la stratégie ?

Oui. Dans Application Gateway, vous pouvez [configurer des suites de chiffrement](application-gateway-ssl-policy-overview.md). Pour définir une stratégie personnalisée, vous pouvez activer au moins une des suites de chiffrement suivantes. 

* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256 
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
* TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_128_GCM_SHA256
* TLS_RSA_WITH_AES_256_CBC_SHA256
* TLS_RSA_WITH_AES_128_CBC_SHA256

Application Gateway utilise SHA256 pour la gestion des serveurs principaux.

### <a name="how-many-ssl-certificates-does-application-gateway-support"></a>Nombre de certificats SSL Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu'à 100 certificats SSL.

### <a name="how-many-authentication-certificates-for-backend-reencryption-does-application-gateway-support"></a>Certificats d’authentification combien de rechiffrement de backend Application Gateway prend-il en charge ?

Application Gateway prend en charge jusqu'à 10 certificats d’authentification. La valeur par défaut est 5.

### <a name="does-application-gateway-natively-integrate-with-azure-key-vault"></a>Application Gateway s’intègre-t-il en mode natif avec Azure Key Vault ?

Oui, la référence SKU de passerelle d’Application v2 prend en charge Key Vault. Pour plus d’informations, consultez [une terminaison SSL avec certificats Key Vault](key-vault-certs.md).

### <a name="how-do-i-configure-https-listeners-for-com-and-net-sites"></a>Comment configurer les écouteurs HTTPS pour les sites .com et .net ? 

Pour plusieurs domaine routage basé sur (basée sur l’hôte), vous pouvez créer des écouteurs multisites, configurer des écouteurs qui utilisent HTTPS comme protocole et associer les écouteurs avec les règles de routage. Pour plus d’informations, consultez [hébergement de plusieurs sites Application Gateway en utilisant](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview). 

## <a name="configuration---web-application-firewall-waf"></a>Configuration - pare-feu d’applications web (WAF)

### <a name="does-the-waf-sku-offer-all-the-features-available-in-the-standard-sku"></a>Est-ce que la référence SKU WAF offre toutes les fonctionnalités disponibles dans la référence SKU Standard ?

Oui. WAF prend en charge toutes les fonctionnalités dans la référence SKU Standard.

### <a name="which-crs-versions-does-application-gateway-support"></a>Les versions CRS Application Gateway prend-il en charge ?

Application Gateway prend en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et CRS [3.0](application-gateway-crs-rulegroups-rules.md#owasp30).

### <a name="how-do-i-monitor-waf"></a>Comment puis-je surveiller le pare-feu d’application web ?

WAF moniteur via la journalisation des Diagnostics. Pour plus d’informations, consultez [journalisation des diagnostics et métriques pour Application Gateway](application-gateway-diagnostics.md).

### <a name="does-detection-mode-block-traffic"></a>Est-ce que le mode de détection bloque le trafic ?

Non. Mode de détection consigne uniquement le trafic qui déclenche une règle de pare-feu d’applications Web.

### <a name="can-i-customize-waf-rules"></a>Puis-je personnaliser les règles de pare-feu d’application web ?

Oui. Pour plus d’informations, consultez [WAF personnaliser et groupes de règles](application-gateway-customize-waf-rules-portal.md).

### <a name="what-rules-are-currently-available-for-waf"></a>Quelles sont les règles actuellement disponibles pour les pare-feu d’applications Web ?

WAF prend actuellement en charge CRS [2.2.9](application-gateway-crs-rulegroups-rules.md#owasp229) et [3.0](application-gateway-crs-rulegroups-rules.md#owasp30). Ces règles fournissent la sécurité de base contre la plupart des 10 premières vulnérabilités Open Web Application Security Project (OWASP) identifie : 

* Protection contre les injections de code SQL
* Protection contre les scripts intersites
* Protection contre les attaques web courantes telles que l’injection de commande, dissimulation, fractionnement, de la réponse HTTP de la requête HTTP et attaque par inclusion de fichier distant
* Protection contre les violations de protocole HTTP
* Protection contre les anomalies de protocole HTTP comme un agent-utilisateur hôte manquant et les en-têtes Accept
* Protection contre les robots, les crawlers et les scanneurs
* Détection d’application courantes (autrement dit, Apache, IIS et ainsi de suite)

Pour plus d’informations, consultez [des vulnérabilités OWASP top 10](https://www.owasp.org/index.php/Top10#OWASP_Top_10_for_2013).

### <a name="does-waf-support-ddos-protection"></a>WAF prend-il en charge la protection DDoS ?

Oui. Vous pouvez activer le service protection DDoS sur le réseau virtuel dans lequel la passerelle d’application est déployée. Ce paramètre garantit que le service Protection DDoS Azure protège également l’application gateway adresse IP virtuelle (VIP).

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-logs-does-application-gateway-provide"></a>Quels types de journaux de passerelle d’Application fournit-il ?

Application Gateway fournit trois types de journaux : 

* **ApplicationGatewayAccessLog**: Le journal d’accès contient toutes les demandes envoyées vers le serveur de passerelle application frontal. Les données incluent l’appelant IP URL demandée, latence de réponse, le code de retour et octets d’entrée et de sortie. Le journal d’accès est collecté toutes les 300 secondes. Il contient un enregistrement par passerelle d’application.
* **ApplicationGatewayPerformanceLog**: Le journal des performances capture des informations de performances pour chaque passerelle d’application. Informations incluent le débit en octets, le nombre total de demandes présentées, nombre de demandes ayant échoué et le nombre d’instances de serveur principal correctes et incorrectes.
* **ApplicationGatewayFirewallLog**: Pour les passerelles d’application que vous configurez avec WAF, le journal du pare-feu contient les demandes consignées via le mode de détection ou mode de prévention.

Pour plus d’informations, consultez [back-end d’intégrité, les journaux de diagnostic et métriques pour la passerelle d’Application](application-gateway-diagnostics.md).

### <a name="how-do-i-know-if-my-backend-pool-members-are-healthy"></a>Comment savoir si les membres de mon pool back-end sont intègres ?

Vérifier l’intégrité à l’aide de l’applet de commande PowerShell `Get-AzApplicationGatewayBackendHealth` ou le portail. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="whats-the-retention-policy-for-the-diagnostic-logs"></a>Qu’est la stratégie de rétention pour les journaux de diagnostic ?

Les journaux de diagnostic circulent vers le compte de stockage client. Les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Journaux de diagnostic peuvent également être envoyées à un concentrateur d’événements ou journaux Azure Monitor. Pour plus d’informations, consultez [Diagnostics Application Gateway](application-gateway-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-application-gateway"></a>Comment puis-je obtenir des journaux d’audit pour Application Gateway ?

Dans le portail, dans le panneau de menu d’une passerelle d’application, sélectionnez **journal d’activité** pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-application-gateway"></a>Puis-je définir des alertes avec Application Gateway ?

Oui. Dans la passerelle d’Application, alertes sont configurées sur les mesures. Pour plus d’informations, consultez [les métriques de passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#metrics) et [recevoir des notifications d’alerte](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

### <a name="how-do-i-analyze-traffic-statistics-for-application-gateway"></a>Comment analyser les statistiques de trafic pour Application Gateway ?

Vous pouvez afficher et analyser les journaux d’accès de plusieurs façons. Utiliser Azure Monitor journaux, Excel, Power BI et ainsi de suite.

Vous pouvez également utiliser un modèle Resource Manager qui installe et exécute le fameux [GoAccess](https://goaccess.io/) connecter analyzer pour accéder aux journaux de passerelle d’Application. GoAccess fournit des statistiques de trafic HTTP utiles telles que les visiteurs uniques, fichiers demandés, les hôtes, les systèmes d’exploitation, navigateurs et codes d’état HTTP. Pour plus d’informations, dans GitHub, consultez le [fichier Lisez-moi dans le dossier de modèles Resource Manager](https://aka.ms/appgwgoaccessreadme).

### <a name="what-could-cause-backend-health-to-return-an-unknown-status"></a>Ce qui peut provoquer l’intégrité du serveur principal retourner un état inconnu ?

En règle générale, vous consultez un état inconnu lors de l’accès au serveur principal est bloqué par un groupe de sécurité réseau (NSG), DNS personnalisé ou défini par l’utilisateur de routage (UDR) sur le sous-réseau de passerelle d’application. Pour plus d’informations, consultez [back-end d’intégrité, la journalisation des diagnostics et métriques pour la passerelle d’Application](application-gateway-diagnostics.md).

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur Application Gateway, consultez [What ' s Azure Application Gateway ?](overview.md).
