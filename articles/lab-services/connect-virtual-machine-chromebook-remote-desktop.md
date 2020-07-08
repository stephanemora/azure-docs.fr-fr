---
title: Guide pratique pour se connecter à une machine virtuelle Azure Lab Services à partir de Chromebook | Microsoft Docs
description: Découvrez comment vous connecter à partir d’un Chromebook à d’une machine virtuelle dans Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: d2a936a6acb6491c57cb1460c83ec0253561b102
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445659"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Se connecter à une machine virtuelle à l’aide du Protocole Bureau à distance sur un Chromebook
Cette section montre comment un étudiant peut se connecter à une machine virtuelle de labo de classe à partir d’un Chromebook en utilisant le protocole RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Installer Bureau à distance Microsoft sur un Chromebook
1. Ouvrez l’App Store sur votre Chromebook et recherchez **Bureau à distance Microsoft**.

    ![Bureau à distance Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Installez la dernière version de Bureau à distance Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Accédez à la machine virtuelle à partir de votre Chromebook à l’aide de RDP
1. Ouvrez le fichier **RDP** qui est téléchargé sur votre ordinateur lorsque **Bureau à distance Microsoft** est installé. Il doit commencer à se connecter à la machine virtuelle. 

    ![Se connecter à une machine virtuelle](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Entrez votre mot de passe lorsque vous y êtes invité.
    ![Se connecter à une machine virtuelle](./media/how-to-use-classroom-lab/password-chromebook.png)


1. Sélectionnez **Continuer** si vous recevez l’avertissement suivant. 

    ![Avertissement de certificat](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Vous devez voir le Bureau de la machine virtuelle à laquelle vous vous connectez.

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur la connexion aux machines virtuelles Linux, consultez [Se connecter à des machines virtuelles Linux](how-to-use-remote-desktop-linux-student.md)


