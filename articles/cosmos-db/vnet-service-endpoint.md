---
title: Sécuriser l’accès aux comptes Azure Cosmos DB à l’aide du point de terminaison de service de réseau virtuel
description: Ce document présente le contrôle d’accès à un réseau virtuel et à un sous-réseau pour un compte Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: af1032de9aabac45ad7a86cfe1f36ed2c04c0f71
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444622"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Accéder à Azure Cosmos DB à partir de réseaux virtuels (VNet)

Vous pouvez configurer le compte Azure Cosmos pour n'autoriser l'accès qu'à partir d'un sous-réseau spécifique de réseau virtuel (VNet). En activant l’option [Point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) pour accéder à Azure Cosmos DB sur le sous-réseau au sein d’un réseau virtuel, le trafic à partir de ce sous-réseau est envoyé à Azure Cosmos DB avec l’identité du sous-réseau et du réseau virtuel. Une fois le point de terminaison de service Azure Cosmos DB activé, vous pouvez limiter l’accès au sous-réseau en l’ajoutant à votre compte Azure Cosmos.

Par défaut, un compte Azure Cosmos est accessible depuis n’importe quelle source tant que la demande est accompagnée d’un jeton d’autorisation valide. Lorsque vous ajoutez un ou plusieurs sous-réseaux au sein de réseaux virtuels, seules les requêtes provenant de ces sous-réseaux obtiendront une réponse valide. Les requêtes provenant de toute autre source recevront une réponse 403 (Interdit). 

## <a name="frequently-asked-questions"></a>Forum aux questions

Voici quelques questions fréquemment posées sur la configuration de l’accès à partir de réseaux virtuels :

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Puis-je spécifier à la fois le point de terminaison de service de réseau virtuel et la stratégie de contrôle d’accès IP sur un compte Azure Cosmos ? 

Vous pouvez activer à la fois le point de terminaison de service de réseau virtuel et une stratégie de contrôle d’accès IP (également appelé pare-feu) sur votre compte Azure Cosmos. Ces deux fonctionnalités sont complémentaires et garantissent collectivement l’isolation et la sécurité de votre compte Azure Cosmos. L’utilisation du pare-feu IP permet de s’assurer que les adresses IP statiques peuvent accéder à votre compte. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Comment limiter l’accès à un sous-réseau au sein d’un réseau virtuel ? 

Il existe deux étapes requises pour limiter l’accès au compte Azure Cosmos à partir d’un sous-réseau. Tout d’abord, vous autorisez le trafic provenant du sous-réseau afin de communiquer à Azure Cosmos DB l’identité de son sous-réseau et de son réseau virtuel. Pour cela, vous activez le point de terminaison de service pour Azure Cosmos DB sur le sous-réseau. Ensuite, vous ajoutez une règle au compte Azure Cosmos en spécifiant ce sous-réseau comme source à partir de laquelle le compte est accessible.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Les listes de contrôle d'accès (ACL) du réseau virtuel et le pare-feu IP rejetteront-ils les requêtes ou les connexions ? 

Quand un pare-feu IP ou des règles d’accès à un réseau virtuel sont ajoutés, seules les requêtes provenant de sources autorisées obtiennent des réponses valides. Les autres requêtes sont rejetées avec une erreur 403 (Interdit). Il est important de distinguer le pare-feu du compte Azure Cosmos d’un pare-feu au niveau de la connexion. La source peut toujours se connecter au service et les connexions elles-mêmes ne sont pas rejetées.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mes requêtes ont été bloquées lorsque j’ai activé le point de terminaison de service dans Azure Cosmos DB sur le sous-réseau. Que s’est-il passé ?

