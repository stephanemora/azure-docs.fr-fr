---
title: Migrer des ressources réseau Azure depuis Azure Allemagne vers Azure global
description: Cet article fournit des informations sur la migration de vos ressources réseau Azure depuis Azure Allemagne vers Azure global.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
ms.openlocfilehash: 2d6814653967fa76ad22bcefcc94df42d60d67b1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67033667"
---
# <a name="migrate-network-resources-to-global-azure"></a>Migrer des ressources réseau Azure vers Azure global

La plupart des services de mise en réseau ne prennent pas en charge la migration d’Azure Allemagne vers Azure global. Toutefois, vous pouvez connecter vos réseaux dans les deux environnements cloud à l’aide d’un VPN de site à site. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Les étapes à suivre pour configurer un VPN de site à site entre des clouds sont similaires aux étapes à suivre pour déployer un VPN de site à site entre votre réseau local et Azure. Définissez une passerelle dans les deux clouds, puis indiquez aux VPN comment communiquer entre eux. [Créer une connexion de site à site dans le Portail Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) décrit les étapes à suivre pour déployer un VPN de site à site. Voici un résumé des étapes :

1. Définissez un réseau virtuel.
1. Définissez un espace d’adressage.
1. Définissez des sous-réseaux.
1. Définissez un sous-réseau de passerelle.
1. Définissez une passerelle pour le réseau virtuel.
1. Définissez une passerelle pour le réseau local (votre appareil VPN local).
1. Configurez un appareil VPN local.
1. Créez la connexion.

Pour connecter des réseaux virtuels entre Azure global et Azure Allemagne :

1. Effectuez les étapes 1 à 5 de la procédure précédente dans Azure global.
1. Effectuez les étapes 1 à 5 dans Azure Allemagne.
1. Effectuez l’étape 6 dans Azure global :
   - Entrez l’adresse IP publique de la passerelle VPN dans Azure Allemagne.
1. Effectuez l’étape 6 dans Azure Allemagne :
   - Entrez l’adresse IP publique de la passerelle VPN dans Azure global.
1. Ignorez l’étape 7.
1. Effectuez l’étape 8.

## <a name="virtual-networks"></a>Réseaux virtuels

