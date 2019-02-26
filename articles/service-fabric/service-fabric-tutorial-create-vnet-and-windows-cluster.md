---
title: Créer un cluster Windows Service Fabric dans Azure | Microsoft Docs
description: Dans ce didacticiel, vous découvrez comment déployer un cluster Windows Service Fabric dans un réseau virtuel Azure et un groupe de sécurité réseau à l’aide de PowerShell.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/19/2019
ms.author: ryanwi
ms.custom: mvc
ms.openlocfilehash: 590e1e5853ccf4a525477f194c78f1fd8ce679ed
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453067"
---
# <a name="tutorial-deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Tutoriel : Déployer un cluster Windows Service Fabric dans un réseau virtuel Azure

Ce tutoriel est la première partie d’une série d’étapes. Vous allez apprendre à déployer un cluster Service Fabric exécutant Windows dans un [réseau virtuel Azure](../virtual-network/virtual-networks-overview.md) et un [groupe de sécurité réseau virtuel](../virtual-network/virtual-networks-nsg.md) à l’aide de PowerShell et d’un modèle. Lorsque vous avez terminé, vous disposez d’un cluster en cours d’exécution dans le cloud sur lequel vous pouvez déployer des applications.  Pour créer un cluster Linux à l’aide de l’interface de ligne de commande Azure, consultez la page [Créer un cluster Linux sécurisé sur Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Ce didacticiel décrit un scénario de production.  Si vous souhaitez créer rapidement un petit cluster à des fins de test, consultez [Créer un cluster de test](./scripts/service-fabric-powershell-create-secure-cluster-cert.md).

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de PowerShell
> * Créer un coffre de clés et charger un certificat
> * Configurer l’authentification Azure Active Directory
> * Créer un cluster Service Fabric sécurisé dans Azure PowerShell
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * Créer un cluster sécurisé sur Azure
> * [Mettre à l’échelle un cluster](service-fabric-tutorial-scale-cluster.md)
> * [Mettre à niveau le runtime d’un cluster](service-fabric-tutorial-upgrade-cluster.md)
> * [Supprimer un cluster](service-fabric-tutorial-delete-cluster.md)

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Installez le [Kit de développement logiciel (SDK) Service Fabric et le module PowerShell](service-fabric-get-started.md).
* Installez le [module Azure PowerShell, version 4.1 ou ultérieure](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
* Passez en revue les concepts clés des [clusters Azure](service-fabric-azure-clusters-overview.md).

Les procédures suivantes créent un cluster Service Fabric à sept nœuds. Pour calculer le coût lié à l’exécution d’un cluster Service Fabric dans Azure, utilisez la [calculatrice de prix Azure](https://azure.microsoft.com/pricing/calculator/).

## <a name="download-and-explore-the-template"></a>Télécharger et explorer le modèle

Téléchargez les fichiers de modèle Resource Manager suivants :

* [azuredeploy.json][template]
* [azuredeploy.parameters.json][parameters]

Ce modèle déploie un cluster sécurisé de sept machines virtuelles et trois types de nœuds dans un réseau virtuel et un groupe de sécurité réseau.  D’autres exemples de modèles sont disponibles sur [GitHub](https://github.com/Azure-Samples/service-fabric-cluster-templates).  Le modèle [azuredeploy.json][template] déploie un certain nombre de ressources, notamment celles ci-dessous.

### <a name="service-fabric-cluster"></a>Cluster Service Fabric

Dans la ressource **Microsoft.servicefabric/clusters**, un cluster Windows est configuré avec les caractéristiques suivantes :

* Trois types de nœuds
* Cinq nœuds dans le type de nœud principal (configurable dans les paramètres du modèle) et un nœud de chacun des deux autres types
* Système d’exploitation : Windows Server 2016 Datacenter avec Containers (configurables dans les paramètres du modèle)
* certificat sécurisé (configurable dans les paramètres du modèle)
* [proxy inverse](service-fabric-reverseproxy.md) activé
* [service DNS](service-fabric-dnsservice.md) activé
* [niveau de durabilité](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster) Bronze (configurable dans les paramètres du modèle)
* [niveau de fiabilité](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) Silver (configurable dans les paramètres du modèle)
* point de terminaison de connexion client : 19000 (configurable dans les paramètres du modèle)
* point de terminaison de passerelle HTTP : 19080 (configurable dans les paramètres du modèle)

### <a name="azure-load-balancer"></a>Équilibrage de charge Azure

Dans la ressource **Microsoft.Network/loadBalancers**, un équilibreur de charge est configuré et des sondes et règles sont configurées pour les ports suivants :

* point de terminaison de connexion client : 19000
* point de terminaison de passerelle HTTP : 19080
* port de l’application : 80
* port de l’application : 443
* proxy inverse de Service Fabric : 19081

Si d’autres ports de l’application sont nécessaires, vous devez ajuster les ressources **Microsoft.Network/loadBalancers** et **Microsoft.Network/networkSecurityGroups** pour autoriser le trafic entrant.

### <a name="virtual-network-subnet-and-network-security-group"></a>Réseau virtuel, sous-réseau et groupe de sécurité réseau

Les noms du réseau virtuel, du sous-réseau et du groupe de sécurité réseau sont déclarés dans les paramètres du modèle.  Les espaces d’adressage du réseau virtuel et du sous-réseau sont également déclarés dans les paramètres de modèle et configurés dans la ressource **Microsoft.Network/virtualNetworks** :

* Espace d’adressage du réseau virtuel : 172.16.0.0/20
* Espace d’adressage du sous-réseau Service Fabric : 172.16.2.0/23

Les règles de trafic entrant suivantes sont activées dans la ressource **Microsoft.Network/networkSecurityGroups**. Vous pouvez modifier les valeurs de port en modifiant les variables de modèle.

* ClientConnectionEndpoint (TCP) : 19000
* HttpGatewayEndpoint (HTTP/TCP) : 19080
* SMB : 445
* Internodecommunication : 1025, 1026, 1027
* Plage de ports éphémères : 49152 à 65534 (256 ports min. nécessaires)
* Ports pour l’utilisation de l’application : 80 et 443
* Plage de ports de l’application : 49152 à 65534 (utilisés pour les communications entre les services ; ne sont pas ouverts sur l’équilibreur de charge)
* Bloquer tous les autres ports

Si d’autres ports de l’application sont nécessaires, vous devez ajuster les ressources **Microsoft.Network/loadBalancers** et **Microsoft.Network/networkSecurityGroups** pour autoriser le trafic entrant.

### <a name="windows-defender"></a>Windows Defender
Par défaut, l’[antivirus Windows Defender](/windows/security/threat-protection/windows-defender-antivirus/windows-defender-antivirus-on-windows-server-2016) est installé sur Windows Server 2016 et fonctionne correctement. L’interface utilisateur est installée par défaut sur certaines références SKU, mais elle n’est pas obligatoire.  Pour tous les types de nœuds ou groupes de machines virtuelles identiques déclarés dans le modèle, l’[extension Azure VM Antimalware](/azure/virtual-machines/extensions/iaas-antimalware-windows) est utilisée pour exclure les répertoires et les processus Service Fabric :

```json
{
"name": "[concat('VMIaaSAntimalware','_vmNodeType0Name')]",
"properties": {
        "publisher": "Microsoft.Azure.Security",
        "type": "IaaSAntimalware",
        "typeHandlerVersion": "1.5",
        "settings": {
        "AntimalwareEnabled": "true",
        "Exclusions": {
                "Paths": "D:\\SvcFab;D:\\SvcFab\\Log;C:\\Program Files\\Microsoft Service Fabric",
                "Processes": "Fabric.exe;FabricHost.exe;FabricInstallerService.exe;FabricSetup.exe;FabricDeployer.exe;ImageBuilder.exe;FabricGateway.exe;FabricDCA.exe;FabricFAS.exe;FabricUOS.exe;FabricRM.exe;FileStoreService.exe"
        },
        "RealtimeProtectionEnabled": "true",
        "ScheduledScanSettings": {
                "isEnabled": "true",
                "scanType": "Quick",
                "day": "7",
                "time": "120"
        }
        },
        "protectedSettings": null
}
}
```

## <a name="set-template-parameters"></a>Définir les paramètres de modèle

Le fichier de paramètres [azuredeploy.parameters.json][parameters] déclare de nombreuses valeurs servant à déployer le cluster et les ressources associées. Voici certains des paramètres que vous devrez peut-être modifier pour votre déploiement :

|Paramètre|Exemple de valeur|Notes|
|---|---||
|adminUsername|vmadmin| Nom d’utilisateur administrateur pour les machines virtuelles de cluster.[Exigences de nom d’utilisateur pour la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-username-requirements-when-creating-a-vm) |
|adminPassword|Password#1234| Mot de passe d’administrateur pour les machines virtuelles de cluster. [Conditions requises pour les mots de passe de la machine virtuelle](https://docs.microsoft.com/azure/virtual-machines/windows/faq#what-are-the-password-requirements-when-creating-a-vm)|
|clusterName|mysfcluster123| Nom du cluster. Peut contenir seulement des lettres et des chiffres. Le nom peut contenir entre 3 et 23 caractères.|
|location|southcentralus| Emplacement du cluster. |
|certificateThumbprint|| <p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat.</p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez la valeur d’empreinte du certificat SHA-1. Par exemple, « 6190390162C988701DB5676EB81083EA608DCCF3 »</p>. |
|certificateUrlValue|| <p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat. </p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez l’URL du certificat. par exemple, « https://mykeyvault.vault.azure.net:443/secrets/mycertificate/02bea722c9ef4009a76c5052bcbf8346 ».</p>|
|sourceVaultValue||<p>La valeur doit être vide si vous créez un certificat auto-signé ou si vous fournissez un fichier de certificat.</p><p>Pour utiliser un certificat existant déjà chargé dans un coffre de clés, renseignez la valeur de coffre source. Par exemple, « /subscriptions/333cc2c84-12fa-5778-bd71-c71c07bf873f/resourceGroups/MyTestRG/providers/Microsoft.KeyVault/vaults/MYKEYVAULT ».</p>|

## <a name="set-up-azure-active-directory-client-authentication"></a>Configurer l’authentification client Azure Active Directory
Pour les clusters Service Fabric déployés dans un réseau public hébergé dans Azure, les recommandations concernant l’authentification mutuelle client à nœud sont les suivantes :
* Utiliser Azure Active Directory pour l’identité client
* Utiliser un certificat pour l’identité du serveur et le chiffrement SSL de la communication HTTP

La configuration d’Azure AD pour authentifier les clients d’un cluster Service Fabric doit être effectuée avant de [créer le cluster](#createvaultandcert).  Azure AD permet aux organisation (appelées locataires) de gérer l’accès utilisateur aux applications. 

Un cluster Service Fabric offre plusieurs points d’entrée pour ses fonctionnalités de gestion, notamment les outils web [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)et [Visual Studio](service-fabric-manage-application-in-visual-studio.md). Par conséquent, vous allez créer deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native.  Après avoir créé les applications, vous devez affecter les utilisateurs aux rôles en lecture seule et administrateur.

> [!NOTE]
> Vous devez exécuter les étapes suivantes avant de créer le cluster. Étant donné que les scripts attendent des noms de cluster et des points de terminaison, ces valeurs doivent être des valeurs planifiées et non celles que vous avez déjà créées.

Dans cet article, nous partons du principe que vous avez déjà créé un locataire. Si ce n’est pas le cas, commencez par lire [Guide pratique pour obtenir un locataire Azure Active Directory](../active-directory/develop/quickstart-create-new-tenant.md).

Pour simplifier certaines des étapes impliquées dans la configuration d’Azure AD avec un cluster Service Fabric, nous avons créé un ensemble de scripts Windows PowerShell. [Téléchargez les scripts](https://github.com/robotechredmond/Azure-PowerShell-Snippets/tree/master/MicrosoftAzureServiceFabric-AADHelpers/AADTool) sur votre ordinateur.

### <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Créer des applications Azure AD et attribuer des rôles aux utilisateurs
Créez deux applications Azure AD pour contrôler l’accès au cluster : une application web et une application native. Une fois que vous avez créé les applications pour représenter votre cluster, affectez les utilisateurs aux [rôles pris en charge par Service Fabric](service-fabric-cluster-security-roles.md) : lecture seule et administrateur.

Exécutez `SetupApplications.ps1`, puis entrez l’ID du locataire, le nom du cluster et l’URL de réponse de l’application web en tant que paramètres.  Spécifiez également les noms d’utilisateur et les mots de passe des utilisateurs.  Par exemple : 

```PowerShell
$Configobj = .\SetupApplications.ps1 -TenantId '<MyTenantID>' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Pour les clouds nationaux (par exemple, Azure Government, Azure Chine, Azure Allemagne), vous devez également spécifier le paramètre `-Location`.

Vous trouverez votre *ID de locataire*, ou ID de répertoire, dans le [portail Azure](https://portal.azure.com). Sélectionnez **Azure Active Directory -> Propriétés**, puis copiez l’**ID de répertoire**.

Le paramètre *ClusterName* est utilisé pour préfixer les applications Azure AD créées par le script. Il ne doit pas forcément correspondre précisément au nom du cluster. Il sert uniquement à simplifier le mappage des artefacts Azure AD au cluster Service Fabric avec lequel ils sont utilisés.

Le paramètre *WebApplicationReplyUrl* est le point de terminaison par défaut qui est retourné par Azure AD aux utilisateurs une fois qu’ils se sont connectés. Définissez ce point de terminaison en tant que point de terminaison Service Fabric Explorer pour votre cluster, qui est par défaut :

https://&lt;cluster_domain&gt;:19080/Explorer

Vous êtes invité à vous connecter à un compte qui dispose de privilèges d’administration pour le locataire Azure AD. Une fois que vous vous êtes connecté, le script crée les applications web et native pour représenter votre cluster Service Fabric. Si vous regardez les applications du locataire dans le [portail Azure](https://portal.azure.com), vous devez voir deux nouvelles entrées :

   * *ClusterName*\_Cluster
   * *ClusterName*\_Client

Comme le script imprime le code JSON exigé par le modèle Azure Resource Manager lorsque vous créez le cluster, il est préférable de garder la fenêtre PowerShell ouverte.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

### <a name="add-azure-ad-configuration-to-use-azure-ad-for-client-access"></a>Ajouter la configuration Azure AD afin d’utiliser Azure AD pour l’accès client
Dans [azuredeploy.json][template], configurez Azure AD dans la section **Microsoft.servicefabric/clusters**.  Ajoutez des paramètres pour l’ID du locataire, l’ID de l’application de cluster et l’ID de l’application cliente.  

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...

    "aadTenantId": {
      "type": "string",
      "defaultValue": "0e3d2646-78b3-4711-b8be-74a381d9890c"
    },
    "aadClusterApplicationId": {
      "type": "string",
      "defaultValue": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
    },
    "aadClientApplicationId": {
      "type": "string",
      "defaultValue": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
    }
  },

...

{
  "apiVersion": "2018-02-01",
  "type": "Microsoft.ServiceFabric/clusters",
  "name": "[parameters('clusterName')]",
  ...
  "properties": {
    ...
    "azureActiveDirectory": {
      "tenantId": "[parameters('aadTenantId')]",
      "clusterApplication": "[parameters('aadClusterApplicationId')]",
      "clientApplication": "[parameters('aadClientApplicationId')]"
    },
    ...
  }
}
```

Ajoutez des valeurs dans le fichier de paramètres [azuredeploy.parameters.json][parameters].  Par exemple : 

```json
"aadTenantId": {
"value": "0e3d2646-78b3-4711-b8be-74a381d9890c"
},
"aadClusterApplicationId": {
"value": "cb147d34-b0b9-4e77-81d6-420fef0c4180"
},
"aadClientApplicationId": {
"value": "7a8f3b37-cc40-45cc-9b8f-57b8919ea461"
}
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>

## <a name="deploy-the-virtual-network-and-cluster"></a>Déployer le réseau virtuel et le cluster

Puis, configurez la topologie de réseau et déployez le cluster Service Fabric. Le modèle Resource Manager [azuredeploy.json][template] crée un réseau virtuel (VNET), ainsi qu’un groupe de sécurité réseau (NSG) et un sous-réseau pour Service Fabric. Le modèle déploie également un cluster avec la sécurité de certificat activée.  Pour les clusters de production, utilisez un certificat délivré par une autorité de certification (AC) en tant que certificat de cluster. Un certificat auto-signé peut être utilisé pour garantir la sécurité des clusters de test.

Dans cet article, le modèle déploie un cluster qui utilise l’empreinte numérique du certificat pour identifier le certificat de cluster.  Deux certificats ne peuvent pas avoir la même empreinte numérique, ce qui complique leur gestion. Basculer un cluster déployé de l’utilisation des empreintes de certificat à l’utilisation des noms communs de certificat simplifie considérablement la gestion des certificats.  Pour savoir comment mettre à jour le cluster pour que celui-ci utilise des noms communs de certificat pour la gestion des certificats, lisez [Modifier un cluster pour qu’il passe de l’utilisation d’une empreinte de certificat à l’utilisation d’un nom commun](service-fabric-cluster-change-cert-thumbprint-to-cn.md).

### <a name="create-a-cluster-using-an-existing-certificate"></a>Créer un cluster à l’aide d’un certificat existant

Le script suivant utilise la cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) et un modèle pour déployer un nouveau cluster dans Azure. La cmdlet crée aussi un coffre de clés dans Azure et charge votre certificat.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$clustername = "mysfcluster123"  # must match the clusterName parameter in the template
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateFile $certpath
```

### <a name="create-a-cluster-using-a-new-self-signed-certificate"></a>Créer un cluster à l’aide d’un nouveau certificat auto-signé

Le script suivant utilise la cmdlet [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) et un modèle pour déployer un nouveau cluster dans Azure. L’applet de commande crée aussi un coffre de clés dans Azure, ajoute un nouveau certificat auto-signé dans le coffre de clés, puis télécharge le fichier de certificat localement.

```powershell
# Variables.
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"  # must match the location parameter in the template
$templatepath="C:\temp\cluster"

$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster123"
$vaultname = "clusterkeyvault123"
$vaultgroupname="clusterkeyvaultgroup123"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# sign in to your Azure account and select your subscription
Connect-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>

# Create a new resource group for your deployment and give it a name and a location.
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile "$templatepath\azuredeploy.json" `
-ParameterFile "$templatepath\azuredeploy.parameters.json" -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResourceGroupName $vaultgroupname -CertificateSubjectName $subname

```

## <a name="connect-to-the-secure-cluster"></a>Se connecter à un cluster sécurisé

Connectez-vous au cluster à l’aide du module Service Fabric PowerShell installé avec le Kit de développement logiciel (SDK) Service Fabric.  Tout d’abord, installez le certificat dans le magasin personnel de l’utilisateur actuel sur votre ordinateur.  Exécutez la commande PowerShell suivante :

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Vous êtes maintenant prêt à vous connecter à votre cluster sécurisé.

Le module **Service Fabric** PowerShell fournit de nombreuses cmdlets pour la gestion des services, applications et clusters Service Fabric.  Pour vous connecter au cluster sécurisé, utilisez la cmdlet [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster). Les détails du point de terminaison de connexion et de l’empreinte de certificat SHA-1 se trouvent dans la sortie de l’étape précédente.

Si vous avez précédemment configuré l’authentification du client AAD, exécutez la commande suivante : 
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
        -KeepAliveIntervalInSec 10 `
        -AzureActiveDirectory `
        -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10
```

Si vous ne l’avez pas fait, exécutez la commande suivante :
```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster123.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Vérifiez que vous êtes connecté et que le cluster est sain à l’aide de la cmdlet [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth).

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Les autres articles de cette série de didacticiels utilisent le cluster que vous venez de créer. Si vous ne passez pas immédiatement à l’article suivant, vous souhaiterez peut-être [supprimer le cluster](service-fabric-cluster-delete.md) pour éviter de subir des frais.

## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à :

> [!div class="checklist"]
> * Créer un réseau virtuel dans Azure à l’aide de PowerShell
> * Créer un coffre de clés et charger un certificat
> * Configurer l’authentification Azure Active Directory
> * Création d’un cluster Service Fabric sécurisé dans Azure à l’aide de PowerShell
> * Sécuriser le cluster avec un certificat X.509
> * Se connecter à un cluster à l’aide de PowerShell
> * Supprimer un cluster

Maintenant, passez au didacticiel suivant pour savoir comment mettre à l’échelle votre cluster.
> [!div class="nextstepaction"]
> [Mise à l’échelle d’un cluster](service-fabric-tutorial-scale-cluster.md)

[template]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
[parameters]:https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.Parameters.json
