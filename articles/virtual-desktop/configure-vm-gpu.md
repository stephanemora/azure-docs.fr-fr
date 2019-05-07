---
title: Configurer le GPU pour Preview liée à un bureau virtuel Windows - Azure
description: Comment activer le rendu et l’encodage en version préliminaire de bureau virtuel Windows avec accélération GPU.
services: virtual-desktop
author: gundarev
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 05/06/2019
ms.author: denisgun
ms.openlocfilehash: a6a67c89253a1b16f9266d7917655d1b1104022e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65159569"
---
# <a name="configure-graphics-processing-unit-gpu-acceleration-for-windows-virtual-desktop-preview"></a>Configurer l’accélération graphique (GPU) pour Windows Virtual Desktop Preview de traitement graphique

Windows Virtual Desktop Preview prend en charge avec accélération GPU de rendu et un encodage pour les performances des applications améliorée et l’évolutivité. Accélération GPU est particulièrement importante pour les applications graphiques.

Suivez les instructions de cet article pour créer une machine virtuelle GPU optimisé, ajoutez-le à votre pool de l’hôte et configurez-le pour utiliser l’accélération GPU pour le codage et de rendu. Cet article suppose que vous avez déjà un client de bureau virtuel Windows configuré.

## <a name="select-a-gpu-optimized-azure-virtual-machine-size"></a>Sélectionnez une taille de machine virtuelle GPU optimisé

Azure offre un nombre de [tailles de machine virtuelle au GPU optimisé](/azure/virtual-machines/windows/sizes-gpu). Le bon choix pour votre pool hôte dépend de plusieurs facteurs, y compris vos charges de travail application particulière, de la qualité souhaités de l’expérience utilisateur et le coût. En général, plus volumineuse et plus performante GPU offrent une meilleure expérience utilisateur à une densité de l’utilisateur donné.

## <a name="create-a-host-pool-provision-your-virtual-machine-and-configure-an-app-group"></a>Créer un pool de l’hôte, approvisionner votre machine virtuelle et configurer un groupe de l’application

Créer un nouveau pool d’hôte à l’aide d’une machine virtuelle de la taille que vous avez sélectionné. Pour obtenir des instructions, consultez [didacticiel : Créer un pool de l’hôte avec la place de marché Azure](/azure/virtual-desktop/create-host-pools-azure-marketplace).

Windows Virtual Desktop Preview prend en charge avec accélération GPU de rendu et de codage dans les systèmes d’exploitation suivants :

* Windows 10 version 1511 ou ultérieure
* Windows Server 2016 ou version ultérieure

Vous devez également configurer un groupe de l’application, ou utiliser le groupe d’application de bureau de par défaut (nommé « Groupe d’applications de bureau ») est créé automatiquement lorsque vous créez un nouveau pool d’hôte. Pour obtenir des instructions, consultez [didacticiel : Gérer des groupes d’applications pour Windows Virtual Desktop Preview](/azure/virtual-desktop/manage-app-groups).

>[!NOTE]
>Windows Virtual Desktop Preview prend uniquement en charge le type de groupe d’application « Desktop » pour les pools d’hôte prenant en charge GPU. Groupes d’applications de type « RemoteApp » ne sont pas pris en charge pour les pools d’hôte prenant en charge GPU.

## <a name="install-supported-graphics-drivers-in-your-virtual-machine"></a>Installer des pilotes graphiques pris en charge dans votre machine virtuelle

Pour tirer parti des fonctionnalités GPU des machines virtuelles série N Azure en version préliminaire de Windows Virtual Desktop, vous devez installer des pilotes graphiques NVIDIA. Suivez les instructions de [pilotes d’installer les GPU NVIDIA sur des machines virtuelles série N exécutant Windows](/azure/virtual-machines/windows/n-series-driver-setup) pour installer des pilotes, soit manuellement ou à l’aide de la [Extension du pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows).