Actuellement, il n’est pas possible de migrer des réseaux virtuels depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer de nouveaux réseaux virtuels dans la région cible et de migrer des ressources dans ces réseaux virtuels.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur les réseaux virtuels Azure](https://docs.microsoft.com/azure/virtual-network/#step-by-step-tutorials).
- Consultez la [Vue d’ensemble des réseaux virtuels](../virtual-network/virtual-networks-overview.md).
- Découvrez comment [planifier des réseaux virtuels](../virtual-network/virtual-network-vnet-plan-design-arm.md).

## <a name="network-security-groups"></a>Groupes de sécurité réseau

Actuellement, il n’est pas possible de migrer des groupes de sécurité réseau depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer des groupes de sécurité réseau dans la région cible et d’appliquer les règles de groupes de sécurité réseau au nouvel environnement d’application.

Récupérez la configuration actuelle d’un groupe de sécurité réseau à partir du portail ou en exécutant les commandes PowerShell suivantes :

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

Pour plus d'informations :

- Actualisez vos [connaissances des groupes de sécurité réseau](../virtual-network/security-overview.md#network-security-groups).
- Consultez la [Vue d’ensemble de la sécurité du réseau Azure](../virtual-network/security-overview.md).
- Découvrez comment [gérer les groupes de sécurité réseau](../virtual-network/manage-network-security-group.md).

## <a name="expressroute"></a>ExpressRoute

Actuellement, il n’est pas possible de migrer une instance ExpressRoute Azure depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer de nouveaux circuits ExpressRoute et une nouvelle passerelle ExpressRoute dans Azure global.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur ExpressRoute](https://docs.microsoft.com/azure/expressroute/#step-by-step-tutorials).
- Découvrez comment [créer une passerelle ExpressRoute](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md).
- Apprenez-en davantage sur les [fournisseurs de service et emplacements ExpressRoute](../expressroute/expressroute-locations.md).
- Apprenez-en davantage sur les [passerelles de réseau virtuel pour ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="vpn-gateway"></a>Passerelle VPN

Actuellement, il n’est pas possible de migrer une instance de passerelles VPN Azure depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance de passerelles VPN Azure dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle VPN actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzVirtualNetworkGateway*`.

Assurez-vous de mettre à jour la configuration locale. En outre, supprimez toutes les règles existantes des anciennes plages d’adresses IP une fois que vous aurez mis à jour votre environnement réseau Azure.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur la passerelle VPN](https://docs.microsoft.com/azure/vpn-gateway/#step-by-step-tutorials).
- Découvrez comment [créer une connexion de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).
- Examinez les cmdlets [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell.
- Lisez le billet de blog [Créer une connexion de site à site](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/).
 
## <a name="application-gateway"></a>Application Gateway

Actuellement, il n’est pas possible de migrer une instance Azure Application Gateway depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle passerelle dans Azure global.

Vous pouvez collecter des informations sur la configuration de votre passerelle actuelle à l’aide du portail ou de PowerShell. Dans PowerShell, utilisez un ensemble de cmdlets qui commencent par `Get-AzApplicationGateway*`.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Application Gateway](https://docs.microsoft.com/azure/application-gateway/#step-by-step-tutorials).
- Découvrez comment [créer une passerelle d’application](../application-gateway/quick-create-portal.md).
- Examinez les cmdlets [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell.

## <a name="dns"></a>DNS

Pour migrer votre configuration Azure DNS à partir d’Azure Allemagne vers Azure global, exporter le fichier de zone DNS, puis importez-le dans le nouvel abonnement. Actuellement, la seule façon d’exporter le fichier de zone est d’utiliser Azure CLI.

Après vous être connecté à votre abonnement source dans Azure Allemagne, configurez Azure CLI pour utiliser le mode Azure Resource Manager. Exportez la zone en exécutant la commande suivante :

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Exemple :

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

Cette commande appelle le service Azure DNS pour exporter la zone `contoso.com` dans le groupe de ressources `myresourcegroup`. Le résultat est stocké comme un fichier de zone compatible BIND dans contoso.com.txt dans le dossier actif.

Lorsque l’exportation est terminée, supprimez les enregistrements NS du fichier de zone. Les nouveaux enregistrements NS sont créés pour la nouvelle région et le nouvel abonnement.

Maintenant, connectez-vous à votre environnement cible, créez un groupe de ressources (ou sélectionnez-en un), puis importez le fichier de zone :

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Lorsque la zone a été importée, vous devez valider la zone en exécutant la commande suivante :

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

Lorsque la validation est terminée, contactez votre bureau d’enregistrement de domaine et redéléguez les enregistrements NS. Pour obtenir des informations d’enregistrement NS, exécutez cette commande :

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Azure DNS](https://docs.microsoft.com/azure/dns/#step-by-step-tutorials).
- Consultez la [vue d’ensemble d’Azure DNS](../dns/dns-overview.md).
- Apprenez-en davantage sur [l’importation et l’exportation Azure DNS](../dns/dns-import-export.md).

## <a name="network-watcher"></a>Network Watcher

Actuellement, il n’est pas possible de migrer une instance Azure Network Watcher depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer une nouvelle instance Network Watcher dans Azure global. Ensuite, comparez les résultats entre l’ancien et le nouvel environnement.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Network Watcher](https://docs.microsoft.com/azure/network-watcher/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Network Watcher](../network-watcher/network-watcher-monitoring-overview.md).
- Apprenez-en davantage sur les [journaux d’activité des flux de groupe de sécurité réseau](../network-watcher/network-watcher-nsg-flow-logging-portal.md).
- Apprenez-en davantage sur le [moniteur de connexion](../network-watcher/connection-monitor.md).

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager peut vous aider à effectuer une migration en douceur. Toutefois, vous ne pouvez pas migrer les profils Traffic Manager que vous créez dans Azure Allemagne vers Azure global. (Lors d’une migration, vous migrez les points de terminaison Traffic Manager vers l’environnement cible. Vous devez donc quand même mettre à jour le profil Traffic Manager.)

Vous pouvez définir des points de terminaison supplémentaires dans l’environnement cible à l’aide de Traffic Manager pendant qu’il est en cours d’exécution dans l’environnement source. Lorsque Traffic Manager est en cours d’exécution dans le nouvel environnement, vous pouvez toujours définir des points de terminaison que vous n’avez pas encore migrés dans l’environnement source. Ce scénario est appelé le [scénario bleu-vert](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/). Ce scénario implique les étapes suivantes :

1. Créez un profil Traffic Manager dans Azure global.
1. Définissez les points de terminaison dans Azure Allemagne.
1. Modifiez votre enregistrement DNS CNAME dans le nouveau profil Traffic Manager.
1. Désactivez l’ancien profil Traffic Manager.
1. Migrez et configurez les points de terminaison. Pour chaque point de terminaison dans Azure Allemagne :
   1. Migrez le point de terminaison vers Azure global.
   1. Modifiez le profil Traffic Manager pour utiliser le nouveau point de terminaison.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/#step-by-step-tutorials).
- Examinez la [vue d’ensemble de Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- En savoir plus sur la [création d’un profil Traffic Manager](../traffic-manager/traffic-manager-create-profile.md).

## <a name="load-balancer"></a>Load Balancer

Actuellement, il n’est pas possible de migrer une instance Azure Load Balancer depuis Azure Allemagne vers Azure global. Nous vous recommandons de créer et de configurer un nouvel équilibreur de charge dans Azure global.

Pour plus d'informations :

- Actualisez vos connaissances en suivant les [tutoriels sur Load Balancer](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials).
- Consultez la [Vue d’ensemble de Load Balancer](../load-balancer/load-balancer-overview.md).
- Découvrez comment [créer un équilibreur de charge](../load-balancer/quickstart-load-balancer-standard-public-portal.md).

## <a name="next-steps"></a>Étapes suivantes

Informez-vous sur les outils, techniques et suggestions pour migrer des ressources dans les catégories de service suivantes :

- [Calcul](./germany-migration-compute.md)
- [Stockage](./germany-migration-storage.md)
- [web](./germany-migration-web.md)
- [Bases de données](./germany-migration-databases.md)
- [Analyse](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Intégration](./germany-migration-integration.md)
- [Identité](./germany-migration-identity.md)
- [Sécurité](./germany-migration-security.md)
- [Outils de gestion](./germany-migration-management-tools.md)
- [Média](./germany-migration-media.md)
