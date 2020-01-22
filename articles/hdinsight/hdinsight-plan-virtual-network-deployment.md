---
title: Planifier un réseau virtuel pour Azure HDInsight
description: Apprenez à planifier un déploiement Azure Virtual Network pour connecter HDInsight à d'autres ressources de cloud ou à des ressources de votre centre de données.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 1e6a21e8bf9c284c83af09885aa66b612b52ad7c
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/16/2020
ms.locfileid: "76044715"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Planifier un réseau virtuel pour Azure HDInsight

Cet article fournit des informations générales sur l’utilisation des [Réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md) avec Azure HDInsight. Il aborde également les décisions de conception et d’implémentation qui doivent être prises avant de pouvoir implémenter un réseau virtuel pour votre cluster HDInsight. Une fois la phase de planification terminée, vous pouvez procéder à la [création de réseaux virtuels pour les clusters Azure HDInsight](hdinsight-create-virtual-network.md). Pour plus d’informations sur les adresses IP de gestion HDInsight nécessaires pour configurer correctement les groupes de sécurité réseau et les itinéraires définis par l’utilisateur, consultez [Adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).

L’utilisation d’un réseau virtuel Azure permet les scénarios suivants :

* connexion à HDInsight directement à partir d’un réseau local ;
* connexion de HDInsight à des banques de données dans un réseau virtuel Azure ;
* accès direct aux services [Apache Hadoop](https://hadoop.apache.org/) qui ne sont pas disponibles publiquement sur Internet ; tels que les API [Apache Kafka](https://kafka.apache.org/) ou l’API Java [Apache HBase](https://hbase.apache.org/).

> [!IMPORTANT]
> La création d’un cluster HDInsight dans un réseau virtuel permet de créer plusieurs ressources réseau, telles que des ressources réseau et des équilibreurs de charge. Ne **supprimez pas** ces ressources réseau, car elles sont nécessaires au bon fonctionnement de votre cluster avec le réseau virtuel (VNET).
>
> Après le 28 février 2019, les ressources réseau (par exemple, les cartes réseau, les équilibreurs de charge, etc.) des NOUVEAUX clusters HDInsight créés dans un réseau virtuel seront approvisionnées dans le même groupe de ressources de cluster HDInsight. Auparavant, ces ressources étaient approvisionnées dans le groupe de ressources du réseau virtuel. Les clusters en cours d’exécution et les clusters créés sans réseau virtuel ne seront pas modifiés.

## <a name="planning"></a>Planification

Les questions auxquelles vous devez répondre lors de la planification de l’installation de HDInsight dans un réseau virtuel sont les suivantes :

* Devez installer HDInsight dans un réseau virtuel existant ? Ou bien créez-vous un réseau ?

    Si vous utilisez un réseau virtuel existant, vous devrez peut-être modifier la configuration de celui-ci avant d’installer HDInsight. Pour plus d’informations, voir la section [Ajouter HDInsight à un réseau virtuel existant](#existingvnet).

* Vous souhaitez connecter le réseau virtuel contenant HDInsight à un autre réseau virtuel ou à votre réseau local ?

    Pour utiliser aisément des ressources de différents réseaux, il se peut que vous deviez créer un DNS personnalisé et configurer un transfert de DNS. Pour plus d’informations, voir la section [Connecter plusieurs réseaux](#multinet).

* Souhaitez-vous restreindre/rediriger le trafic entrant ou sortant échangé avec HDInsight ?

    HDInsight doit disposer d’une communication illimitée avec les adresses IP spécifiques dans le centre de données Azure. Il existe également plusieurs ports qui doivent être autorisés au travers de pare-feu pour la communication du client. Pour plus d’informations, voir la section [Contrôler le trafic réseau](#networktraffic).

## <a id="existingvnet"></a>Ajouter HDInsight à un réseau virtuel existant

Suivez les étapes de cette section pour découvrir comment ajouter un nouveau cluster HDInsight à un réseau virtuel Azure existant.

> [!NOTE]  
> Vous ne pouvez pas ajouter un cluster HDInsight existant à un réseau virtuel.

1. Utilisez-vous un modèle de déploiement classique ou Resource Manager pour le réseau virtuel ?

    HDInsight 3.4 et versions ultérieures nécessitent un réseau virtuel Resource Manager. Les versions antérieures de HDInsight nécessitaient un réseau virtuel classique.

    Si votre réseau existant est un réseau virtuel classique, vous devez créer un réseau virtuel Resource Manager, puis connecter les deux. [Connexion de réseaux virtuels classiques aux nouveaux réseaux virtuels](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Une fois joint, HDInsight installé dans le réseau peut interagir avec des ressources du réseau classique.

2. Utilisez-vous des groupes de sécurité réseau, des itinéraires définis par l'utilisateur ou des appliances de réseau virtuel pour restreindre le trafic échangé avec le réseau virtuel ?

    Service administré, HDInsight requiert un accès illimité à plusieurs adresses IP dans le centre de données Azure. Pour permettre la communication avec ces adresses IP, mettez à jour des groupes de sécurité réseau ou des itinéraires définis par l'utilisateur.
    
    HDInsight héberge plusieurs services qui une série de ports. Ne bloquez pas le trafic vers ces ports. Pour obtenir la liste des ports auxquels autoriser l’accès via des pare-feu d’appliance virtuelle, voir la section Sécurité.
    
    Pour rechercher votre configuration de sécurité existante, utilisez les commandes Azure PowerShell ou Azure CLI suivantes :

    * Groupes de sécurité réseau

        Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel, puis entrez la commande :
    
        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```
    
        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Pour plus d’informations, voir le document [Résoudre les problèmes relatifs aux groupes de sécurité réseau](../virtual-network/diagnose-network-traffic-filter-problem.md).

        > [!IMPORTANT]  
        > Les règles de groupe de sécurité réseau sont appliquées dans un ordre basé sur leur priorité. La première règle correspondant au modèle de trafic est appliquée, et aucune autre n’est appliquée à ce trafic. Règles d’ordre de la plus permissive à la moins permissive. Pour plus d’informations, voir le document [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/security-overview.md).

    * Itinéraires définis par l’utilisateur

        Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel, puis entrez la commande :

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Pour plus d’informations, voir le document [Résoudre les problèmes relatifs aux itinéraires](../virtual-network/diagnose-network-routing-problem.md).

3. Créez un cluster HDInsight et sélectionnez le réseau virtuel Azure pendant la configuration. Pour comprendre le processus de création du cluster, utilisez les étapes indiquées dans les documents suivants :

    * [Créer un cluster HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Créer un cluster HDInsight à l’aide d’Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Créer un cluster HDInsight à l’aide d’Azure Classic CLI](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Créer un cluster HDInsight à l’aide d’un modèle Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > L’ajout de HDInsight à un réseau virtuel est une étape de configuration facultative. Veillez à sélectionner le réseau virtuel lors de la configuration du cluster.

## <a id="multinet"></a>Connexion de plusieurs réseaux

Le principal défi avec une configuration de réseau multiples est la résolution de noms entre les réseaux.

Azure assure la résolution de noms pour les services Azure installés dans un réseau virtuel. Cette résolution de noms intégrée permet à HDInsight de se connecter aux ressources suivantes à l’aide d’un nom de domaine complet (FQDN) :

* Toute ressource disponible sur Internet. Par exemple, microsoft.com ou windowsupdate.com.

* Toute ressource figurant dans le même réseau virtuel Azure, en utilisant le __nom DNS interne__ de la ressource. Par exemple, lors de l’utilisation de la résolution de noms par défaut, voici des exemples de noms DNS internes attribués aux nœuds Worker HDInsight :

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Les deux nœuds peuvent communiquer directement entre eux et avec d’autres nœuds dans HDInsight en utilisant des noms DNS internes.

La résolution de noms par défaut ne permet __pas__ à HDInsight de résoudre les noms des ressources en réseaux joints au réseau virtuel. Par exemple, il est courant de joindre un réseau local au réseau virtuel. Avec uniquement la résolution de noms par défaut, HDInsight ne peut pas accéder aux ressources du réseau local par leur nom. L’inverse est également vrai ; les ressources de votre réseau local ne peuvent pas accéder aux ressources du réseau virtuel par leur nom.

> [!WARNING]  
> Vous devez créer le serveur DNS personnalisé et configurer le réseau virtuel pour l’utiliser avant de créer le cluster HDInsight.

Pour permettre la résolution de noms entre le réseau virtuel et les ressources dans des réseaux joints, vous devez effectuer les actions suivantes :

1. Créez un serveur DNS personnalisé dans le réseau virtuel Azure où vous prévoyez d’installer HDInsight.

2. Configurez le réseau virtuel pour utiliser le serveur DNS personnalisé.

3. Recherchez le suffixe DNS qu'Azure à affecté à votre réseau virtuel. Cette valeur est similaire à `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Pour plus d’informations sur la recherche de suffixe DNS, consultez la section [Exemple : DNS personnalisé](hdinsight-create-virtual-network.md#example-dns).

4. Configurez le transfert entre les serveurs DNS. La configuration dépend du type de réseau distant.

   * Si le réseau distant est un réseau local, configurez le DNS comme suit :
        
     * __DNS personnalisé__ (dans le réseau virtuel) :

         * Transférez les demandes relatives au suffixe DNS du réseau virtuel au programme de résolution récursive d’Azure (168.63.129.16). Azure gère les demandes de ressources dans le réseau virtuel.

         * Transférez toutes les autres demandes au serveur DNS local. Le serveur DNS local gère toutes les autres demandes de résolution de noms, y compris les demandes de ressources Internet telles que Microsoft.com.

     * __DNS local__ : transférer les requêtes de suffixe DNS de réseau virtuel vers le serveur DNS personnalisé. Le serveur DNS personnalisé transfère alors les demandes au programme de résolution récursive d’Azure.

       Cette configuration a pour effet de router les demandes de noms de domaine complets (FQDN) qui contiennent le suffixe DNS du réseau virtuel vers le serveur DNS personnalisé. Toutes les autres demandes (même d’adresses Internet publiques) sont gérées par le serveur DNS local.

   * Si le réseau distant est un autre réseau virtuel Azure, configurez DNS comme suit :

     * __DNS personnalisé__ (dans chaque réseau virtuel) :

         * Les demandes de suffixe DNS des réseaux virtuels sont transférées aux serveurs DNS personnalisés. Le DNS de chaque réseau virtuel est chargé de résoudre les ressources au sein de son réseau.

         * Transférez toutes les autres demandes au programme de résolution récursive d’Azure. Le programme de résolution récursive est responsable de la résolution des ressources locales et Internet.

       Le serveur DNS de chaque réseau transfère les demandes à l’autre, en fonction du suffixe DNS. Les autres requêtes sont résolues à l’aide du programme de résolution récursive d’Azure.

     Pour obtenir un exemple de chaque configuration, consultez la section [Exemple : DNS personnalisé](hdinsight-create-virtual-network.md#example-dns).

Pour plus d’informations, voir le document [Résolution de noms pour les machines virtuelles et les instances de rôle](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name="directly-connect-to-apache-hadoop-services"></a>Se connecter directement aux services Apache Hadoop

Vous pouvez vous connecter au cluster à l’adresse `https://CLUSTERNAME.azurehdinsight.net`. Il s’agit d’une adresse IP publique, qui ne sera peut-être pas accessible si vous avez utilisé des groupes de sécurité réseau pour limiter le trafic entrant d’Internet. En outre, lorsque vous déployez le cluster dans un réseau virtuel, vous pouvez y accéder à partir du point de terminaison privé `https://CLUSTERNAME-int.azurehdinsight.net`. Ce point de terminaison correspond à une adresse IP privée au sein du réseau virtuel pour l’accès au cluster.

Pour vous connecter à Apache Ambari et à d’autres pages web via le réseau virtuel, procédez comme suit :

1. Pour découvrir les noms de domaine complets (FQDN) internes des nœuds de cluster HDInsight, utilisez l’une des méthodes suivantes :

    Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel, puis entrez la commande :

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    Dans la liste des nœuds retournés, recherchez le nom de domaine complet des nœuds principaux, puis utilisez-les pour vous connecter à Ambari et à d’autres services web. Par exemple, utilisez `http://<headnode-fqdn>:8080` pour accéder à Ambari.

    > [!IMPORTANT]  
    > Certains services hébergés sur les nœuds principaux ne sont actifs que sur un seul nœud à la fois. Si vous tentez d’accéder à un service sur un nœud principal et que l’opération retourne l’erreur 404, basculez vers l’autre nœud principal.

2. Pour déterminer le nœud et le port sur lesquels un service est disponible, voir [Ports utilisés par les services Hadoop dans HDInsight](./hdinsight-hadoop-port-settings-for-services.md).

## <a id="networktraffic"></a> Contrôler le trafic réseau

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Les techniques permettant de contrôler le trafic entrant et sortant de clusters HDInsight

Le trafic réseau dans les réseaux virtuels Azure peut être contrôlé à l’aide des méthodes suivantes :

* Les **Groupes de sécurité réseau** (NSG) vous permettent de filtrer le trafic entrant et sortant changé avec le réseau. Pour plus d’informations, voir le document [Filtrer le trafic réseau avec les groupes de sécurité réseau](../virtual-network/security-overview.md).

* Les **appliances virtuelles réseau** (NVA) peuvent uniquement être utilisées avec le trafic sortant. Les appliances virtuelles réseau répliquent les fonctionnalités d’appareils tels que des routeurs et des pare-feux. Pour plus d’informations, voir le document [Appliances réseau](https://azure.microsoft.com/solutions/network-appliances).

En tant que service managé, HDInsight doit avoir un accès illimité aux services d’intégrité et de gestion de HDInsight pour le trafic entrant et sortant du réseau virtuel. Lorsque vous utilisez des groupes de sécurité réseau, vous devez vous assurer que ces services peuvent toujours communiquer avec le cluster HDInsight.

![Diagramme des entités HDInsight créées dans un réseau virtuel Azure personnalisé](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>Utiliser HDInsight avec des groupes de sécurité réseau

Si vous prévoyez d’utiliser des **groupes de sécurité réseau** pour contrôler le trafic réseau, effectuez les actions suivantes avant d’installer HDInsight :

1. Identifiez la région Azure que vous projetez d’utiliser pour HDInsight.

2. Identifiez les étiquettes de service demandées par HDInsight pour votre région. Pour plus d’informations, consultez [Étiquettes de service de groupe de sécurité réseau (NSG) pour Azure HDInsight](hdinsight-service-tags.md).

3. Créez ou modifiez les groupes de sécurité réseau pour le sous-réseau dans lequel vous prévoyez d’installer HDInsight.

    * __Groupes de sécurité réseau__ : autorisez le trafic __entrant__ sur le port __443__ à partir des adresses IP. Cela garantit que les services de gestion HDInsight peuvent atteindre le cluster depuis un point en dehors du réseau virtuel.

Pour plus d’informations sur les groupes de sécurité réseau, consultez [Présentation des groupes de sécurité réseau](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Contrôle du trafic sortant à partir de clusters HDInsight

Pour plus d’informations sur le contrôle du trafic sortant à partir de clusters HDInsight, consultez [Configurer la restriction du trafic réseau sortant pour les clusters Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Tunneling forcé en local

Le tunneling forcé est une configuration d’itinéraire défini par l’utilisateur où tout le trafic en provenance d’un sous-réseau est acheminé de force vers un réseau ou un emplacement spécifique, tel que votre réseau local. HDInsight ne prend __pas__ en charge le tunneling forcé vers les réseaux locaux. 

## <a id="hdinsight-ip"></a> Adresses IP requises

Si vous utilisez des groupes de sécurité réseau ou d’itinéraires définis par l’utilisateur pour contrôler le trafic, consultez [Adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).
    
## <a id="hdinsight-ports"></a> Ports requis

Si vous prévoyez d’utiliser un **pare-feu** et d’accéder au cluster depuis l’extérieur sur certains ports, vous devez autoriser le trafic sur ces ports nécessaires à votre scénario. Par défaut, aucune liste verte spéciale de ports n’est nécessaire tant que le trafic de gestion azure expliqué dans la section précédente est autorisé à atteindre le cluster sur le port 443.

Pour obtenir la liste des ports utilisés pour des services spécifiques, voir le document [Ports utilisés par les services Apache Hadoop sur HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pour plus d’informations sur les règles de pare-feu pour les appliances virtuelles, voir le document [Scénario d’appliance virtuelle](../virtual-network/virtual-network-scenario-udr-gw-nva.md).

## <a name="load-balancing"></a>Équilibrage de la charge

Lorsque vous créez un cluster HDInsight, un équilibreur de charge est également créé. Le type de cet équilibreur de charge se trouve au [niveau de la référence SKU de base](../load-balancer/concepts-limitations.md#skus) qui a certaines contraintes. L’une de ces contraintes est que si vous avez deux réseaux virtuels dans des régions différentes, vous ne pouvez pas vous connecter aux équilibreurs de charge de base. Pour plus d’informations, consultez [FAQ sur les réseaux virtuels : contraintes sur le peering de réseaux virtuels globaux](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

## <a name="transport-layer-security"></a>Protocole Transport Layer Security (TLS)

Les connexions au cluster via le point de terminaison de cluster public `https://<clustername>.azurehdinsight.net` sont transmises par proxy via des nœuds de passerelle de cluster. Ces connexions sont sécurisées à l’aide d’un protocole appelé TLS. L’application de versions ultérieures du protocole TLS sur les passerelles améliore la sécurité de ces connexions. Pour plus d’informations sur les raisons pour lesquelles vous devez utiliser des versions plus récentes du protocole TLS, consultez [Résolution du problème TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Vous pouvez contrôler la ou les versions minimales du protocole TLS prises en charge sur les nœuds de passerelle pour votre cluster HDInsight à l’aide de la propriété *minSupportedTlsVersion* dans un modèle Resource Manager au moment du déploiement. Pour obtenir un exemple de modèle, consultez [Modèle de démarrage rapide TLS 1.2 minimum pour HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Cette propriété prend en charge trois valeurs : « 1.0 », « 1.1 » et « 1.2 », qui correspondent respectivement à TLS 1.0+, TLS 1.1+ et TLS 1.2+. Par défaut, si vous ne spécifiez pas cette propriété, les clusters Azure HDInsight acceptent les connexions TLS 1.2 sur les points de terminaison HTTPS publics, ainsi que les versions plus anciennes pour la compatibilité descendante. Finalement, HDInsight applique le protocole TLS 1.2 ou une version ultérieure sur toutes les connexions de nœud de passerelle.

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir des exemples de code et des exemples de création de réseaux virtuels Azure, consultez [Créer des réseaux virtuels pour les clusters Azure HDInsight](hdinsight-create-virtual-network.md).
* Pour un exemple de bout en bout de configuration de HDInsight pour se connecter à un réseau local, voir [Connecter HDInsight à un réseau local](./connect-on-premises-network.md).
* Pour configurer des clusters Apache HBase dans des réseaux virtuels Azure, consultez [Créer des clusters Apache HBase sur HDInsight dans un réseau virtuel Azure](hbase/apache-hbase-provision-vnet.md).
* Pour configurer la géoréplication Apache HBase, consultez [Configurer la réplication de cluster Apache HBase dans les réseaux virtuels Azure](hbase/apache-hbase-replication.md).
* Pour plus d’informations sur les réseaux virtuels Azure, voir [Vue d'ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).
* Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité réseau](../virtual-network/security-overview.md).
* Pour plus d’informations sur les routages par l’utilisateur, consultez [Routage définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).
