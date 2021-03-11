---
title: Guide pratique pour déployer Windows 10 sur Azure avec des droits d’hébergement multilocataire
description: Découvrez comment optimiser les avantages de votre contrat Software Assurance pour Windows pour mettre des licences locales sur Azure avec des droits d’hébergement multilocataire.
author: mimckitt
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 2/2/2021
ms.author: mimckitt
ms.custom: rybaker, chmimckitt
ms.openlocfilehash: bb86ba6867ad796ef0f5eeb1357a6df9e93e9f9e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555769"
---
# <a name="how-to-deploy-windows-10-on-azure-with-multitenant-hosting-rights"></a>Guide pratique pour déployer Windows 10 sur Azure avec des droits d’hébergement multilocataire 
Pour les clients avec Windows 10 Entreprise E3/E5 par utilisateur ou Windows Virtual Desktop Access par utilisateur (licences d’abonnement utilisateur ou licences d’abonnement utilisateur de composant additionnel), les droits d’hébergement multilocataire pour Windows 10 permettent de placer les licences Windows 10 sur le cloud et d’exécuter des machines virtuelles Windows 10 sur Azure sans acheter de licence supplémentaire. Les droits d’hébergement multilocataire sont disponibles uniquement pour Windows 10 (version 1703 ou ultérieure).

Pour plus d’informations, consultez [Hébergement multilocataire pour Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).

> [!NOTE]
> - Pour utiliser les images Windows 7, 8.1 et 10 pour le développement ou le test, consultez [Client Windows dans Azure pour les scénarios de dev/test](client-images.md).
> - Pour les avantages des licences Windows Server, reportez-vous à [Avantages Utilisation hybride d’Azure pour les images Windows Server](hybrid-use-benefit-licensing.md).

## <a name="subscription-licenses-that-qualify-for-multitenant-hosting-rights"></a>Licences d’abonnement qui bénéficient de droits d’hébergement multilocataires

À l’aide du [centre d’administration Microsoft](/microsoft-365/admin/admin-overview/about-the-admin-center), vous pouvez vérifier si une licence Windows 10 prise en charge a été attribuée à un utilisateur.

