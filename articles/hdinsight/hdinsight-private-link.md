---
title: Activer Private Link sur un cluster HDInsight restreint (préversion)
description: Découvrez comment vous connecter en dehors du cluster HDInsight à l’aide d’Azure Private Link.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 9f432d37e58069decdc9a97e55d926aed3b7277f
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710027"
---
# <a name="enable-private-link-on-hdinsight-cluster-preview"></a>Activer Private Link sur un cluster HDInsight (préversion)

## <a name="overview"></a>Vue d’ensemble
Cet article explique comment tirer parti d’Azure Private Link pour vous connecter à votre cluster HDInsight de manière privée dans des réseaux sur le réseau principal de Microsoft. Cet article est une extension de notre article principal [Restreindre la connectivité du cluster dans Azure HDInsight](./hdinsight-restrict-public-connectivity.md) qui se concentre sur la restriction de la connectivité publique. Si vous optez pour une connectivité publique à et dans vos clusters HDInsight et ressources dépendantes, envisagez de restreindre la connectivité de votre cluster en suivant les instructions fournies dans [Contrôler le trafic réseau dans Azure HDInsight](./control-network-traffic.md).

Vous pouvez tirer parti de Private Link dans des scénarios inter-réseaux virtuels où l’appairage de réseaux virtuels n’est pas disponible ou activé. Par exemple, si vous souhaitez intégrer Azure Data Factory avec Azure HDInsight, il est nécessaire qu’Azure Data Factory se connecte à des clusters HDInsight sur un réseau privé (par exemple, une liaison privée) à des fins de conformité et de sécurité.

> [!NOTE]
> La restriction de la connectivité publique est une condition préalable à l’activation de Private Link et ne doit pas être considérée comme la même capacité.

Private Link est une fonctionnalité facultative désactivée par défaut. La fonctionnalité est disponible uniquement quand la propriété de réseau `resourceProviderConnection` est définie sur *sortant*, comme décrit dans l’article [Restreindre la connectivité du cluster dans Azure HDInsight](./hdinsight-restrict-public-connectivity.md).

Lorsque `privateLink` est définie sur *activé*, des [équilibreurs de charge standard](../load-balancer/load-balancer-overview.md) (SLB) internes sont créés et un service Azure Private Link est approvisionné pour chaque SLB. Le service Azure Private Link vous permet d’accéder au cluster HDInsight à partir de points de terminaison privés.

## <a name="prerequisites"></a>Prérequis

Les équilibreurs de charge standard ne fournissent pas automatiquement de [NAT sortante publique](../load-balancer/load-balancer-outbound-connections.md) comme le font les équilibreurs de charge de base. Vous devez fournir votre propre solution NAT, telle qu’une passerelle NAT, ou la traduction d’adresses réseau (NAT) fournie par votre [pare-feu](./hdinsight-restrict-outbound-traffic.md), pour vous connecter à des dépendances HDInsight publiques sortantes. Votre cluster HDInsight doit toujours avoir accès à ses dépendances sortantes. Si ces dépendances sortantes ne sont pas autorisées, la création du cluster peut échouer. Un groupe de sécurité réseau doit également être configuré sur le sous-réseau pour activer la connectivité sortante.

### <a name="1--configure-a-default-network-security-group-nsg-on-the-subnet"></a>1. Configurer un groupe de sécurité réseau par défaut (NSG) sur le sous-réseau

Créez et ajoutez un groupe de sécurité réseau sur le sous-réseau dans lequel vous souhaitez déployer le cluster HDInsight.

### <a name="2--disable-network-policies-for-private-link-service"></a>2. Désactiver les stratégies réseau pour le service de liaison privée

Pour réussir la création de services de liaison privée, vous devez explicitement [désactiver les stratégies réseau pour les services de liaison privée](../private-link/disable-private-link-service-network-policy.md).

### <a name="3--configure-a-nat-gateway-on-the-subnet"></a>3. Configurer une NAT Gateway sur le sous-réseau

