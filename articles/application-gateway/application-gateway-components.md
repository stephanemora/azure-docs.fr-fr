---
title: Composants de passerelle d’Application Azure
description: Cet article fournit des informations sur les différents composants de la passerelle d’Application
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 44c8b331ebb258c39a003c91e0711e6dfb87cb12
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58076291"
---
# <a name="application-gateway-components"></a>Composants de passerelle d’application

 Une passerelle d’application sert de point unique de contact pour les clients. Elle distribue le trafic applicatif entrant dans plusieurs pools principaux, tels que les machines virtuelles Azure, machines virtuelles identiques, Services d’application ou les serveurs sur site/externe. Pour ce faire, il utilise plusieurs composants décrits dans cet article.

![application-gateway-components](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-address"></a>Adresse IP du serveur frontal

Une adresse IP frontale est l’adresse IP associée à la passerelle d’application. Vous pouvez configurer la passerelle d’application pour avoir une adresse IP publique ou une adresse IP privée ou les deux. Une seule adresse IP publique ou une adresse IP privée est pris en charge. Votre réseau virtuel et une adresse IP publique doivent être dans le même emplacement que votre passerelle d’application.

Une adresse IP de Frontend est associée à un *écouteur* après sa création. 

### <a name="static-vs-dynamic-public-ip-address"></a>Adresse IP publique dynamique et statique

La référence (SKU) v1 prend en charge les adresses IP internes statiques, mais ne prend pas en charge les adresses IP publiques statiques. L’adresse IP virtuelle peut changer si la passerelle d’application est arrêtée et démarrée. Le nom DNS associé à la passerelle d’application ne change pas pendant toute la durée de vie de la passerelle. Pour cette raison, il est recommandé d’utiliser un alias CNAME et de le faire pointer vers l’adresse DNS de la passerelle d’application.

La référence SKU de passerelle d’Application v2 prend en charge les adresses IP publiques statiques, ainsi que des adresses IP internes statiques. 

## <a name="listeners"></a>Écouteurs

Avant de commencer à l’aide de votre passerelle d’application, vous devez ajouter un ou plusieurs écouteurs. Un écouteur est une entité logique qui vérifie les demandes de connexion entrantes et accepte les demandes si le protocole de port, hôte et adresse IP associé à la demande de correspondance avec le protocole, la port, l’hôte et l’adresse IP associée à la configuration de l’écouteur. Il peut y avoir plusieurs écouteurs attachés à une passerelle d’application, et ils peuvent être utilisés pour le même protocole. Une fois que l’écouteur détecte les demandes entrantes des clients, la passerelle d’Application achemine ces demandes vers les membres du pool principal, en utilisant les règles de routage de demande que vous définissez pour l’écouteur qui a reçu la demande entrante.

Écouteurs prennent en charge les ports et protocoles suivants :

### <a name="ports"></a>Ports

Il s’agit du port sur lequel l’écouteur écoute pour la demande du client. Vous pouvez configurer les ports compris entre 1 et 65502 pour la référence (SKU) V1 et 1 pour 65199 V2 référence (SKU).

### <a name="protocols"></a>Protocoles

Passerelle d’application prend en charge les quatre protocoles suivants : HTTP, HTTPS, HTTP/2 et WebSocket

Vous spécifiez explicitement le choix entre les protocoles HTTP et HTTPS dans la configuration de l’écouteur. Le [prennent en charge des protocoles WebSocket et HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) est fourni en mode natif. [Prise en charge WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) est activée par défaut. Il n’existe aucun paramètre configurable par l’utilisateur permettant d’activer ou de désactiver de manière sélective la prise en charge de WebSocket. Vous pouvez utiliser les WebSockets avec des écouteurs HTTP et HTTPS. La prise en charge du protocole HTTP/2 est disponible pour les clients se connectant aux écouteurs Application Gateway uniquement. La communication avec les pools du serveur principal est sur HTTP/1.1. Par défaut, la prise en charge du protocole HTTP/2 est désactivée. Vous pouvez choisir pour l’activer.

Vous pouvez utiliser un écouteur HTTPS pour un arrêt SSL. Un écouteur HTTPS décharge le chiffrement et déchiffrement fonctionnent à votre passerelle d’application afin que vos serveurs web ne sont pas une prise en charge par cette surcharge. Vos applications sont ensuite libres de se concentrer sur leur logique métier.

### <a name="custom-error-pages"></a>Pages d’erreur personnalisées

Application gateway vous permet de créer des pages d’erreurs personnalisées au lieu d’afficher des pages d’erreurs par défaut. Vous pouvez utiliser votre marque et votre mise en page personnelle à l’aide d’une page d’erreur personnalisée. Application Gateway peut afficher une page d’erreur personnalisée lorsqu’une requête ne peut pas atteindre le serveur principal. Pour plus d’informations, consultez [de pages d’erreur personnalisées pour votre passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Types de ports d’écoute

Il existe deux types de ports d’écoute :

- **De base** : Ce type de l’écouteur écoute sur un site de domaine unique où il a un seul mappage DNS à l’adresse IP de la passerelle d’application. Cette configuration de l’écouteur est requise lorsque vous hébergez un site unique derrière une passerelle d’application.
- **Plusieurs sites**: Cette configuration de l’écouteur est requise lorsque vous configurez plusieurs applications web sur la même instance de passerelle d’application. Cela vous permet de configurer une topologie plus efficace pour vos déploiements en ajoutant jusqu'à 100 sites Web à une passerelle application gateway. Chaque site web peut être dirigé vers son propre pool principal. Par exemple :   Pour les trois sous-domaines — abc.contoso.com, xyz.contoso.com et pqr.contoso.com pointant vers l’adresse IP de la passerelle d’application. Créer trois écouteurs du type *multisite* et configurer chaque port d’écoute pour le port respectif et paramètre de protocole. Pour plus d’informations, consultez [hébergeant plusieurs sites](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Après avoir créé un écouteur, associez-la à une règle de routage de demande qui détermine comment la demande reçue sur l’écouteur doit être acheminé vers le serveur principal.

Les écouteurs sont traités selon leur ordre d’affichage. Pour cette raison, si un écouteur de base correspond à une demande entrante il traite en premier. Les écouteurs multisites doivent être configurés avant un écouteur élémentaire pour garantir l’acheminement du trafic vers le serveur back-end correct.

## <a name="request-routing-rule"></a>Règle de routage des demandes

Ceci est le composant le plus important de la passerelle d’application et détermine la façon dont le trafic sur l’écouteur associé à cette règle est acheminé. La règle lie l’écouteur, le pool de serveurs back-end et les paramètres de serveur principal HTTP. Une fois qu’une demande est acceptée par un écouteur, la règle de routage de demande détermine si la demande doit être transféré vers le serveur principal ou redirigée. Si la demande est déterminée pour être transféré vers le serveur principal, la règle de routage de demande définit quel pool de serveurs back-end doit être transféré vers. En outre, la règle de routage de demande détermine également si les en-têtes dans la demande doivent être réécrites. Un écouteur peut être attaché à une seule règle.

Il peut y avoir deux types de règles de routage de demande :

- **De base :** Toutes les demandes sur l’écouteur associé (par exemple : blog.contoso.com/*) sont transférés vers le pool back-end associé à l’aide du paramètre HTTP associé.
- **Basé sur le chemin d’accès :** Ce type de règle vous permet d’acheminer les demandes sur l’écouteur associé à un pool principal spécifique basé sur l’URL dans la demande. Si le chemin d’accès de l’URL dans une requête correspond au modèle de chemin d’accès dans une règle basée sur le chemin d’accès, la demande est acheminée à l’aide de cette règle. Le modèle de chemin d’accès est appliqué uniquement pour le chemin d’accès de l’URL, pas à ses paramètres de requête. Si le chemin d’accès de l’URL d’une demande sur un port d’écoute ne correspond à aucune les règles basées sur le chemin d’accès, la demande est acheminée vers le *par défaut* pool principal et le *par défaut* paramètres HTTP. Pour plus d’informations, consultez [routage basé sur URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Prise en charge de la redirection

La règle de routage de demande vous permet également de rediriger le trafic sur la passerelle d’application. Il s’agit d’un mécanisme de redirection générique, vous pouvez ainsi rediriger depuis et vers n’importe quel port défini à l’aide de règles. Vous pouvez choisir la cible de redirection doit être un autre écouteur (qui peut aider à activer automatique redirection HTTP vers HTTPS) ou un site externe, choisissez la redirection temporaire ou permanente ou choisissez l’ajout de la chaîne de chemin d’accès et de requête URI à l’URL redirigée. Pour plus d’informations, consultez [rediriger le trafic sur votre passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Réécrire les en-têtes HTTP

À l’aide de demander les règles de routage de demande vous pouvez ajouter, supprimer ou mettre à jour de HTTP (S) et les en-têtes de réponse, alors que la demande et les paquets de réponse de se déplacent entre les pools de client et serveur principal, via la passerelle d’application. Les en-têtes ne peuvent pas être uniquement définis à des valeurs statiques, mais également à d’autres en-têtes et les variables d’importantes du serveur. Cela vous aidera à accomplir plusieurs cas d’utilisation important, telles que l’extraction des adresses IP des clients, suppression des informations sensibles sur le serveur principal, ajout de mesures de sécurité supplémentaires, etc. Pour plus d’informations, consultez [en-têtes HTTP de réécriture sur votre passerelle d’application](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Paramètres HTTP

Les itinéraires de passerelle d’application du trafic vers le serveur principal (spécifié dans la règle de routage de demande auquel sont associés les paramètres HTTP) à l’aide de son numéro de port de protocole et d’autres paramètres spécifiés dans ce composant. Le port et le protocole utilisé dans les paramètres HTTP déterminent si le trafic entre les serveurs de passerelle et le serveur principal d’applications est chiffré, par conséquent, pour accomplir SSL de bout en bout ou non chiffrés. Ce composant est également utilisé pour : déterminer si une session utilisateur doit être conservé sur le même serveur à l’aide de la [affinité de session basée sur les cookies](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), effectuer une suppression sans perte de données de membres du pool principal à l’aide de [connexion drainage](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining), associer une sonde personnalisée pour surveiller l’intégrité du serveur principal, de définir l’intervalle de délai d’attente de demande, de remplacer le nom d’hôte et le chemin d’accès dans la demande et de fournir facilement un clic pour spécifier le paramètre du principal de serveur principal de service App. 

## <a name="backend-pool"></a>Pool principal

Le pool principal est utilisé pour acheminer les demandes vers les serveurs principaux, qui fournit la demande. Pools backend peuvent être composés de cartes réseau, la machine virtuelle à l’échelle jeux, public IP adresses, interne IP adresses, nom de domaine complet et architecture mutualisée des serveurs principaux tels que Azure App Service. Membres du pool backend application gateway ne sont pas liés à un groupe à haute disponibilité. Application Gateway peut communiquer avec des instances en dehors du réseau virtuel qu’il se trouve dans, par conséquent, les membres des pools principaux peuvent être sur des clusters, les centres de données, ou en dehors d’Azure, tant qu’il existe une connectivité de l’adresse IP. Si vous prévoyez d’utiliser des adresses IP internes en tant que membres du pool backend, il vous faudra utiliser [VNET Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) ou [une passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). L’homologation est pris en charge et est utile pour l’équilibrage de charge de trafic dans les autres réseaux virtuels. La passerelle d’application peut également communiquer avec aux serveurs locaux lorsqu’ils sont connectés via ExpressRoute ou des tunnels VPN, tant que le trafic est autorisé.

Vous pouvez créer des pools principaux différents pour différents types de demandes. Par exemple, créez un pool principal pour les demandes générales et l’autre pool principal pour les demandes pour les microservices pour votre application.

## <a name="health-probes"></a>Sondes d’intégrité

Passerelle d’application par défaut analyse l’intégrité de toutes les ressources dans son pool principal et supprime automatiquement les ressources considérées comme non intègre du pool. Il continue à surveiller les instances défaillantes et les ajoute au pool principal intègre une fois qu’ils deviennent disponibles et répondent aux sondes d’intégrité. En plus d’utiliser la surveillance par sonde d’intégrité par défaut, vous pouvez aussi personnaliser la sonde d’intégrité pour répondre aux exigences de votre application. Les sondes personnalisées vous permettent d’avoir un contrôle plus précis de l’analyse de l’intégrité. Lorsque vous utilisez des sondes personnalisées, vous pouvez configurer l’intervalle d’analyse, l’URL du chemin à tester et des réponses en échec combien accepter avant de l’instance de pool back-end est considéré comme défectueux. Il est recommandé de configurer les sondes personnalisées pour surveiller l’intégrité de chaque pool principal. Pour plus d’informations, consultez [surveiller l’intégrité de votre passerelle d’Application](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Étapes suivantes

Après avoir découvert les composants de la passerelle d’Application, vous pouvez :
* [Créer une passerelle d’Application dans le portail Azure](quick-create-portal.md)
* [Créer une passerelle d’Application à l’aide d’Azure PowerShell](quick-create-powershell.md)
* [Créer une passerelle d’Application à l’aide de l’interface CLI Azure](quick-create-cli.md)
