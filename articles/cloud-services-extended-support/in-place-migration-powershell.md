---
title: Migrer vers Azure Cloud Services (support étendu) à l’aide de PowerShell
description: Comment opérer une migration d’Azure Cloud Services (classique) vers Azure Cloud Services (support étendu) à l’aide de PowerShell
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
author: hirenshah1
ms.author: hirshah
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f7626b2af5f5d8c12a29602e62b38ebef2abfd48
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113434279"
---
# <a name="migrate-to-azure-cloud-services-extended-support-using-powershell"></a>Migrer vers Azure Cloud Services (support étendu) à l’aide de PowerShell

Ces étapes montrent comment utiliser des commandes Azure PowerShell opérer une migration d’[Azure Cloud Services (classique)](../cloud-services/cloud-services-choose-me.md) vers [Azure Cloud Services (support étendu)](overview.md).

## <a name="1-plan-for-migration"></a>1) Planifier la migration
La planification est l’étape la plus importante d’une expérience de migration réussie. Consultez [Vue d’ensemble d’Azure Cloud Services (support étendu)](overview.md) et [Planification de la migration des ressources IaaS d’Azure Classic vers Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-plan.md) avant de commencer toute étapes de migration. 

## <a name="2-install-the-latest-version-of-powershell"></a>2) Installer la dernière version de PowerShell
Il existe deux options principales pour l’installation d’Azure PowerShell : [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) ou [Web Platform Installer (WebPI)](https://aka.ms/webpi-azps). WebPI reçoit des mises à jour mensuelles. PowerShell Gallery reçoit des mises à jour en continu. Cet article est basé sur Azure PowerShell version 2.1.0.

Pour connaître la procédure d’installation, consultez l’article [Installation et configuration d’Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps?preserve-view=true&view=azuresmps-4.0.0).

## <a name="3-ensure-admin-permissions"></a>3) Veiller aux autorisations d’administrateur
Pour effectuer cette migration, vous devez être ajouté en tant que coadministrateur de l’abonnement dans le [Portail Azure](https://portal.azure.com).

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans le menu **Hub**, sélectionnez **Abonnement**. Si vous ne le voyez pas, sélectionnez **Tous les services**.
3. Recherchez l’entrée d’abonnement appropriée, puis examinez le champ **MON RÔLE**. Pour un coadministrateur, la valeur doit être *Administrateur de compte*.

Si vous n'êtes pas en mesure d'ajouter un coadministrateur, contactez un administrateur de service ou un coadministrateur de l'abonnement afin qu'il vous ajoute.

## <a name="4-register-the-classic-provider-and-cloudservice-feature"></a>4) Inscrire le fournisseur classique et la fonctionnalité CloudService
Commencez par démarrer une invite de commandes PowerShell. Pour la migration, configurez votre environnement à la fois pour l’environnement classique et pour Resource Manager.

Connectez-vous à votre compte pour le modèle Resource Manager.

```powershell
Connect-AzAccount
```

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

```powershell
Get-AzSubscription | Sort Name | Select Name
```

Définissez votre abonnement Azure pour la session active. Cet exemple définit le nom d’abonnement par défaut sur **My Azure Subscription**. Remplacez l’exemple de nom d’abonnement par le vôtre.

```powershell
Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

Inscrivez-vous auprès du fournisseur de ressources de migration à l’aide de la commande suivante :

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```
> [!NOTE]
> L’inscription constitue une étape unique, mais vous devez la faire une fois avant de tenter la migration. Sans vous inscrire, vous verrez le message d’erreur suivant :
>
> *BadRequest : L'abonnement n'est pas inscrit pour la migration.*

Inscrivez la fonctionnalité CloudServices pour votre abonnement. Les inscriptions peuvent prendre plusieurs minutes. 

```powershell
Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Patientez cinq minutes le temps que l’inscription se termine. 

Vérifiez l’état de l’approbation de fournisseur classique à l’aide de la commande suivante :

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Vérifiez l’état de l’inscription à l’aide de ce qui suit :  
```powershell
Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute
```

Assurez-vous que RegistrationState est `Registered` pour les deux avant de continuer.

Avant de basculer vers le modèle de déploiement Classic, vérifiez que vous avez un quota suffisant d’UC Azure Resource Manager dans la région Azure de votre déploiement ou réseau virtuel actuel. Vous pouvez utiliser la commande PowerShell suivante pour vérifier la quantité de processeurs virtuels dont vous disposez actuellement dans Azure Resource Manager. Pour en savoir plus sur les quotas de processeurs virtuels, consultez [Limites et Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Cet exemple vérifie la disponibilité dans la région **USA Ouest**. Remplacez l’exemple de nom de région par le vôtre.

```powershell
Get-AzVMUsage -Location "West US"
```

À présent, connectez-vous à votre compte pour le modèle de déploiement classique.

```powershell
Add-AzureAccount
```

Obtenez tous les abonnements disponibles à l’aide de la commande suivante :

```powershell
Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Définissez votre abonnement Azure pour la session active. Cet exemple définit l’abonnement par défaut sur **My Azure Subscription**. Remplacez l’exemple de nom d’abonnement par le vôtre.

```powershell
Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="5-migrate-your-cloud-services"></a>5) Migrer vos services cloud 
Avant de commencer la migration, familiarisez-vous avec le déroulement de la [procédure de migration](./in-place-migration-overview.md#migration-steps) et le rôle de chaque étape. 

* [Migrer un service cloud ne figurant pas dans un réseau virtuel](#51-option-1---migrate-a-cloud-service-not-in-a-virtual-network)
* [Migrer un service cloud figurant dans un réseau virtuel](#51-option-2---migrate-a-cloud-service-in-a-virtual-network)

> [!NOTE]
> Toutes les opérations décrites ici sont idempotentes. Si vous rencontrez un problème autre qu’une fonctionnalité non prise en charge ou qu’une erreur de configuration, nous vous recommandons de réexécuter la procédure de préparation, d’abandon ou de validation. La plateforme réessaie alors d’exécuter l’action.


### <a name="51-option-1---migrate-a-cloud-service-not-in-a-virtual-network"></a>5.1) Option 1 : Migrer un service cloud ne figurant pas dans un réseau virtuel
Récupérez la liste des services cloud à l’aide de la commande suivante. Sélectionnez ensuite le service cloud que vous souhaitez migrer.

```powershell
Get-AzureService | ft Servicename
```

Obtenez le nom du déploiement du service cloud. Dans cet exemple, le nom du service est **Mon service**. Remplacez l’exemple de nom de service avec votre propre nom de service.

```powershell
$serviceName = "My Service"
$deployment = Get-AzureDeployment -ServiceName $serviceName
$deploymentName = $deployment.DeploymentName
```

Tout d’abord, vérifiez que vous pouvez migrer le service cloud à l’aide des commandes suivantes. Elles affichent les éventuelles erreurs qui bloquent la migration. 

```powershell
$validate = Move-AzureService -Validate -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
$validate.ValidationMessages
 ```

Si la validation se déroule sans erreurs ou avec de simples avertissements, vous pouvez passer à l’étape de préparation. 

```powershell
Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

Vérifiez la configuration du service cloud (support étendu) préparé avec Azure PowerShell ou le Portail Azure. Si vous ne voulez pas effectuer la migration tout de suite et souhaitez revenir à l’ancien état, abandonnez la migration.
```powershell
Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```
Si vous voulez effectuer la migration, validez-la.

```powershell
Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName -CreateNewVirtualNetwork
```

### <a name="51-option-2---migrate-a-cloud-service-in-a-virtual-network"></a>5.1) Option 2 : Migrer un service cloud figurant dans un réseau virtuel

Pour migrer un service cloud figurant dans un réseau virtuel, vous migrez le réseau virtuel. Le service cloud migre automatiquement avec le réseau virtuel.

> [!NOTE]
> Le nom du réseau virtuel peut différer de ce qui est affiché dans le nouveau portail. Le nouveau Portail Azure affiche le nom sous la forme `[vnet-name]`, mais le nom réel du réseau virtuel est du type `Group [resource-group-name] [vnet-name]`. Avant de migrer, recherchez le nom réel du réseau virtuel à l’aide de la commande `Get-AzureVnetSite | Select -Property Name`, ou affichez-le dans l’ancien Portail Azure. 

Cet exemple définit le nom de réseau virtuel sur **myVnet**. Remplacez l’exemple de nom de réseau virtuel par le vôtre.

```powershell
$vnetName = "myVnet"
```

La première étape consiste à vérifier si vous pouvez migrer le réseau virtuel à l’aide de la commande suivante :

```powershell
Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

La commande suivante affiche les avertissements et erreurs qui bloquent la migration. Si la validation se déroule correctement, vous pouvez passer à l’étape de la préparation suivante :

```powershell
Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Vérifiez la configuration du service cloud (support étendu) préparé avec Azure PowerShell ou le Portail Azure. Si vous n’êtes pas prêt pour la migration et que vous souhaitez revenir à l’ancien état, utilisez la commande suivante :

```powershell
Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Si la configuration préparée semble correcte, vous pouvez continuer et valider les ressources à l’aide de la commande suivante :

```powershell
Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```


## <a name="next-steps"></a>Étapes suivantes

Consultez la section [Modifications après la migration](post-migration-changes.md) pour voir les modifications apportées aux fichiers de déploiement, à l’automatisation et à d’autres attributs de votre nouveau déploiement d’Azure Cloud Services (support étendu).
