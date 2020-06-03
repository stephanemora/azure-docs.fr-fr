---
title: Mise à l’échelle d’un type de nœud Azure Service Fabric
description: Découvrez comment mettre à l’échelle un cluster Service Fabric en ajoutant un groupe de machines virtuelles identiques.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 5ea4f37a6c088c6f738ef05db8b5b295982c27fe
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674225"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Monter en puissance un type de nœud principal de cluster Service Fabric
Cet article explique comment monter en puissance un type de nœud principal de cluster Service Fabric en augmentant les ressources de machine virtuelle. Un cluster Service Fabric est un groupe de machines virtuelles ou physiques connectées au réseau, sur lequel vos microservices sont déployés et gérés. Une machine ou une machine virtuelle faisant partie d’un cluster est appelée un nœud. Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure que vous utilisez pour déployer et gérer une collection de machines virtuelles en tant que groupe. Chaque type de nœud défini dans un cluster Azure est [ configuré comme un groupe identique distinct](service-fabric-cluster-nodetypes.md). Chaque type de nœud peut alors faire l’objet d’une gestion séparée. Une fois que vous avez créé un cluster Service Fabric, vous pouvez mettre à l’échelle le type de nœud d’un cluster verticalement (changement des ressources des nœuds) ou mettre à niveau le système d’exploitation des machines virtuelles du type de nœud.  Une mise à l’échelle peut s’effectuer à tout moment, même lorsque des charges de travail sont en cours d’exécution sur le cluster.  Lorsque vous mettez vos nœuds à l’échelle, vos applications sont automatiquement mises à l’échelle.

> [!WARNING]
> Ne commencez pas à modifier la référence SKU de la machine virtuelle de la propriété nodetype principale en cas d’absence d’intégrité du cluster. En cas de non-intégrité du cluster, vous ne ferez que le déstabiliser encore plus en essayant de modifier la référence SKU de la machine virtuelle.
>
> Nous vous recommandons de ne pas modifier la référence des machines virtuelles d’un type de nœud/d’un groupe identique présentant un niveau de [durabilité inférieur à Silver](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster). Modifier la taille de référence (SKU) des machines virtuelles est une opération d’infrastructure sur place destructrice de données. Faute de pouvoir ne serait-ce que retarder ou surveiller cette modification, il est possible que l’opération occasionne une perte de données pour les services avec état ou provoque d’autres problèmes opérationnels imprévus, même pour les charges de travail sans état. Cela signifie le type de votre nœud principal, qui exécute les services système de la structure de service avec état, ou tout type de nœud exécutant les charges de travail de votre d’application avec état.
>


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Procédure de mise à niveau de la taille et du système d’exploitation des machines virtuelles du type de nœud principal
Voici la procédure pour mettre à jour la taille et le système d’exploitation des machines virtuelles du type de nœud principal.  Après la mise à niveau, la taille des machines virtuelles du type de nœud principal sera Standard D4_V2 et leur système d’exploitation sera Windows Server 2016 Datacenter avec Conteneurs.

> [!WARNING]
> Avant de tenter cette procédure sur un cluster de production, nous vous recommandons d’examiner les exemples de modèles et de vérifier la procédure sur un cluster de test. Par ailleurs, le cluster n’est pas disponible pendant un temps. Vous ne pouvez pas modifier en parallèle plusieurs groupes de machines virtuelles identiques déclarés comme un même NodeType. Vous devrez effectuer des opérations de déploiement séparées pour appliquer les modifications à chacun des groupes de machines virtuelles identiques.

1. Déployez le cluster initial avec deux types de nœuds et deux groupes identiques (un groupe identique par type de nœud) en utilisant ces exemples de fichiers de [modèle](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) et de [paramètres](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json).  Les deux groupes identiques ont la taille Standard D2_V2 et exécutent Windows Server 2012 R2 Datacenter.  Attendez que le cluster procède à la mise à niveau de référence.   
2. Facultatif : Déployez un exemple avec état dans le cluster.
3. Après avoir décidé de mettre à niveau les machines virtuelles du type de nœud principal, ajoutez un nouveau groupe identique au type de nœud principal en utilisant ces exemples de fichiers de [modèle](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) et de [paramètres](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) afin que le type de nœud principal compte deux groupes identiques.  Les services système et les logiciels des utilisateurs peuvent migrer entre les machines virtuelles dans les deux groupes identiques.  Les machines virtuelles du nouveau groupe identique ont la taille Standard D4_V2 et exécutent Windows Server Datacenter 2016 avec Conteneurs.  Un nouvel équilibreur de charge et une adresse IP publique sont également ajoutés avec le nouveau groupe identique.  
    Pour trouver le nouveau groupe identique dans le modèle, recherchez la ressource « Microsoft.Compute/virtualMachineScaleSets » nommée par le paramètre *vmNodeType2Name*.  Le nouveau groupe identique est ajouté au type de nœud principal à l’aide des propriétés->virtualMachineProfile->extensionProfile->extensions->propriétés->paramètres->paramètre nodeTypeRef.
