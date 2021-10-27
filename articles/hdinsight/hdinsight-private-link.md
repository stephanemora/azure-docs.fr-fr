---
title: Activer Private Link sur un cluster Azure HDInsight
description: Découvrez comment vous connecter à un cluster HDInsight à l’aide d’Azure Private Link.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: d21f543d7736ee69a9c15a7b2a4bf0ccd0e877e2
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165202"
---
# <a name="enable-private-link-on-an-hdinsight-cluster"></a>Activer Private Link sur un cluster HDInsight

Cet article explique comment tirer parti d’Azure Private Link pour vous connecter à votre cluster HDInsight de manière privée dans des réseaux sur le réseau principal de Microsoft. Cet article est une extension de l’article [Restreindre la connectivité du cluster dans Azure HDInsight](./hdinsight-restrict-public-connectivity.md) qui se concentre sur la restriction de la connectivité publique. Si vous souhaitez de la connectivité publique à oudans vos clusters HDInsight et ressources dépendantes, envisagez de restreindre la connectivité de votre cluster en suivant les instructions fournies dans [Contrôler le trafic réseau dans Azure HDInsight](./control-network-traffic.md).

Vous pouvez tirer parti de Private Link dans des scénarios inter-réseaux virtuels où l’appairage de réseaux virtuels n’est pas disponible ou activé.

> [!NOTE]
> La restriction de la connectivité publique est une condition préalable à l’activation de Private Link et ne doit pas être considérée comme la même capacité.

L’utilisation d’un lien privé pour se connecter à un cluster HDInsight est une fonctionnalité facultative qui est désactivée par défaut. La fonctionnalité est disponible uniquement quand la propriété de réseau `resourceProviderConnection` est définie sur *sortant*, comme décrit dans l’article [Restreindre la connectivité du cluster dans Azure HDInsight](./hdinsight-restrict-public-connectivity.md).

Lorsque `privateLink` est définie sur *activé*, des [équilibreurs de charge standard](../load-balancer/load-balancer-overview.md) (SLB) internes sont créés et un service Azure Private Link est approvisionné pour chaque SLB. Le service Azure Private Link vous permet d’accéder au cluster HDInsight à partir de points de terminaison privés.

## <a name="prerequisites"></a>Prérequis

Les équilibreurs de charge standard ne fournissent pas automatiquement de [NAT sortante publique](../load-balancer/load-balancer-outbound-connections.md) comme le font les équilibreurs de charge de base. Vous devez fournir votre propre solution NAT, telle qu’une passerelle NAT, ou la traduction d’adresses réseau (NAT) fournie par votre [pare-feu](./hdinsight-restrict-outbound-traffic.md), pour vous connecter à des dépendances HDInsight publiques sortantes. 

Votre cluster HDInsight doit toujours avoir accès à ses dépendances sortantes. Si ces dépendances sortantes ne sont pas autorisées, la création du cluster peut échouer. 

### <a name="configure-a-default-network-security-group-on-the-subnet"></a>Configurer un groupe de sécurité réseau par défaut sur le sous-réseau

Créez et ajoutez un groupe de sécurité réseau sur le sous-réseau dans lequel vous souhaitez déployer le cluster HDInsight. Un groupe de sécurité réseau est requis pour l’activation de la connectivité sortante.

### <a name="disable-network-policies-for-the-private-link-service"></a>Désactiver les stratégies réseau pour le service de liaison privée

Pour réussir la création de services de liaison privée, vous devez explicitement [désactiver les stratégies réseau pour les services de liaison privée](../private-link/disable-private-link-service-network-policy.md).

### <a name="configure-a-nat-gateway-on-the-subnet"></a>Configurer une NAT Gateway sur le sous-réseau

Vous pouvez choisir d’utiliser une passerelle NAT si vous ne souhaitez pas configurer un pare-feu ou une appliance virtuelle réseau (appliance virtuelle réseau) pour NAT. Sinon, passez à l’étape préliminaire suivante.

Pour commencer, ajoutez une passerelle NAT (avec une nouvelle adresse IP publique dans votre réseau virtuel) au sous-réseau configuré de votre réseau virtuel. Cette passerelle assume la conversion de votre adresse IP interne privée en adresses publiques lorsque le trafic doit être placé en dehors de votre réseau virtuel.

### <a name="configure-a-firewall-optional"></a>Configuration d’un pare-feu (facultatif)
Pour initier une installation de base :

1. Ajoutez un nouveau sous-réseau nommé *AzureFirewallSubnet* à votre réseau virtuel. 
1. Utilisez le nouveau sous-réseau pour configurer un nouveau pare-feu et ajouter vos stratégies de pare-feu. 
1. Utilisez l’adresse IP privée du nouveau pare-feu comme valeur `nextHopIpAddress` dans votre table de routage. 
1. Ajoutez cette table de routage au sous-réseau configuré de votre réseau virtuel.

Pour plus d’informations sur la configuration d’un pare-feu, consultez [Contrôler le trafic réseau dans Azure HDInsight](./control-network-traffic.md).

