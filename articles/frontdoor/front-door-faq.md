---
title: Service de la porte d’entrée Azure - Forum aux Questions sur la porte d’entrée | Microsoft Docs
description: Cette page fournit des réponses aux questions fréquemment posées sur le Service de porte d’entrée Azure
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2019
ms.author: sharadag
ms.openlocfilehash: 256435dfd016ebbd86dbbe49f4abbb346fb1cd19
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407747"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Forum aux questions pour Azure porte Service

Cet article répond aux questions courantes sur les fonctionnalités de Service de porte d’Azure. Si vous ne trouvez pas de réponse à votre question ici, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

1. La section Commentaires de cet article
2. [Porte d’entrée Azure Service UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Prise en charge de Microsoft :** Pour créer une demande de support, dans le portail Azure, sous l’onglet **Aide**, sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.

## <a name="general"></a>Généralités

### <a name="what-is-azure-front-door-service"></a>Qu’est-ce que le service Azure Front Door Service ?

Azure Service porte d’entrée est un réseau de distribution d’Application (ADN) en tant que service, offrant diverses fonctionnalités d’équilibrage de charge layer 7 pour vos applications. Il fournit l’accélération de site dynamique (DSA), ainsi que l’équilibrage de charge global avec près de basculement en temps réel. C’est un service hautement disponible et évolutif, entièrement géré par Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Quelles sont les fonctionnalités prises en charge par Service de porte d’entrée Azure ?

Azure Service porte d’entrée prend en charge l’accélération de site dynamique (DSA), le déchargement SSL et SSL de bout en bout, pare-feu d’applications Web, affinité de session basée sur les cookies, le routage basé sur le chemin d’accès url, certificats gratuits et gestion de plusieurs domaines et autres. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [présentation de Service de porte d’entrée Azure](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Quelle est la différence entre le Service de porte d’entrée Azure et Azure Application Gateway ?

Bien que la porte d’entrée et de passerelle d’Application sont de couche 7 équilibreurs de charge (HTTP/HTTPS), la principale différence est que porte d’entrée est un service global, tandis que la passerelle d’Application est un service régional. Bien que la porte d’entrée peut équilibrer la charge entre vos différentes unités/clusters/horodatage unités d’échelle entre les régions, Application Gateway vous permet à équilibrer la charge entre vos etc. de machines virtuelles/conteneurs qui se trouve dans l’unité d’échelle.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Lorsque nous devons pour déployer une passerelle d’Application derrière la porte d’entrée ?

Les principaux scénarios pourquoi un doit utiliser la passerelle d’Application derrière la porte d’entrée sont :

- Porte d’entrée peut effectuer le chemin d’accès-équilibrage de charge uniquement au niveau global, mais si un veut équilibrer le trafic encore plus loin dans son réseau virtuel (VNET), puis ils doivent utiliser la passerelle d’Application.
- Étant donné que la porte d’entrée ne fonctionne pas à un niveau de la machine virtuelle/conteneur, il ne peut pas faire drainage de connexion. Toutefois, Application Gateway vous permet de faire de drainage de connexion. 
- Avec une passerelle d’Application derrière AFD, un peut atteindre 100 % SSL décharger et acheminer uniquement les requêtes HTTP au sein de leur réseau virtuel (VNET).
- Porte d’entrée et la passerelle d’Application prend en charge l’affinité de session. Alors que la porte d’entrée peut diriger le trafic suivant à partir d’une session utilisateur sur le même cluster ou serveur principal dans une région donnée, Application Gateway peuvent affiner directement le trafic vers le même serveur au sein du cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Déployer l’équilibreur de charge Azure derrière la porte d’entrée ?

Azure Service porte d’entrée a besoin d’une adresse IP virtuelle publique ou un nom DNS disponible publiquement pour acheminer le trafic vers. Déploiement d’un équilibreur de charge Azure derrière la porte d’entrée est le cas d’usage commun.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Quels protocoles Azure porte Service prend-il en charge ?

Azure Service porte d’entrée prend en charge HTTP, HTTPS et HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Comment Azure porte Service prend en charge HTTP/2 ?

Prise en charge du protocole HTTP/2 est disponible pour les clients qui se connectent au Service de porte d’entrée Azure uniquement. La communication aux serveurs principaux dans le pool principal est sur HTTP/1.1. Prise en charge HTTP/2 est activée par défaut.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quelles sont les ressources actuellement prises en charge dans le pool backend ?

Pools principaux peuvent être composés de stockage, application Web, les instances de Kubernetes ou tout autre nom d’hôte personnalisé qui dispose d’une connexion publique. Service de porte d’entrée Azure nécessite que les serveurs principaux sont définis via une adresse IP publique ou un nom d’hôte DNS pouvant être résolu publiquement. Membres des pools principaux peuvent être entre des zones, régions, ou même en dehors d’Azure tant qu’ils disposent de la connectivité.

### <a name="what-regions-is-the-service-available-in"></a>Dans quelles régions le service est-il disponible ?

Azure Service porte d’entrée est un service global et n’est pas lié à n’importe quelle région Azure spécifique. Le seul emplacement, que vous devez spécifier lors de la création d’une porte d’entrée est l’emplacement de groupe de ressources, qui est essentiellement en spécifiant où seront stockées les métadonnées pour le groupe de ressources. Ressource porte elle-même est créée comme une ressource globale et la configuration est déployée dans le monde entier pour tous les points de présence (Point de présence). 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quelles sont les emplacements POP Azure porte service ?

Azure Service porte d’entrée a la même liste d’emplacements POP (Point de présence) en tant qu’Azure CDN à partir de Microsoft. Pour obtenir la liste complète de nos points de présence, Merci reportez-vous [emplacements POP du CDN Azure à partir de Microsoft](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Est un Service Azure porte d’un déploiement dédié pour mon application ou est-il partagé entre les clients ?

Azure Service porte d’entrée est un service mutualisé mondialement distribué. Par conséquent, l’infrastructure pour la porte d’entrée est partagé entre tous ses clients. Toutefois, en créant une porte d’entrée, vous définissez la configuration spécifique requise pour votre application et 

### <a name="is-http-https-redirection-supported"></a>La redirection HTTP->HTTPS est-elle prise en charge ?

Porte d’entrée ne prend en charge la redirection d’URL.

### <a name="in-what-order-are-routing-rules-processed"></a>Dans quel ordre les règles de routage sont traitées ?

Itinéraires pour votre porte d’entrée ne sont pas ordonnées et un itinéraire spécifique est sélectionné selon la meilleure correspondance. En savoir plus sur [la porte d’entrée correspond de requêtes pour une règle de routage](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-service"></a>Comment verrouiller l’accès à mon service principal pour seulement le Service de porte d’entrée Azure ?

Vous pouvez configurer les ACL IP pour vos serveurs principaux pour accepter le trafic du Service de porte d’Azure. Vous pouvez limiter votre application pour accepter les connexions entrantes uniquement à partir de l’espace d’IP principal de Service de porte d’entrée Azure. Nous œuvrons actuellement pour l’intégration avec [balises de Service et de plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519) mais si vous pouvez maintenant faire référence les plages d’adresses IP comme indiqué ci-dessous :
 
- **IPv4** - `147.243.0.0/16`
- **IPv6** - `2a01:111:2050::/44`

> [!WARNING]
> Notre espace IP du serveur principal peut changer plus tard, toutefois, nous allons nous assurer qu’avant que cela se produit, que nous serait ont intégré avec [balises de Service et de plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519). Nous recommandons que vous vous abonnez à [balises de Service et de plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519) pour apporter les modifications ou mises à jour. 

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>L’adresse IP anycast peut modifier la durée de vie de mon porte d’entrée ?

L’adresse IP anycast de serveur frontal pour votre porte d’entrée ne doit généralement pas modifier et peut-être rester statique pour la durée de vie de la porte d’entrée. Toutefois, il existe **aucune garantie** pour le même. Merci ne prennent aucune dépendance directe sur l’adresse IP.  

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Service de porte d’entrée Azure prend-il en charge les adresses IP statique ou dédié ?

Non, actuellement Azure porte Service ne gère pas statique ou dédié frontend anycast adresses IP. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Service de porte d’entrée Azure prend-il en charge les en-têtes x-forwarded-for ?

Oui, Service de porte d’entrée Azure prend en charge les en-têtes X-Forwarded-For, hôte transférés X et X-Forwarded-Proto. Pour X-Forwarded-For if l’en-tête existait déjà, puis porte d’entrée y ajoute l’adresse IP du socket client. Sinon, il ajoute l’en-tête avec l’adresse IP du socket client comme valeur. Pour l’hôte transférés X et X-Forwarded-Proto, la valeur est remplacée.

En savoir plus sur la [porte d’entrée pris en charge les en-têtes HTTP](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Combien de temps faut-il pour déployer un Service de la porte d’entrée Azure ? Mon porte d’entrée fonctionne toujours lors de la mise à jour ?

La nouvelle création d’un porte d’entrée ou les mises à jour à une porte d’entrée existant prend environ 3 à 5 minutes pour le déploiement global. Cela signifie que dans environ 3 à 5 minutes, la configuration de votre porte d’entrée sera déployée sur l’ensemble de nos points de présence dans le monde entier.

Remarque - personnalisé mises à jour du certificat SSL en environ 30 minutes pour être déployé dans le monde entier.

## <a name="configuration"></a>Configuration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Équilibrage de charge Azure porte d’entrée ou de la route le trafic au sein d’un réseau virtuel ?

Porte d’entrée Azure (AFD) nécessite une adresse IP publique ou le nom DNS pouvant être résolu publiquement pour acheminer le trafic. Par conséquent, la réponse est aucune AFD ne peut pas acheminer directement au sein d’un réseau virtuel, mais à l’aide d’une passerelle d’Application ou un équilibreur de charge Azure entre les deux est résoudre ce scénario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quels sont les délais d’attente et les limites pour le Service de porte d’entrée Azure différents ?

En savoir plus sur toutes la documentation [des délais d’attente et les limites pour le Service de porte d’entrée Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Performances

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Comment Azure porte Service prend-il en charge une haute disponibilité et l’évolutivité ?

Azure Service porte d’entrée est une plateforme de mutualisée globalement distribuée avec très grands volumes de capacité pour répondre aux besoins en évolutivité de votre application. Remis à partir de la périphérie de réseau global de Microsoft, porte d’entrée fournit la fonction globale de l’équilibrage de charge qui vous permet de faire basculer votre application dans son intégralité ou les microservices individuels même entre les régions ou différents clouds.

## <a name="ssl-configuration"></a>Configuration SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quelles versions TLS sont prises en charge par le Service Azure porte d’entrée ?

Porte d’entrée prend en charge TLS versions 1.0, 1.1 et 1.2. TLS 1.3 n’est pas encore pris en charge.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Les certificats sont pris en charge sur le Service de porte d’entrée Azure ?

Pour activer le protocole HTTPS pour la distribution sécurisée de contenu sur un domaine personnalisé de porte d’entrée, vous pouvez choisir d’utiliser un certificat qui est géré par le Service de porte d’entrée Azure ou votre propre certificat.
La porte gérés dispositions d’option un certificat SSL standard via Digicert et stockés au premier plan Key Vault de la porte. Si vous choisissez d’utiliser votre propre certificat, vous pouvez intégrer un certificat à partir d’une autorité de certification pris en charge et peut être une norme SSL, certificat de validation étendue ou même un certificat générique. Les certificats auto-signés ne sont pas pris en charge. En savoir plus [comment activer HTTPS pour un domaine personnalisé](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quelles sont les suites de chiffrement actuellement pris en charge par le Service de porte d’entrée Azure ?

Les suites de chiffrement actuellement pris en charge par le Service Azure porte d’entrée sont les suivantes :

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure porte d’entrée Service prend-il également en charge nouveau chiffrement du trafic vers le serveur principal ?

Oui, Service de porte d’entrée Azure prend en charge le déchargement SSL et SSL de bout en bout, qui chiffre à nouveau le trafic vers le serveur principal. En fait, étant donné que les connexions au serveur principal s’effectuer via il est l’adresse IP publique, il est recommandé de configurer votre porte d’entrée pour utiliser HTTPS comme protocole de transfert.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Puis-je configurer la stratégie SSL pour gérer les versions du protocole SSL ?

Non, actuellement porte d’entrée ne prend en charge pour refuser des versions spécifiques de TLS ni définir les versions TLS minimale. 

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Puis-je configurer porte d’entrée pour prendre en charge uniquement des suites de chiffrement spécifique ?

Non, configuration porte d’entrée pour les suites de chiffrement spécifique n’est pas pris en charge. 

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quels types de métriques et les journaux sont disponibles avec le Service de porte d’entrée Azure ?

Pour plus d’informations sur les journaux et autres fonctionnalités de diagnostic, consultez [analyse des métriques et journaux pour la porte d’entrée](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Quelle est la stratégie de conservation sur les journaux de diagnostic ?

Les journaux de diagnostic circulent vers le compte de stockage des clients, et les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un hub d’événements ou à des journaux d’activité Azure Monitor. Pour plus d’informations, consultez [Diagnostics du Service Azure porte d’entrée](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Comment obtenir des journaux d’audit pour le Service de porte d’entrée Azure ?

Journaux d’audit sont disponibles pour le Service de porte d’Azure. Dans le portail, cliquez sur **journal d’activité** dans le panneau de menu de votre porte d’entrée pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Puis-je définir des alertes avec le Service de porte d’entrée Azure ?

Oui, Service de porte d’entrée Azure prend en charge les alertes. Les alertes sont configurées d’après les métriques utilisées. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).