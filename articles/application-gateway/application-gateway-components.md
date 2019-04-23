---
title: Composants de passerelle d’application
description: Cet article fournit des informations sur les différents composants dans une passerelle d’application
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60008067"
---
# <a name="application-gateway-components"></a>Composants de passerelle d’application

 Une passerelle d’application sert de point unique de contact pour les clients. Elle distribue le trafic applicatif entrant dans plusieurs pools principaux, qui incluent des machines virtuelles Azure, machines virtuelles identiques, Azure App Service et les serveurs sur site/externe. Pour distribuer le trafic, une passerelle d’application utilise plusieurs composants décrits dans cet article.

![Les composants utilisés dans une passerelle d’application](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Adresses IP de Frontend

Une adresse IP frontale est l’adresse IP associée à une passerelle d’application. Vous pouvez configurer une passerelle d’application pour avoir une adresse IP publique, une adresse IP privée ou les deux. Une passerelle d’application prend en charge un public ou une adresse IP privée. Votre réseau virtuel et une adresse IP publique doivent être dans le même emplacement que votre passerelle d’application. Après sa création, une adresse IP frontale est associée à un écouteur.

### <a name="static-versus-dynamic-public-ip-address"></a>Statiques et l’adresse IP publique dynamique

La référence (SKU) v2 d’Azure Application Gateway prend en charge à la fois internes et statiques adresses IP publiques statiques, bien que la référence (SKU) v1 prend en charge uniquement les adresses IP internes statiques. L’adresse IP virtuelle (VIP) peut changer si une passerelle d’application est arrêtée et démarrée.

Le nom DNS associé à une passerelle d’application ne change pas avec le cycle de vie de la passerelle. Par conséquent, vous devez utiliser un alias CNAME et pointer vers l’adresse DNS de la passerelle d’application.

## <a name="listeners"></a>Écouteurs

Un écouteur est une entité logique qui vérifie les demandes de connexion entrantes. Un écouteur accepte une demande si le protocole, port, hôte et adresse IP associée à la demande correspondent les mêmes éléments associés à la configuration de l’écouteur.

Avant d’utiliser une passerelle d’application, vous devez ajouter au moins un écouteur. Il peut y avoir plusieurs écouteurs attachés à une passerelle d’application, et ils peuvent être utilisés pour le même protocole.

Une fois un écouteur détecte les demandes entrantes des clients, la passerelle d’application achemine ces demandes aux membres du pool principal. La passerelle d’application utilise les règles de routage de demande définis pour l’écouteur qui a reçu la demande entrante.

Écouteurs prend en charge les ports et protocoles suivants.

### <a name="ports"></a>Ports

Un port est où un écouteur écoute la demande du client. Vous pouvez configurer les ports compris entre 1 et 65502 pour la référence SKU v1 et 1 à 65199 pour la référence SKU v2.

### <a name="protocols"></a>Protocoles

Application Gateway prend en charge les quatre protocoles : HTTP, HTTPS, HTTP/2 et WebSocket :

- Spécifiez entre HTTP et HTTPS protocoles dans la configuration de l’écouteur.
- Prise en charge de [protocoles WebSocket et HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) est fourni en mode natif, et [prise en charge WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) est activée par défaut. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. Utiliser des WebSockets avec des écouteurs HTTP et HTTPS.
- La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant aux écouteurs Application Gateway uniquement. La communication avec les pools du serveur principal est sur HTTP/1.1. Par défaut, la prise en charge du protocole HTTP/2 est désactivée. Vous pouvez choisir pour l’activer.

Utilisez un écouteur HTTPS pour un arrêt SSL. Un écouteur HTTPS permet de décharger le travail de chiffrement et le déchiffrement à votre passerelle d’application, afin de vos serveurs web ne sont pas encombrés par surcharge. Vos applications sont ensuite libres de se concentrer sur la logique métier.

### <a name="custom-error-pages"></a>Pages d’erreur personnalisées

Application Gateway vous permet de créer des pages d’erreurs personnalisées au lieu d’afficher des pages d’erreurs par défaut. Vous pouvez utiliser votre marque et votre mise en page personnelle à l’aide d’une page d’erreur personnalisée. Passerelle d’application affiche une page d’erreur personnalisée lorsqu’une demande ne peut pas atteindre le serveur principal.

Pour plus d’informations, consultez [de pages d’erreur personnalisées pour votre passerelle d’application](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Types de ports d’écoute

Il existe deux types de ports d’écoute :

- **De base**. Ce type de l’écouteur écoute sur un site de domaine unique, où il a un seul mappage DNS à l’adresse IP de la passerelle d’application. Cette configuration de l’écouteur est requise lorsque vous hébergez un site unique derrière une passerelle d’application.

- **Plusieurs sites**. Cette configuration de l’écouteur est requise lorsque vous configurez plusieurs applications web sur la même instance de passerelle d’application. Il vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu'à 100 sites Web à une passerelle application gateway. Chaque site web peut être dirigé vers son propre pool principal. Par exemple, trois des sous-domaines, abc.contoso.com, xyz.contoso.com et pqr.contoso.com, pointent sur l’adresse IP de la passerelle d’application. Vous créeriez trois écouteurs multisites et configurer chaque port d’écoute pour le paramètre de protocole et le port respectif.

    Pour plus d’informations, consultez [l’hébergement de plusieurs sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Après avoir créé un écouteur, associez-la à une règle de routage de demande. Cette règle détermine comment la demande reçue sur l’écouteur doit être routée vers le serveur principal.

Passerelle d’application traite les écouteurs dans l’ordre indiqué. Si l’écouteur de base correspond à une demande entrante, elle est traitée en premier. Pour acheminer le trafic vers le serveur principal correct, configurer un écouteur multisite avant un écouteur de base.

## <a name="request-routing-rules"></a>Règles de routage de demande

Une règle de routage de demande est un composant clé d’une passerelle d’application, car il détermine comment acheminer le trafic sur l’écouteur. La règle lie l’écouteur, le pool de serveurs back-end et les paramètres de serveur principal HTTP.

Lorsqu’un écouteur accepte une demande, la règle de routage de demande transfère la requête au serveur principal ou redirige ailleurs. Si la demande est transmise au serveur principal, la règle de routage de demande définit quel pool de serveurs principaux pour le transmettre à. En outre, la règle de routage de demande détermine également si les en-têtes dans la demande doivent être réécrites. Un écouteur peut être attaché à une seule règle.

Il existe deux types de règles de routage de demande :

- **De base**. Toutes les demandes sur l’écouteur associé (par exemple, blog.contoso.com/*) sont transférées vers le pool principal associé à l’aide du paramètre HTTP associé.

- **Basé sur le chemin**. Cette règle de routage vous permet d’acheminer les demandes sur l’écouteur associé à un pool de serveur principal spécifique, basé sur l’URL dans la demande. Si le chemin d’accès de l’URL dans une requête correspond au modèle de chemin d’accès dans une règle basée sur le chemin d’accès, la règle achemine cette demande. Il s’applique le modèle de chemin d’accès uniquement pour le chemin d’URL, et non à ses paramètres de requête. Si le chemin d’URL sur une demande de l’écouteur ne correspond pas à aucune des règles de chemin d’accès, il achemine la demande vers le pool principal par défaut et les paramètres HTTP.

Pour plus d’informations, consultez [basé sur l’URL de routage](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Prise en charge de la redirection

La règle de routage de demande vous permet également de rediriger le trafic sur la passerelle d’application. Il s’agit d’un mécanisme de redirection générique, afin que vous pouvez rediriger vers et à partir de n’importe quel port que vous définissez à l’aide de règles.

Vous pouvez choisir la cible de redirection doit être un autre écouteur (qui peut aider à activer automatique redirection HTTP vers HTTPS) ou un site externe. Vous pouvez également choisir d’utiliser la redirection temporaire ou permanente, ou pour ajouter la chaîne de chemin d’accès et de requête URI à l’URL redirigée.

Pour plus d’informations, consultez [rediriger le trafic sur votre passerelle d’application](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Réécrire les en-têtes HTTP

En utilisant les règles de routage de demande, vous pouvez ajouter, supprimer ou mettre à jour de la demande de HTTP (S) et les en-têtes de réponse en tant que les paquets de demande et de réponse déplacement pools entre le client et le serveur principal via la passerelle d’application.

Les en-têtes peuvent être définis à des valeurs statiques ou à d’autres en-têtes et les variables de serveur. Cela permet par importante cas d’utilisation, telles que l’extraction des adresses IP client, suppression des informations sensibles sur le serveur principal, en ajoutant davantage de sécurité et ainsi de suite.

Pour plus d’informations, consultez [en-têtes HTTP de réécriture sur votre passerelle d’application](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Paramètres HTTP

Une passerelle application gateway achemine le trafic vers les serveurs principaux (spécifié dans la règle de routage de demande qui incluent des paramètres HTTP) en utilisant le numéro de port, protocole et d’autres paramètres détaillés dans ce composant.

Le port et le protocole utilisé dans les paramètres HTTP déterminent si le trafic entre les serveurs de passerelle et le serveur principal d’application est chiffré (à condition SSL de bout en bout) ou non chiffrés.

Ce composant est également utilisé pour :

- Déterminer si une session utilisateur doit être conservé sur le même serveur à l’aide de la [affinité de session basée sur les cookies](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Supprimer correctement les membres du pool principal à l’aide de [drainage de connexion](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Associer une sonde personnalisée pour surveiller l’intégrité du serveur principal, définir l’intervalle de délai d’attente de demande, remplacer le nom d’hôte et le chemin d’accès dans la demande et fournir facilement un clic pour spécifier des paramètres pour le backend App Service.

## <a name="backend-pools"></a>Pools principaux

Un pool principal achemine la demande vers les serveurs principaux, qui fournit la demande. Les pools backend peuvent contenir :

- Cartes réseau
- Groupes identiques de machines virtuelles 
- Adresses IP publiques
- Adresses IP internes
- Nom de domaine complet
- Serveurs principaux multi-locataires (par exemple, App Service)

Membres du pool backend Application Gateway ne sont pas liées à un groupe à haute disponibilité. Une passerelle d’application peut communiquer avec des instances en dehors du réseau virtuel qu’il est. Par conséquent, les membres des pools principaux peuvent être sur des clusters, entre les centres de données, ou en dehors d’Azure, tant qu’il existe une connectivité de l’adresse IP.

Si vous utilisez des adresses IP internes en tant que membres du pool principal, vous devez utiliser [homologation de réseaux virtuels](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou un [passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Homologation de réseaux virtuels est pris en charge utile pour l’équilibrage de charge le trafic et d’autres réseaux virtuels.

Une passerelle d’application peut également communiquer avec aux serveurs locaux lorsqu’elles sont connectées par Azure ExpressRoute ou des tunnels VPN si le trafic est autorisé.

Vous pouvez créer des pools principaux différents pour différents types de demandes. Par exemple, créez un pool principal pour les demandes générales et un autre pool principal pour les demandes pour les microservices pour votre application.

## <a name="health-probes"></a>Sondes d’intégrité

Par défaut, une passerelle d’application surveille l’intégrité de toutes les ressources dans son pool principal et supprime automatiquement celles défectueux. Il surveille les instances défectueuses et les ajoute au pool principal sain lorsqu’ils deviennent disponibles et répondent aux sondes d’intégrité.

En plus d’utiliser la surveillance par sonde d’intégrité par défaut, vous pouvez aussi personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Les sondes personnalisées permettent un contrôle plus précis sur l’analyse d’intégrité. Lorsque vous utilisez des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL du chemin à tester et des réponses en échec combien accepter avant de l’instance de pool principal soit marquée comme défectueuse. Nous vous recommandons de configurer les sondes personnalisées pour surveiller l’intégrité de chaque pool principal.

Pour plus d’informations, consultez [surveiller l’intégrité de votre passerelle d’application](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Étapes suivantes

Créer une passerelle Application Gateway :

* [Dans le portail Azure](quick-create-portal.md)
* [À l’aide d’Azure PowerShell](quick-create-powershell.md)
* [À l’aide de l’interface CLI Azure](quick-create-cli.md)
