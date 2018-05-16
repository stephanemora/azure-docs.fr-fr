---
title: Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack | Microsoft Docs
description: Découvrez comment un opérateur cloud peut ajouter des machines virtuelles identiques à la Marketplace Azure Stack
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: kivenkat
ms.openlocfilehash: 12425ab53ca16bb985a0a8658b5058998565b01a
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Mettre les groupes de machines virtuelles identiques à disposition dans Azure Stack

*S’applique à : systèmes intégrés Azure Stack et Kit de développement Azure Stack*

Les groupes de machines virtuelles identiques constituent une ressource de calcul Azure Stack. Vous pouvez les utiliser pour déployer et gérer un groupe de machines virtuelles identiques. Dans la mesure où toutes les machines virtuelles sont configurées de la même façon, les groupes identiques ne requièrent pas d’approvisionnement préalable des machines virtuelles. Il est plus simple de créer des services à grande échelle qui ciblent le Big Compute, le Big Data et les charges de travail en conteneurs.

Cet article vous guide tout au long du processus de mise à disposition des groupes identiques dans la marketplace Azure Stack. Lorsque vous aurez suivi cette procédure, vos utilisateurs pourront ajouter des groupes de machines virtuelles identiques à leurs abonnements.

Les groupes de machines virtuelles identiques sur Azure Stack suivent le même principe que sur Azure. Pour plus d’informations, consultez les vidéos suivantes :
* [Mark Russinovich parle des groupes identiques Azure](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/)
* [Jeux de mise à l’échelle de machine virtuelle, avec Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman)

Sur Azure Stack, les groupes de machines virtuelles identiques ne sont pas compatibles avec la mise à l’échelle automatique. Vous pouvez ajouter d’autres instances à un groupe identique avec le portail Azure Stack, les modèles Resource Manager ou PowerShell.

## <a name="prerequisites"></a>Prérequis

* **PowerShell et outils**

   Installer et configurez PowerShell pour Azure Stack et les outils Azure Stack. Consultez la page [Devenir opérationnel avec PowerShell dans Azure Stack](azure-stack-powershell-configure-quickstart.md).

   Après avoir installé les outils Azure Stack, veillez à importer le module PowerShell suivant (chemin d’accès relatif au dossier .\ComputeAdmin du dossier AzureStack-Tools-master) :
  ````PowerShell
        Import-Module .\AzureStack.ComputeAdmin.psm1
  ````

* **Image du système d’exploitation**

   Si vous n’avez pas ajouté d’image de système d’exploitation à votre Marketplace Azure Stack, consultez la page [Ajouter l’image de machine virtuelle de Windows Server 2016 à la marketplace Azure Stack](azure-stack-add-default-image.md).

   Pour la prise en charge de Linux, téléchargez Ubuntu Server 16.04 et ajoutez-le à l’aide de ```Add-AzsPlatformImage``` avec les paramètres suivants : ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.


## <a name="add-the-virtual-machine-scale-set"></a>Ajouter le groupe de machines virtuelles identiques

Modifiez le script PowerShell suivant en fonction de votre environnement, puis exécutez-le pour ajouter un groupe de machines virtuelles identiques à votre Marketplace Azure Stack. 

``$User`` est le compte servant à se connecter au portail d’administration. Par exemple : serviceadmin@contoso.onmicrosoft.com.

````PowerShell  
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
````

## <a name="update-images-in-a-virtual-machine-scale-set"></a>Mettre à jour des images dans un groupe de machines virtuelles identiques 
Après avoir créé un groupe de machines virtuelles identiques, les utilisateurs peuvent mettre à jour des images dans un groupe identique sans devoir recréer aucun groupe identique. Le processus de mise à jour d’une image varie selon les scénarios suivants :

1. Le modèle de déploiement du groupe de machines virtuelles identiques **spécifie dernière** pour *version* :  

   Lorsque la *version* est définie sur **dernière** dans la section *imageReference* du modèle pour un groupe identique, les opérations de montage en puissance sur le groupe identique utilisent la dernière version disponible de l’image pour les instances de groupe identique. Une fois une montée en puissance terminée, vous pouvez supprimer les instances de groupes de machines virtuelles identiques les plus anciennes.  (Les valeurs de *serveur de publication*, *offre* et *référence sku* restent inchangées). 

   Vous trouverez ci-dessous un exemple de spécification de la *dernière* :  

    ```Json  
    "imageReference": {
        "publisher": "[parameters('osImagePublisher')]",
        "offer": "[parameters('osImageOffer')]",
        "sku": "[parameters('osImageSku')]",
        "version": "latest"
        }
    ```

   Avant que la montée en puissance puisse utiliser une nouvelle image, vous devez télécharger cette nouvelle image :  

   - Lorsque l’image sur la Place de marché est une version plus récente que l’image dans le groupe identique : téléchargez la nouvelle image qui remplace l’ancienne image. Une fois l’image remplacée, un utilisateur peut continuer à monter en puissance. 

   - Lorsque la version de l’image sur la Place de marché est identique à l’image du groupe identique : supprimez l’image qui est utilisée dans le groupe identique et téléchargez la nouvelle image. Pendant l’intervalle de temps entre la suppression de l’image d’origine et le téléchargement de la nouvelle image, vous ne pouvez pas monter en puissance. 
      
     Ce processus est requis pour resyndiquer les images qui utilisent le format de fichier partiellement alloué, introduit avec la version 1803. 
 

2. Le modèle de déploiement du groupe de machines virtuelles identiques **ne spécifie pas de dernière** pour *version* et spécifie un numéro de version à la place :  

     Si vous téléchargez une image avec une version plus récente (ce qui modifie la version disponible), le groupe identique ne peut pas monter en puissance. Ceci est normal, car la version de l’image spécifiée dans le modèle du groupe identique doit être disponible.  

Pour plus d’informations, consultez [disques et images du système d’exploitation](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>Supprimer un groupe de machines virtuelles identiques

Pour supprimer un élément de la galerie du groupe de machines virtuelles identiques, exécutez la commande PowerShell suivante :

```PowerShell  
    Remove-AzsVMSSGalleryItem
````

> [!NOTE]
> L’élément de la galerie n’est pas forcément supprimé immédiatement de la marketplace. Pour que l’élément apparaisse comme étant supprimé de la marketplace, vous devrez peut-être actualiser le portail plusieurs fois.

## <a name="next-steps"></a>Étapes suivantes
[Forum aux questions sur Azure Stack](azure-stack-faq.md)