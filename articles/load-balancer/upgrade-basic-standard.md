---
title: Passer de Public De base vers Public Standard - Azure Load Balancer
description: Cet article explique comment mettre à niveau Azure Public Load Balancer en passant de la référence SKU De base à la référence SKU Standard
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 66c56ae6730043022a0d8bf3c94f7c6ce14d9852
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84809348"
---
# <a name="upgrade-azure-public-load-balancer"></a>Mettre à niveau l’équilibreur de charge public Azure
[Azure Standard Load Balancer](load-balancer-overview.md) offre un ensemble complet de fonctionnalités et une haute disponibilité avec la redondance de zone. Pour en savoir plus sur la référence SKU de Load Balancer, consultez le [tableau comparatif](https://docs.microsoft.com/azure/load-balancer/skus#skus).

Une mise à niveau comprend trois phases :

1. Migrer la configuration
2. Ajout de machines virtuelles aux pools de back-ends de Standard Load Balancer

Cet article couvre la migration de la configuration. L’ajout de machines virtuelles aux pools de back-ends peut varier en fonction de votre environnement spécifique. Toutefois, certaines suggestions générales de haut niveau [sont fournies](#add-vms-to-backend-pools-of-standard-load-balancer).

## <a name="upgrade-overview"></a>Présentation de la mise à niveau

Un script Azure PowerShell est disponible qui effectue les opérations suivantes :

* Il crée un équilibreur de charge Standard dans le groupe de ressources et à l’emplacement que vous spécifiez.
* Il copie de façon fluide les configurations de l’équilibreur de charge De base sur l’équilibreur de charge Standard nouvellement créé.
* Il crée une règle de trafic sortant par défaut qui permet une connectivité sortante.

### <a name="caveatslimitations"></a>Mises en garde/Limitations

* Le script prend en charge la mise à niveau de Public Load Balancer uniquement. Pour plus d’informations sur la mise à niveau du Basic Load Balancer interne et pour obtenir des instructions, consultez [cette page](https://docs.microsoft.com/azure/load-balancer/upgrade-basicinternal-standard).
* Standard Load Balancer dispose d’une nouvelle adresse publique. Il est impossible de déplacer de façon fluide les adresses IP associées à l’instance Basic Load Balancer existante vers Standard Load Balancer, car il s’agit de deux références SKU différentes.
* Si l’instance Standard Load Balancer est créée dans une autre région, vous ne pouvez pas associer les machines virtuelles existant dans l’ancienne région avec l’instance Standard Load Balancer nouvellement créée. Pour contourner cette limitation, prenez soin de créer la machine virtuelle dans la nouvelle région.
* Si votre équilibreur de charge ne dispose pas de configuration d’adresse IP front-end ni de pool de back-ends, vous risquez de rencontrer une erreur lors de l’exécution du script. Assurez-vous qu’ils ne sont pas vides.

## <a name="download-the-script"></a>Télécharger le script

Téléchargez le script de migration à partir de [PowerShell Gallery](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/2.0).
## <a name="use-the-script"></a>Utiliser le script

Vous disposez de deux options selon vos préférences et votre configuration de l’environnement PowerShell local :

* Si vous n’avez pas installé les modules Azure Az ou si vous êtes prêt à désinstaller les modules Azure Az, la meilleure option consiste à utiliser l’option `Install-Script` pour exécuter le script.
* Si vous devez conserver les modules Azure Az, la meilleure solution qui s’offre à vous consiste à télécharger le script et à l’exécuter directement.

Pour déterminer si vous avez installé les modules Azure Az, exécutez `Get-InstalledModule -Name az`. Si vous ne voyez aucun module Az installé, vous pouvez utiliser la méthode `Install-Script`.

### <a name="install-using-the-install-script-method"></a>Installer à l’aide de la méthode Install-Script

Pour pouvoir utiliser cette option, les modules Azure Az ne doivent pas être installés sur votre ordinateur. S’ils sont installés, la commande suivante affiche une erreur. Vous pouvez désinstaller les modules Azure Az ou utiliser l’autre option pour télécharger le script manuellement et l’exécuter.
  
Exécutez le script avec la commande suivante :

`Install-Script -Name AzurePublicLBUpgrade`

Cette commande installe également les modules Az requis.  

### <a name="install-using-the-script-directly"></a>Installer en utilisant directement le script

Si certains modules Azure Az sont installés et ne peuvent pas être désinstallés (ou si vous ne souhaitez pas les désinstaller), vous pouvez télécharger manuellement le script en utilisant l’onglet **Téléchargement manuel** dans le lien de téléchargement du script. Le script est téléchargé sous forme de fichier nupkg brut. Pour installer le script à partir de ce fichier nupkg, consultez [Téléchargement manuel de package](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

Pour exécuter le script :

1. Utilisez `Connect-AzAccount` pour vous connecter à Azure.

1. Utilisez `Import-Module Az` pour importer les modules Az.

1. Examinez les paramètres requis :

   * **oldRgName : [Chaîne] : Obligatoire** – Il s’agit du groupe de ressources pour votre instance Basic Load Balancer existante que vous souhaitez mettre à niveau. Pour trouver cette valeur de chaîne, accédez au Portail Azure, sélectionnez votre source Basic Load Balancer, puis cliquez sur la **Vue d’ensemble** de l’équilibreur de charge. Le groupe de ressources figure dans cette page.
   * **oldLBName : [Chaîne] : Obligatoire** – Il s’agit du nom de l’instance Basic Load Balancer existante que vous souhaitez mettre à niveau. 
   * **newrgName : [Chaîne] : Obligatoire** – Il s’agit du groupe de ressources dans lequel l’instance Standard Load Balancer est créée. Ce peut être un nouveau groupe de ressources ou un groupe existant. Si vous choisissez un groupe de ressources existant, notez que le nom de l’équilibreur de charge doit être unique au sein du groupe de ressources. 
   * **newlocation : [Chaîne] : Obligatoire** – Il s’agit de l’emplacement où l’instance Standard Load Balancer est créée. Pour une meilleure association avec d’autres ressources existantes, il est préférable que l’instance Standard Load Balancer hérite du même emplacement que l’instance Basic Load Balancer.
   * **newLBName : [Chaîne] : Obligatoire** – Il s’agit du nom de l’instance Standard Load Balancer à créer.
1. Exécutez le script en utilisant les paramètres appropriés. Cette opération peut prendre entre cinq et sept minutes.

    **Exemple**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newrgName "test_userInput3_rg" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

### <a name="add-vms-to-backend-pools-of-standard-load-balancer"></a>Ajout de machines virtuelles aux pools de back-ends de Standard Load Balancer

Tout d’abord, vérifiez soigneusement que le script a bien créé une nouvelle instance Standard Load Balancer publique en migrant la configuration exacte à partir de votre instance Basic Load Balancer. Vous pouvez vérifier cela à partir du portail Azure.

Veillez à envoyer une petite quantité de trafic via Standard Load Balancer en guise de test manuel.
  
Voici quelques scénarios illustrant la façon d’ajouter des machines virtuelles aux pools de back-ends de l’instance Standard Load Balancer publique nouvellement créée, avec nos recommandations pour chacun de ces scénarios :

* **Déplacement des machines virtuelles existantes depuis les pools de back-ends de l’ancienne instance Basic Load Balancer publique vers les pools de back-ends de l’instance Standard Load Balancer publique nouvellement créée**.
    1. Pour toutes les tâches de ce guide de démarrage rapide, connectez-vous au [portail Azure](https://portal.azure.com).
 
    1. Sélectionnez **Toutes les ressources** dans le menu de gauche, puis sélectionnez l’instance **Standard Load Balancer nouvellement créée** dans la liste de ressources.
   
    1. Sous **Paramètres**, sélectionnez **Backend Pools (Pools principaux)** .
   
    1. Sélectionnez le pool de back-ends correspondant au pool de back-ends de Basic Load Balancer, puis sélectionnez la valeur suivante : 
      - **Machine virtuelle** : Faites défiler et sélectionnez les machines virtuelles dans le pool de back-ends correspondant de Basic Load Balancer.
    1. Sélectionnez **Enregistrer**.
    >[!NOTE]
    >Pour les machines virtuelles avec des adresses IP publiques, vous devez d’abord créer des adresses IP standard là où la même adresse IP n’est pas garantie. Dissociez les machines virtuelles des adresses IP de base et associez-les aux adresses IP standard nouvellement créées. Vous êtes alors en mesure de suivre les instructions permettant d’ajouter des machines virtuelles au pool de back-ends de Standard Load Balancer. 

* **Création de machines virtuelles à ajouter aux pools de back-ends de l’instance Standard Load Balancer publique nouvellement créée**.
    * Des instructions supplémentaires sur la création d’une machine virtuelle et son association à Standard Load Balancer sont disponibles [ici](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal#create-virtual-machines).

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Créer une règle de trafic sortant pour une connexion sortante

Suivez les [instructions](https://docs.microsoft.com/azure/load-balancer/configure-load-balancer-outbound-portal#create-outbound-rule-configuration) pour créer une règle de trafic sortant afin de pouvoir
* définir la NAT de trafic sortant à partir de zéro ;
* mettre à l’échelle et adapter le comportement de la NAT de trafic sortant.

## <a name="common-questions"></a>Questions courantes

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Existe-t-il des restrictions avec le script Azure PowerShell pour migrer la configuration de la version v1 à v2 ?

Oui. Consultez [Mises en garde/Limitations](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Le script Azure PowerShell bascule-t-il également le trafic de mon instance Basic Load Balancer nouvellement créée ?

Non. Le script Azure PowerShell migre uniquement la configuration. La migration de trafic réelle est de votre responsabilité et sous votre contrôle.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>J’ai rencontré des problèmes en utilisant ce script. Comment obtenir de l’aide ?
  
Vous pouvez envoyer un e-mail à slbupgradesupport@microsoft.com, ouvrir un dossier de support auprès du support Azure ou les deux.

## <a name="next-steps"></a>Étapes suivantes

[Présentation de la référence Standard d’Azure Load Balancer (préversion)](load-balancer-overview.md)