> [!IMPORTANT]
> Les utilisateurs doivent disposer de l’une des licences d’abonnement ci-dessous pour pouvoir utiliser des images Windows 10 dans Azure. Si vous n’avez pas l’une de ces licences d’abonnement, vous pouvez les acheter via votre [partenaire de service cloud](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/) ou directement par l’intermédiaire de [Microsoft](https://www.microsoft.com/microsoft-365?rtc=1).

**Licences d’abonnement éligibles :**

-   Microsoft 365 E3/E5 
-   Microsoft 365 F3 
-   Microsoft 365 A3/A5 
-   Windows 10 Entreprise E3/E5
-   Windows 10 Éducation A3/A5 
-   Windows VDA E3/E5


## <a name="deploying-windows-10-image-from-azure-marketplace"></a>Déploiement de l’image Windows 10 à partir de la Place de marché Azure 
Pour les déploiements PowerShell, CLI et avec un modèle Azure Resource Manager, des images Windows 10 sont disponibles avec `PublisherName: MicrosoftWindowsDesktop` et `Offer: Windows-10`. Windows 10 version Creators Update (1809) ou version ultérieure est pris en charge pour les droits d’hébergement multilocataires. 

```powershell
Get-AzVmImageSku -Location '$location' -PublisherName 'MicrosoftWindowsDesktop' -Offer 'Windows-10'

Skus                        Offer      PublisherName           Location 
----                        -----      -------------           -------- 
rs4-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs4-pron                    Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprise              Windows-10 MicrosoftWindowsDesktop eastus   
rs5-enterprisen             Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pro                     Windows-10 MicrosoftWindowsDesktop eastus   
rs5-pron                    Windows-10 MicrosoftWindowsDesktop eastus  
```

Pour plus d’informations sur les images disponibles, consultez [Rechercher et utiliser des images de machine virtuelle de la Place de marché Azure avec Azure PowerShell](./cli-ps-findimage.md)

## <a name="uploading-windows-10-vhd-to-azure"></a>Chargement d’un VHD Windows 10 sur Azure
Si vous chargez un VHD Windows 10 généralisé, notez que Windows 10 n’a pas de compte Administrateur intégré activé par défaut. Pour activer le compte Administrateur intégré, incluez la commande suivante dans le cadre de l’extension de script personnalisé.

```powershell
Net user <username> /active:yes
```

L’extrait de code PowerShell suivant permet de marquer tous les comptes Administrateur comme étant actifs, y compris le compte Administrateur intégré. Cet exemple est utile si le nom d’utilisateur du compte Administrateur intégré est inconnu.
```powershell
$adminAccount = Get-WmiObject Win32_UserAccount -filter "LocalAccount=True" | ? {$_.SID -Like "S-1-5-21-*-500"}
if($adminAccount.Disabled)
{
    $adminAccount.Disabled = $false
    $adminAccount.Put()
}
```
Pour plus d'informations : 
* [Guide pratique pour charger un VHD sur Azure](upload-generalized-managed.md)
* [Guide pratique pour préparer un VHD Windows à charger sur Azure](prepare-for-upload-vhd-image.md)


## <a name="deploying-windows-10-with-multitenant-hosting-rights"></a>Déploiement de Windows 10 avec des droits d’hébergement multilocataire
Vérifiez que vous avez [installé et configuré la dernière version d’Azure PowerShell](/powershell/azure/). Une fois votre disque dur virtuel préparé, chargez-le dans votre compte Azure Storage en utilisant l’applet de commande `Add-AzVhd` comme suit :

```powershell
Add-AzVhd -ResourceGroupName "myResourceGroup" -LocalFilePath "C:\Path\To\myvhd.vhd" `
    -Destination "https://mystorageaccount.blob.core.windows.net/vhds/myvhd.vhd"
```


**Déploiement à l’aide d’un modèle Azure Resource Manager** Dans vos modèles Resource Manager, vous pouvez spécifier un paramètre supplémentaire pour `licenseType`. Pour en savoir plus sur la création de modèles Azure Resource Manager, [cliquez ici](../../azure-resource-manager/templates/template-syntax.md). Une fois que votre disque dur virtuel téléchargé dans Azure, modifiez votre modèle Resource Manager pour inclure le type de licence dans le fournisseur de calcul et déployez votre modèle normalement :
```json
"properties": {
    "licenseType": "Windows_Client",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

**Déploiement par le biais de PowerShell** Quand vous déployez votre machine virtuelle Windows Server par le biais de PowerShell, vous disposez d’un paramètre supplémentaire pour `-LicenseType`. Une fois votre disque dur virtuel chargé dans Azure, vous créez une machine virtuelle en utilisant `New-AzVM` et spécifiez le type de licence comme suit :
```powershell
New-AzVM -ResourceGroupName "myResourceGroup" -Location "West US" -VM $vm -LicenseType "Windows_Client"
```

## <a name="verify-your-vm-is-utilizing-the-licensing-benefit"></a>Vérifiez que votre machine virtuelle utilise l’avantage de licence
Une fois votre machine virtuelle déployée par le biais de PowerShell ou de Resource Manager, vérifiez le type de licence avec `Get-AzVM` comme suit :
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Le résultat ressemble à l’exemple suivant pour Windows 10 avec le type de licence correct :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Ce résultat est différent de celui de la machine virtuelle suivante déployée sans la licence Azure Hybrid Use Benefit, comme une machine virtuelle déployée directement depuis la Galerie Azure :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

## <a name="additional-information-about-joining-azure-ad"></a>Informations supplémentaires pour rejoindre Azure AD
Azure approvisionne toutes les machines virtuelles Windows avec un compte Administrateur intégré, qui ne peut pas être utilisé pour joindre AAD. Par exemple, *Paramètres > Compte > Accès Professionnel ou Scolaire > + Se connecter* ne fonctionne pas. Vous devez créer un deuxième compte Administrateur et ouvrir une session sous celui-ci pour joindre Azure AD manuellement. Vous pouvez également configurer Azure AD à l’aide d’un package de provisionnement. Pour plus d’informations, utilisez le lien approprié dans la section *Étapes suivantes*.

## <a name="next-steps"></a>Étapes suivantes
- Découvrez plus en détail la [configuration de VDA pour Windows 10](/windows/deployment/vda-subscription-activation).
- Découvrez plus en détail [l’hébergement multilocataire pour Windows 10](https://www.microsoft.com/en-us/CloudandHosting/licensing_sca.aspx).