Vous pouvez choisir d’utiliser une passerelle NAT si vous ne souhaitez pas configurer un pare-feu ou une appliance virtuelle réseau pour NAT. Autrement, passez à la condition préalable suivante.

Pour commencer, ajoutez simplement une passerelle NAT (avec une nouvelle adresse IP publique dans votre réseau virtuel) au sous-réseau configuré de votre réseau virtuel. Cette passerelle assume la conversion de votre adresse IP interne privée en adresses publiques lorsque le trafic doit être placé en dehors de votre réseau virtuel.

### <a name="4--using-firewall-or-network-virtual-appliance-nvas-for-nat-optional"></a>4. Utiliser un pare-feu ou une appliance virtuelle réseau pour NAT (facultatif)
Pour une configuration de base initiale, commencez par ajouter un nouveau sous-réseau « AzureFirewallSubnet » à votre réseau virtuel. Une fois ce sous-réseau créé, utilisez-le pour configurer un nouveau pare-feu et ajouter vos stratégies de pare-feu. Une fois votre pare-feu configuré, utilisez son adresse IP privée feu comme `nextHopIpAddress` dans votre table de routage. Ajoutez cette table de routage au sous-réseau configuré de votre réseau virtuel.
Pour plus d’informations sur la configuration d’un pare-feu, consultez [Contrôler le trafic réseau dans Azure HDInsight](./control-network-traffic.md)

Le diagramme suivant montre un exemple de configuration de mise en réseau requise avant de créer un cluster. Dans cet exemple, tout le trafic sortant est forcé vers Pare-feu Azure en utilisant un UDR, et les dépendances sortantes requises doivent être « autorisées » sur le pare-feu avant de créer un cluster. Pour les clusters Pack Sécurité Entreprise, la connectivité réseau à Azure Active Directory Domain Services peut être fournie par l’appairage de réseaux virtuels.

:::image type="content" source="media/hdinsight-private-link/before-cluster-creation.png" alt-text="Diagramme de l’environnement de liaison privée avant la création du cluster":::

## <a name="manage-private-endpoints-for-azure-hdinsight"></a>Gérer des points de terminaison privés pour Azure HDInsight

Vous pouvez utiliser des [points de terminaison privés](../private-link/private-endpoint-overview.md) pour vos clusters Azure HDInsight afin de permettre aux clients d’un réseau virtuel (VNet) d’accéder en toute sécurité à votre cluster via une [liaison privée](../private-link/private-link-overview.md). Le trafic réseau entre les clients sur le réseau virtuel et le cluster HDInsight passe par le réseau principal Microsoft, éliminant ainsi l’exposition à partir de l’Internet public.

:::image type="content" source="media/hdinsight-private-link/private-endpoint-experience.png" alt-text="Diagramme de l’expérience de gestion de point de terminaison privé":::

L’utilisateur du service Private Link peut choisir entre deux méthodes d’approbation des connexions (par exemple, Azure Data Factory) :
* **Automatique** : si l’utilisateur du service a des autorisations de RBAC Azure sur la ressource HDInsight, le consommateur peut choisir la méthode d’approbation automatique. Dans ce cas, lorsque la demande atteint la ressource HDInsight, celle-ci n’a rien à faire, car la connexion est automatiquement approuvée.
* **Manuelle** : à l’inverse, si l’utilisateur du service ne dispose pas d’autorisations de RBAC Azure sur la ressource HDInsight, il peut choisir la méthode d’approbation manuelle. Dans ce cas, la demande de connexion apparaît En attente sur les ressources HDInsight. La demande doit être approuvée manuellement par la ressource HDInsight avant que des connexions puissent être établies. 

Pour gérer les points de terminaison privés, dans votre vue de cluster dans le portail Azure, sous Sécurité + mise en réseau, accédez à la section Mise en réseau (préversion). Vous pourrez voir ici toutes les connexions existantes, les états de connexion et les détails du point de terminaison privé.
Vous pouvez également approuver, rejeter ou supprimer des connexions existantes. Lorsque vous créez une connexion privée, vous pouvez également spécifier la sous-ressource HDInsight (Gateway, Headnode, etc.) à laquelle vous souhaitez vous connecter.