Notez que seul [pilotes NVIDIA GRID](/azure/virtual-machines/windows/n-series-driver-setup#nvidia-grid-drivers) distribués par Azure sont pris en charge pour Windows Virtual Desktop Preview.

Après l’installation du pilote, un redémarrage de la machine virtuelle est nécessaire. Utilisez les étapes de vérification dans les instructions ci-dessus pour confirmer que les pilotes graphiques ont été correctement installés.

## <a name="configure-gpu-accelerated-app-rendering"></a>Configurer le rendu de l’application avec accélération GPU

Par défaut, les applications et les postes de travail en cours d’exécution dans des configurations de sessions multiples sont rendues avec le processeur et n’exploitent pas GPU disponibles pour le rendu. Configurer la stratégie de groupe pour l’hôte de session activer le rendu avec accélération GPU :

1. Se connecter au bureau de la machine virtuelle à l’aide d’un compte avec des privilèges d’administrateur local.
2. Ouvrez le début menu, puis tapez « gpedit.msc » pour ouvrir l’éditeur de stratégie de groupe.
3. Naviguez dans l’arborescence à **Configuration ordinateur** > **modèles d’administration** > **les composants Windows**  >   **Services Bureau à distance** > **hôte de Session Bureau à distance** > **environnement de Session à distance**.
4. Sélectionnez la stratégie **utiliser l’adaptateur de graphiques de matériel par défaut pour toutes les sessions de Services Bureau à distance** et définir cette stratégie sur **activé** pour activer le rendu GPU dans la session à distance.

## <a name="configure-gpu-accelerated-frame-encoding"></a>Configurer l’encodage du frame avec accélération GPU

Bureau à distance encode tous les graphiques sont rendus par les applications et les postes de travail (si le rendu avec GPU ou avec les UC) pour la transmission aux clients de bureau à distance. Par défaut, le Bureau à distance n’exploite pas GPU disponibles pour cet encodage. Configurer la stratégie de groupe pour l’hôte de session activer le codage avec accélération GPU de frame. Continuer les étapes ci-dessus :

1. Sélectionnez la stratégie **mode hiérarchiser h.264/AVC 444 graphiques pour les connexions Bureau à distance** et définir cette stratégie sur **activé** pour forcer les codec h.264/AVC 444 dans la session à distance.
2. Sélectionnez la stratégie **h.264/AVC de configurer matériel encodage pour les connexions Bureau à distance** et définir cette stratégie sur **activé** pour activer le codage de matériel pour AVC/H.264 dans la session à distance.

    >[!NOTE]
    >Dans Windows Server 2016, définissez l’option **préférez un encodage de matériel AVC** à **toujours essayer**.

3. Maintenant que les stratégies de groupe ont été modifiées, forcer une mise à jour de stratégie de groupe. Ouvrez l’invite de commandes et tapez :

    ```batch
    gpupdate.exe /force
    ```

4. Se déconnecter de la session Bureau à distance.

## <a name="verify-gpu-accelerated-app-rendering"></a>Vérifier le rendu de l’application avec accélération GPU

Pour vérifier que les applications utilisent le GPU pour le rendu, essayez l’une des opérations suivantes :

* Utilisez le `nvidia-smi` utilitaire comme décrit dans [vérifier l’installation du pilote](/azure/virtual-machines/windows/n-series-driver-setup#verify-driver-installation) pour vérifier l’utilisation du GPU lorsque vos applications en cours d’exécution.
* Dans les versions de système d’exploitation pris en charge, vous pouvez utiliser le Gestionnaire des tâches pour vérifier l’utilisation du GPU. Sélectionnez le GPU dans l’onglet « Performances » pour voir si les applications utilisent le GPU.

## <a name="verify-gpu-accelerated-frame-encoding"></a>Vérifiez le codage avec accélération GPU de frame

Pour vérifier que le Bureau à distance est à l’aide de codage avec accélération GPU :

1. Se connecter au bureau de la machine virtuelle à l’aide du client de bureau virtuel Windows.
2. Lancer l’Observateur d’événements et accédez au nœud suivant : **Journaux des applications et Services** > **Microsoft** > **Windows** > **RemoteDesktopServices-RdpCoreTS**  >  **Opérationnelle**
3. Pour déterminer si de codage avec accélération GPU est utilisé, recherchez l’événement ID 170. Si vous voyez « encodeur de matériel AVC activé : 1 » puis l’encodage du GPU est utilisé.
4. Pour déterminer si le mode AVC 444 est utilisé, recherchez l’événement ID 162. Si vous voyez « AVC disponible : 1 profil initial : 2048" AVC 444 est utilisé.

## <a name="next-steps"></a>Étapes suivantes

Ces instructions doivent avoir vous en cours d’exécution avec accélération GPU sur une machine virtuelle d’hôte de session unique. Certaines considérations supplémentaires pour l’activation de l’accélération GPU sur un plus grand pool hôte :

* Envisagez d’utiliser le [Extension du pilote GPU NVIDIA](/azure/virtual-machines/extensions/hpccompute-gpu-windows) pour simplifier l’installation du pilote et des mises à jour sur un nombre de machines virtuelles.
* Envisagez d’utiliser la stratégie de groupe Active Directory pour simplifier la configuration de stratégie de groupe sur un nombre de machines virtuelles. Pour plus d’informations sur le déploiement de stratégie de groupe dans le domaine Active Directory, consultez [utilisation des objets de stratégie de groupe](https://go.microsoft.com/fwlink/p/?LinkId=620889).