Une fois que le point de terminaison de service pour Azure Cosmos DB est activé sur un sous-réseau, la source du trafic qui atteint le compte bascule de l’adresse IP publique vers le réseau virtuel et le sous-réseau. Si votre compte Azure Cosmos est uniquement protégé par un pare-feu basé sur IP, le trafic provenant du sous-réseau avec service ne respecte plus les règles du pare-feu IP et, par conséquent, il sera rejeté. Passez en revue les étapes pour migrer en toute transparence d’un pare-feu basé sur IP à un contrôle d’accès basé sur un réseau virtuel.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Des privilèges RBAC supplémentaires sont-ils nécessaires pour les comptes Azure Cosmos dotés de points de terminaison de service de réseau virtuel ?

Si vous souhaitez apporter des modifications aux paramètres de compte après avoir ajouté les points de terminaison de service de réseau virtuel à un compte Azure Cosmos, vous devez accéder à l’action `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` pour tous les réseaux virtuels configurés sur votre compte Azure Cosmos. Ce privilège est requis, car le processus d’autorisation valide l’accès aux ressources (telles que les ressources de base de données et de réseau virtuel) avant d’évaluer des propriétés.
 
L’autorisation valide le privilège pour l’action de ressource de réseau virtuel, même si l’utilisateur ne spécifie pas les ACL de réseau virtuel à l’aide d’Azure CLI. Actuellement, le plan de contrôle du compte Azure Cosmos prend en charge la configuration de l’état complet du compte Azure Cosmos. L’un des paramètres des appels du plan de contrôle est `virtualNetworkRules`. Si ce paramètre n’est pas spécifié, Azure CLI effectue un appel d’extraction de base de données pour récupérer `virtualNetworkRules` et utilise cette valeur dans l’appel de mise à jour.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Les réseaux virtuels homologués ont-ils également accès au compte Azure Cosmos ? 
Seuls le réseau virtuel et ses sous-réseaux ajoutés au compte Azure Cosmos y ont accès. Leurs réseaux virtuels homologués ne peuvent pas accéder au compte tant que les sous-réseaux des réseaux virtuels homologués ne sont pas ajoutés au compte.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Quel est le nombre maximal de sous-réseaux autorisés à accéder à un seul compte Cosmos ? 
Actuellement, vous pouvez avoir au maximum 64 sous-réseaux autorisés pour un compte Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Puis-je activer l’accès à partir d’un VPN et d’Express Route ? 
Pour accéder localement au compte Azure Cosmos via ExpressRoute, vous devez activer le Peering Microsoft. Lorsque vous activez un pare-feu IP ou définissez des règles d’accès à un réseau virtuel, vous pouvez ajouter les adresses IP publiques utilisées pour le peering Microsoft au pare-feu IP de votre compte Azure Cosmos afin d’autoriser l’accès des services locaux au compte Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Dois-je mettre à jour les règles des groupes de sécurité réseau (NSG) ? 
Les règles des groupes de sécurité réseau permettent de limiter la connectivité vers et depuis un sous-réseau de réseau virtuel. Lorsque vous ajoutez un point de terminaison de service pour Azure Cosmos DB au sous-réseau, inutile d’ouvrir la connectivité sortante dans le groupe de sécurité réseau pour votre compte Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Existe-t-il des points de terminaison de service pour tous les réseaux virtuels ?
Non, seuls les réseaux virtuels Azure Resource Manager peuvent bénéficier d’un point de terminaison de service. Les réseaux virtuels classiques ne prennent pas en charge les points de terminaison de service.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Puis-je « Accepter les connexions provenant des centres de données Azure publics » quand l’accès aux points de terminaison de service est activé pour Azure Cosmos DB ?  
Ceci est nécessaire uniquement lorsque vous souhaitez que votre compte Azure Cosmos DB soit accessible par d’autres services Azure internes tels qu’Azure Data Factory, Recherche cognitive Azure ou tout autre service déployé dans une région Azure donnée.


## <a name="next-steps"></a>Étapes suivantes

* [Comment limiter l’accès au compte Azure Cosmos à des sous-réseaux au sein de réseaux virtuels](how-to-configure-vnet-service-endpoint.md)
* [Comment configurer le pare-feu IP de votre compte Azure Cosmos](how-to-configure-firewall.md)

