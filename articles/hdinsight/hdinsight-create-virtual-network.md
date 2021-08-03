---
title: Créer des réseaux virtuels pour les clusters Azure HDInsight
description: Découvrez comment créer un réseau virtuel Azure pour connecter HDInsight à d’autres ressources de Cloud ou à des ressources de votre centre de connaissances.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/12/2021
ms.openlocfilehash: 28d2cc40d1272fdf29b6df3f08469418ecbc36da
ms.sourcegitcommit: 832e92d3b81435c0aeb3d4edbe8f2c1f0aa8a46d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/07/2021
ms.locfileid: "111559391"
---
# <a name="create-virtual-networks-for-azure-hdinsight-clusters"></a>Créer des réseaux virtuels pour les clusters Azure HDInsight

Cet article fournit des exemples et des exemples de code pour la création et la configuration de [Réseaux virtuels Azure](../virtual-network/virtual-networks-overview.md). À utiliser avec des clusters Azure HDInsight. Des exemples détaillés de création de groupes de sécurité réseau (groupes) et de configuration DNS sont présentés.

Pour obtenir des informations générales sur l’utilisation de réseaux virtuels avec Azure HDInsight, consultez[Planifier un réseau virtuel pour Azure HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="prerequisites-for-code-samples-and-examples"></a>Conditions préalables pour les exemples de code et les exemples

Avant d’exécuter l’un des exemples de code de cet article, assurez-vous de bien comprendre la mise en réseau TCP/IP. Si vous connaissez mal la mise en réseau TCP/IP, consultez quelqu’un connaissant le sujet avant d’apporter des modifications aux réseaux de production.

Les autres conditions préalables pour les exemples de cet article sont les suivantes :

* Si vous utilisez PowerShell, vous avez besoin d’installer le [module AZ](/powershell/azure/).
* Si vous voulez utiliser Azure CLI et que vous ne l’avez pas encore installé, consultez [Installer Azure CLI](/cli/azure/install-azure-cli).

> [!IMPORTANT]  
> Si vous recherchez des instructions pas à pas pour connecter HDInsight à votre réseau local en utilisant un réseau virtuel Azure, consultez le document [Connecter HDInsight à votre réseau local](connect-on-premises-network.md).

## <a name="example-network-security-groups-with-hdinsight"></a><a id="hdinsight-nsg"></a>Exemple : groupes de sécurité réseau avec HDInsight

Les exemples de cette section montrent comment créer des règles de groupe de sécurité réseau. Les règles autorisent HDInsight à communiquer avec les services de gestion Azure. Avant d’utiliser les exemples, ajustez les adresses IP pour qu’elles correspondent à celles de la région Azure que vous utilisez. Vous pouvez trouver ces informations dans les [adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).

### <a name="azure-resource-management-template"></a>Modèle de gestion des ressources Azure

Le modèle de gestion des ressources suivant crée un réseau virtuel qui restreint le trafic entrant, mais autorise le trafic en provenance des adresses IP requises par HDInsight. Ce modèle crée également un cluster HDInsight dans le réseau virtuel.

* [Déployer un réseau virtuel Azure sécurisé et un cluster Hadoop HDInsight](https://azure.microsoft.com/resources/templates/hdinsight-secure-vnet/)

### <a name="azure-powershell"></a>Azure PowerShell

Utilisez le script PowerShell suivant pour créer un réseau virtuel qui restreint le trafic entrant et autorise le trafic en provenance des adresses IP de la région Europe Nord.

> [!IMPORTANT]  
> Modifiez les adresses IP utilisées pour `hdirule1` et `hdirule2` dans cet exemple afin de les faire correspondre à la région Azure que vous utilisez. Vous pouvez trouver ces informations dans les [adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).

```azurepowershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"

# Get the Virtual Network object
$vnet = Get-AzVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName

# Get the region the Virtual network is in.
$location = $vnet.Location

# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName

# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `

# Set the changes to the security group
Set-AzNetworkSecurityGroup -NetworkSecurityGroup $nsg

# Apply the NSG to the subnet
Set-AzVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzVirtualNetwork
```

Cet exemple montre comment ajouter des règles pour autoriser le trafic entrant sur les adresses IP requises. Il ne contient pas de règle pour restreindre l’accès entrant à partir d’autres sources. Le code suivant montre comment activer l’accès SSH depuis Internet :

```azurepowershell
Get-AzNetworkSecurityGroup -Name hdisecure -ResourceGroupName RESOURCEGROUP |
Add-AzNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
```

### <a name="azure-cli"></a>Azure CLI

Suivez les étapes suivantes pour créer un réseau virtuel qui restreint le trafic entrant, mais autorise le trafic en provenance des adresses IP requises par HDInsight.

1. Utilisez la commande suivante pour créer un groupe de sécurité réseau nommé `hdisecure`. Remplacez `RESOURCEGROUP` par le groupe de ressources qui contient le réseau virtuel Azure. Remplacez `LOCATION` par l’emplacement (région) où le groupe a été créé.

    ```azurecli
    az network nsg create -g RESOURCEGROUP -n hdisecure -l LOCATION
    ```

    Une fois que le groupe est créé, vous recevez des informations sur le nouveau groupe.

2. Utilisez ce qui suit pour ajouter des règles au nouveau groupe de sécurité réseau qui autorisent les communications entrantes sur le port 443 à partir du service de gestion et de contrôle d’intégrité Azure HDInsight. Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel Azure.

    > [!IMPORTANT]  
    > Modifiez les adresses IP utilisées pour `hdirule1` et `hdirule2` dans cet exemple afin de les faire correspondre à la région Azure que vous utilisez. Vous pouvez trouver ces informations dans les [adresses IP de gestion HDInsight](hdinsight-management-ip-addresses.md).

    ```azurecli
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule3 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule4 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n hdirule6 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Pour récupérer l’identificateur unique pour ce groupe de sécurité réseau, utilisez la commande suivante :

    ```azurecli
    az network nsg show -g RESOURCEGROUP -n hdisecure --query "id"
    ```

    Cette commande retourne une valeur semblable au texte suivant :

    ```output
    "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

4. Pour appliquer un groupe de sécurité réseau à un sous-réseau, utilisez la commande suivante. Remplacez les valeurs `GUID` et `RESOURCEGROUP` par celles renvoyées à l’étape précédente. Remplacez `VNETNAME` et `SUBNETNAME` par le nom de réseau virtuel et le nom de sous-réseau que vous souhaitez créer.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUP --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Une fois l’exécution de cette commande terminée, vous pouvez installer HDInsight dans le réseau virtuel.

Ces étapes donnent uniquement accès au service de gestion et de contrôle d’intégrité de HDInsight sur le cloud Azure. Tout autre accès au cluster HDInsight à partir de l’extérieur du réseau virtuel est bloqué. Pour activer l’accès depuis l’extérieur du réseau virtuel, vous devez ajouter des règles supplémentaires pour le groupe de sécurité réseau.

Le code suivant montre comment activer l’accès SSH depuis Internet :

```azurecli
az network nsg rule create -g RESOURCEGROUP --nsg-name hdisecure -n ssh --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
```

## <a name="example-dns-configuration"></a><a id="example-dns"></a> Exemple : Configuration DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Résolution de noms entre un réseau virtuel et un réseau local connecté

Cet exemple repose sur les hypothèses suivantes :

* Vous avez un réseau virtuel Azure qui est connecté à un réseau local à l’aide d’une passerelle VPN.

* Le serveur DNS personnalisé dans le réseau virtuel exécute le système d’exploitation Linux ou Unix.

* [Bind](https://www.isc.org/downloads/bind/) est installé sur le serveur DNS personnalisé.

Sur le serveur DNS personnalisé dans le réseau virtuel :

1. Utilisez Azure PowerShell ou Azure CLI pour rechercher le suffixe DNS du réseau virtuel :

    Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel, puis entrez la commande :

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

1. Sur le serveur DNS personnalisé pour le réseau virtuel, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.local` :

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Remplacez la valeur `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` par le suffixe DNS de votre réseau virtuel.

    Cette configuration a pour effet de router toutes les demandes DNS pour le suffixe DNS du réseau virtuel vers le programme de résolution récursive d’Azure.

1. Sur le serveur DNS personnalisé pour le réseau virtuel, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.options` :

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Remplacez la valeur `10.0.0.0/16` par la plage d'adresses IP de votre réseau virtuel. Cette entrée permet que la résolution de noms demande des adresses à l’intérieur de cette plage.

    * Ajoutez la plage d’adresses IP du réseau local à la section `acl goodclients { ... }`.  L’entrée autorise les demandes de résolution de noms en provenance de ressources dans le réseau local.
    
    * Remplacez la valeur `192.168.0.1` par l’adresse IP de votre serveur DNS local. Cette entrée a pour effet de router toutes les autres demandes DNS vers le serveur DNS local.

1. Pour utiliser la configuration, redémarrez Bind. Par exemple : `sudo service bind9 restart`.

1. Ajouter un redirecteur conditionnel au serveur DNS local. Configurez le redirecteur conditionnel de façon envoyer des demandes du suffixe DNS de l’étape 1 au serveur DNS personnalisé.

    > [!NOTE]  
    > Pour plus de détails sur l’ajout d’un redirecteur conditionnel, consultez la documentation de votre logiciel DNS.

Après avoir suivi ces étapes, vous pouvez vous connecter aux ressources de chaque réseau à l’aide de noms de domaine complets (FQDN). Vous pouvez à présent installer HDInsight dans le réseau virtuel.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Résolution de noms entre deux réseaux virtuels connectés

Cet exemple repose sur les hypothèses suivantes :

* Vous avez deux réseaux virtuels Azure connectés à l’aide d’une passerelle VPN ou d’un peering.

* Le serveur DNS personnalisé dans les deux réseaux exécute le système d’exploitation Linux ou Unix.

* [Bind](https://www.isc.org/downloads/bind/) est installé sur les serveurs DNS personnalisés.

1. Utilisez Azure PowerShell ou Azure CLI pour rechercher le suffixe DNS des deux réseaux virtuels :

    Remplacez `RESOURCEGROUP` par le nom du groupe de ressources qui contient le réseau virtuel, puis entrez la commande :

    ```azurepowershell
    $NICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP"
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Sur le serveur DNS personnalisé, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.config.local`. Apportez cette modification sur le serveur DNS personnalisé dans les deux réseaux virtuels.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Remplacez la valeur `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` par le suffixe DNS de l’__autre__ réseau virtuel. Cette entrée a pour effet de router les demandes du suffixe DNS du réseau distant vers le DNS personnalisé dans ce réseau.

3. Sur les serveurs DNS personnalisés dans les deux réseaux virtuels, utilisez le texte suivant en tant que contenu du fichier `/etc/bind/named.conf.options`:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

   Remplacez les valeurs `10.0.0.0/16` et `10.1.0.0/16` par les plages d’adresses IP de vos réseaux virtuels. Cette entrée a pour effet de permettre aux ressources de chaque réseau d’effectuer des demandes des serveurs DNS.

    Toute demande n’ayant pas trait aux suffixes DNS des réseaux virtuels (par exemple, microsoft.com) est gérée par le programme de résolution récursive d’Azure.

4. Pour utiliser la configuration, redémarrez Bind. Par exemple, `sudo service bind9 restart` sur les deux serveurs DNS.

Après avoir suivi ces étapes, vous pouvez vous connecter aux ressources du réseau virtuel à l’aide de noms de domaine complets (FQDN). Vous pouvez à présent installer HDInsight dans le réseau virtuel.

## <a name="test-your-settings-before-deploying-an-hdinsight-cluster"></a>Tester vos paramètres avant de déployer un cluster HDInsight

Avant de déployer votre cluster, vous pouvez vérifier que vos nombreux paramètres de configuration réseau sont corrects en exécutant l’outil [networkValidator](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/blob/main/HDInsightNetworkValidator) sur une machine virtuelle se trouvant dans les mêmes réseau virtuel et sous-réseau que le cluster planifié.

**Pour déployer une machine virtuelle afin d’exécuter le script networkValidator.sh**

1. Ouvrez la page [Ubuntu Server 18.04 LTS](https://portal.azure.com/?feature.customportal=false#create/Canonical.UbuntuServer1804LTS-ARM) du portail Azure , puis cliquez sur **créer**.

1. Sous l’onglet **Options de base**, sous **Détails du projet**, sélectionnez votre abonnement, puis choisissez un groupe de ressources existant ou créez-en un.

    :::image type="content" source="./media/hdinsight-create-virtual-network/project-details.png" alt-text="Capture d’écran de la section Détails du projet montrant où vous sélectionnez l’abonnement Azure et le groupe de ressources pour la machine virtuelle.":::

1. Sous **Détails de l’instance**, entrez un **Nom de machine virtuelle** unique, sélectionnez la même **Région** que celle de votre réseau virtuel, choisissez *Aucune redondance d’infrastructure requise* pour les **Options de disponibilité**, choisissez *Ubuntu 18.04 LTS* pour votre **image**, laissez le champ **Instance Azure Spot** vide, puis choisissez Standard_B1s (ou plus grand) pour la **Taille**.

    :::image type="content" source="./media/hdinsight-create-virtual-network/instance-details.png" alt-text="Capture d’écran de la section Détails de l’instance où vous spécifiez un nom pour la machine virtuelle et où vous sélectionnez sa région, son image et sa taille.":::

1. Sous **Compte administrateur**, sélectionnez **Mot de passe**, puis entrez un nom d’utilisateur et un mot de passe pour le compte administrateur. 

    :::image type="content" source="./media/hdinsight-create-virtual-network/administrator-account.png" alt-text="Capture d’écran de la section Compte d’administrateur où vous sélectionnez un type d’authentification et où fournissez les informations d’identification de l’administrateur.":::

1. Sous **Règles des ports d’entrée** > **Ports d’entrée publics**, choisissez **Autoriser les ports sélectionnés**, sélectionnez **SSH (22)** dans la liste déroulante, puis cliquez sur **Suivant : Disques >** .

    :::image type="content" source="./media/hdinsight-create-virtual-network/inbound-port-rules.png" alt-text="Capture d’écran de la section Règles des ports d’entrée où vous sélectionnez les ports sur lesquels les connexions entrantes sont autorisées.":::

1. Sous **options de disque**, choisissez *SSD Standard pour le type de disque du système d’exploitation*, puis cliquez sur **Suivant : Mise en réseau >** .

1. Sur la page **Mise en réseau**, sous **Interface réseau**, sélectionnez le **Réseau virtuel** et le **Sous-réseau** dans lesquels vous envisagez d’ajouter le cluster HDInsight, puis sélectionnez le bouton **Examiner et créer** au bas de la page.

    :::image type="content" source="./media/hdinsight-create-virtual-network/vnet.png" alt-text="Capture d’écran de la section de l’interface réseau dans laquelle vous sélectionnez le réseau virtuel et le sous-réseau dans lesquels ajouter la machine virtuelle.":::

1. Sur la page **Create a virtual machine** (Créer une machine virtuelle), vous pouvez voir les détails de la machine virtuelle que vous allez créer. Lorsque vous êtes prêt, sélectionnez **Créer**.

1. Une fois le déploiement terminé, sélectionnez **Accéder à la ressource**.

1. Dans la page de votre nouvelle machine virtuelle, sélectionnez l’adresse IP publique et copiez-la dans le presse-papiers.

    :::image type="content" source="./media/hdinsight-create-virtual-network/ip-address.png" alt-text="Capture d’écran montrant comment copier l’adresse IP pour la machine virtuelle.":::

**Exécuter le script/networkValidator.sh**

1. Connectez-vous avec le protocole SSH à la nouvelle machine virtuelle.
1. Copiez tous les fichiers de [GitHub](https://github.com/Azure-Samples/hdinsight-diagnostic-scripts/tree/main/HDInsightNetworkValidator) sur la machine virtuelle à l’aide de la commande suivante :

    `wget -i https://raw.githubusercontent.com/Azure-Samples/hdinsight-diagnostic-scripts/main/HDInsightNetworkValidator/all.txt`

1. Ouvrez le fichier params.txt dans un éditeur de texte, puis ajoutez des valeurs à toutes les variables. Utilisez une chaîne vide ("") lorsque vous souhaitez omettre la validation associée.
1. Exécutez `sudo chmod +x ./setup.sh` pour créer un exécutable setup.sh, puis exécutez-le avec `sudo ./setup.sh` afin d’installer pip pour Python 2.x ainsi que les modules Python 2.x requis.
1. Exécutez le script principal avec `sudo python2 ./networkValidator.py`.
1. Une fois l’exécution du script terminée, la section Summary (Résumé) indique si les vérifications ont réussi, si vous pouvez créer le cluster ou si des problèmes ont été rencontrés. auquel cas vous devez examiner la sortie d’erreur et la documentation associée pour corriger l’erreur.

    Après avoir essayé de corriger des erreurs, vous pouvez réexécuter le script pour vérifier votre progression.
1. Lorsque vous avez terminé vos vérifications et que le résumé indique "SUCCESS: You can create your HDInsight cluster in this VNet/Subnet", vous pouvez créer votre cluster. 
1. Lorsque vous avez fini d’exécuter le script de validation, supprimez la nouvelle machine virtuelle. 

## <a name="next-steps"></a>Étapes suivantes

* Pour obtenir un exemple complet de configuration de HDInsight pour se connecter à un réseau local, consultez [Connecter HDInsight à un réseau local](./connect-on-premises-network.md).
* Pour configurer des clusters Apache HBase dans des réseaux virtuels Azure, consultez [Créer des clusters Apache HBase sur HDInsight dans un réseau virtuel Azure](hbase/apache-hbase-provision-vnet.md).
* Pour configurer la géoréplication Apache HBase, consultez [Configurer la réplication de cluster Apache HBase dans les réseaux virtuels Azure](hbase/apache-hbase-replication.md).
* Pour plus d’informations sur les réseaux virtuels Azure, voir [Vue d'ensemble de Réseau virtuel Azure](../virtual-network/virtual-networks-overview.md).

* Pour plus d’informations sur les groupes de sécurité réseau, consultez [Groupes de sécurité réseau](../virtual-network/network-security-groups-overview.md).

* Pour plus d’informations sur les routages par l’utilisateur, consultez [Routage définis par l’utilisateur et transfert IP](../virtual-network/virtual-networks-udr-overview.md).