Le diagramme suivant montre un exemple de configuration de mise en réseau requise avant de créer un cluster. Dans cet exemple, tout le trafic sortant est forcé vers le pare-feu Azure via un itinéraire défini par l’utilisateur. Les dépendances sortantes requises doivent être autorisées sur le pare-feu avant la création du cluster. Pour les clusters Pack Sécurité Entreprise,l’appairage de réseaux virtuels peut permettre la connectivité réseau à Azure Active Directory Domain Services.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramme de l’environnement de liaison privée avant la création du cluster.":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Gérer des points de terminaison privés pour Azure HDInsight

Vous pouvez utiliser des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour vos clusters Azure HDInsight afin de permettre aux clients d’un réseau virtuel d’accéder en toute sécurité à votre cluster via une [liaison privée](../private-link/private-link-overview.md). Le trafic réseau entre les clients sur le réseau virtuel et le cluster HDInsight passe par le réseau principal Microsoft, éliminant ainsi l’exposition à partir de l’Internet public.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagramme de l’expérience de gestion de point de terminaison privé.":::

L’utilisateur du service Private Link peut choisir entre deux méthodes d’approbation des connexions (par exemple, Azure Data Factory) :

* **Automatique** : Si l’utilisateur du service a des autorisations du contrôle d’accès en fonction du rôle Azure sur la ressource HDInsight, il peut choisir la méthode d’approbation automatique. Dans ce cas, lorsque la demande atteint la ressource HDInsight, celle-ci n’a rien à faire, car la connexion est automatiquement approuvée.
* **Manuelle** : si l’utilisateur du service ne dispose pas d’autorisations de RBAC Azure sur la ressource HDInsight, il peut choisir la méthode d’approbation manuelle. Dans ce cas, la demande de connexion apparaît **En attente** sur les ressources HDInsight. La demande doit être approuvée manuellement par la ressource HDInsight avant que des connexions puissent être établies. 

Pour gérer les points de terminaison privés, dans votre vue de cluster dans le portail Azure, sous **Sécurité + mise en réseau**, accédez à la section **Mise en réseau**. Vous pourrez voir ici toutes les connexions existantes, les états de connexion et les détails du point de terminaison privé.

Vous pouvez également approuver, rejeter ou supprimer des connexions existantes. Lorsque vous créez une connexion privée, vous pouvez spécifier la sous-ressource HDInsight (par exemple, le nœud principal ou la passerelle) à laquelle vous souhaitez également vous connecter.

Le tableau suivant montre les différentes actions de ressource HDInsight et les états de connexion qui en résultent pour les points de terminaison privés. Une ressource HDInsight peut également modifier l’état de la connexion de point de terminaison privé ultérieurement sans intervention de l’utilisateur. Chaque action met à jour l’état du point de terminaison côté client.

| Action du fournisseur de services | État du point de terminaison privé de l’utilisateur du service | Description |
| --------- | --------- | --------- |
| None | Pending | La connexion a été créée manuellement et est en attente de l’approbation du propriétaire de la ressource Private Link. |
| Approbation | Approved | La connexion a été approuvée automatiquement ou manuellement et est prête à être utilisée. |
| Rejeter | Rejeté | La connexion a été rejetée par le propriétaire de la ressource Private Link. |
| Supprimer | Déconnecté | La connexion a été supprimée par le propriétaire de la ressource Private Link. Private Endpoint devient informatif et doit être supprimé pour le nettoyage. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Configurer DNS pour se connecter via des points de terminaison privés

Une fois que vous avez configuré la mise en réseau, vous pouvez créer un cluster avec une connexion de fournisseur de ressources sortante et une liaison privée activée.

Pour accéder aux clusters privés, vous pouvez utiliser des extensions DNS et des points de terminaison privés Private Link. Lorsque `privateLink` est défini sur *activé*, vous pouvez créer des points de terminaison privés et configurer la résolution DNS par le biais de zones DNS privées.

Les entrées Azure Private Link créées dans la zone DNS publique gérée par Azure `azurehdinsight.net` sont les suivantes :

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
L’illustration suivante montre un exemple des entrées DNS privées configurées pour permettre l’accès à un cluster à partir d’un réseau virtuel qui n’est pas appairé ou qui ne dispose pas d’une ligne de vue directe sur le cluster. Vous pouvez utiliser une zone privée Azure DNS pour remplacer les noms de domaine complets (FQDN) `*.privatelink.azurehdinsight.net` et résoudre les adresses IP de points de terminaison privés dans le réseau du client. La configuration vaut uniquement pour `<clustername>.azurehdinsight.net` dans l’exemple, mais elle s’étend également à d’autres points de terminaison de cluster.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramme de l’architecture Private Link.":::

## <a name="create-clusters"></a>Créer des clusters

L’extrait de code JSON suivant comprend les deux propriétés réseau que vous devez configurer dans votre modèle Azure Resource Manager pour créer un cluster HDInsight privé :

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound",
    "privateLink": "Enabled"
}
```

Pour obtenir un modèle complet avec de nombreuses fonctionnalités de sécurité d’entreprise HDInsight, notamment Azure Private Link, consultez [Modèle de sécurité d’entreprise HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Pour créer un cluster à l’aide de PowerShell, consultez l'[exemple](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Pour créer un cluster à l’aide d’Azure CLI, consultez l'[exemple](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Étapes suivantes

* [Pack Sécurité Entreprise pour Azure HDInsight](enterprise-security-package.md)
* [Informations et recommandations générales sur la sécurité d’entreprise dans Azure HDInsight](./domain-joined/general-guidelines.md)