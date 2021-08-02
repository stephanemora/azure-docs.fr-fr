---
title: Créer des images de machines virtuelles personnalisées pour votre appareil Azure Stack Edge Pro avec GPU
description: Décrit comment créer des images de machines virtuelles Windows et Linux personnalisées pour le déploiement de machines virtuelles sur des appareils Azure Stack Edge Pro avec GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/09/2021
ms.author: alkohli
ms.openlocfilehash: a1f6b51c8ab36d779ad2771c1e12de78673e6fc1
ms.sourcegitcommit: f9e368733d7fca2877d9013ae73a8a63911cb88f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111902404"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>Créer des images de machines virtuelles personnalisées pour votre appareil Azure Stack Edge Pro avec GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Pour déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro avec GPU, vous devez être en mesure de créer des images de machines virtuelles personnalisées à utiliser pour créer des machines virtuelles dans Azure. Cet article décrit les étapes de la création d’images de machines virtuelles personnalisées dans Azure pour les machines virtuelles Windows et Linux, et la procédure de téléchargement ou de copie de ces images dans un compte de stockage Azure.

Un workflow est nécessaire pour la préparation d’une image de machine virtuelle personnalisée. Pour la source de l’image, vous devez utiliser un disque dur virtuel fixe, d’une machine virtuelle Gen1 dont la taille est prise en charge par Azure. Pour les options de taille de machine virtuelle, consultez [Tailles de machines virtuelles prises en charge](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

## <a name="prerequisites"></a>Prérequis

Avant de créer votre image de machine virtuelle, conformez-vous aux prérequis suivants :

- [Téléchargez AzCopy](/azure/storage/common/storage-use-azcopy-v10#download-azcopy). AzCopy vous offre un moyen de copie rapide d’un disque de système d’exploitation dans un compte de stockage Azure.

---

## <a name="create-a-custom-vm-image"></a>Créer une image de machine virtuelle personnalisée

Les étapes de préparation d’une image de machine virtuelle personnalisée varient selon qu’il s’agit d’une machine virtuelle Windows ou Linux.


### <a name="windows"></a>[Windows](#tab/windows)

Effectuez les étapes suivantes pour créer une image de machine virtuelle Windows :

1. Créez une machine virtuelle Windows dans Azure. Pour obtenir les instructions du portail, consultez [Créer une machine virtuelle Windows dans le portail Azure](/azure/virtual-machines/windows/quick-create-portal). Pour obtenir des instructions PowerShell, consultez [Tutoriel : Créer et gérer des machines virtuelles Windows avec Azure PowerShell](../virtual-machines/windows/tutorial-manage-vm.md).

   La machine virtuelle doit être une machine virtuelle de génération 1. Le disque de système d’exploitation que vous utilisez pour créer votre image de machine virtuelle doit être un disque dur virtuel dont la taille fixe est prise en charge par Azure. Pour les options de taille de machine virtuelle, consultez [Tailles de machines virtuelles prises en charge](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).  

2. Généralisez la machine virtuelle. Pour généraliser la machine virtuelle, [connectez-vous à la machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm), ouvrez une invite de commandes, puis exécutez la commande `sysprep` suivante :

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > Une fois la commande terminée, la machine virtuelle s’arrête. **Ne redémarrez pas la machine virtuelle.** Le redémarrage de la machine virtuelle endommagera le disque que vous venez de préparer.


### <a name="linux"></a>[Linux](#tab/linux)

Effectuez les étapes suivantes pour créer une image de machine virtuelle Linux :

1. Créer une machine virtuelle Linux dans Azure. Pour obtenir les instructions du portail, consultez [Démarrage rapide : Créer une machine virtuelle Linux dans le portail Azure](../virtual-machines/linux/quick-create-portal.md).  Pour obtenir des instructions PowerShell, consultez [Démarrage rapide : Créer une machine virtuelle Linux dans Azure avec PowerShell](../virtual-machines/linux/quick-create-powershell.md).

   Vous pouvez utiliser n’importe quelle machine virtuelle Gen1 dotée d’un disque dur virtuel de taille fixe dans la Place de marché Azure pour créer des images personnalisées Linux, à l’exception des images Red Hat Enterprise Linux (RHEL) qui nécessitent des étapes supplémentaires. Pour obtenir la liste des images appropriées de la Place de Marché Azure, consultez [Éléments disponibles sur la Place de Marché Azure pour Azure Stack Hub](/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1910&preserve-view=true). Pour obtenir des conseils sur les images RHEL, consultez [Utilisation des images RHEL BYOS](#using-rhel-byos-images), ci-dessous. 

1. Annulez l’approvisionnement de la machine virtuelle. Utilisez l’agent de machine virtuelle Azure pour supprimer les fichiers et les données propres à la machine. Utilisez la commande `waagent` avec le paramètre `-deprovision+user` sur votre machine virtuelle Linux source. Pour plus d’informations, consultez [Comprendre et utiliser l’agent Linux Azure](../virtual-machines/extensions/agent-linux.md).

    1. [Connectez-vous à votre machine virtuelle Linux avec un client SSH](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm).
    2. Dans la fenêtre SSH, entrez la commande suivante :
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > Exécutez uniquement cette commande sur une machine virtuelle que vous allez capturer en tant qu’image. Cette commande ne garantit pas que l’image est exempte de toute information sensible ou qu’elle convient pour la redistribution. Le paramètre `+user` supprime également le dernier compte d’utilisateur provisionné. Pour garder les informations d’identification du compte d’utilisateur dans la machine virtuelle, utilisez uniquement `-deprovision`.
     
    3. Tapez **Y** pour continuer. Vous pouvez ajouter le paramètre `-force` pour éviter cette étape de confirmation.
    4. Une fois la commande exécutée, entrez **exit** pour fermer le client SSH.  La machine virtuelle est toujours en cours d’exécution à ce stade.


### <a name="using-rhel-byos-images"></a>Utilisation d’images RHEL BYOS

Si vous utilisez des images Red Hat Enterprise Linux (RHEL), seules les images RHEL BYOS (Red Hat Enterprise Linux Bring Your Own Subscription), également appelées images Red Hat Gold, sont prises en charge et peuvent être utilisées pour créer votre image de machine virtuelle. Sur la Place de marché Azure, les images RHEL standard avec paiement à l’utilisation ne sont pas prises en charge sur Azure Stack Edge.

Pour créer une image de machine virtuelle à l’aide de l’image RHEL BYOS, suivez ces étapes :

1. Connectez-vous à [Red Hat Subscription Management](https://access.redhat.com/management) (Gestion des abonnements Red Hat). Accédez à [Cloud Access Dashboard](https://access.redhat.com/management/cloud) (Tableau de bord Cloud Access) à partir de la barre de menus supérieure.
1. Activez votre abonnement Azure. Consultez les [Instructions détaillées](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access). L’activation de l’abonnement vous permet d’accéder aux images Red Hat Gold.

1. Acceptez les conditions d’utilisation Azure (une seule fois par abonnement Azure et par image), et provisionnez une machine virtuelle. Consultez les [instructions](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access). 

Vous pouvez désormais utiliser la machine virtuelle que vous avez provisionnée pour [Créer une image de machine virtuelle personnalisée](#create-a-custom-vm-image) dans Linux.

---

## <a name="download-os-disk-to-storage-account"></a>Télécharger le disque de système d’exploitation dans le compte de stockage

Si vous voulez utiliser votre image de machine virtuelle personnalisée pour déployer des machines virtuelles sur votre appareil, vous devez télécharger le disque de système d’exploitation dans un compte de stockage Azure. Nous vous recommandons d’utiliser le même compte de stockage que celui que vous avez utilisé pour votre appareil.

Pour télécharger le disque du système d’exploitation de la machine virtuelle dans un compte de stockage Azure, procédez comme suit :

1. [Arrêtez la machine virtuelle dans le portail](/azure/virtual-machines/windows/download-vhd#stop-the-vm). Vous devez le faire pour libérer le disque de système d’exploitation, même si votre machine virtuelle Windows a été arrêtée après votre exécution de `sysprep` pour la généraliser.

1. [Générez une URL de téléchargement pour le disque de système d’exploitation](/azure/virtual-machines/windows/download-vhd#generate-download-url) et prenez note de cette URL. Par défaut, l’URL expire au bout de 3 600 secondes (1 heure). Vous pouvez augmenter ce délai si nécessaire.
      
1. Téléchargez le disque dur virtuel dans votre compte de stockage Azure à l’aide de l’une des méthodes suivantes :
   
   - Méthode 1 : pour un transfert plus rapide, utilisez AzCopy et copiez le disque dur virtuel dans votre compte de stockage Azure. Pour obtenir des instructions, consultez la section [Utiliser AzCopy pour copier une image de machine virtuelle dans un compte de stockage](#copy-vhd-to-storage-account-using-azcopy), ci-dessous. 

   - Méthode 2 : pour une méthode simple, en un clic, vous pouvez sélectionner **Télécharger le fichier de disque dur virtuel** lorsque vous générez une URL de téléchargement (à l’étape 3b) et ainsi télécharger le disque à partir du portail. **Lorsque vous utilisez cette méthode, la copie du disque peut prendre un certain temps, et vous devrez [charger le disque dur virtuel sur votre compte de stockage Azure](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload) pour pouvoir créer des machines virtuelles à l’aide du portail.**

Vous pouvez à présent utiliser ce disque dur virtuel pour créer et déployer des machines virtuelles sur votre appareil Azure Stack Edge Pro avec GPU.

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>Copier le disque dur virtuel dans le compte de stockage avec AzCopy

Les procédures suivantes décrivent l’utilisation d’AzCopy pour copier une image de machine virtuelle personnalisée dans un compte de stockage Azure, afin que vous puissiez utiliser l’image dans le déploiement de machines virtuelles sur votre appareil Azure Stack Edge Pro avec GPU. Nous vous recommandons de stocker vos images de machines virtuelles personnalisées dans le même compte de stockage que celui que vous utilisez pour votre appareil Azure Stack Edge Pro avec GPU. 


### <a name="create-target-uri-for-a-container"></a>Créer un URI cible pour un conteneur

AzCopy a besoin d’un *URI cible* qui indique où copier la nouvelle image dans votre compte de stockage. Avant d’exécuter AzCopy, vous allez générer une URL de signature d’accès partagé (SAS) pour le conteneur d’objets blob dans lequel vous souhaitez copier le fichier. Pour créer l’URI cible, vous devez ajouter le nom du fichier à l’URL SAS.

Pour créer l’URI cible de votre disque dur virtuel préparé, effectuez les étapes suivantes :

1. Générez une URL SAS d’un conteneur dans un compte de stockage Azure en procédant comme suit :

   1. Dans le portail Azure, ouvrez le compte de stockage, puis sélectionnez **Conteneurs**. Sélectionnez le conteneur d’objets blob que vous souhaitez utiliser, puis cliquez dessus avec le bouton droit pour sélectionner **Générer une signature d’accès partagé**.

      ![Capture d’écran de l’option Générer une signature d’accès partagé d’un conteneur d’objets blob dans le portail Azure](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. Dans l’écran **Générer une signature d’accès partagé**, sélectionnez les **Autorisations** **Lecture** et **Écriture**.  

      ![Capture d’écran de l’écran Générer une signature d’accès partagé, avec les autorisations de lecture et d’écriture sélectionnées](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. Sélectionnez **Générer une URL et un jeton SAS**, puis **Copier** pour copier l’**URL SAS d’objet blob**.

      ![Capture de l’écran Générer une signature d’accès partagé, avec les options de génération et de copie d’une URL SAS d’objet blob](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. Pour créer l’URI cible de la commande `azcopy`, ajoutez le nom de fichier souhaité à l’URL SAS.

   L’URL SAS d’objet blob présente le format suivant. 

   ![Représentation d’une URL SAS d’objet blob, avec le chemin du conteneur et l’emplacement où insérer le nouveau nom de fichier étiqueté](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   Insérez le nom de fichier au format `/<filename>.vhd` devant le point d’interrogation qui commence la chaîne de requête. L’extension du nom de fichier doit être VHD. 

   Par exemple, l’URL SAS d’objet blob suivante copie le fichier **osdisk.vhd** sur le conteneur d’objets blob **virtualmachines** dans **mystorageaccount**.

   ![Représentation d’un exemple d’URL SAS d’objet blob pour un disque dur virtuel nommé osdisk](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>Copier le disque dur virtuel dans le conteneur d’objets blob

Pour copier votre disque dur virtuel dans un conteneur d’objets blob à l’aide d’AzCopy, effectuez les étapes suivantes :

 1. [Téléchargez AZCopy](/azure/storage/common/storage-use-azcopy-v10#download-azcopy) si vous ne l’avez pas déjà fait.
 
 1. Dans PowerShell, accédez au répertoire où vous avez stocké azcopy.exe, puis exécutez la commande suivante :

    `.\azcopy copy <source URI> <target URI> --recursive`

    où :
    * `<source URI>` est l’URL de téléchargement que vous avez générée précédemment.
    * `<target URI>` indique le conteneur d’objets blob sur lequel copier la nouvelle image dans votre compte de stockage Azure. Pour obtenir des instructions, consultez [Utiliser AzCopy pour copier une image de machine virtuelle dans un compte de stockage](#copy-vhd-to-storage-account-using-azcopy).

    Par exemple, l’URI suivant copie un fichier nommé **windowsosdisk.vhd** sur le conteneur d’objets blob des **machines virtuelles** dans le compte de stockage **mystorageaccount** :

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>Exemple de sortie

Pour l’exemple de commande AzCopy ci-dessus, la sortie suivante indique qu’une copie a été effectuée correctement.

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>Étapes suivantes

- [Déployer des machines virtuelles sur votre appareil à l'aide du portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Déployer des machines virtuelles sur votre appareil via PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)
