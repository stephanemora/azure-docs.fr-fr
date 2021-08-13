---
title: Vue d'ensemble des machines virtuelles exécutées sur votre appareil Azure Stack Edge
description: Décrit les machines virtuelles exécutées sur votre appareil Azure Stack Edge
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/28/2021
ms.author: alkohli
ms.openlocfilehash: 775cb6f7cfc19b0009fc92cc5afbd3ac383b3ea3
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110663856"
---
# <a name="virtual-machines-on-your-azure-stack-edge-pro-gpu-device"></a>Machines virtuelles exécutées sur votre appareil Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Cet article fournit une brève vue d'ensemble des machines virtuelles exécutées sur vos appareils Azure Stack Edge, présente les tailles de machine virtuelle prises en charge, et résume les différentes méthodes disponibles pour créer des images de machine virtuelle, les déployer, et les gérer. 

## <a name="about-vms"></a>À propos des machines virtuelles

La solution Azure Stack Edge fournit des appareils HaaS (Hardware-as-a-Service) de Microsoft qui permettent de déployer des charges de travail Edge Computing et de bénéficier rapidement d'insights exploitables en périphérie, là où les données sont générées. 

En fonction de votre environnement et du type d'applications que vous exécutez, vous pouvez déployer l'une des charges de travail Edge Computing suivantes sur ces appareils : 

- **Charges de travail conteneurisées** : utilisez IoT Edge ou Kubernetes pour exécuter vos applications conteneurisées.
- **Charges de travail non conteneurisées** : déployez des machines virtuelles Windows et Linux sur vos appareils pour exécuter des applications non conteneurisées. 

Vous déployez une machine virtuelle sur votre appareil lorsque vous avez besoin d'un contrôle accru sur l'environnement informatique. Vous pouvez utiliser des machines virtuelles sur votre appareil de plusieurs manières, du développement et des tests jusqu'à l'exécution d'applications en périphérie.

## <a name="before-you-create-a-vm"></a>Avant de créer une machine virtuelle

Avant de commencer, passez en revue les considérations suivantes :

- La taille de la machine virtuelle que vous utiliserez.
- Le nombre maximum de machines virtuelles qui peuvent être créées sur votre appareil.
- Le système d’exploitation exécuté par la machine virtuelle.
- La configuration de la machine virtuelle après son démarrage.


### <a name="vm-size"></a>Taille de la machine virtuelle

Avant tout déploiement de machines virtuelles, vous devez prendre connaissance des tailles disponibles. Différentes tailles sont disponibles pour les machines virtuelles chargées d'exécuter des applications et des charges de travail sur votre appareil. La taille que vous choisissez détermine ensuite des facteurs comme la puissance de traitement, la mémoire et la capacité de stockage. Pour plus d'informations, consultez [Machines virtuelles prises en charge](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes).

Pour déterminer la taille et le nombre de machines virtuelles que vous pouvez déployer, tenez compte de la capacité de calcul utilisable sur votre appareil et des autres charges de travail que vous exécutez. Si vous exécutez Kubernetes, tenez également compte des besoins en calcul des machines virtuelles Kubernetes Master et Worker.

|Type de machine virtuelle Kubernetes|Configuration requise en termes de mémoire et de processeur|
|---------|---------|
|Machine virtuelle Master|4 cœurs, 4 Go de RAM|
|Machine virtuelle Worker|12 cœurs, 32 Go de RAM|


Pour connaître les capacités de calcul et de mémoire utilisables sur votre appareil, consultez les [Spécifications liées au calcul et à la mémoire](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) pour votre modèle d'appareil. 


### <a name="vm-limits"></a>Limites des machines virtuelles

Vous pouvez exécuter un maximum de 24 machines virtuelles sur votre appareil. Il s'agit également d'un facteur à prendre en compte lors du déploiement de votre charge de travail.

### <a name="operating-system-disks-and-images"></a>Images et disques du système d’exploitation

