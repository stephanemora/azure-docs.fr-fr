---
title: Restreindre la connectivité publique dans Azure HDInsight
description: Découvrez la procédure de suppression de l’accès à toutes les adresses IP publiques sortantes.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 6cc4a73c08d945f5a5ce26cef9c3774ffb5772d0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069137"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight"></a>Restreindre la connectivité publique dans Azure HDInsight

Dans l’[architecture de réseau virtuel par défaut](./hdinsight-virtual-network-architecture.md) d’Azure HDInsight, le fournisseur de ressources HDInsight communique avec le cluster sur un réseau public. Dans cet article, vous allez découvrir les contrôles avancés que vous pouvez utiliser pour créer un cluster HDInsight restreint dans lequel la connectivité entrante est limitée à un réseau privé. 

Si vous souhaitez de la connectivité publique entre vos clusters HDInsight et des ressources dépendantes, envisagez de restreindre la connectivité de votre cluster en suivant les instructions fournies dans [Contrôler le trafic réseau dans Azure HDInsight](./control-network-traffic.md). Outre la restriction de la connectivité publique, vous pouvez configurer les ressources de dépendance compatibles avec Azure Private Link de façon à les utiliser avec les clusters HDInsight.

Le diagramme suivant montre à quoi peut ressembler une potentielle architecture de réseau virtuel HDInsight lorsque `resourceProviderConnection` est défini sur *sortant* :

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramme de l’architecture HDInsight utilisant une connexion de fournisseur de ressources sortante.":::

> [!NOTE]
> La restriction de la connectivité publique est une condition préalable à l’activation de Private Link et ne doit pas être considérée comme la même capacité.

## <a name="initialize-a-restricted-cluster"></a>Initialiser un cluster restreint

Par défaut, le fournisseur de ressources HDInsight utilise une connexion *entrante* vers le cluster avec des adresses IP publiques. Lorsque la propriété réseau `resourceProviderConnection` est définie sur *sortante*, elle inverse les connexions vers le fournisseur de ressources HDInsight, de sorte que les connexions sont toujours initiées de l’intérieur du cluster et vont vers l’extérieur en direction du fournisseur de ressources. 

Dans cette configuration, sans connexion entrante, il n’est pas nécessaire de configurer les balises de service entrantes dans le groupe de sécurité réseau. Il n’est pas non plus nécessaire de contourner le pare-feu ou l’appliance virtuelle réseau via des itinéraires définis par l’utilisateur.

Après avoir créé votre cluster, configurez la résolution DNS appropriée en ajoutant les enregistrements DNS nécessaires pour votre cluster HDInsight restreint. L’enregistrement DNS de nom canonique (CNAME) suivant est créé dans la zone DNS publique gérée par Azure : `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Pour accéder au cluster à l’aide de noms de domaine complets (FQDN), vous pouvez utiliser l’une de ces techniques en fonction de vos besoins :

- Utilisez directement les adresses IP privées de l’équilibreur de charge interne.
- Utilisez votre propre zone DNS privé pour remplacer les points de terminaison du cluster. Dans ce cas, le nom de la zone doit être `azurehdinsight.net`.

Par exemple, pour votre zone DNS privé `azurehdinsight.net`, vous pouvez ajouter vos adresses IP privées le cas échéant :

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> Nous vous déconseillons de placer les clusters restreints dans le même réseau virtuel (avec une zone DNS privé pour `azurehdinsight.net`) que les autres clusters où la connectivité publique est activée. Cela peut entraîner des conflits ou un comportement de résolution DNS inattendu.

Pour faciliter votre configuration DNS, nous renvoyons les noms de domaine complets et les adresses IP privées correspondantes dans le cadre de la réponse `GET` du cluster. Vous pouvez utiliser cet extrait de code PowerShell pour commencer :

```powershell
<#
    This script is an example to help you get started with automation and can be adjusted based on your needs.
    This script assumes:
    - The HDInsight cluster has already been created, and the context where this script is run has permissions to read/write resources in the same resource group.
    - The private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of the Az.HDInsight module.

#>
$subscriptionId = "<Replace with subscription for deploying HDInsight clusters, and containing private DNS zone resource>"

$clusterName = "<Replace with cluster name>"
$clusterResourceGroupName = "<Replace with resource group name>"

# For example, azurehdinsight.net for public cloud.
$dnsZoneName = "<Replace with private DNS zone name>"
$dnsZoneResourceGroupName = "<Replace with private DNS zone resource group name>"

Connect-AzAccount -SubscriptionId $subscriptionId

