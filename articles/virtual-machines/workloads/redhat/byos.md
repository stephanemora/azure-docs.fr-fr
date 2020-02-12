---
title: Images Azure BYOS (Apportez votre propre abonnement) de Red Hat Enterprise Linux | Microsoft Docs
description: Découvrez les images BYOS (apportez votre propre abonnement) pour Red Hat Enterprise Linux sur Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 1/14/2020
ms.author: alsin
ms.openlocfilehash: afda502bcd89423ecdd008c0297c85dd8a5b61fb
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989839"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Images Gold BYOS (Apportez votre propre abonnement) de Red Hat Enterprise Linux dans Azure

Les images RHEL (Red Hat Enterprise Linux) sont disponibles dans Azure sous la forme d’un modèle PAYG (paiement à l’utilisation) ou d’un modèle BYOS (Red Hat Gold Image). Ce document donne une vue d’ensemble des images Red Hat Gold dans Azure.

## <a name="important-points-to-consider"></a>Points importants à prendre en compte

- Les images Red Hat Gold fournies dans ce programme sont des images RHEL prêtes pour la production, similaires aux images RHEL PAYG dans la Galerie/Place de marché Azure.

- Les images suivent nos stratégies actuelles, décrites dans [Images Red Hat Enterprise Linux sur Azure](./redhat-images.md).

- Les stratégies de support standard s’appliquent aux machines virtuelles créées à partir de ces images.

- Les machines virtuelles provisionnées à partir d’images Red Hat Gold ne sont pas soumises aux redevances RHEL associées aux images RHEL PAYG.

- Les images ne sont pas autorisées. Vous devez donc utiliser le gestionnaire d’abonnements pour vous inscrire et vous abonner aux machines virtuelles afin d’obtenir directement les mises à jour auprès de Red Hat.

- Il n’est actuellement pas possible de basculer dynamiquement entre les modèles de facturation BYOS et PAYG pour les images Linux. Il est nécessaire de redéployer la machine virtuelle à partir de l’image correspondante pour changer de modèle de facturation.

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Exigences et conditions pour accéder aux images Red Hat Gold

