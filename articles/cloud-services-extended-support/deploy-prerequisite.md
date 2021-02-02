---
title: Prérequis du déploiement d’Azure Cloud Services (support étendu)
description: Prérequis du déploiement d’Azure Cloud Services (support étendu)
ms.topic: article
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 42416b1fc06ff59a68a6f5044b8bcca5dc7f035f
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880184"
---
# <a name="prerequisites-for-deploying-azure-cloud-services-extended-support"></a>Prérequis du déploiement d’Azure Cloud Services (support étendu)

> [!IMPORTANT]
> Azure Cloud Services (support étendu) est actuellement en préversion publique.
> Cette préversion est fournie sans contrat de niveau de service et n’est pas recommandée pour les charges de travail de production. Certaines fonctionnalités peuvent être limitées ou non prises en charge. Pour plus d’informations, consultez [Conditions d’Utilisation Supplémentaires relatives aux Évaluations Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pour garantir la réussite d’un déploiement d’Azure Cloud Services (support étendu), passez en revue les étapes ci-dessous et effectuez chacune d’entre elles avant de tenter tout déploiement. 

## <a name="register-the-cloudservices-feature"></a>Inscrire la fonctionnalité CloudServices
Inscrivez la fonctionnalité pour votre abonnement. L’inscription peut prendre plusieurs minutes. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Vérifiez l’état de l’inscription à l’aide de ce qui suit :  
```powershell
Get-AzProviderFeature 

#Sample output
FeatureName               ProviderName      RegistrationState
CloudServices           Microsoft.Compute    Registered
```

## <a name="required-service-configuration-cscfg-file-updates"></a>Mises à jour requises du fichier de configuration de service (.cscfg)

### <a name="1-virtual-network"></a>1) Réseau virtuel
Les déploiements d’Azure Cloud Services (support étendu) doivent être dans un réseau virtuel. Vous pouvez créer un réseau virtuel via [Portail Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal), [PowerShell](https://docs.microsoft.com/azure/virtual-network/quick-create-powershell), [Azure CLI](https://docs.microsoft.com/azure/virtual-network/quick-create-cli) ou un [modèle ARM](https://docs.microsoft.com/azure/virtual-network/quick-create-template). Le réseau virtuel et les sous-réseaux doivent également être référencés dans le fichier de configuration de service (.cscfg) sous la section [NetworkConfiguration](schema-cscfg-networkconfiguration.md). 

Pour les réseaux virtuels appartenant au même groupe de ressources que le service cloud, le fait de référencer uniquement le nom du réseau virtuel dans le fichier de configuration de service (.cscfg) suffit. Si le réseau virtuel et le service cloud se trouvent dans deux groupes de ressources différents, l’ID Azure Resource Manager complet du réseau virtuel doit être spécifié dans le fichier de configuration de service (.cscfg).
 
#### <a name="virtual-network-located-in-same-resource-group"></a>Réseau virtuel situé dans le même groupe de ressources
```xml
<VirtualNetworkSite name="<vnet-name>"/> 
  <AddressAssignments> 
    <InstanceAddress roleName="<role-name>"> 
     <Subnets> 
       <Subnet name="<subnet-name>"/> 
     </Subnets> 
    </InstanceAddress> 
```

#### <a name="virtual-network-located-in-different-resource-group"></a>Réseau virtuel situé dans un groupe de ressources différent
```xml
<VirtualNetworkSite name="/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/Microsoft.Network/virtualNetworks/<vnet-name>"/> 
   <AddressAssignments> 
     <InstanceAddress roleName="<role-name>"> 
       <Subnets> 
        <Subnet name="<subnet-name>"/> 
       </Subnets> 
     </InstanceAddress> 
```
### <a name="2-remove-the-old-plugins"></a>2) Supprimer les anciens plug-ins

Supprimez les anciens paramètres de Bureau à distance dans le fichier de configuration de service (.cscfg).  

```xml
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.Enabled" value="true" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountUsername" value="gachandw" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountEncryptedPassword" value="XXXX" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteAccess.AccountExpiration" value="2021-12-17T23:59:59.0000000+05:30" /> 
<Setting name="Microsoft.WindowsAzure.Plugins.RemoteForwarder.Enabled" value="true" /> 
```

## <a name="required-service-definition-file-csdef-updates"></a>Mises à jour requises du fichier de définition de service (.csdef)

### <a name="1-virtual-machine-sizes"></a>1) Tailles de machines virtuelles
Les tailles suivantes sont déconseillées dans Azure Resource Manager. Toutefois, si vous souhaitez continuer à les utiliser, mettez à jour le nom de `vmsize` selon la convention d’affectation de noms Azure Resource Manager associée.  

| Nom de la taille précédente | Nom de la taille mis à jour | 
|---|---|
| Très petite | Standard_A0 | 
| Petite | Standard_A1 |
| Moyenne | Standard_A2 | 
| Grande | Standard_A3 | 
| Très grande | Standard_A4 | 
| A5 | Standard_A5 | 
| A6 | Standard_A6 | 
| A7 | Standard_A7 |  
| A8 | Standard_A8 | 
| A9 | Standard_A9 |
| A10 | Standard_A10 | 
| A11 | Standard_A11 | 
| MSODSG5 | Standard_MSODSG5 | 

 Par exemple, `<WorkerRole name="WorkerRole1" vmsize="Medium"` deviendrait `<WorkerRole name="WorkerRole1" vmsize="Standard_A2"`.
 
> [!NOTE]
> Pour récupérer la liste des tailles disponibles, consultez [SKU de ressources – Liste](https://docs.microsoft.com/rest/api/compute/resourceskus/list) et appliquez les filtres suivants : <br>
`ResourceType = virtualMachines ` <br>
`VMDeploymentTypes = PaaS `


### <a name="2-remove-old-remote-desktop-plugins"></a>2) Supprimer les anciens plug-ins de Bureau à distance
Les modules de Bureau à distance et tous les certificats associés doivent être supprimés du fichier de définition de service (.csdef) pour les déploiements qui utilisaient les anciens plug-ins de Bureau à distance. 

```xml
<Imports> 
<Import moduleName="RemoteAccess" /> 
<Import moduleName="RemoteForwarder" /> 
</Imports> 
```

## <a name="key-vault-creation"></a>Création d’un coffre de clés 

Key Vault est utilisé pour stocker les certificats associés à Azure Cloud Services (support étendu). Ajoutez les certificats à Key Vault, puis référencez leurs empreintes dans le fichier de configuration de service. Vous devez également octroyer à Key Vault les autorisations appropriées afin que la ressource Azure Cloud Services (support étendu) puisse récupérer les certificats stockés sous forme de secrets à partir de Key Vault. Vous pouvez créer un coffre de clés par le biais du [portail Azure](https://docs.microsoft.com/azure/key-vault/general/quick-create-portal) et de [PowerShell](https://docs.microsoft.com/azure/key-vault/general/quick-create-powershell). Le coffre de clés doit être créé dans la même région et le même abonnement que le service cloud. Pour plus d’informations, consultez [Utiliser des certificats avec Azure Cloud Services (support étendu)](certificates-and-key-vault.md).

## <a name="next-steps"></a>Étapes suivantes 
- Consultez les [prérequis du déploiement](deploy-prerequisite.md) d’Azure Cloud Services (support étendu).
- Déployez une instance d’Azure Cloud Services (support étendu) avec le [portail Azure](deploy-portal.md), [PowerShell](deploy-powershell.md), un [modèle](deploy-template.md) ou [Visual Studio](deploy-visual-studio.md).
- Consultez la [foire aux questions (FAQ)](faq.md) relative à Azure Cloud Services (support étendu).
- Rendez-vous sur le [référentiel d’exemples d’Azure Cloud Services (support étendu)](https://github.com/Azure-Samples/cloud-services-extended-support).