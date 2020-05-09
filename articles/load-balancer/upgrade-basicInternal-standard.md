---
title: Passer de la référence SKU interne De base à interne Standard – Azure Load Balancer
description: Cet article explique comment mettre à niveau l’équilibreur de charge interne Azure en passant de la référence SKU De base à la référence SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: article
ms.date: 02/23/2020
ms.author: irenehua
ms.openlocfilehash: 239dc0f3133a5adf59a23d333131c91d3a655597
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770384"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Mettre à niveau l’équilibreur de charge interne Azure – Aucune connexion sortante nécessaire
[Azure Standard Load Balancer](load-balancer-overview.md) offre un ensemble complet de fonctionnalités et une haute disponibilité avec la redondance de zone. Pour en savoir plus sur la référence SKU de Load Balancer, consultez le [tableau comparatif](https://docs.microsoft.com/azure/load-balancer/concepts-limitations#skus).

Cet article présente un script PowerShell qui crée une instance Standard Load Balancer avec la même configuration que le Basic Load Balancer et qui migre le trafic de Basic Load Balancer à Standard Load Balancer.

## <a name="upgrade-overview"></a>Présentation de la mise à niveau

Un script Azure PowerShell est disponible qui effectue les opérations suivantes :

* Crée un équilibreur de charge de référence SKU interne Standard à l’emplacement que vous spécifiez. Notez qu’aucune [connexion sortante](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) ne sera fournie par l’équilibreur de charge interne Standard.
* Il copie de façon fluide les configurations de la référence SKU De base de Load Balancer sur l’instance Standard Load Balancer nouvellement créée.
* Déplacez de façon fluide les adresses IP privées de Basic Load Balancer vers l’instance Standard Load Balancer nouvellement créée.
* Déplacez de façon fluide les machines virtuelles du pool principal du Basic Load Balancer vers le pool principal du Standard Load Balancer.

### <a name="caveatslimitations"></a>Mises en garde/Limitations

* Le script ne prend en charge que la mise à niveau de l’équilibreur de charge interne lorsqu’aucune connexion sortante n’est requise. Si vous avez besoin d’une [connexion sortante](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections) pour certaines de vos machines virtuelles, consultez cette [page](upgrade-InternalBasic-To-PublicStandard.md) pour obtenir des instructions. 
* Si l’instance Standard Load Balancer est créée dans une autre région, vous ne pouvez pas associer les machines virtuelles existant dans l’ancienne région avec l’instance Standard Load Balancer nouvellement créée. Pour contourner cette limitation, prenez soin de créer la machine virtuelle dans la nouvelle région.
* Si votre équilibreur de charge ne dispose pas de configuration d’adresse IP front-end ni de pool de back-ends, vous risquez de rencontrer une erreur lors de l’exécution du script. Vérifiez qu’ils ne sont pas vides.

## <a name="download-the-script"></a>Télécharger le script

Téléchargez le script de migration à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzureILBUpgrade/2.0).
## <a name="use-the-script"></a>Utiliser le script

Vous disposez de deux options selon vos préférences et votre configuration de l’environnement PowerShell local :

* Si vous n’avez pas installé les modules Azure Az ou si vous êtes prêt à désinstaller les modules Azure Az, la meilleure option consiste à utiliser l’option `Install-Script` pour exécuter le script.
* Si vous devez conserver les modules Azure Az, la meilleure solution qui s’offre à vous consiste à télécharger le script et à l’exécuter directement.

Pour déterminer si vous avez installé les modules Azure Az, exécutez `Get-InstalledModule -Name az`. Si vous ne voyez aucun module Az installé, vous pouvez utiliser la méthode `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Installer à l’aide de la méthode Install-Script

Pour pouvoir utiliser cette option, les modules Azure Az ne doivent pas être installés sur votre ordinateur. S’ils sont installés, la commande suivante affiche une erreur. Vous pouvez désinstaller les modules Azure Az ou utiliser l’autre option pour télécharger le script manuellement et l’exécuter.
  
Exécutez le script avec la commande suivante :

`Install-Script -Name AzureILBUpgrade`

Cette commande installe également les modules Az requis.  

### <a name="install-using-the-script-directly"></a>Installer en utilisant directement le script

Si certains modules Azure Az sont installés et ne peuvent pas être désinstallés (ou si vous ne souhaitez pas les désinstaller), vous pouvez télécharger manuellement le script en utilisant l’onglet **Téléchargement manuel** dans le lien de téléchargement du script. Le script est téléchargé sous forme de fichier nupkg brut. Pour installer le script à partir de ce fichier nupkg, consultez [Téléchargement manuel de package](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Pour exécuter le script :

1. Utilisez `Connect-AzAccount` pour vous connecter à Azure.

1. Utilisez `Import-Module Az` pour importer les modules Az.

1. Examinez les paramètres requis :

   * **rgName : [Chaîne] : Obligatoire** – Il s’agit du groupe de ressources pour votre instance Basic Load Balancer existante et votre nouvelle instance Standard Load Balancer. Pour trouver cette valeur de chaîne, accédez au Portail Azure, sélectionnez votre source Basic Load Balancer, puis cliquez sur la **Vue d’ensemble** de l’équilibreur de charge. Le groupe de ressources figure dans cette page.
   * **oldLBName : [Chaîne] : Obligatoire** – Il s’agit du nom de l’instance Basic Load Balancer existante que vous souhaitez mettre à niveau. 
   * **newlocation : [Chaîne] : Obligatoire** – Il s’agit de l’emplacement où l’instance Standard Load Balancer est créée. Pour une meilleure association avec d’autres ressources existantes, il est préférable que l’instance Standard Load Balancer hérite du même emplacement que l’instance Basic Load Balancer.
   * **newLBName : [Chaîne] : Obligatoire** – Il s’agit du nom de l’instance Standard Load Balancer à créer.
1. Exécutez le script en utilisant les paramètres appropriés. Cette opération peut prendre entre cinq et sept minutes.

    **Exemple**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Questions courantes

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existe-t-il des restrictions avec le script Azure PowerShell pour migrer la configuration de la version v1 à v2 ?

Oui. Consultez [Mises en garde/Limitations](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Le script Azure PowerShell bascule-t-il également le trafic de mon instance Basic Load Balancer nouvellement créée ?

Oui, il migre le trafic. Si vous souhaitez migrer le trafic personnellement, utilisez [ce script](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0) qui ne déplace pas les machines virtuelles à votre place.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>J’ai rencontré des problèmes en utilisant ce script. Comment obtenir de l’aide ?
  
Vous pouvez envoyer un e-mail à slbupgradesupport@microsoft.com, ouvrir un dossier de support auprès du support Azure ou les deux.

## <a name="next-steps"></a>Étapes suivantes

[Présentation de la référence Standard d’Azure Load Balancer (préversion)](load-balancer-overview.md)