# 1. Get cluster endpoints
$clusterEndpoints = $(Get-AzHDInsightCluster -ClusterName $clusterName ` -ResourceGroupName $clusterResourceGroupName).ConnectivityEndpoints

$endpointMapping = @{}

foreach($endpoint in $clusterEndpoints)
{
    $label = $endpoint.Location.ToLower().Replace(".$dnsZoneName".ToLower(), "")
    $ip = $endpoint.PrivateIPAddress

    $endpointMapping.Add($label, $ip)
}

# 2. Confirm that the DNS zone exists.
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone:
#    - If the entries already exist, update to the new IP.
#    - If the entries don't exist, create them.
$recordSets = Get-AzPrivateDnsRecordSet -ZoneName $dnsZoneName -ResourceGroupName $dnsZoneResourceGroupName -RecordType A

foreach($label in $endpointMapping.Keys)
{
    $updateRecord = $null

    foreach($record in $recordSets)
    {
        if($record.Name -eq $label)
        {
            $updateRecord = $record
            break;
        }
        
    }

    if($null -ne $updateRecord)
    {
        $updateRecord.Records[0].Ipv4Address = $endpointMapping[$label]
        Set-AzPrivateDnsRecordSet -RecordSet $updateRecord | Out-Null
    }
    else
    {
        New-AzPrivateDnsRecordSet `
            -ResourceGroupName $dnsZoneResourceGroupName `
            -ZoneName $dnsZoneName `
            -Name $label `
            -RecordType A `
            -Ttl 3600 `
            -PrivateDnsRecord (New-AzPrivateDnsRecordConfig -Ipv4Address $endpointMapping[$label]) | Out-Null
    }
}

```

## <a name="add-private-link-connectivity-to-cluster-dependent-resources-optional"></a>Ajouter une connectivité Private Link à des ressources dépendantes du cluster (facultatif)

Configurer `resourceProviderConnection` sur *sortant* vous permet également d’accéder à des ressources spécifiques au cluster à l’aide de points de terminaison privés. Ces ressources incluent :

- Stockage : Azure Data Lake Storage Gen2 et Stockage Blob Azure
- Metastores SQL : Apache Ranger, Ambari, Oozie et Hive
- Azure Key Vault 

Il n’est pas obligatoire d’utiliser des points de terminaison privés pour ces ressources. Mais si vous prévoyez d’utiliser des points de terminaison privés pour ces ressources, vous devez créer les ressources, et configurer les points de terminaison privés et les entrées DNS avant de créer le cluster HDInsight. Toutes ces ressources doivent être accessibles depuis l’intérieur du sous-réseau du cluster, soit par un point de terminaison privé, soit d’une autre manière.

Lorsque vous vous connectez à Azure Data Lake Storage Gen2 sur un point de terminaison privé, assurez-vous que le compte de stockage Gen2 dispose d'un point de terminaison défini pour `blob` et `dfs`. Pour plus d’informations, consultez [Créer un point de terminaison privé](../private-link/create-private-endpoint-portal.md).

## <a name="use-a-firewall-optional"></a>Utiliser un pare-feu (facultatif)
Les clusters HDInsight peuvent toujours se connecter à l’Internet public pour recevoir des dépendances sortantes. Si vous souhaitez restreindre l’accès, vous pouvez [configurer un pare-feu](./hdinsight-restrict-outbound-traffic.md), mais ce n’est pas une obligation.

## <a name="create-clusters"></a>Créer des clusters

L’extrait de code JSON suivant comprend les deux propriétés réseau que vous devez configurer dans votre modèle Azure Resource Manager pour créer un cluster HDInsight privé :

```json
networkProperties: {
    "resourceProviderConnection": "Inbound" | "Outbound"
}
```

Pour obtenir un modèle complet avec de nombreuses fonctionnalités de sécurité d’entreprise HDInsight, notamment Azure Private Link, consultez [Modèle de sécurité d’entreprise HDInsight](https://github.com/Azure-Samples/hdinsight-enterprise-security/tree/main/ESP-HIB-PL-Template).

Pour créer un cluster à l’aide de PowerShell, consultez l'[exemple](/powershell/module/az.hdinsight/new-azhdinsightcluster#example-4--create-an-azure-hdinsight-cluster-with-relay-outbound-and-private-link-feature).

Pour créer un cluster à l’aide d’Azure CLI, consultez l'[exemple](/cli/azure/hdinsight#az_hdinsight_create-examples).

## <a name="next-steps"></a>Étapes suivantes

* [Activer Private Link sur votre cluster](./hdinsight-private-link.md)
* [Pack Sécurité Entreprise pour Azure HDInsight](enterprise-security-package.md)
* [Informations et recommandations générales pour la sécurité des entreprises dans Azure HDInsight](./domain-joined/general-guidelines.md)