4. Vérifiez l’intégrité du cluster et de l’ensemble des nœuds.
5. Désactivez les nœuds dans l’ancien groupe identique du type de nœud principal avec l’intention de les supprimer. Vous pouvez les désactiver tous en même temps, auquel cas les opérations sont mises en file d’attente. Attendez que tous les nœuds soient désactivés, ce qui peut prendre un certain temps.  Au fur et à mesure que les anciens nœuds du type de nœud sont désactivés, les services système et les nœuds initiaux (seed) migrent vers les machines virtuelles du nouveau groupe identique du type de nœud principal.
6. Supprimez l’ancien groupe identique du type de nœud principal. (Une fois les nœuds désactivés comme indiqué à l’étape 5, dans le panneau du groupe de machines virtuelles identiques du portail Azure, désallouez les nœuds de l’ancien type de nœud l’un après l’autre.)
7. Supprimez l’équilibreur de charge associé à l’ancien groupe identique. Le cluster n’est pas disponible pendant que la nouvelle adresse IP publique et l’équilibreur de charge sont configurés pour le nouveau groupe identique.  
8. Stockez les paramètres DNS de l’adresse IP publique associée à l’ancien groupe identique du type de nœud principal dans une variable et supprimez cette adresse IP publique.
9. Remplacez les paramètres DNS de l’adresse IP publique associée au nouveau groupe identique du type de nœud principal par les paramètres DNS de l’adresse IP publique supprimée.  Le cluster est à nouveau accessible.
10. Supprimez l’état des nœuds du cluster.  Si le niveau de durabilité de l’ancien groupe identique était Argent ou Or, cette étape est effectuée automatiquement par le système.
11. Si vous avez déployé l’application avec état dans une étape précédente, vérifiez que l’application fonctionne.

## <a name="set-up-the-test-cluster"></a>Configurer le cluster de test

Commencez par télécharger les deux ensembles de fichiers dont nous aurons besoin pour ce tutoriel : le [modèle]() et les [paramètres]() « avant » ainsi que le [modèle]() et les [paramètres]() « après ».

Ensuite, connectez-vous à votre compte Azure.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Ce tutoriel décrit le scénario de création d’un certificat autosigné. Pour utiliser un certificat existant à partir de Azure Key Vault, ignorez l’étape ci-dessous et reproduisez les étapes pour [déployer le cluster à l’aide d’un certificat existant](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster).

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Générer un certificat autosigné et déployer le cluster

Tout d’abord, attribuez les variables dont vous aurez besoin pour le déploiement du cluster Service Fabric. Ajustez les valeurs de `resourceGroupName`, `certSubjectName`, `parameterFilePath` et `templateFilePath` pour votre compte et votre environnement spécifiques :

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> Assurez-vous que l’emplacement `certOutputFolder` existe sur votre ordinateur local avant d’exécuter la commande pour déployer un nouveau cluster Service Fabric.

Ensuite, ouvrez le fichier *Deploy-2NodeTypes-2ScaleSets.parameters.json*, ajustez les valeurs de `clusterName` et `dnsName` pour qu’elles correspondent aux valeurs dynamiques que vous avez définies dans PowerShell et enregistrez vos modifications.

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

L’opération retournera l’empreinte de certificat, que vous utiliserez pour vous connecter au nouveau cluster et vérifier son état d’intégrité.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Se connecter au nouveau cluster et vérifier l’état d’intégrité

Connectez-vous au cluster et assurez-vous que tous ses nœuds sont sains (en remplaçant les variables `clusterName` et `thumb` pour votre cluster) :

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

Nous sommes prêts à commencer la procédure de mise à niveau.

