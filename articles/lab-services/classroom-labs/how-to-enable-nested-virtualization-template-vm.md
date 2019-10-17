---
title: Activer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services | Microsoft Docs
description: Découvrez comment activer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 9a52fd958d646af5edd09065e9acf95796c8ce33
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981933"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Activer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services
Cet article explique comment configurer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services. La virtualisation imbriquée est utilisée dans Azure Lab Services lorsque chaque étudiant a besoin de plusieurs machines.
 
## <a name="considerations"></a>Considérations
Avant de configurer un laboratoire pour la virtualisation imbriquée, voici quelques éléments à prendre en compte.

- Lorsque vous créez un laboratoire, sélectionnez **Moyenne (virtualisation imbriquée)** ou **Grande** pour la taille de la machine virtuelle. Ces tailles de machine virtuelle prennent en charge la virtualisation imbriquée. 
- Choisissez une taille qui fournira de bonnes performances pour les machines virtuelles clientes et les machines virtuelles hôtes.  N’oubliez pas que lorsque vous utilisez la virtualisation, la taille que vous choisissez doit être adaptée non seulement à la machine, mais également à l’hôte, ainsi qu’aux machines clientes qui doivent être exécutées simultanément.
- Les machines virtuelles clientes n’ont pas accès aux ressources Azure, telles que les serveurs DNS du réseau virtuel Azure.
- La machine virtuelle hôte nécessite que la configuration autorise la machine cliente à être connectée à Internet. 
- Les machines virtuelles clientes sont concédées sous licence comme des machines indépendantes. Pour plus d’informations sur les licences des systèmes d’exploitation et des produits Microsoft, consultez [Licences Microsoft](https://www.microsoft.com/licensing/default). Vérifiez les contrats de licence de tous les autres logiciels que vous utilisiez avant de configurer la machine modèle.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Activer la virtualisation imbriquée sur une machine virtuelle modèle
Les étapes de cette section traitent de la configuration de la virtualisation imbriquée pour Windows Server 2016 ou Windows Server 2019. Vous allez utiliser un script pour configurer la machine modèle avec Hyper-V. Pour une solution automatisée, consultez les scripts fournis dans [Lab Services Hyper-V Scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV). Les étapes suivantes vous indiquent comment utiliser ce script.

1. Si vous utilisez Internet Explorer, vous devrez peut-être ajouter `https://github.com` à la liste des sites de confiance. 
    1. Ouvrez Internet Explorer.
    1. Sélectionnez l’icône en forme de roue dentée, puis choisissez **Options Internet**.  
    1. Quand la boîte de dialogue **Options Internet** s’affiche, sélectionnez **Sécurité**, sélectionnez **Sites de confiance**, puis cliquez sur le bouton **Sites**.
    1. Quand la boîte de dialogue **Sites de confiance** s’affiche, ajoutez `https://github.com` à la liste des sites web de confiance, puis sélectionnez **Fermer**.

        ![Sites de confiance](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1.  Téléchargez les fichiers du dépôt Git, comme indiqué dans les étapes suivantes.  Vous pouvez également cloner le dépôt Git à partir de [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git). 
    1. Accédez à  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Cliquez sur le bouton ***Clone ou Download** (Cloner ou télécharger).
    1. Cliquez sur **Download ZIP** (Télécharger le fichier zip).
    1. Extrayez le fichier zip.
1. Lancez **Azure PowerShell** en mode **Administrateur**.
1. Dans la fenêtre PowerShell, accédez au dossier contenant le script téléchargé. Si vous partez du premier dossier des fichiers du dépôt, le script se trouve à l’emplacement `azure-devtestlab\samples\ClassroomLabs\Scripts\HyperV\`.
1. Vous devrez peut-être modifier la stratégie d’exécution pour exécuter le script. Exécutez la commande suivante :
    
    ```powershell
    Set-ExecutionPolicy bypass -force 
    ```
1. Exécutez le script :
    
    ```powershell
    .\SetupForNestedVirtualization.ps1
    ```

    > [!NOTE]
    > Le script peut nécessiter le redémarrage de la machine. Suivez les instructions du script, puis réexécutez-le jusqu’à ce que **Script completed** (Exécution du script terminée) s’affiche dans la sortie.
1. N’oubliez pas de réinitialiser la stratégie d’exécution. Exécutez la commande suivante : 

    ```powershell
    Set-ExecutionPolicy default -force
    ```

## <a name="conclusion"></a>Conclusion
Votre machine modèle est maintenant prête pour la création de machines virtuelles Hyper-V. Pour obtenir des instructions sur la création de machines virtuelles Hyper-V, consultez [Créer une machine virtuelle dans Hyper-V](/windows-server/virtualization/hyper-v/get-started/create-a-virtual-machine-in-hyper-v). Consultez également le [Centre d’évaluation Microsoft](https://www.microsoft.com/evalcenter/) pour voir les systèmes d’exploitation et les logiciels qui sont disponibles.  

## <a name="next-steps"></a>Étapes suivantes 
Consultez les articles suivants :

- [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md) 
- [Installer le rôle Hyper-V sur Windows Server](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server)
- [Scripts Hyper-V Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)