Sur votre appareil, vous ne pouvez utiliser que des machines virtuelles de génération 1 avec un format de disque dur virtuel (VHD) fixe. Les disques durs virtuels sont utilisés pour stocker le système d'exploitation et les données. Les disques durs virtuels sont également utilisés pour les images que vous employez pour installer un système d’exploitation. 

Les images que vous utilisez pour créer des images de machine virtuelle peuvent être généralisées ou spécialisées. Lorsque vous créez des images pour vos machines virtuelles, vous devez les préparer. Découvrez les différentes méthodes de préparation et d'utilisation des images de machine virtuelle sur votre appareil :

- [Préparer une image généralisée de Windows à partir d'un disque dur virtuel](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
- [Préparer une image généralisée à partir d'un fichier ISO](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
- [Créer des images de machine virtuelle personnalisées à partir d'une machine virtuelle Azure](azure-stack-edge-gpu-create-virtual-machine-image.md)
- [Utiliser une image spécialisée](azure-stack-edge-gpu-deploy-vm-specialized-image-powershell.md)

### <a name="extensions"></a>Extensions

Des extensions de script personnalisé sont disponibles pour les machines virtuelles sur votre appareil. Celles-ci vous permettent de configurer les charges de travail en exécutant votre script au moment de l'approvisionnement de la machine virtuelle.

Pour plus d'informations, consultez [Déployer des extensions de script personnalisé sur des machines virtuelles exécutées sur votre appareil](azure-stack-edge-gpu-deploy-virtual-machine-custom-script-extension.md).

Vous pouvez également utiliser des extensions GPU pour votre machine virtuelle si vous souhaitez installer des pilotes GPU au moment de l'approvisionnement des machines virtuelles GPU. Pour plus d'informations, consultez [Créer des machines virtuelles GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms) et [Installer des extensions GPU](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md).

## <a name="create-a-vm"></a>Créer une machine virtuelle

Pour déployer une machine virtuelle, vous devez d'abord générer toutes les ressources nécessaires à la création d'une machine virtuelle. Quelle que soit la méthode utilisée pour créer une machine virtuelle, procédez comme suit : 

1. Connectez-vous à Azure Resource Manager sur votre appareil. 
1. Identifiez l'abonnement intégré sur l'appareil.
1. Apportez votre propre image de machine virtuelle.
    1. Créez un groupe de ressources dans l'abonnement intégré. Le groupe de ressources contiendra la machine virtuelle et toutes les ressources associées.
    2. Créez un compte de stockage local sur l'appareil afin de stocker le disque dur virtuel qui sera utilisé pour créer une image de machine virtuelle.
    3. Chargez une image source Windows/Linux sur le compte de stockage afin de créer un disque managé.
    4. Utilisez le disque managé pour créer une image de machine virtuelle.
1. Activez le calcul sur un port de l'appareil afin de créer un commutateur virtuel.
    1. Cela crée un réseau virtuel à l'aide du commutateur virtuel attaché au port sur lequel vous avez activé le calcul.  
1. Créez une machine virtuelle à l'aide de l'image de machine virtuelle, du réseau virtuel et des interfaces de réseau virtuel créés précédemment pour communiquer au sein du réseau virtuel, et attribuez une adresse IP publique pour accéder à distance à la machine virtuelle. Vous pouvez également inclure des disques de données pour fournir davantage de stockage à votre machine virtuelle.
 
Le workflow du déploiement est affiché dans le diagramme suivant :

![Diagramme du workflow de déploiement d’une machine virtuelle.](media/azure-stack-edge-gpu-deploy-virtual-machine-powershell/vm-workflow-r.svg)

Il existe plusieurs façons de déployer une machine virtuelle sur votre appareil. Votre choix dépend de votre environnement. Le tableau suivant récapitule les différentes méthodes disponibles pour déployer une machine virtuelle sur votre appareil :

|Méthode|Article|
|---------|---------|
|Dans le portail Azure|[Déployer une machine virtuelle sur votre appareil via le portail Azure](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)|
|Modèles|[Déployer une machine virtuelle sur votre appareil à l'aide de modèles](azure-stack-edge-gpu-deploy-virtual-machine-templates.md)|
|PowerShell|[Déployer une machine virtuelle sur votre appareil à l'aide de cmdlets Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)<br>[Déployer une machine virtuelle sur votre appareil par le biais d'un script Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell-script.md)|
|CLI/Python|[Déployer une machine virtuelle sur votre appareil via Azure CLI/Python](azure-stack-edge-gpu-deploy-virtual-machine-cli-python.md)|
|GPU|[Déployer une machine virtuelle sur votre appareil à l'aide de GPU](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)|


## <a name="manage-your-vm"></a>Gérer votre machine virtuelle

Pour gérer les machines virtuelles exécutées sur votre appareil, vous pouvez utiliser le portail Azure, l'interface PowerShell de l'appareil ou les API. Voici quelques tâches de gestion classiques :

- Obtenir des informations sur une machine virtuelle
- Se connecter à une machine virtuelle ; démarrer, arrêter et supprimer des machines virtuelles
- Gérer les disques, les tailles de machine virtuelle, les interfaces réseau, les commutateurs virtuels
- Sauvegarder des disques de machine virtuelle

### <a name="get-information-about-your-vm"></a>Obtenir des informations sur votre machine virtuelle

Pour obtenir plus d'informations sur votre machine virtuelle via le portail Azure, procédez comme suit :

1. Accédez à la ressource Azure Stack Edge de votre appareil, puis à **Machines virtuelles > Vue d'ensemble**. 
1. Sur la page **Vue d'ensemble**, accédez à **Machines virtuelles** et sélectionnez la machine virtuelle qui vous intéresse. Vous pouvez alors consulter les informations relatives à cette machine virtuelle. 

### <a name="connect-to-your-vm"></a>Connexion à votre machine virtuelle

Selon le système d'exploitation exécuté par votre machine virtuelle, vous pouvez vous connecter à celle-ci comme suit : 

- [Se connecter à une machine virtuelle Windows exécutée sur un appareil](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-windows-vm)
- [Se connecter à une machine virtuelle Linux exécutée sur un appareil](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#connect-to-linux-vm)

### <a name="start-stop-delete-vms"></a>Démarrer, arrêter et supprimer des machines virtuelles

Vous pouvez [activer la machine virtuelle](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#turn-on-the-vm), mais aussi [la suspendre ou l'arrêter](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#suspend-or-shut-down-the-vm). Enfin, vous pouvez [supprimer les machines virtuelles](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#delete-the-vm) lorsque vous avez fini de les utiliser.

### <a name="manage-network-interfaces-virtual-switches"></a>Gérer les interfaces réseau et les commutateurs virtuels

Vous pouvez [ajouter, modifier et détacher des interfaces réseau](azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal.md) pour vos machines virtuelles. Vous pouvez également [créer des commutateurs virtuels](azure-stack-edge-gpu-create-virtual-switch-powershell.md) sur votre appareil afin de déployer des machines virtuelles. 

### <a name="manage-data-disks-vm-size"></a>Gérer les disques de données et la taille de machine virtuelle

Vous pouvez ajouter un [disque de données à une machine virtuelle existante](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk), [attacher un disque existant](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#add-a-data-disk), [détacher un disque de données](azure-stack-edge-gpu-manage-virtual-machine-disks-portal.md#detach-a-data-disk) et enfin [redimensionner la machine virtuelle](azure-stack-edge-gpu-manage-virtual-machine-resize-portal.md#resize-a-vm) proprement dite via le portail Azure.

### <a name="back-up-vms"></a>Sauvegarder des machines virtuelles

Vous pouvez sauvegarder les disques des machines virtuelles et, en cas de panne d'un appareil, restaurer les données à partir des sauvegardes. Pour plus d'informations, consultez [Sauvegarder des disques de machine virtuelle](azure-stack-edge-gpu-back-up-virtual-machine-disks.md).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les [Tailles et types de machines virtuelles pour Azure Stack Edge Pro GPU](azure-stack-edge-gpu-virtual-machine-sizes.md).


