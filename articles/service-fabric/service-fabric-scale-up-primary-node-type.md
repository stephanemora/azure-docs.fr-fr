---
title: Effectuer un scale-up sur un type de nœud primaire Azure Service Fabric
description: Mettez à l’échelle votre cluster Service Fabric verticalement en ajoutant un nouveau type de nœud et en supprimant le précédent.
ms.date: 12/11/2020
ms.author: pepogors
ms.topic: how-to
ms.openlocfilehash: 325ece761481077171a670c52e9d98071237601a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98251173"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Monter en puissance un type de nœud principal de cluster Service Fabric

Cet article explique comment effectuer un scale-up sur un type de nœud principal de cluster Service Fabric en avec un temps d’arrêt minimal. La stratégie générale de mise à niveau d’un type de nœud de cluster Service Fabric est la suivante :

1. Ajoutez un nouveau type de nœud à votre cluster Service Fabric, qui s’appuie sur la référence SKU et la configuration de votre groupe de machines virtuelles identiques mis à niveau (ou modifié). Cette étape implique également la configuration d’un nouvel équilibreur de charge, d’un sous-réseau et d’une IP publique pour le groupe identique.

1. Une fois que les groupes identiques d’origine et mis à niveau sont exécutés côte à côte, désactivez les instances de nœud d’origine une par une afin que les services système (ou réplicas de services avec état) migrent vers le nouveau groupe identique.

1. Vérifiez que le cluster et les nouveaux nœuds sont intègres, puis supprimez le groupe identique d’origine (et les ressources associées) et l’état du nœud pour les nœuds supprimés.

Vous trouverez ci-dessous une description du processus de mise à jour de la taille et du système d’exploitation des machines virtuelles de type nœud principal d’un exemple de cluster avec [durabilité Argent](service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster), qui repose sur un seul groupe identique avec cinq nœuds. Nous allons mettre à niveau le type de nœud principal :

- De la taille de machine virtuelle *Standard_D2_V2* à *Standard_D4_V2*, et
- Du système d’exploitation de machine virtuelle *Windows Server 2016 Datacenter avec conteneurs* à *Windows Server 2019 Datacenter avec conteneurs*.

> [!WARNING]
> Avant de tenter cette procédure sur un cluster de production, nous vous recommandons d’examiner les exemples de modèles et de vérifier la procédure sur un cluster de test. Le cluster peut également être indisponible pendant une brève période de temps.
>
> N’essayez pas une procédure de mise à l’échelle de type de nœud principal si l’état du cluster n’est pas sain, car cela déstabilisera uniquement le cluster.

Voici les modèles de déploiement Azure, étape par étape, que nous allons utiliser pour réaliser cet exemple de scénario de mise à niveau : https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade.

## <a name="set-up-the-test-cluster"></a>Configurer le cluster de test

Configurons le cluster de test Service Fabric initial. Tout d’abord, [téléchargez](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade) les exemples de modèles Azure Resource Manager que nous allons utiliser pour terminer ce scénario.

Ensuite, connectez-vous à votre compte Azure.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

Ouvrez ensuite le fichier [*parameters.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/parameters.json) et mettez à jour la valeur de `clusterName` sur un nom unique (dans Azure).

