---
title: Activer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services | Microsoft Docs
description: Découvrez comment créer un modèle de machine virtuelle contenant plusieurs machines virtuelles.  En d’autres termes, activez la virtualisation imbriquée sur un modèle de machine virtuelle dans Azure Lab Services.
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
ms.openlocfilehash: 64097a5b3b62bcd5a84f4472a844bb95cf24cd6f
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555082"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Activer la virtualisation imbriquée pour une machine virtuelle modèle dans Azure Lab Services

Actuellement, Azure Lab Services vous permet de configurer un seul modèle de machine virtuelle dans un labo et de rendre disponible une seule copie pour chacun de vos utilisateurs. Si vous êtes professeur enseignant des classes de mise en réseau, de sécurité ou d’informatique, il peut être nécessaire de fournir à chacun de vos étudiants un environnement où plusieurs machines virtuelles peuvent communiquer entre elles sur un réseau.

La virtualisation imbriquée vous permet de créer un environnement avec plusieurs machines virtuelles au sein de la machine virtuelle d’un modèle de labo. La publication du modèle fournit à chaque utilisateur du labo une machine virtuelle configurée avec plusieurs machines virtuelles en son sein.  Cet article explique comment configurer la virtualisation imbriquée pour une machine modèle dans Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Qu’est-ce que la virtualisation imbriquée ?

La virtualisation imbriquée vous permet de créer des machines virtuelles au sein d’une machine virtuelle. La virtualisation imbriquée est effectuée via Hyper-V et elle est disponible seulement sur les machines virtuelles Windows.

Pour plus d’informations sur la visualisation imbriquée, consultez les articles suivants :

- [Virtualisation imbriquée dans Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Guide pratique pour activer la virtualisation imbriquée dans une machine virtuelle Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Considérations

Avant de configurer un laboratoire pour la virtualisation imbriquée, voici quelques éléments à prendre en compte.

- Lorsque vous créez un laboratoire, sélectionnez **Moyenne (virtualisation imbriquée)** ou **Grande (virtualisation imbriquée)** pour la taille de la machine virtuelle. Ces tailles de machine virtuelle prennent en charge la virtualisation imbriquée.
- Choisissez une taille qui fournira de bonnes performances pour les machines virtuelles clientes et les machines virtuelles hôtes.  N’oubliez pas que lorsque vous utilisez la virtualisation, la taille que vous choisissez doit être adaptée non seulement à la machine, mais également à l’hôte, ainsi qu’aux machines clientes qui doivent être exécutées simultanément.
- Les machines virtuelles clientes n’ont pas accès aux ressources Azure, telles que les serveurs DNS du réseau virtuel Azure.
- La machine virtuelle hôte nécessite que la configuration autorise la machine cliente à être connectée à Internet.
- Les machines virtuelles clientes sont concédées sous licence comme des machines indépendantes. Pour plus d’informations sur les licences des systèmes d’exploitation et des produits Microsoft, consultez [Licences Microsoft](https://www.microsoft.com/licensing/default). Vérifiez les contrats de licence de tous les autres logiciels que vous utilisiez avant de configurer la machine modèle.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Activer la virtualisation imbriquée sur une machine virtuelle modèle

Cet article suppose que vous avez déjà créé un compte de labo et un labo.  Pour plus d’informations sur la création d’un compte de labo, consultez le [tutoriel Configurer un compte de labo](tutorial-setup-lab-account.md). Pour plus d’informations sur la création d’un labo, consultez le [tutoriel Configurer un labo de salle de classe](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Sélectionnez **Grande (virtualisation imbriquée)** ou **Moyenne (virtualisation imbriquée)** pour la taille de la machine virtuelle lorsque vous créez le labo.  Sinon, la virtualisation imbriquée ne fonctionnera pas.  

Pour vous connecter à la machine modèle, consultez [Créer et gérer un modèle de salle de classe](how-to-create-manage-template.md). 

Les étapes de cette section traitent de la configuration de la virtualisation imbriquée pour Windows Server 2016 ou Windows Server 2019. Vous allez utiliser un script pour configurer la machine modèle avec Hyper-V.  Les étapes suivantes vous guident tout au long de l’utilisation des [scripts Hyper-V Lab Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV).

1. Si vous utilisez Internet Explorer, vous devrez peut-être ajouter `https://github.com` à la liste des sites de confiance.
    1. Ouvrez Internet Explorer.
    1. Sélectionnez l’icône en forme de roue dentée, puis choisissez **Options Internet**.  
    1. Quand la boîte de dialogue **Options Internet** s’affiche, sélectionnez **Sécurité**, sélectionnez **Sites de confiance**, puis cliquez sur le bouton **Sites**.
    1. Quand la boîte de dialogue **Sites de confiance** s’affiche, ajoutez `https://github.com` à la liste des sites web de confiance, puis sélectionnez **Fermer**.

        ![Sites de confiance](../media/how-to-enable-nested-virtualization-template-vm/trusted-sites-dialog.png)
1. Téléchargez les fichiers du dépôt Git, comme indiqué dans les étapes suivantes.
    1. Accédez à  [https://github.com/Azure/azure-devtestlab/](https://github.com/Azure/azure-devtestlab/).
    1. Cliquez sur le bouton **Clone ou Download** (Cloner ou télécharger).
    1. Cliquez sur **Download ZIP** (Télécharger le fichier zip).
    1. Extrayez le fichier zip.

    >[!TIP]
    >Vous pouvez également cloner le référentiel Git à l’adresse [https://github.com/Azure/azure-devtestlab.git](https://github.com/Azure/azure-devtestlab.git).

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