## <a name="upgrade-the-primary-node-type-vms"></a>Mettre à niveau les machines virtuelles du type de nœud principal

Après avoir décidé de mettre à niveau les machines virtuelles du type de nœud principal, ajoutez un nouveau groupe identique au type de nœud principal. Ainsi, le type de nœud principal compte deux groupes identiques. Un échantillon de fichiers [modèle](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) et de [paramètres](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) a été fourni pour montrer les modifications nécessaires. Les machines virtuelles du nouveau groupe identique ont la taille Standard D4_V2 et exécutent Windows Server Datacenter 2016 avec Containers. Un nouvel équilibreur de charge et une adresse IP publique sont également ajoutés avec le nouveau groupe identique. 

Pour trouver le nouveau groupe identique dans le modèle, recherchez la ressource « Microsoft.Compute/virtualMachineScaleSets » nommée par le paramètre vmNodeType2Name. Le nouveau groupe identique est ajouté au type de nœud principal à l’aide des propriétés->virtualMachineProfile->extensionProfile->extensions->propriétés->paramètres->paramètre nodeTypeRef.

### <a name="deploy-the-updated-template"></a>Déployer le modèle mis à jour

Ajustez `parameterFilePath` et `templateFilePath` selon les besoins, puis exécutez la commande suivante :

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Une fois le déploiement terminé, vérifiez à nouveau l’intégrité du cluster et assurez-vous que tous les nœuds (sur l’original et sur le nouveau groupe identique) sont sains.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Migrez les nœuds vers le nouveau groupe identique

Nous sommes maintenant prêts à désactiver les nœuds du groupe identique d’origine. À mesure que ces nœuds sont désactivés, les services système et les nœuds initiaux (seed) migrent vers les machines virtuelles du nouveau groupe identique, car ils sont également marqués comme type de nœud principal.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

Utilisez Service Fabric Explorer pour surveiller la migration des nœuds initiaux vers le nouveau groupe identique et la progression des nœuds dans le groupe identique d’origine de l’état *Désactivation* à *Désactivé*.

> [!NOTE]
> L’exécution de l’opération de désactivation sur tous les nœuds du groupe identique d’origine peut prendre un certain temps. Pour garantir la cohérence des données, un seul nœud initial peut changer à la fois. Chaque modification du nœud initial requiert une mise à jour du cluster ; par conséquent, le remplacement d’un nœud initial nécessite deux mises à niveau du cluster (une pour l’ajout et l’autre pour la suppression de nœuds). La mise à niveau des cinq nœuds initiaux dans cet exemple de scénario entraîne la mise à niveau de dix clusters.

## <a name="remove-the-original-scale-set"></a>Supprimer le groupe identique d’origine

Une fois l’opération de désactivation terminée, supprimez le groupe identique.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Dans Service Fabric Explorer, les nœuds supprimés (et donc l’*état d’intégrité du cluster*) s’affichent désormais à l’état *Erreur*.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>Supprimer l’ancien équilibreur de charge et mettre à jour les paramètres DNS

Nous pouvons désormais supprimer les ressources associées à l’ancien type de nœud principal, en commençant par l’équilibreur de charge et l’ancienne adresse IP publique. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Ensuite, nous mettons à jour les paramètres DNS de la nouvelle adresse IP publique pour reproduire les paramètres de l’adresse IP publique de l’ancien type de nœud principal.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

Vérifiez à nouveau l’intégrité du cluster

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Enfin, supprimez l’état du nœud pour chacun des nœuds associés. Si le niveau de durabilité de l’ancien groupe identique était Argent ou Or, cela se fera automatiquement.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

Le type de nœud principal du cluster a été mis à niveau. Vérifiez que toutes les applications déployées fonctionnent correctement et que le cluster est sain.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [ajouter un type de nœud à un cluster](virtual-machine-scale-set-scale-node-type-scale-out.md).
* Découvrez-en plus sur l’[extensibilité des applications](service-fabric-concepts-scalability.md).
* [Procédez à une montée et une descente en puissance d’un cluster Azure](service-fabric-tutorial-scale-cluster.md).
* [Mettez à l’échelle un cluster Azure par programmation](service-fabric-cluster-programmatic-scaling.md), à l’aide du kit de développement logiciel de calcul Azure.
* [Augmentez ou diminuez la taille des instances d’un cluster autonome](service-fabric-cluster-windows-server-add-remove-nodes.md).

