---
title: Configurer un disque Microsoft Azure Data Box | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à configurer un disque Azure Data Box
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143480"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Didacticiel : Déballer, connecter et déverrouiller un disque Azure Data Box

Ce didacticiel explique comment déballer, connecter et déverrouiller votre disque Azure Data Box.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Déballer votre disque Data Box
> * Connecter et déverrouiller le disque Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez terminé le [Didacticiel : Commander un disque Azure Data Box](data-box-disk-deploy-ordered.md).
2. Vous avez reçu vos disques et l’état de la commande dans le portail affiche **Delivered (Livrée)**.
3. Vous disposez d’un ordinateur hôte sur lequel vous pouvez installer l’outil de déverrouillage de disque Data Box. Votre ordinateur hôte doit
    - Exécuter un [système d’exploitation pris en charge](data-box-disk-system-requirements.md)
    - Avoir [Windows PowerShell 4 installé](https://www.microsoft.com/download/details.aspx?id=40855)
    - Avoir [.NET Framework 4.5.1 installé](https://www.microsoft.com/download/details.aspx?id=30653)
    - Avoir [BitLocker activé](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server)
    - Avoir [Windows Management Framework 4 installé](https://www.microsoft.com/en-us/download/details.aspx?id=40855) 

## <a name="unpack-your-disks"></a>Déballer vos disques

 Procédez comme suit pour déballer vos disques.

1. Les disques Data Box sont livrés dans une boîte d’envoi de petite taille. Ouvrez la boîte et sortez son contenu. Vérifiez que la boîte contient entre 1 à 5 disques SSD, ainsi qu’un câble de connexion USB par disque. Vérifiez que la boîte n’a pas été endommagée. 

    ![Boîte d’envoi de disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Si la boîte d’envoi est altérée ou gravement endommagée, ne l’ouvrez pas. Contactez le Support Microsoft pour savoir si vous pouvez utiliser les disques ou si Microsoft doit vous envoyer des disques de remplacement.
3. Vérifiez que la boîte contient une pochette transparente avec une étiquette d’expédition (sous l’étiquette actuelle) pour l’expédition de retour. Si cette étiquette a été perdue ou endommagée, vous pouvez en télécharger et en imprimer une nouvelle à partir du portail Azure. 

    ![Étiquette d’expédition de disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Conservez la boîte et le papier d’emballage pour renvoyer les disques ultérieurement.

## <a name="connect-and-unlock-your-disks"></a>Connecter et déverrouiller vos disques

Procédez comme suit pour connecter et déverrouiller vos disques.

1. Utilisez le câble fourni pour connecter le disque à un ordinateur Windows exécutant un système d’exploitation pris en charge tel qu’indiqué dans les prérequis. 

    ![Connexion du disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Dans le portail Azure, accédez à **Général > Détails de l’appareil**. 
3. Cliquez sur **Télécharger l’outil de déverrouillage de disque Data Box**. 

    ![Téléchargement de l’outil de déverrouillage de disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Extrayez l’outil sur l’ordinateur que vous allez utiliser pour copier les données.
5. Ouvrez une fenêtre d’invite de commandes ou exécutez Windows PowerShell en tant qu’administrateur sur le même ordinateur.
6. (Facultatif) Pour vérifier que l’ordinateur que vous utilisez pour déverrouiller le disque répond à la configuration requise de système d’exploitation, exécutez la commande de vérification système. Voici un exemple de sortie obtenue. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Dans le portail Azure, accédez à **Général > Détails de l’appareil**. Utilisez l’icône de copie pour copier la clé d’accès.
8. Exécutez `DataBoxDiskUnlock.exe` et indiquez la clé d’accès. La lettre de lecteur affectée au disque s’affiche. Voici un exemple de sortie obtenue.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Répétez les étapes 6 à 8 pour tout nouvel ajout de disque. Utilisez la commande d’aide si vous avez besoin d’aide concernant l’outil de déverrouillage de disque Data Box.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Une fois que le disque est déverrouillé, vous pouvez afficher son contenu.    

    ![Contenu du disque Data Box](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Déballer votre disque Data Box
> * Connecter et déverrouiller des disques Data Box


Passez au didacticiel suivant pour découvrir comment copier des données sur votre disque Data Box.

> [!div class="nextstepaction"]
> [Copier des données sur votre disque Data Box](./data-box-disk-deploy-copy-data.md)

