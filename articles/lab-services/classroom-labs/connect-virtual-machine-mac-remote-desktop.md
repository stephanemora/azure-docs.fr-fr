---
title: Guide pratique pour se connecter à une machine virtuelle Azure Lab Services à partir de Mac | Microsoft Docs
description: Découvrez comment vous connecter à partir d’un Mac à une machine virtuelle dans Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704364"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Se connecter à une machine virtuelle à l’aide du Protocole Bureau à distance sur un Mac
Cette section montre comment un étudiant peut se connecter à une machine virtuelle de labo de classe à partir d’un Mac en utilisant le protocole RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Installer Bureau à distance Microsoft sur un Mac
1. Ouvrez l’App Store sur votre Mac et recherchez **Bureau à distance Microsoft**.

    ![Bureau à distance Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Installez la dernière version de Bureau à distance Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Accéder à la machine virtuelle à partir de votre Mac à l’aide de RDP
1. Ouvrez le fichier **RDP** qui est téléchargé sur votre ordinateur lorsque **Bureau à distance Microsoft** est installé. Il doit commencer à se connecter à la machine virtuelle. 

    ![Se connecter à une machine virtuelle](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Sélectionnez **Continuer** si vous recevez l’avertissement suivant. 

    ![Avertissement de certificat](../media/how-to-use-classroom-lab/certificate-error.png)
1. Vous devez voir la machine virtuelle. 

    > [!NOTE]
    > L’exemple suivant correspond à une machine virtuelle CentOS Linux. 

    ![Machine virtuelle](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment se connecter à des machines virtuelles Linux à l’aide du protocole RDP, consultez [Utiliser le Bureau à distance pour les machines virtuelles Linux](how-to-use-remote-desktop-linux-student.md)