Le tableau ci-dessous montre les différentes actions de ressource HDInsight et les états de connexion qui en résultent pour les points de terminaison privés. Une ressource HDInsight peut également modifier l’état de la connexion de point de terminaison privé ultérieurement sans intervention de l’utilisateur. Chaque action met à jour l’état du point de terminaison côté client.

| Action du fournisseur de services | État du point de terminaison privé de l’utilisateur du service | Description |
| --------- | --------- | --------- |
| None | Pending | La connexion a été créée manuellement et est en attente de l’approbation du propriétaire de la ressource Private Link. |
| Approbation | Approved | La connexion a été approuvée automatiquement ou manuellement et est prête à être utilisée. |
| Rejeter | Rejeté | La connexion a été rejetée par le propriétaire de la ressource Private Link. |
| Supprimer | Déconnecté | La connexion ayant été supprimée par le propriétaire de la ressource Private Link, le point de terminaison privé devient informatif et doit être supprimé dans le cadre d’un nettoyage. |

## <a name="configure-dns-to-connect-over-private-endpoints"></a>Configurer DNS pour se connecter via des points de terminaison privés

Une fois que vous avez configuré la mise en réseau, vous pouvez créer un cluster avec une connexion de fournisseur de ressources sortante et une liaison privée activée, comme illustré dans la figure suivante.
Pour accéder aux clusters privés, vous pouvez utiliser des extensions DNS et des points de terminaison privés Private Link. Lorsque le paramètre `privateLink` est défini sur activé, vous pouvez créer des points de terminaison privés et configurer la résolution DNS par le biais de zones DNS privées.
Les entrées Azure Private Link créées dans la zone DNS publique gérée par Azure `azurehdinsight.net` sont les suivantes :

```dns
<clustername>        CNAME    <clustername>.privatelink
<clustername>-int    CNAME    <clustername>-int.privatelink
<clustername>-ssh    CNAME    <clustername>-ssh.privatelink
```
L’illustration suivante montre un exemple des entrées DNS privées configurées pour permettre l’accès à un cluster à partir d’un réseau virtuel qui n’est pas appairé ou qui ne dispose pas d’une ligne de vue directe sur le cluster. Vous pouvez utiliser une zone privée Azure pour remplacer les noms de domaine complets (FQDN) `*.privatelink.azurehdinsight.net` et résoudre les adresses IP de points de terminaison privés dans le réseau du client.
Cela n’apparaît que pour `<clustername>.azurehdinsight.net`, mais s’étend également à d’autres points de terminaison de cluster.

:::image type="content" source="media/hdinsight-private-link/access-private-clusters.png" alt-text="Diagramme de l’architecture de liaison privée":::

## <a name="how-to-create-clusters"></a>Comment créer des clusters ?
### <a name="use-arm-template-properties"></a>Utiliser les propriétés du modèle Resource Manager

L’extrait de code JSON suivant comprend les deux propriétés réseau que vous devez configurer dans votre modèle Resource Manager pour créer un cluster HDInsight privé.

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Pour obtenir un modèle complet avec de nombreuses fonctionnalités de sécurité d’entreprise HDInsight, notamment Azure Private Link, consultez [Modèle de sécurité d’entreprise HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

### <a name="use-azure-powershell"></a>Utilisation d'Azure PowerShell

Pour utiliser PowerShell, consultez l'exemple disponible [ici](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

### <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Pour utiliser l'interface de ligne de commande Azure, consultez l'exemple disponible [ici](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Étapes suivantes

* [Pack Sécurité Entreprise pour Azure HDInsight](enterprise-security-package.md)
* [Informations et recommandations générales sur la sécurité d’entreprise dans Azure HDInsight](./domain-joined/general-guidelines.md)