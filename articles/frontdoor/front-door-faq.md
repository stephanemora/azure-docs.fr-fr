---
title: Azure Front Door Service – Forum aux questions
description: Cette page fournit des réponses aux questions les plus souvent posées sur Azure Front Door Service.
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
ms.openlocfilehash: fa9de74c89ba3f0351169f143146dc21b80ee666
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790533"
---
# <a name="frequently-asked-questions-for-azure-front-door-service"></a>Questions fréquentes (FAQ) sur Azure Front Door Service

Cet article répond aux questions courantes sur les fonctionnalités d’Azure Front Door Service. Si vous ne trouvez pas de réponse à votre question ici, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

1. La section Commentaires de cet article
2. [Azure Front Door Service UserVoice](https://feedback.azure.com/forums/217313-networking?category_id=345025).
3. **Support Microsoft :** Pour créer une demande de support, dans le portail Azure, sous l’onglet **Aide**, sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.

## <a name="general"></a>Généralités

### <a name="what-is-azure-front-door-service"></a>Qu’est-ce que le service Azure Front Door Service ?

Azure Front Door Service est un réseau ADN (Application Delivery Network) sous forme de service qui offre à vos applications plusieurs fonctionnalités d’équilibrage de charge de couche 7. Il fournit une accélération de site dynamique ainsi qu’un équilibrage de charge global avec un basculement en quasi temps réel. Il s’agit d’un service hautement disponible et scalable, complètement managé par Azure.

### <a name="what-features-does-azure-front-door-service-support"></a>Quelles sont les fonctionnalités prises en charge par Azure Front Door Service ?

Azure Front Door Service prend en charge, entre autres, l’accélération de site dynamique, le déchargement SSL et le protocole SSL de bout en bout, le pare-feu d’applications web, l’affinité de session basée sur les cookies, le routage basé sur le chemin d’URL, les certificats gratuits et la gestion multidomaine. Pour obtenir une liste complète des fonctionnalités prises en charge, consultez [Présentation d’Azure Front Door Service](front-door-overview.md).

### <a name="what-is-the-difference-between-azure-front-door-service-and-azure-application-gateway"></a>Quelle est la différence entre Azure Front Door Service et Azure Application Gateway ?

Bien que Front Door et Application Gateway soient tous deux des équilibreurs de charge de couche 7 (HTTP/HTTPS), la principale différence est que Front Door est un service mondial, tandis qu’Application Gateway est un service régional. Alors que Front Door peut équilibrer la charge entre vos différentes unités d’échelle/clusters/unités d’horodatage dans plusieurs régions, Application Gateway vous permet d’équilibrer la charge entre vos machines virtuelles/conteneurs, et ainsi de suite, qui se trouvent dans l’unité d’échelle.

### <a name="when-should-we-deploy-an-application-gateway-behind-front-door"></a>Quand faut-il déployer Application Gateway derrière Front Door ?

Les principaux scénarios pouvant justifier l’utilisation d’Application Gateway derrière Front Door sont les suivants :

- Front Door peut effectuer un équilibrage de charge basé sur le chemin uniquement au niveau mondial, mais si vous souhaitez équilibrer encore davantage le trafic avec votre réseau virtuel, vous devez utiliser Application Gateway.
- Étant donné que Front Door ne fonctionne pas au niveau de la machine virtuelle ou du conteneur, il ne peut pas effectuer de drainage des connexions, contrairement à Application Gateway. 
- Avec Application Gateway derrière AFD, vous pouvez atteindre un déchargement SSL de 100 % et router uniquement les requêtes HTTP au sein de votre réseau virtuel.
- Front Door et Application Gateway prennent tous deux en charge l’affinité de session. Alors que Front Door peut diriger le trafic ultérieur d’une session utilisateur vers le même cluster ou back-end dans une région donnée, Application Gateway peuvent diriger avec affinité le trafic vers le même serveur au sein du cluster.  

### <a name="can-we-deploy-azure-load-balancer-behind-front-door"></a>Est-il possible de déployer Azure Load Balancer derrière Front Door ?

Azure Front Door Service a besoin d’une adresse IP virtuelle publique ou d’un nom DNS disponible publiquement vers lequel router le trafic. Le déploiement d’Azure Load Balancer derrière Front Door est un cas d’usage courant.

### <a name="what-protocols-does-azure-front-door-service-support"></a>Quels sont les protocoles pris en charge par Azure Front Door Service ?

Azure Front Door Service prend en charge HTTP, HTTPS et HTTP/2.

### <a name="how-does-azure-front-door-service-support-http2"></a>Comment Azure Front Door Service prend-il en charge HTTP/2 ?

La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant uniquement à Azure Front Door Service. La communication vers les back-ends dans le pool de back-ends s’effectue sur HTTP/1.1. La prise en charge d’HTTP/2 est activée par défaut.

### <a name="what-resources-are-supported-today-as-part-of-backend-pool"></a>Quelles sont les ressources actuellement prises en charge dans le pool backend ?

Les pools de back-ends peuvent être composés d’instances de Kubernetes, de Stockage, de Web App ou tout autre nom d’hôte personnalisé qui dispose d’une connectivité publique. Azure Front Door Service exige que les back-ends soient définis par le biais d’une adresse IP publique ou d’un nom d’hôte DNS pouvant être résolu publiquement. Les membres des pools de back-ends peuvent être dans différentes zones ou régions, ou même en dehors d’Azure tant qu’ils disposent d’une connectivité IP.

### <a name="what-regions-is-the-service-available-in"></a>Dans quelles régions le service est-il disponible ?

Azure Front Door Service est un service mondial ; il n’est pas lié à une région Azure spécifique. Le seul emplacement que vous devez spécifier lors de la création d’une porte d’entrée est l’emplacement du groupe de ressources, qui correspond en gros à l’emplacement où seront stockées les métadonnées pour le groupe de ressources. Les ressources Front Door proprement dites sont créées en tant que ressource mondiale, et la configuration est déployée dans le monde entier sur tous les points de présence. 

### <a name="what-are-the-pop-locations-for-azure-front-door-service"></a>Quels sont les emplacements de points de présence pour Azure Front Door Service ?

Azure Front Door Service a la même liste d’emplacements de points de présence que Microsoft Azure CDN. Pour obtenir la liste complète de nos points de présence, consultez [Emplacements POP du CDN Azure par région](https://docs.microsoft.com/azure/cdn/cdn-pop-locations).

### <a name="is-azure-front-door-service-a-dedicated-deployment-for-my-application-or-is-it-shared-across-customers"></a>Azure Front Door Service est-il un déploiement dédié à mon application ou est-il partagé entre les clients ?

Azure Front Door Service est un service multilocataire distribué à l’échelle mondiale. L’infrastructure pour Front Door est donc partagée entre tous ses clients. Toutefois, en créant un profil Front Door, vous définissez la configuration spécifique requise pour votre application et aucune modification apportée à votre porte d’entrée n’impacte d’autres configurations Front Door.

### <a name="is-http-https-redirection-supported"></a>La redirection HTTP->HTTPS est-elle prise en charge ?

Oui. En fait, Azure Front Door Service prend en charge la redirection de chaîne de requête, de chemin et d’hôte ainsi qu’une partie de la redirection d’URL. Découvrez-en plus sur la [redirection d’URL](front-door-url-redirect.md). 

### <a name="in-what-order-are-routing-rules-processed"></a>Dans quel ordre les règles de routage sont-elles traitées ?

Les routes pour votre porte d’entrée ne sont pas ordonnées, et une route spécifique est sélectionnée en fonction de la meilleure correspondance. Découvrez [comment Front Door fait correspondre les requêtes à une règle de routage](front-door-route-matching.md).

### <a name="how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door"></a>Comment faire pour restreindre l’accès à mon back-end uniquement à Azure Front Door ?

Pour verrouiller votre application afin de n’accepter que le trafic en provenant de votre porte d’entrée spécifique, vous devez configurer les ACL IP pour votre back-end, puis restreindre l’ensemble des valeurs acceptées pour l’en-tête « X-Forwarded-Host » envoyé par Azure Front Door. Voici le détail de ces étapes :

- Configurez les ACL IP pour vos back-ends de manière à accepter le trafic en provenance uniquement de l’espace d’adressage IP back-end d’Azure Front Door et des services d’infrastructure d’Azure. Nous œuvrons actuellement pour l’intégration avec les [balises de service et les plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519), mais pour l’heure vous pouvez faire référence aux plages d’adresses IP comme indiqué ci-dessous :
 
    - Espace IP back-end **IPv4** de Front Door : `147.243.0.0/16`
    - Espace IP back-end **IPv6** de Front Door : `2a01:111:2050::/44`
    - [Services d’infrastructure de base](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) d’Azure par le biais des adresses IP hôte virtualisées : `168.63.129.16` et `169.254.169.254`

    > [!WARNING]
    > L’espace IP back-end de Front Door pourra changer ultérieurement, mais avant cela nous ferons en sorte d’avoir procédé à l’intégration avec les [balises de service et les plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519). Nous vous recommandons de vous abonner aux [balises de service et aux plages d’adresses IP Azure](https://www.microsoft.com/download/details.aspx?id=56519) pour vous tenir informé des modifications ou mises à jour.

-   Filtrez sur les valeurs acceptables pour l’en-tête entrant « **X-Forwarded-Host** » envoyé par Front Door. Les seules valeurs autorisées pour l’en-tête doivent être tous les hôtes front-end tels que définis dans la configuration de votre porte d’entrée. Plus précisément, il s’agit uniquement des noms des hôtes en provenance desquels vous souhaitez accepter le trafic sur votre back-end spécifique.
    - Supposons, par exemple, que la configuration de votre porte d’entrée a les hôtes front-end _`contoso.azurefd.net`_ (A), _`www.contoso.com`_ (B), _ (C) et _`notifications.contoso.com`_ (D). Supposons que vous disposez de deux back-ends : X et Y. 
    - Le back-end X ne doit accepter que le trafic en provenance des noms d’hôte A et B, tandis que le back-end Y peut accepter le trafic en provenance de A, C et D.
    - Ainsi, sur le back-end X, vous ne devez accepter que le trafic dont l’en-tête « **X-Forwarded-Host** » est défini sur _`contoso.azurefd.net`_ ou _`www.contoso.com`_ . Dans tous les autres cas, le back-end X doit refuser le trafic.
    - De même, sur le back-end Y, vous ne devez accepter que le trafic dont l’en-tête « **X-Forwarded-Host** » est défini sur _`contoso.azurefd.net`_ , _`api.contoso.com`_ ou _`notifications.contoso.com`_ . Dans tous les autres cas, le back-end Y doit refuser le trafic.

### <a name="can-the-anycast-ip-change-over-the-lifetime-of-my-front-door"></a>L’adresse IP anycast peut-elle changer pendant la durée de vie de ma porte d’entrée ?

L’adresse IP anycast front-end de votre porte d’entrée ne devrait généralement pas changer, et peut rester statique pendant toute la durée de vie de Front Door. Toutefois, il n’existe **aucune garantie** à ce sujet. Veillez à ne créer aucune dépendance directe envers l’adresse IP.

### <a name="does-azure-front-door-service-support-static-or-dedicated-ips"></a>Azure Front Door Service prend-il en charge les adresses IP statiques ou dédiées ?

Non, actuellement Azure Front Door Service ne prend pas en charge les adresses IP anycast front-end statiques ou dédiées. 

### <a name="does-azure-front-door-service-support-x-forwarded-for-headers"></a>Azure Front Door Service prend-il en charge les en-têtes x-forwarded-for ?

Oui, Azure Front Door Service prend en charge les en-têtes X-Forwarded-For, X-Forwarded-Host et X-Forwarded-Proto. Pour X-Forwarded-For, si l’en-tête était déjà présent, Front Door y ajoute l’adresse IP de socket du client. Sinon, il ajoute l’en-tête avec l’adresse IP de socket du client comme valeur. Pour X-Forwarded-Host et X-Forwarded-Proto, la valeur est remplacée.

Pour en savoir plus sur les en-têtes HTTP pris en charge par Front Door, consultez [cet article](front-door-http-headers-protocol.md).  

### <a name="how-long-does-it-take-to-deploy-an-azure-front-door-service-does-my-front-door-still-work-when-being-updated"></a>Combien de temps faut-il pour déployer Azure Front Door Service ? Ma porte d’entrée fonctionne-t-elle toujours pendant sa mise à jour ?

La création d’une nouvelle porte d’entrée ou la mise à jour d’une porte d’entrée existante prend de trois à cinq minutes pour un déploiement mondial. Cela signifie qu’au bout de trois à cinq minutes la configuration de votre porte d’entrée sera déployée sur l’ensemble de nos points de présence dans le monde entier.

Remarque : Le déploiement dans le monde entier des mises à jour des certificats SSL personnalisés prend environ 30 minutes.

## <a name="configuration"></a>Configuration

### <a name="can-azure-front-door-load-balance-or-route-traffic-within-a-virtual-network"></a>Azure Front Door peut-il équilibrer la charge ou router le trafic au sein d’un réseau virtuel ?

Azure Front Door (AFD) nécessite une adresse IP publique ou un nom DNS pouvant être résolu publiquement pour router le trafic. Par conséquent, la réponse est non. AFD ne peut pas effectuer un routage direct au sein d’un réseau virtuel, mais vous pouvez utiliser Application Gateway ou Azure Load Balancer comme élément intermédiaire pour satisfaire aux exigences de ce scénario.

### <a name="what-are-the-various-timeouts-and-limits-for-azure-front-door-service"></a>Quels sont les différents délais d’attente et limites applicables à Azure Front Door Service ?

Pour en savoir plus sur tous les délais d’attente et limites documentés pour Azure Front Door Service, consultez [cet article](https://docs.microsoft.com/azure/azure-subscription-service-limits#azure-front-door-service-limits).

## <a name="performance"></a>Performances

### <a name="how-does-azure-front-door-service-support-high-availability-and-scalability"></a>Comment Azure Front Door Service prend-il en charge la haute disponibilité et la scalabilité ?

Azure Front Door Service est une plateforme multilocataire mondialement distribuée avec des volumes de capacité élevés afin de répondre aux besoins de scalabilité de votre application. Délivré à partir de la périphérie du réseau mondial Microsoft, le service Front Door offre des fonctionnalités d’équilibrage de charge mondial qui vous permettent de faire basculer toute votre application ou certains microservices spécifiques entre différentes régions ou différents clouds.

## <a name="ssl-configuration"></a>Configuration SSL

### <a name="what-tls-versions-are-supported-by-azure-front-door-service"></a>Quelles sont les versions de TLS prises en charge par Azure Front Door Service ?

Tous les profils Front Door créés après septembre 2019 utilisent TLS 1.2 comme configuration minimale par défaut.

Front Door prend en charge les versions TLS 1.0, 1.1 et 1.2. TLS 1.3 n’est pas encore pris en charge.

### <a name="what-certificates-are-supported-on-azure-front-door-service"></a>Quels sont les certificats pris en charge sur Azure Front Door Service ?

Pour permettre au protocole HTTPS de distribuer le contenu de manière sécurisée sur un domaine personnalisé Front Door, vous pouvez choisir d’utiliser un certificat managé par Azure Front Door Service ou votre propre certificat.
L’option managée par Front Door provisionne un certificat SSL standard délivré par le biais de Digicert et stocké dans le coffre de clés Front Door. Si vous choisissez d’utiliser votre propre certificat, vous pouvez intégrer un certificat délivré par une autorité de certification prise en charge ; il peut s’agir d’un certificat SSL standard, d’un certificat de validation étendue ou même d’un certificat générique. Les certificats auto-signés ne sont pas pris en charge. Découvrez [comment activer le protocole HTTPS pour un domaine personnalisé](https://aka.ms/FrontDoorCustomDomainHTTPS).

### <a name="does-front-door-support-autorotation-of-certificates"></a>Front Door prend-il en charge la rotation automatique des certificats ?

Concernant l’option de certificat managé Front Door, Front Door assure la rotation automatique des certificats. Si vous utilisez un certificat géré Front Door et que vous voyez que la date d’expiration du certificat est inférieure à 60 jours, envoyez un ticket de support.
</br>Pour votre propre certificat SSL personnalisé, la rotation automatique n’est pas prise en charge. À l’image de sa configuration initiale pour un domaine personnalisé donné, vous devez pointer Front Door sur la version de certificat appropriée dans votre coffre de clés et vous assurer que le principal de service pour Front Door a toujours accès au coffre de clés. Cette opération de déploiement de certificat mis à jour par Front Door est atomique et n’a aucun impact sur la production, à condition que le nom de l’objet ou le SAN pour le certificat ne change pas.

### <a name="what-are-the-current-cipher-suites-supported-by-azure-front-door-service"></a>Quelles sont les suites de chiffrement actuellement prises en charge par Azure Front Door Service ?

Voici les suites de chiffrement actuellement prises en charge par Azure Front Door Service :

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
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384

### <a name="does-azure-front-door-service-also-support-re-encryption-of-traffic-to-the-backend"></a>Azure Front Door Service prend-il également en charge le nouveau chiffrement du trafic vers le back-end ?

Oui, Azure Front Door Service prend en charge le déchargement SSL et le protocole SSL de bout en bout, qui chiffre à nouveau le trafic vers le back-end. En fait, étant donné que les connexions au back-end s’effectuent par le biais de son adresse IP publique, nous vous recommandons de configurer votre porte d’entrée de façon à utiliser HTTPS comme protocole de transfert.

### <a name="can-i-configure-ssl-policy-to-control-ssl-protocol-versions"></a>Puis-je configurer la stratégie SSL pour gérer les versions du protocole SSL ?

Vous pouvez configurer une version TLS minimale dans Azure Front Door via l’[API Azure REST](https://docs.microsoft.com/rest/api/frontdoorservice/frontdoor/frontdoors/createorupdate#minimumtlsversion). Actuellement, vous pouvez choisir entre 1.0 et 1.2.

### <a name="can-i-configure-front-door-to-only-support-specific-cipher-suites"></a>Puis-je configurer Front Door pour prendre en charge uniquement des suites de chiffrement spécifiques ?

Non, la configuration de Front Door pour des suites de chiffrement spécifiques n’est pas prise en charge. 

## <a name="diagnostics-and-logging"></a>Diagnostics et journalisation

### <a name="what-types-of-metrics-and-logs-are-available-with-azure-front-door-service"></a>Quels sont les types de métriques et de journaux disponibles avec Azure Front Door Service ?

Pour plus d’informations sur les journaux et autres fonctionnalités de diagnostic, consultez [Supervision des métriques et des journaux pour Front Door](front-door-diagnostics.md).

### <a name="what-is-the-retention-policy-on-the-diagnostics-logs"></a>Quelle est la stratégie de conservation sur les journaux de diagnostic ?

Les journaux de diagnostic circulent vers le compte de stockage des clients, et les clients peuvent définir la stratégie de rétention en fonction de leurs préférences. Les journaux de diagnostic peuvent également être envoyés à un hub d’événements ou à des journaux d’activité Azure Monitor. Pour plus d’informations, consultez [Diagnostics Azure Front Door Service](front-door-diagnostics.md).

### <a name="how-do-i-get-audit-logs-for-azure-front-door-service"></a>Comment obtenir des journaux d’audit pour Azure Front Door Service ?

Des journaux d’audit sont disponibles pour Azure Front Door Service. Dans le portail, cliquez sur **Journal d’activité** dans le panneau du menu de votre porte d’entrée pour accéder au journal d’audit. 

### <a name="can-i-set-alerts-with-azure-front-door-service"></a>Puis-je définir des alertes avec Azure Front Door Service ?

Oui, Azure Front Door Service prend en charge les alertes. Les alertes sont configurées d’après les métriques utilisées. 

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).
