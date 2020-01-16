---
title: Gérer les adresses IP réservées Azure (classiques) | Microsoft Docs
description: Comprendre les adresses IP réservées (classiques) et la manière de les gérer avec Azure PowerShell et Azure CLI.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 5bcbb8c6845928cf21b5b6a1c04f609305bd7983
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979524"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Adresses IP réservées (déploiement classique)

 Il existe deux catégories d’adresses IP dans Azure, les réservées et les dynamiques. Les adresses IP publiques gérées par Azure sont dynamiques par défaut. Cela signifie que l'adresse IP utilisée pour un service cloud donné (adresse IP virtuelle) ou pour accéder à une machine virtuelle ou à une instance de rôle directement (ILPIP) peut changer à tout moment, lorsque les ressources sont arrêtées ou désallouées.

Pour empêcher la modification des adresses IP, vous pouvez réserver une adresse IP. Les adresses IP réservées peuvent uniquement servir d’adresse IP virtuelle, garantissant que l'adresse IP utilisée pour le service cloud reste la même, et ce même si les ressources sont arrêtées ou désallouées. Par ailleurs, vous pouvez convertir une adresse IP dynamique existante utilisée comme adresse IP virtuelle en adresse IP réservée.

> [!IMPORTANT]
> Azure a deux modèles de déploiement différents pour créer et utiliser des ressources :  [Resource Manager et classique](../azure-resource-manager/management/deployment-models.md). Cet article traite du modèle de déploiement classique. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Découvrez comment réserver une adresse IP publique statique à l’aide du [modèle de déploiement Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Pour en savoir plus sur les adresses IP dans Azure, voir l’article [Adresses IP](virtual-network-ip-addresses-overview-classic.md).

## <a name="when-do-i-need-a-reserved-ip"></a>Quand ai-je besoin d’une adresse IP réservée ?
* **Vous souhaitez vous assurer que l'adresse IP est réservée dans votre abonnement**. Si vous souhaitez réserver une adresse IP qui reste associée à votre abonnement en toute circonstance, vous devez utiliser une adresse IP publique réservée.  
* **Vous souhaitez que votre adresse IP reste associée à votre service cloud, même lorsque les machines virtuelles sont arrêtées ou désallouées**. Si vous souhaitez que votre service soit accessible à l'aide d'une adresse IP qui ne change pas, même lorsque les machines virtuelles dans le service cloud sont arrêtées ou désallouées.
* **Vous souhaitez vous assurer que le trafic provenant d'Azure utilise une adresse IP prévisible**. Il se peut que la configuration de votre pare-feu local autorise uniquement le trafic provenant d'adresses IP spécifiques. En réservant une adresse IP, vous connaissez l'adresse IP source et n'avez pas besoin de mettre à jour les règles de pare-feu suite à une modification d'adresse IP.

## <a name="faqs"></a>FAQ
- Puis-je utiliser une adresse IP réservée pour tous les services Azure ?
    Non. Les adresses IP réservées peuvent être utilisées uniquement pour les machines virtuelles et les rôles d'instance de service cloud exposés par une adresse IP virtuelle.
- Combien d’adresses IP réservées puis-je avoir ?
    Pour plus d’informations, consultez l’article sur les [limites Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- L’obtention d’adresses IP réservées est-elle payante ?
    Parfois. Pour plus d’informations sur la tarification, consultez la [Tarification – Adresses IP réservées](https://go.microsoft.com/fwlink/?LinkID=398482).
- Comment réserver une adresse IP ?
    Vous pouvez utiliser PowerShell, l’[API REST de gestion Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx) ou le [portail Azure](https://portal.azure.com) pour réserver une adresse IP dans une région Azure. Une adresse IP réservée est associée à votre abonnement.
- Puis-je utiliser une adresse IP réservée avec des réseaux virtuels basés sur un groupe d'affinités ?
    Non. Les adresses IP réservées sont uniquement prises en charge dans les réseaux virtuels régionaux. Les adresses IP réservées ne sont pas prises en charge dans les réseaux virtuels associés à des groupes d’affinités. Pour plus d'informations sur l'association d'un réseau virtuel à une région ou un groupe d'affinités, consultez l’article [À propos des réseaux virtuels et groupes d’affinités régionaux](virtual-networks-migrate-to-regional-vnet.md).

## <a name="manage-reserved-vips"></a>Gérer les adresses IP virtuelles réservées

### <a name="using-azure-powershell-classic"></a>Utilisation d’Azure PowerShell (classique)

Avant de pouvoir utiliser une adresse IP réservée, vous devez l'ajouter à votre abonnement. Créez une adresse IP réservée à partir du pool d’adresses IP publiques disponibles dans la région *USA Centre*, comme suit :

> [!NOTE]
> Pour le modèle de déploiement classique, vous devez installer la version Service Management d’Azure PowerShell. Pour plus d’informations, consultez [Installer le module Azure PowerShell Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Toutefois, veuillez noter que vous ne pouvez pas spécifier quelle adresse IP vous souhaitez réserver. Pour voir quelles adresses IP sont réservées dans votre abonnement, exécutez la commande PowerShell suivante et notez les valeurs de *ReservedIPName* et *Address* :

```powershell
Get-AzureReservedIP
```

Sortie attendue :

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Lorsque vous créez une adresse IP réservée avec PowerShell, vous ne pouvez pas spécifier un groupe de ressources pour y créer l’adresse IP réservée. Azure place automatiquement cette adresse dans un groupe de ressources nommé *Default-Networking*. Si vous créez l’adresse IP réservée à l’aide du [portail Azure](https://portal.azure.com), vous pouvez spécifier le groupe de ressources de votre choix. Cependant, si vous créez l’adresse IP réservée dans un groupe de ressources autres que *Default-Networking*, chaque fois que vous référencez l’adresse IP réservée avec des commandes telles que `Get-AzureReservedIP` et `Remove-AzureReservedIP`, vous devez référencer le nom *Group resource-group-name reserved-ip-name*.  Par exemple, si vous créez une adresse IP réservée nommée *myReservedIP* dans un groupe de ressources nommé *myResourceGroup*, vous devez référencer le nom de l’adresse IP réservée comme *Group myResourceGroup myReservedIP*.   


Une fois une adresse IP réservée, elle reste associée à votre abonnement jusqu'à ce que vous la supprimiez. Supprimez une adresse IP réservée, comme suit :

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Utilisation d’Azure CLI (classique)
Créez une adresse IP réservée à partir du pool d’adresses IP publiques disponibles dans la région *USA Centre* en utilisant l’interface Azure CLI classique, comme suit :

> [!NOTE]
> Pour un déploiement classique, vous devez utiliser l’interface Azure CLI classique. Pour plus d’informations sur l’installation de l’interface Azure CLI classique, consultez [Installer l’interface Azure CLI classique](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest).
  
 Commande :
 
```azurecli
azure network reserved-ip create <name> <location>
```
Exemple :
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Vous pouvez voir les adresses IP qui sont réservées dans votre abonnement à l’aide d’Azure CLI, comme suit : 

Commande :
```azurecli
azure network reserved-ip list
```
Une fois une adresse IP réservée, elle reste associée à votre abonnement jusqu'à ce que vous la supprimiez. Supprimez une adresse IP réservée, comme suit :

Commande :

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Exemple :  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Réserver l’adresse IP d’un service cloud existant
Vous pouvez réserver l’adresse IP d’un service cloud existant en ajoutant le paramètre `-ServiceName`. Réservez l’adresse IP d’un service cloud *TestService* dans la région *USA Centre*, comme suit :

- Utilisation d’Azure PowerShell (classique) :

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Utilisation d’Azure CLI (classique) :
  
    Commande :

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Exemple :

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Associer une adresse IP réservée à un service cloud
Le script suivant crée une adresse IP réservée, puis l’associe à un nouveau service cloud nommé *TestService*.

### <a name="using-azure-powershell-classic"></a>Utilisation d’Azure PowerShell (classique)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Lorsque vous créez une adresse IP réservée à utiliser avec un service cloud, vous devez toujours faire référence à la machine virtuelle en utilisant *VIP:&lt;numéro de port>* pour les communications entrantes. Avoir une adresse IP réservée ne signifie pas que vous pouvez vous connecter directement à la machine virtuelle. L'adresse IP réservée est affectée au service cloud sur lequel la machine virtuelle a été déployée. Si vous souhaitez vous connecter à une machine virtuelle directement avec l’adresse IP, vous devez configurer une adresse IP publique de niveau de l'instance. Une adresse IP publique de niveau d'instance est un type d'adresse IP publique (appelée ILPIP) qui est affectée directement à votre machine virtuelle. Elle ne peut pas être réservée. Pour plus d’informations, consultez l’article [Adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Supprimer une adresse IP réservée d’un déploiement en cours d’exécution

Supprimez une adresse IP réservée ajoutée à un nouveau service cloud, comme suit : 
### <a name="using-azure-powershell-classic"></a>Utilisation d’Azure PowerShell (classique)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Utilisation d’Azure CLI (classique)
Commande :

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Exemple :

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Si vous supprimez une adresse IP réservée d’un déploiement en cours d'exécution, la réservation ne sera pas supprimée de votre abonnement. Cela libérera simplement l'adresse IP à utiliser par une autre ressource dans votre abonnement.
> 

Pour supprimer totalement une adresse IP réservée d’un abonnement, exécutez la commande suivante :

Commande :

```azurecli
azure network reserved-ip delete <name>
```
Exemple :

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Associer une adresse IP réservée à un déploiement en cours d’exécution

### <a name="using-azure-powershell-classic"></a>Utilisation d’Azure PowerShell (classique)

Les commandes suivantes créent un service cloud nommé *TestService2* avec une nouvelle machine virtuelle appelée *TestVM2*. L’adresse IP réservée nommée *MyReservedIP* est ensuite associée au service cloud.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Utilisation d’Azure CLI (classique)
Vous pouvez associer une nouvelle adresse IP réservée à votre déploiement de service cloud en cours d’exécution à l’aide d’Azure CLI, comme suit :

Commande :
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Exemple :
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Associer une adresse IP réservée à un service cloud à l’aide d’un fichier de configuration de service
Vous pouvez aussi associer une IP réservée à un service cloud à l’aide d’un fichier de configuration de service (CSCFG). L’exemple de code xml suivant indique comment configurer un service cloud pour l’utilisation d’une adresse IP réservée nommée *MyReservedIP* :
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [l’adressage IP](virtual-network-ip-addresses-overview-classic.md) fonctionne dans le modèle de déploiement Classic.
* En savoir plus sur [les adresses IP privées réservées](virtual-networks-reserved-private-ip.md).
* En savoir plus sur [les adresses IP publiques de niveau d’instance (ILPIP)](virtual-networks-instance-level-public-ip.md).

