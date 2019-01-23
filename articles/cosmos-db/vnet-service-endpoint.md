---
title: Sécuriser l’accès à un compte Azure Cosmos DB à l’aide du point de terminaison de service Réseau virtuel Azure
description: Ce document présente le contrôle d’accès à un réseau virtuel et à un sous-réseau pour un compte Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 25a05df42029fe444b8d5ceddb2972f779f1b232
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358726"
---
# <a name="access-azure-cosmos-db-resources-from-virtual-networks"></a>Accéder aux ressources Azure Cosmos DB à partir de réseaux virtuels

Vous pouvez configurer les comptes Azure Cosmos DB pour autoriser l’accès uniquement à partir d’un sous-réseau spécifique de réseau virtuel Azure (VNET). En activant l’option [Point de terminaison de service](../virtual-network/virtual-network-service-endpoints-overview.md) pour accéder à Azure Cosmos DB sur le sous-réseau au sein d’un réseau virtuel, le trafic à partir de ce sous-réseau est envoyé à Azure Cosmos DB avec l’identité du sous-réseau et du réseau virtuel. Une fois le point de terminaison de service Azure Cosmos DB activé, vous pouvez limiter l’accès au sous-réseau en l’ajoutant à votre compte Azure Cosmos.

Par défaut, un compte Azure Cosmos est accessible depuis n’importe quelle source tant que la demande est accompagnée d’un jeton d’autorisation valide. Lorsque vous ajoutez un ou plusieurs sous-réseaux au sein de réseaux virtuels, seules les requêtes provenant de ces sous-réseaux obtiendront une réponse valide. Les requêtes provenant de toute autre source recevront une réponse 403 (Interdit). 

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

Voici quelques questions fréquemment posées sur la configuration de l’accès à partir de réseaux virtuels :

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Puis-je spécifier à la fois le point de terminaison du service de réseau virtuel et la stratégie de contrôle d’accès IP sur un compte Azure Cosmos ? 

Vous pouvez activer à la fois le point de terminaison du service de réseau virtuel et une stratégie de contrôle d’accès IP (également appelé pare-feu) sur votre compte Azure Cosmos. Ces deux fonctionnalités sont complémentaires et garantissent collectivement l’isolation et la sécurité de votre compte Azure Cosmos. L’utilisation du pare-feu IP permet de s’assurer que les adresses IP statiques peuvent accéder à votre compte. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Comment limiter l’accès à un sous-réseau au sein d’un réseau virtuel ? 

Il existe deux étapes requises pour limiter l’accès au compte Azure Cosmos à partir d’un sous-réseau. Tout d’abord, vous autorisez le trafic provenant du sous-réseau afin de communiquer à Azure Cosmos DB l’identité de son sous-réseau et de son réseau virtuel. Pour cela, vous activez le point de terminaison de service pour Azure Cosmos DB sur le sous-réseau. Ensuite, vous ajoutez une règle au compte Azure Cosmos en spécifiant ce sous-réseau comme source à partir de laquelle le compte est accessible.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Les listes de contrôle d'accès (ACL) du réseau virtuel et le pare-feu IP rejetteront-ils les requêtes ou les connexions ? 

Quand un pare-feu IP ou des règles d’accès à un réseau virtuel sont ajoutés, seules les requêtes provenant de sources autorisées obtiennent des réponses valides. Les autres requêtes sont rejetées avec une erreur 403 (Interdit). Il est important de distinguer le pare-feu du compte Azure Cosmos d’un pare-feu au niveau de la connexion. La source peut toujours se connecter au service et les connexions elles-mêmes ne sont pas rejetées.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Mes requêtes ont été bloquées lorsque j’ai activé le point de terminaison de service dans Azure Cosmos DB sur le sous-réseau. Que s’est-il passé ?

Une fois que le point de terminaison de service pour Azure Cosmos DB est activé sur un sous-réseau, la source du trafic qui atteint le compte bascule de l’adresse IP publique vers le réseau virtuel et le sous-réseau. Si votre compte Azure Cosmos est uniquement protégé par un pare-feu basé sur IP, le trafic provenant du sous-réseau avec service ne respecte plus les règles du pare-feu IP et, par conséquent, il sera rejeté. Passez en revue les étapes pour migrer en toute transparence d’un pare-feu basé sur IP à un contrôle d’accès basé sur un réseau virtuel.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Les réseaux virtuels homologués ont-ils également accès au compte Azure Cosmos ? 
Seuls le réseau virtuel et ses sous-réseaux ajoutés au compte Azure Cosmos y ont accès. Leurs réseaux virtuels homologués ne peuvent pas accéder au compte tant que les sous-réseaux au sein des réseaux virtuels homologués n’ont pas été ajoutés au compte.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Quel est le nombre maximal de sous-réseaux autorisés à accéder à un seul compte Cosmos ? 
Actuellement, vous pouvez avoir au maximum 64 sous-réseaux autorisés pour un compte Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Puis-je activer l’accès à partir d’un VPN et d’Express Route ? 
Pour accéder localement au compte Azure Cosmos via ExpressRoute, vous devez activer l’homologation Microsoft. Lorsque vous activez un pare-feu IP ou définissez des règles d’accès à un réseau virtuel, vous pouvez ajouter les adresses IP publiques utilisées pour l’homologation Microsoft au pare-feu IP de votre compte Azure Cosmos afin d’autoriser l’accès des services locaux au compte Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Dois-je mettre à jour les règles des groupes de sécurité réseau (NSG) ? 
Les règles des groupes de sécurité réseau permettent de limiter la connectivité vers et depuis un sous-réseau de réseau virtuel. Lorsque vous ajoutez un point de terminaison de service pour Azure Cosmos DB au sous-réseau, inutile d’ouvrir la connectivité sortante dans le groupe de sécurité réseau pour votre compte Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Existe-t-il des points de terminaison de service pour tous les réseaux virtuels ?
Non, seuls les réseaux virtuels Azure Resource Manager peuvent bénéficier d’un point de terminaison de service. Les réseaux virtuels classiques ne prennent pas en charge les points de terminaison de service.


## <a name="next-steps"></a>Étapes suivantes

* [Comment limiter l’accès au compte Azure Cosmos à des sous-réseaux au sein de réseaux virtuels](how-to-configure-vnet-service-endpoint.md)
* [Comment configurer le pare-feu IP de votre compte Azure Cosmos](how-to-configure-firewall.md)

