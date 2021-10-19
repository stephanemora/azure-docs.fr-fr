---
title: Restreindre la connectivité publique dans Azure HDInsight (préversion)
description: En savoir plus sur la suppression de l’accès à toutes les IP publiques sortantes
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/20/2021
ms.openlocfilehash: 8d4fc269137b9d11ab0db046288f1d548b911d7a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129716468"
---
# <a name="restrict-public-connectivity-in-azure-hdinsight-preview"></a>Restreindre la connectivité publique dans Azure HDInsight (préversion)

## <a name="overview"></a>Vue d’ensemble
Dans l’[architecture de réseau virtuel par défaut](./hdinsight-virtual-network-architecture.md) d’Azure HDInsight, le fournisseur de ressources HDInsight communique avec le cluster sur un réseau public. Dans cet article, vous allez découvrir les contrôles avancés que vous pouvez utiliser pour créer un cluster HDInsight restreint dans lequel la connectivité entrante est limitée au réseau privé. Si vous souhaitez une connectivité publique entre vos clusters HDInsight et les ressources dépendantes, envisagez de restreindre la connectivité de votre cluster en suivant les instructions de [contrôle du trafic dans Azure HDInsight](./control-network-traffic.md). Outre la restriction de la connectivité publique, nous ajoutons une prise en charge des ressources de dépendance avec liaison privée qui peuvent être configurées pour être utilisées avec ces clusters.

Le diagramme suivant montre à quoi peut ressembler une potentielle architecture de réseau virtuel HDInsight lorsque `resourceProviderConnection` est définie sur *sortante* :

:::image type="content" source="media/hdinsight-private-link/outbound-resource-provider-connection-only.png" alt-text="Diagramme de l’architecture HDInsight utilisant une connexion de fournisseur de ressources sortante":::

> [!NOTE]
> La restriction de la connectivité publique est une condition préalable à l’activation de Private Link et ne doit pas être considérée comme la même capacité.

## <a name="initialize-a-restricted-cluster"></a>Initialiser un cluster restreint

Par défaut, le fournisseur de ressources HDInsight utilise une connexion *entrante* vers le cluster en utilisant des IP publiques. Lorsque la propriété réseau `resourceProviderConnection` est définie sur *sortante*, elle inverse les connexions vers le fournisseur de ressources HDInsight, de sorte que les connexions sont toujours initiées de l’intérieur du cluster vers l’extérieur en direction du fournisseur de ressources. Dans cette configuration, sans connexion entrante, il n’est pas nécessaire de configurer les étiquettes de service entrantes dans le groupe de sécurité du réseau (NSG). Il n’est pas non plus nécessaire de contourner le pare-feu ou l’appliance virtuelle réseau via des itinéraires définis par l’utilisateur (UDR).

Après avoir créé votre cluster, configurez la résolution DNS appropriée en ajoutant les enregistrements DNS nécessaires pour votre cluster HDInsight restreint. L’enregistrement DNS de nom canonique (CNAME) suivant est créé sur la zone DNS publique gérée par Azure : `azurehdinsight.net`.

```dns
<clustername>    CNAME    <clustername>-int
```

Pour accéder au cluster à l’aide de noms de domaine complets du cluster, vous pouvez soit utiliser directement les adresses IP privées de l’équilibreur de charge interne, soit utiliser votre propre zone DNS privée (dans ce cas, le nom de la zone doit être `azurehdinsight.net`) pour remplacer les points de terminaison du cluster en fonction de vos besoins. Par exemple, pour votre zone DNS privée `azurehdinsight.net`, vous pouvez ajouter vos adresses IP privées le cas échéant :

```dns
<clustername>        A   10.0.0.1
<clustername-ssh>    A   10.0.0.2
```

> [!NOTE]
> Il est déconseillé d’avoir des clusters restreints dans le même réseau virtuel (avec une zone DNS privée pour `azurehdinsight.net`) que d’autres clusters où la connectivité publique est activée, car cela peut entraîner un comportement/conflit involontaire de résolution DNS.

Pour faciliter votre configuration DNS, nous renvoyons les noms de domaine complets et les adresses IP privées correspondantes dans le cadre de la réponse `GET` du cluster. Vous pouvez utiliser cet extrait de code PowerShell pour commencer.

```powershell
<#
    This script is offered as an example to help get started with automation and can be adjusted based on your needs.
    This script assumes:
    - HDInsight cluster has already been created and the context where this script is run has permissions to read/write resources in the same resource group.
    - Private DNS zone resource exists in the same subscription as the HDInsight cluster.
We recommend that you use the latest version of Az.HDInsight module

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

# 2. Confirm DNS zone exists
Get-AzPrivateDnsZone -ResourceGroupName $dnsZoneResourceGroupName -Name $dnsZoneName -ErrorAction Stop

# 3. Update DNS entries for the cluster in the private DNS zone
#    - If the entries already exist, update to the new IP
#    - If the entries do not exist, create them
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

## <a name="adding-private-link-connectivity-private-endpoints-to-cluster-dependent-resources-optional"></a>Ajout de la connectivité de liaison privée (points de terminaison privés) aux ressources dépendantes du cluster (facultatif)

La configuration de `resourceProviderConnection` sur *sortante* vous permet également d’accéder à des ressources spécifiques au cluster, telles que Stockage (Azure Data Lake Storage Gen2 et Windows Azure Storage Blob), les metastores SQL (Apache Ranger, Ambari, Oozie et Hive) et Azure Key Vault, à l’aide de points de terminaison privés. Il n’est pas obligatoire d’utiliser des points de terminaison privés pour ces ressources, mais si vous prévoyez d’utiliser des points de terminaison privés pour ces ressources, vous devez créer ces ressources, configurer les points de terminaison privés et les entrées DNS avant de créer le cluster HDInsight. Toutes ces ressources doivent être accessibles depuis l’intérieur du sous-réseau du cluster, soit par un point de terminaison privé, soit d’une autre manière.
Lorsque vous vous connectez à Azure Data Lake Storage Gen2 sur un point de terminaison privé, assurez-vous que le compte de stockage Gen2 dispose d'un point de terminaison défini pour « blob » et « dfs ». Pour plus d’informations, consultez [Créer un point de terminaison privé](../private-link/create-private-endpoint-portal.md).

## <a name="using-firewall-optional"></a>Utilisation du pare-feu (facultatif)
Les clusters HDInsight peuvent toujours se connecter à l’Internet public pour recevoir des dépendances sortantes. Si vous souhaitez restreindre davantage l’accès, vous pouvez [configurer un pare-feu](./hdinsight-restrict-outbound-traffic.md), mais ce n’est pas une obligation.

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

* [Activer Private Link sur votre cluster](./hdinsight-private-link.md)
* [Pack Sécurité Entreprise pour Azure HDInsight](enterprise-security-package.md)
* [Informations et recommandations générales sur la sécurité d’entreprise dans Azure HDInsight](./domain-joined/general-guidelines.md)