Les commandes suivantes vous guident dans la génération d’un nouveau certificat autosigné et le déploiement du cluster de test. Si vous avez déjà un certificat que vous souhaitez utiliser, passez à [Utiliser un certificat existant pour déployer le cluster](#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Générer un certificat autosigné et déployer le cluster

Tout d’abord, attribuez les variables dont vous aurez besoin pour le déploiement du cluster Service Fabric. Ajustez les valeurs de `resourceGroupName`, `certSubjectName`, `parameterFilePath` et `templateFilePath` pour votre compte et votre environnement spécifiques :

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$parameterFilePath = "C:\parameters.json"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
```

> [!NOTE]
> Assurez-vous que l’emplacement `certOutputFolder` existe sur votre ordinateur local avant d’exécuter la commande pour déployer un nouveau cluster Service Fabric.

Déployez ensuite le cluster de test Service Fabric :

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Une fois le déploiement terminé, localisez le fichier *.pfx* (`$certPfx`) sur votre ordinateur local et importez-le dans votre magasin de certificats :

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

L’opération retournera l’empreinte de certificat, que vous pouvez utiliser pour [vous connecter au nouveau cluster](#connect-to-the-new-cluster-and-check-health-status) et vérifier son état d’intégrité. (Ignorez la section suivante, qui est une autre approche du déploiement de cluster.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Utiliser un certificat existant pour déployer le cluster

Vous pouvez également utiliser un certificat Azure Key Vault existant pour déployer le cluster de test. Pour ce faire, vous devez [obtenir les références de votre Key Vault](#obtain-your-key-vault-references) et l’empreinte du certificat.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Ensuite, désignez un nom de groupe de ressources pour le cluster et définissez les emplacements `templateFilePath` et `parameterFilePath` :

> [!NOTE]
> Le groupe de ressources désigné doit déjà exister et se trouver dans la même région que votre Key Vault.

```powershell
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Initial-TestClusterSetup.json"
$parameterFilePath = "C:\parameters.json"
```

Enfin, exécutez la commande suivante pour déployer le cluster de test initial :

```powershell
# Deploy the initial test cluster
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Se connecter au nouveau cluster et vérifier l’état d’intégrité

Connectez-vous au cluster et assurez-vous que ses cinq nœuds sont intègres (remplacez les variables `clusterName` et `thumb` par vos propres valeurs) :

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Avec cela, nous sommes prêts à commencer la procédure de mise à niveau.

## <a name="deploy-a-new-primary-node-type-with-upgraded-scale-set"></a>Déployer un nouveau type de nœud principal avec un groupe identique mis à niveau

Pour mettre à niveau (mettre à l’échelle verticalement) un type de nœud, nous devons tout d’abord déployer un nouveau type de nœud reposant sur un nouveau groupe identique et les ressources de prise en charge. Le nouveau groupe identique sera marqué comme principal (`isPrimary: true`), tout comme le groupe identique d’origine (sauf si vous effectuez la mise à niveau d’un type de nœud non principal). Les ressources créées dans la section suivante deviendront finalement le nouveau type de nœud principal de votre cluster, et les ressources du type de nœud principal d’origine seront supprimées.

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>Mettre à jour le modèle de cluster avec le groupe identique mis à niveau

Voici les modifications section par section du modèle de déploiement du cluster d’origine pour l’ajout d’un nouveau type de nœud principal et des ressources de prise en charge.

Les modifications requises pour cette étape ont déjà été effectuées pour vous dans le fichier de modèle [*Step1-AddPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step1-AddPrimaryNodeType.json), et ce qui suit expliquera ces modifications en détail. Si vous préférez, vous pouvez ignorer l’explication et continuer à [obtenir vos références Key Vault](#obtain-your-key-vault-references) et [déployer le modèle mis à jour](#deploy-the-updated-template) qui ajoute un nouveau type de nœud principal à votre cluster.

> [!Note]
> Veillez à utiliser des noms propres au type de nœud d’origine, au groupe identique, à l’équilibreur de charge, à l’IP publique et au sous-réseau du type de nœud principal d’origine, car ces ressources seront supprimées à une étape ultérieure du processus.

#### <a name="create-a-new-subnet-in-the-existing-virtual-network"></a>Créer un sous-réseau dans le réseau virtuel existant

```json
{
    "name": "[variables('subnet1Name')]",
    "properties": {
        "addressPrefix": "[variables('subnet1Prefix')]"
    }
}
```

#### <a name="create-a-new-public-ip-with-a-unique-domainnamelabel"></a>Créer une nouvelle IP publique avec un domainNameLabel unique

```json
{
    "apiVersion": "[variables('publicIPApiVersion')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))]",
    "location": "[variables('computeLocation')]",
    "properties": {
    "dnsSettings": {
        "domainNameLabel": "[concat(variables('dnsName'),'-','nt1')]"
    },
    "publicIPAllocationMethod": "Dynamic"
    },
    "tags": {
    "resourceType": "Service Fabric",
    "clusterName": "[parameters('clusterName')]"
    }
}
```

#### <a name="create-a-new-load-balancer-for-the-public-ip"></a>Créer un nouvel équilibreur de charge pour l’IP publique

```json
"dependsOn": [
    "[concat('Microsoft.Network/publicIPAddresses/',concat(variables('lbIPName'),'-',variables('vmNodeType1Name')))]"
]
```

#### <a name="create-a-new-virtual-machine-scale-set-with-upgraded-vm-and-os-skus"></a>Créer un groupe de machines virtuelles identiques (avec les SKU de machine virtuelle et de système d’exploitation mises à niveau)

Ref type de nœud

```json
"nodeTypeRef": "[variables('vmNodeType1Name')]"
```

Référence de la machine virtuelle

```json
"sku": {
    "name": "[parameters('vmNodeType1Size')]",
    "capacity": "[parameters('nt1InstanceCount')]",
    "tier": "Standard"
}
```

Référence (SKU) du système d’exploitation

```json
"imageReference": {
    "publisher": "[parameters('vmImagePublisher1')]",
    "offer": "[parameters('vmImageOffer1')]",
    "sku": "[parameters('vmImageSku1')]",
    "version": "[parameters('vmImageVersion1')]"
}
```

En outre, assurez-vous d’inclure toutes les extensions supplémentaires requises pour votre charge de travail.

#### <a name="add-a-new-primary-node-type-to-the-cluster"></a>Ajouter un type de nœud primaire au cluster

Maintenant que le nouveau type de nœud (vmNodeType1Name) a son propre nom, sous-réseau, adresse IP, équilibreur de charge et groupe identique, il peut réutiliser toutes les autres variables du type de nœud d’origine (par exemple, `nt0applicationEndPort`, `nt0applicationStartPort` et `nt0fabricTcpGatewayPort`) :

```json
"name": "[variables('vmNodeType1Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt1InstanceCount')]"
```

Une fois que vous avez implémenté toutes les modifications apportées à vos fichiers de modèle et de paramètres, passez à la section suivante pour obtenir vos références Key Vault et déployer les mises à jour sur votre cluster.

### <a name="obtain-your-key-vault-references"></a>Obtenir vos références Key Vault

Pour déployer la configuration mise à jour, vous aurez besoin de plusieurs références au certificat de cluster stocké dans votre coffre de clés. Le moyen le plus simple de rechercher ces valeurs consiste à utiliser le Portail Azure. Vous devez disposer des éléments suivants :

* **L’URL Key Vault de votre certificat de cluster.** À partir de votre Key Vault dans Portail Azure, sélectionnez **Certificats** > *Le certificat souhaité* > **Identificateur de secret** :

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **L’empreinte de votre certificat de cluster.** (Vous l’avez probablement déjà si vous [vous êtes connecté au cluster initial](#connect-to-the-new-cluster-and-check-health-status) pour vérifier son état d’intégrité.) À partir du même panneau de certificat (**Certificats** > *Le certificat souhaité*) dans Portail Azure, copiez **l’empreinte SHA-1 X.509 (en hexadécimal)**  :

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **L’ID de ressource de votre Key Vault.** À partir de votre Key Vault dans Portail Azure, sélectionnez **Propriétés** > **ID de ressource** :

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>Déployer le modèle mis à jour

Ajustez `templateFilePath` selon les besoins et exécutez la commande suivante :

```powershell
# Deploy the new node type and its resources
$templateFilePath = "C:\Step1-AddPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Une fois le déploiement terminé, vérifiez à nouveau l’intégrité du cluster et assurez-vous que tous les nœuds des deux types de nœuds sont intègres.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-node-type"></a>Migrer les nœuds initiaux vers le nouveau type de nœud

Nous sommes maintenant prêts à mettre à jour le type de nœud d’origine comme non primaire et à désactiver ses nœuds. Lorsque les nœuds sont désactivés, les services système et les nœuds initiaux du cluster migrent vers le nouveau groupe identique.

### <a name="unmark-the-original-node-type-as-primary"></a>Supprimer la marque « principal » du type de nœud d’origine

Tout d’abord, supprimez la désignation `isPrimary` dans le modèle à partir du type de nœud d’origine.

```json
{
    "isPrimary": false,
}
```

Déployez ensuite le modèle avec la mise à jour. Cela lancera la migration des nœuds initiaux vers le nouveau groupe identique.

```powershell
$templateFilePath = "C:\Step2-UnmarkOriginalPrimaryNodeType.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!Note]
> Il faudra un certain temps pour achever la migration des nœuds initiaux vers le nouveau groupe identique. Pour garantir la cohérence des données, un seul nœud initial peut changer à la fois. Chaque modification du nœud initial requiert une mise à jour du cluster ; par conséquent, le remplacement d’un nœud initial nécessite deux mises à niveau du cluster (une pour l’ajout et l’autre pour la suppression de nœuds). La mise à niveau des cinq nœuds initiaux dans cet exemple de scénario entraîne la mise à niveau de dix clusters.

Utilisez Service Fabric Explorer pour superviser la migration des nœuds initiaux vers le nouveau groupe identique. Les nœuds du type de nœud d’origine (nt0vm) doivent tous être *false* dans la colonne **Nœud initial**, et ceux du nouveau type de nœud (nt1vm) sont *true*.

### <a name="disable-the-nodes-in-the-original-node-type-scale-set"></a>Désactiver les nœuds dans le groupe identique du type de nœud d’origine

Une fois que tous les nœuds initiaux ont été migrés vers le nouveau groupe identique, vous pouvez désactiver les nœuds du groupe identique d’origine.

```powershell
# Disable the nodes in the original scale set.
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Disabling nodes..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Disable-ServiceFabricNode -Intent RemoveNode -NodeName $node.NodeName -Force
  }
}
```

Utilisez Service Fabric Explorer pour surveiller la progression des nœuds dans le groupe identique d’origine de l’état *Désactivation* à *Désactivé*.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status.png" alt-text="Service Fabric Explorer présentant l’état de nœuds désactivés":::

Pour les durabilités Argent et Or, certains nœuds passent à l’état Désactivé, tandis que d’autres peuvent rester à l’état *Désactivation*. Dans Service Fabric Explorer, vérifiez l’onglet **Détails** des nœuds à l’état Désactivation. S’ils affichent un *contrôle de sécurité en attente* de type *EnsurePartitionQuorem* (assurant le quorum pour les partitions de service d’infrastructure), il est possible de continuer.

:::image type="content" source="./media/scale-up-primary-node-type/service-fabric-explorer-node-status-disabling.png" alt-text="Vous pouvez poursuivre l’arrêt des données et la suppression des nœuds bloqués à l’état « Désactivation » s’ils affichent un contrôle de sécurité en attente de type « EnsurePartitionQuorum ».":::

Si votre cluster dépend de la durabilité Bronze, attendez que tous les nœuds atteignent l’état *Désactivé*.

### <a name="stop-data-on-the-disabled-nodes"></a>Arrêter les données sur les nœuds désactivés

Vous pouvez maintenant arrêter les données sur les nœuds désactivés.

```powershell
# Stop data on the disabled nodes.
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Start-ServiceFabricNodeTransition -Stop -OperationId (New-Guid) -NodeInstanceId $node.NodeInstanceId -NodeName $node.NodeName -StopDurationInSeconds 10000
  }
}
```

## <a name="remove-the-original-node-type-and-cleanup-its-resources"></a>Supprimer le type de nœud d’origine et nettoyer ses ressources

Nous sommes prêts à supprimer le type de nœud d’origine et les ressources qui lui sont associées pour terminer la procédure de mise à l’échelle verticale.

### <a name="remove-the-original-scale-set"></a>Supprimer le groupe identique d’origine

Tout d’abord, supprimez le groupe identique de soutien du type de nœud.

```powershell
$scaleSetName = "nt0vm"
$scaleSetResourceType = "Microsoft.Compute/virtualMachineScaleSets"

Remove-AzResource -ResourceName $scaleSetName -ResourceType $scaleSetResourceType -ResourceGroupName $resourceGroupName -Force
```

### <a name="delete-the-original-ip-and-load-balancer-resources"></a>Supprimer l’adresse IP d’origine et les ressources de l’équilibreur de charge

Vous pouvez maintenant supprimer l’adresse IP d’origine et les ressources de l’équilibreur de charge. Au cours de cette étape, vous allez également mettre à jour le nom DNS.

> [!Note]
> Cette étape est facultative si vous utilisez déjà une IP publique et un équilibreur de charge du SKU *Standard*. Dans ce cas, vous pouvez avoir plusieurs types de nœuds/groupes identiques sous le même équilibreur de charge.

Exécutez les commandes suivantes, en modifiant la valeur `$lbname` le cas échéant.

```powershell
# Delete the original IP and load balancer resources
$lbName = "LB-sftestupgrade-nt0vm"
$lbResourceType = "Microsoft.Network/loadBalancers"
$ipResourceType = "Microsoft.Network/publicIPAddresses"
$oldPublicIpName = "PublicIP-LB-FE-nt0vm"
$newPublicIpName = "PublicIP-LB-FE-nt1vm"

$oldPrimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $resourceGroupName
$primaryDNSName = $oldPrimaryPublicIP.DnsSettings.DomainNameLabel
$primaryDNSFqdn = $oldPrimaryPublicIP.DnsSettings.Fqdn

Remove-AzResource -ResourceName $lbName -ResourceType $lbResourceType -ResourceGroupName $resourceGroupName -Force
Remove-AzResource -ResourceName $oldPublicIpName -ResourceType $ipResourceType -ResourceGroupName $resourceGroupName -Force

$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $resourceGroupName
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

### <a name="remove-node-state-from-the-original-node-type"></a>Supprimer l’état du nœud du type de nœud d’origine

Les nœuds du type de nœud d’origine affichent désormais *Erreur* pour leur **état d’intégrité**. Supprimez l’état du nœud du cluster.

```powershell
# Remove state of the obsolete nodes from the cluster
$nodeType = "nt0vm"
$nodes = Get-ServiceFabricNode

Write-Host "Removing node state..."
foreach($node in $nodes)
{
  if ($node.NodeType -eq $nodeType)
  {
    $node.NodeName

    Remove-ServiceFabricNodeState -NodeName $node.NodeName -Force
  }
}
```

Service Fabric Explorer doit maintenant refléter uniquement les cinq nœuds du nouveau type de nœud (nt1vm), tous avec des valeurs d’état d’intégrité *OK*. L’état d’intégrité de votre cluster affichera toujours *Erreur*. Nous y remédierons ensuite en mettant à jour le modèle pour qu’il reflète les dernières modifications et en le redéployant.

### <a name="update-the-deployment-template-to-reflect-the-newly-scaled-up-primary-node-type"></a>Mettre à jour le modèle de déploiement pour refléter le type de nœud principal nouvellement mis à l’échelle

Les modifications requises pour cette étape ont déjà été effectuées pour vous dans le fichier de modèle [*Step3-CleanupOriginalPrimaryNodeType.json*](https://github.com/microsoft/service-fabric-scripts-and-templates/tree/master/templates/nodetype-upgrade/Step3-CleanupOriginalPrimaryNodeType.json), et les sections suivantes expliquent en détail ces modifications de modèle. Si vous préférez, vous pouvez ignorer l’explication et continuer à [déployer le modèle mis à jour](#deploy-the-finalized-template) et suivre le tutoriel.

#### <a name="update-the-cluster-management-endpoint"></a>Mettre à jour le point de terminaison de gestion du cluster

Mettez à jour le cluster `managementEndpoint` sur le modèle de déploiement pour référencer la nouvelle adresse IP (en mettant à jour *vmNodeType0Name* avec *vmNodeType1Name*).

```json
  "managementEndpoint": "[concat('https://',reference(concat(variables('lbIPName'),'-',variables('vmNodeType1Name'))).dnsSettings.fqdn,':',variables('nt0fabricHttpGatewayPort'))]",
```

#### <a name="remove-the-original-node-type-reference"></a>Supprimer la référence au type de nœud d’origine

Supprimez la référence au type de nœud d’origine de la ressource Service Fabric dans le modèle de déploiement.

```json
"name": "[variables('vmNodeType0Name')]",
"applicationPorts": {
    "endPort": "[variables('nt0applicationEndPort')]",
    "startPort": "[variables('nt0applicationStartPort')]"
},
"clientConnectionEndpointPort": "[variables('nt0fabricTcpGatewayPort')]",
"durabilityLevel": "Bronze",
"ephemeralPorts": {
    "endPort": "[variables('nt0ephemeralEndPort')]",
    "startPort": "[variables('nt0ephemeralStartPort')]"
},
"httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
"isPrimary": true,
"reverseProxyEndpointPort": "[variables('nt0reverseProxyEndpointPort')]",
"vmInstanceCount": "[parameters('nt0InstanceCount')]"
```

#### <a name="configure-health-policies-to-ignore-existing-errors"></a>Configurer des stratégies d’intégrité pour ignorer les erreurs existantes

Pour les clusters de durabilité Argent et supérieure uniquement, mettez à jour la ressource de cluster dans le modèle et configurez les stratégies d’intégrité de manière à ignorer l’intégrité de l’application `fabric:/System` en ajoutant *applicationDeltaHealthPolicies* sous les propriétés de la ressource de cluster, comme indiqué ci-dessous. La stratégie ci-dessous ignore les erreurs existantes, mais n’autorise pas les nouvelles erreurs d’intégrité.

```json
"upgradeDescription":  
{ 
 "forceRestart": false, 
 "upgradeReplicaSetCheckTimeout": "10675199.02:48:05.4775807", 
 "healthCheckWaitDuration": "00:05:00", 
 "healthCheckStableDuration": "00:05:00", 
 "healthCheckRetryTimeout": "00:45:00", 
 "upgradeTimeout": "12:00:00", 
 "upgradeDomainTimeout": "02:00:00", 
 "healthPolicy": { 
   "maxPercentUnhealthyNodes": 100, 
   "maxPercentUnhealthyApplications": 100 
 }, 
 "deltaHealthPolicy":  
 { 
   "maxPercentDeltaUnhealthyNodes": 0, 
   "maxPercentUpgradeDomainDeltaUnhealthyNodes": 0, 
   "maxPercentDeltaUnhealthyApplications": 0, 
   "applicationDeltaHealthPolicies":  
   { 
       "fabric:/System":  
       { 
           "defaultServiceTypeDeltaHealthPolicy":  
           { 
                   "maxPercentDeltaUnhealthyServices": 0 
           } 
       } 
   } 
 } 
}
```

#### <a name="remove-supporting-resources-for-the-original-node-type"></a>Supprimer les ressources de prise en charge pour le type de nœud d’origine

Supprimez toutes les autres ressources associées au type de nœud d’origine à partir du modèle ARM et du fichier de paramètres. Supprimez le code suivant :

```json
    "vmImagePublisher": {
      "value": "MicrosoftWindowsServer"
    },
    "vmImageOffer": {
      "value": "WindowsServer"
    },
    "vmImageSku": {
      "value": "2016-Datacenter-with-Containers"
    },
    "vmImageVersion": {
      "value": "latest"
    },
```

#### <a name="deploy-the-finalized-template"></a>Déployer le modèle finalisé

Enfin, déployez le modèle Azure Resource Manager modifié.

```powershell
# Deploy the updated template file
$templateFilePath = "C:\Step3-CleanupOriginalPrimaryNodeType"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

> [!NOTE]
> Cette étape prendra un certain temps, mais généralement pas plus de deux heures.

Comme la mise à niveau modifie les paramètres d’*InfrastructureService*, un redémarrage du nœud est nécessaire. Dans ce cas, *forceRestart* est ignoré. Le paramètre `upgradeReplicaSetCheckTimeout` spécifie la durée maximale pendant laquelle Service Fabric doit attendre qu'une partition soit sécurisée, si ce n'est pas encore le cas. Une fois les contrôles de sécurité réussis pour toutes les partitions d'un nœud, Service Fabric procède à la mise à niveau sur ce nœud. La valeur du paramètre `upgradeTimeout` peut être réduite à 6 heures, mais pour une sécurité maximale, il convient d'utiliser 12 heures.

Une fois le déploiement terminé, vérifiez dans Portail Azure que l’état de la ressource Service Fabric est *Prêt*. Vérifiez que vous pouvez atteindre le nouveau point de terminaison de Service Fabric Explorer, que l’**état d’intégrité du cluster** est *OK*, et que toutes les applications déployées fonctionnent correctement.

Avec cela, vous avez mis à l’échelle verticalement un type de nœud principal de cluster !

## <a name="next-steps"></a>Étapes suivantes

* Découvrez comment [ajouter un type de nœud à un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md).
* Découvrez-en plus sur l’[extensibilité des applications](service-fabric-concepts-scalability.md).
* [Procédez à une montée et une descente en puissance d’un cluster Azure](service-fabric-tutorial-scale-cluster.md).
* [Mettez à l’échelle un cluster Azure par programmation](service-fabric-cluster-programmatic-scaling.md), à l’aide du kit de développement logiciel de calcul Azure.
* [Augmentez ou diminuez la taille des instances d’un cluster autonome](service-fabric-cluster-windows-server-add-remove-nodes.md).
