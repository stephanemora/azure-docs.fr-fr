---
title: Restaurer un fichier à partir d’un instantané à l’aide d’un client avec Azure NetApp Files | Microsoft Docs
description: Décrit comment restaurer un fichier à partir d’un instantané à l’aide d’un client avec le volume monté à l’aide d’Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/16/2021
ms.author: b-juche
ms.openlocfilehash: db7336901ae349a6a50f71e2adf0102100344871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700532"
---
# <a name="restore-a-file-from-a-snapshot-using-a-client-with-azure-netapp-files"></a>Restaurer un fichier à partir d’un instantané à l’aide d’un client avec Azure NetApp Files

Les [instantanés](snapshots-introduction.md) autorisent la récupération jusqu’à une date et heure des volumes. Si vous ne souhaitez pas [restaurer l’intégralité de l’instantané sur un volume](snapshots-restore-new-volume.md), vous avez la possibilité de restaurer un fichier à partir d’un instantané à l’aide d’un client sur lequel le volume est monté.  

Le volume monté contient un répertoire d’instantanés nommé `.snapshot` (sur les clients NFS) ou `~snapshot` (sur les clients SMB) accessible au client. Le répertoire d’instantanés contient des sous-répertoires correspondant aux instantanés du volume. Chaque sous-répertoire contient les fichiers de l’instantané. Si vous supprimez ou remplacez accidentellement un fichier, vous pouvez le restaurer dans le répertoire de lecture-écriture parent en le copiant à partir d’un sous-répertoire d’instantanés et en le collant dans le répertoire de lecture-écriture. 

Vous pouvez contrôler l’accès aux répertoires d’instantanés avec l’option [Masquer le chemin des instantanés](snapshots-edit-hide-path.md). Cette option détermine si le répertoire doit être masqué pour les clients. Par conséquent, il contrôle également l’accès aux fichiers et aux dossiers des instantanés.  

NFS v4.1 n’affiche pas le répertoire `.snapshot` (`ls -la`). Toutefois, même lorsque l’option Masquer le chemin des instantanés n’est pas définie, vous pouvez accéder au répertoire `.snapshot` avec NFS v4.1 en exécutant la commande `cd <snapshot-path>` à partir de la ligne de commande du client. 

## <a name="restore-a-file-by-using-a-linux-nfs-client"></a>Restaurer un fichier à l’aide d’un client NFS Linux 

1. Utilisez la commande `ls` Linux pour lister le fichier que vous souhaitez restaurer à partir du répertoire `.snapshot`. 

    Par exemple :

    `$ ls my.txt`   
    `ls: my.txt: No such file or directory`   

    `$ ls .snapshot`   
    `daily.2020-05-14_0013/              hourly.2020-05-15_1106/`   
    `daily.2020-05-15_0012/              hourly.2020-05-15_1206/`   
    `hourly.2020-05-15_1006/             hourly.2020-05-15_1306/`   

    `$ ls .snapshot/hourly.2020-05-15_1306/my.txt`   
    `my.txt`

2. Utilisez la commande `cp` pour copier le fichier dans le répertoire parent.  

    Par exemple : 

    `$ cp .snapshot/hourly.2020-05-15_1306/my.txt .`   

    `$ ls my.txt`   
    `my.txt`   

## <a name="restore-a-file-by-using-a-windows-client"></a>Restaurer un fichier à l’aide d’un client Windows 

1. Si le répertoire `~snapshot` du volume est masqué, [affichez les éléments masqués](https://support.microsoft.com/help/4028316/windows-view-hidden-files-and-folders-in-windows-10) dans le répertoire parent de façon à afficher `~snapshot`.

    ![Capture d’écran montrant des éléments masqués d’un répertoire.](../media/azure-netapp-files/snapshot-show-hidden.png) 

2. Accédez au sous-répertoire dans `~snapshot` pour trouver le fichier à restaurer.  Cliquez avec le bouton droit sur le fichier. Sélectionnez **Copier**.  

    ![Capture d’écran montrant comment copier un fichier à restaurer.](../media/azure-netapp-files/snapshot-copy-file-restore.png) 

3. Revenez au répertoire parent. Cliquez avec le bouton droit dans le répertoire parent, puis sélectionnez `Paste` pour coller le fichier dans le répertoire.

    ![Capture d’écran montrant comment coller un fichier à restaurer.](../media/azure-netapp-files/snapshot-paste-file-restore.png) 

4. Vous pouvez également cliquer avec le bouton droit sur le répertoire parent, sélectionner **Propriétés**, cliquer sur l’onglet **Versions précédentes** pour voir la liste des instantanés, puis sélectionner **Restaurer** pour restaurer un fichier.  

    ![Capture d’écran montrant les versions précédentes des propriétés.](../media/azure-netapp-files/snapshot-properties-previous-version.png) 

## <a name="next-steps"></a>Étapes suivantes

* [En savoir plus sur les instantanés](snapshots-introduction.md) 
* [Limites des ressources pour Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Vidéo sur les instantanés Azure NetApp Files](https://www.youtube.com/watch?v=uxbTXhtXCkw)
* [Vue d’ensemble des instantanés Azure NetApp Files](https://anfcommunity.com/2021/01/31/azure-netapp-files-snapshot-overview/)