1. Familiarisez-vous avec les termes du [programme Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) et activez vos abonnements Red Hat pour Cloud Access sur [Red Hat Subscription Manager](https://access.redhat.com/management/cloud). Vous devez disposer des abonnements Azure qui vont être inscrits pour Cloud Access.

1. Si vous avez activé des abonnements Red Hat pour Cloud Access qui répondent aux conditions d’éligibilité appropriées, vos abonnements Azure seront automatiquement activés pour l’accès aux images Gold.

### <a name="expected-time-for-image-access"></a>Délai attendu pour l’accès aux images

À la fin des étapes d’activation de Cloud Access, Red Hat valide votre éligibilité pour les images Red Hat Gold. Si la validation réussit, vous recevrez l’accès aux images Gold dans un délai de trois heures.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Utiliser les images Red Hat Gold depuis le portail Azure

1. Une fois que votre abonnement a reçu l’accès aux images Red Hat Gold, vous pouvez les trouver dans le [portail Azure](https://portal.azure.com) en accédant à **Créer une ressource**, puis à **Tout afficher**.

1. En haut de la page, vous verrez que vous disposez d’offres privées.

    ![Offres privées de la place de marché](./media/rhel-byos-privateoffers.png)

1. Vous pouvez cliquer sur le lien violet ou faire défiler la page vers le bas pour voir vos offres privées.

1. La suite du provisionnement dans l’interface utilisateur ne sera pas différente de celui d’une image Red Hat existante. Choisissez votre version RHEL et suivez les invites pour provisionner votre machine virtuelle. Ce processus vous permet également d’accepter les conditions de l’image à la dernière étape.

>[!NOTE]
>Ces étapes n’activent pas encore le déploiement programmatique de votre image Red Hat Gold. Une étape supplémentaire est nécessaire, comme l’explique la section « Informations supplémentaires » ci-dessous.

La suite de ce document se concentre sur la méthode CLI pour provisionner et accepter les conditions sur l’image. L’interface utilisateur et l’interface CLI sont entièrement interchangeables en ce qui concerne le résultat final (une machine virtuelle RHEL Gold Image provisionnée).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Utiliser les images Red Hat Gold depuis Azure CLI
La série d’instructions suivante vous guide tout au long du processus de déploiement initial pour une machine virtuelle RHEL avec Azure CLI. Elle part du principe [qu’Azure CLI est installé](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Veillez à mettre toutes les lettres en minuscules dans les références à l’éditeur, à l’offre, à l’abonnement et à l’image pour toutes les commandes suivantes.

1. Vérifiez que vous êtes dans l’abonnement souhaité :
    ```azurecli
    az account show -o=json
    ```

1. Créez un groupe de ressources pour votre machine virtuelle Red Hat Gold Image :
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Acceptez les conditions de l’image :
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Ces conditions doivent être acceptées *une fois par abonnement Azure, par référence SKU d’image*.

1. (Facultatif) Validez le déploiement de votre machine virtuelle avec la commande suivante :
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Provisionnez votre machine virtuelle en exécutant la commande ci-dessus sans l’argument `--validate` :
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Établissez une liaison SSH sur votre machine virtuelle et vérifiez que vous disposez d’une image non autorisée. Pour cela, exécutez `sudo yum repolist` (pour RHEL 8, utilisez `sudo dnf repolist`). La sortie vous demande d’utiliser le gestionnaire d’abonnements pour inscrire la machine virtuelle auprès de Red Hat.

>[!NOTE]
>Sur RHEL 8, `dnf` et `yum` sont interchangeables. Pour plus d’informations à ce sujet, consultez le [Guide d’administration de RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Utiliser les images Red Hat Gold depuis PowerShell
Voici un exemple de script. Vous devez remplacer le groupe de ressources, l’emplacement, le nom de la machine virtuelle, les informations de connexion et d’autres variables par la configuration de votre choix. Les informations du serveur de publication et du plan doivent être en minuscules.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Chiffrer des images Gold BYOS (Apportez votre propre abonnement) de Red Hat Enterprise Linux

Les images Gold BYOS de Red Hat Enterprise Linux peuvent être sécurisées à l’aide d’[Azure Disk Encryption](../../linux/disk-encryption-overview.md). Toutefois, vous **devez** inscrire l’abonnement avant d’activer le chiffrement.  Vous trouverez des détails sur l’inscription d’une image Gold BYOS RHEL sur le site Red Hat. Consultez [How to register and subscribe a system to the Red Hat Customer Portal using Red Hat Subscription-Manager ](https://access.redhat.com/solutions/253273) ; si vous disposez d’un abonnement Red Hat actif, vous pouvez également lire [Creating Red Hat Customer Portal Activation Keys](https://access.redhat.com/articles/1378093).

Azure Disk Encryption n’est pas pris en charge sur les [images personnalisées Red Hat](/linux/redhat-create-upload-vhd). D’autres conditions et prérequis ADE sont documentés dans [Azure Disk Encryption pour les machines virtuelles Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Les étapes d’application d’Azure Disk Encryption sont disponibles dans [Scénarios Azure Disk Encryption sur les machines virtuelles Linux](../../linux/disk-encryption-linux.md) et les articles connexes.  

## <a name="additional-information"></a>Informations supplémentaires

- Si vous tentez de provisionner une machine virtuelle sur un abonnement non activé pour cette offre, vous obtiendrez l’erreur suivante :

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```
    
    Dans ce cas, contactez Microsoft ou Red Hat pour activer votre abonnement.

- Si vous modifiez un instantané à partir d’une image BYOS RHEL et que vous tentez de publier cette image personnalisée sur [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), vous devez fournir des informations d’abonnement qui correspondent à la source d’origine de l’instantané. Par exemple, la commande peut se présenter comme suit :

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Notez les paramètres de plan sur la dernière ligne ci-dessus.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) n’est pas pris en charge sur les images personnalisées.

- Si vous utilisez l’automatisation pour provisionner des machines virtuelles à partir des images BYOS RHEL, vous devez fournir des paramètres d’abonnement similaires à ce qui est indiqué ci-dessus. Par exemple, avec Terraform, les informations d’abonnement sont indiquées dans un [bloc d’abonnement](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Étapes suivantes
- Des guides pas-à-pas et des détails sur les programmes pour Cloud Access sont disponibles dans la documentation de [Red Hat Cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Découvrez [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Pour plus d’informations sur les images Red Hat dans Azure, accédez à la [page de documentation](./redhat-images.md).
- Vous trouverez des informations sur les stratégies de prise en charge de Red Hat pour toutes les versions de RHEL sur la page [Cycle de vie de Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata).
- Vous trouverez de la documentation supplémentaire sur les images RHEL Gold dans la [documentation de Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
