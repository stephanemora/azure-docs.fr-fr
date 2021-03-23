---
title: Utilisation de l’outil MSIXMGR – Azure
description: Guide pratique pour utiliser l’outil MSIXMGR pour Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 02/23/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4b34fb0d3bb2d49255007b9722a0a636c1441b8c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743088"
---
# <a name="using-the-msixmgr-tool"></a>Utilisation de l’outil MSIXMGR

L’outil MSIXMGR sert au développement d’applications empaquetées MSIX dans des images MSIX. L’outil utilise une application empaquetée par MSIX (.MSIX) et la développe dans un fichier VHD, VHDx ou CIM. L’image MSIX obtenue est stockée dans le compte Stockage Azure utilisé par votre déploiement Windows Virtual Desktop. Cet article vous explique comment utiliser l’outil MSIXMGR.

>[!NOTE]
>Pour garantir la compatibilité, assurez-vous que les modèles CIM qui stockent vos images MSIX sont générées sur la version du système d’exploitation que vous utilisez dans vos pools d’hôtes de Windows Virtual Desktop. MSIXMGR peut créer des fichiers CIM, mais vous ne pouvez utiliser ces fichiers qu’avec un pool d’hôtes exécutant Windows 10 20H2.

## <a name="requirements"></a>Configuration requise

Avant de suivre les instructions de cet article, vous devez effectuer les opérations suivantes :

- [Télécharger l’outil MSIXMGR](https://aka.ms/msixmgr)
- Obtenir une application empaquetée MSIX (fichier .MSIX)
- Obtenir des autorisations administratives pour la machine sur laquelle vous allez créer l’image MSIX

## <a name="create-an-msix-image"></a>Créer une image MSIX

L’expansion est le processus qui consiste à prendre une application MSIX empaquetée (.MSIX) et la décompresser dans une image MSIX (.VHD(x) ou fichier .CIM).

Pour développer un fichier MSIX :

1. [Téléchargez l’outil MSIXMGR](https://aka.ms/msixmgr) si vous ne l’avez pas déjà fait.

2. Décompressez MSIXMGR.zip dans un dossier local.

3. Ouvrez une invite de commande en mode élevé.

4. Recherchez le dossier local de l’étape 2.

5. Exécutez la commande suivante dans l’invite de commandes pour créer une image MSIX.

    ```cmd
    msixmgr.exe -Unpack -packagePath <path to package> -destination <output folder> [-applyacls] [-create] [-vhdSize <size in MB>] [-filetype <CIM | VHD | VHDX>] [-rootDirectory <rootDirectory>]
    ```

    N’oubliez pas de remplacer les valeurs d’espace réservé par les valeurs appropriées. Par exemple :

    ```cmd
    msixmgr.exe -Unpack -packagePath "C:\Users\%username%\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
    ```

6. Maintenant que vous avez créé l’image, accédez au dossier de destination et vérifiez que vous avez correctement créé l’image MSIX (.VHDX).

## <a name="create-an-msix-image-in-a-cim-file"></a>Créer une image MSIX dans un fichier CIM

Vous pouvez également utiliser la commande de [l’étape 5](#create-an-msix-image) pour créer des fichiers CIM et VHDX en remplaçant le type de fichier et le chemin de destination.

Par exemple, voici comment utiliser cette commande pour créer un fichier CIM :

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.cim" -applyacls -create -vhdSize 200 -filetype "cim" -rootDirectory apps
```

Voici comment utiliser cette commande pour créer un disque VHDX :

```cmd
msixmgr.exe -Unpack -packagePath "C:\Users\ssa\Desktop\packageName_3.51.1.0_x64__81q6ced8g4aa0.msix" -destination "c:\temp\packageName.vhdx" -applyacls -create -vhdSize 200 -filetype "vhdx" -rootDirectory apps
```

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur l’attachement d’application MSIX : [Qu’est-ce que l’attachement d’application MSIX ?](what-is-app-attach.md)

Pour découvrir comment configurer l’attachement d’application, consultez les articles suivants :

- [Configurer l’attachement d’application MSIX avec le portail Azure](app-attach-azure-portal.md)
- [Configurer l’attachement d’application MSIX avec PowerShell](app-attach-powershell.md)
- [Créer des scripts PowerShell pour l’attachement d’application MSIX](app-attach.md)
- [Préparer une image MSIX pour Windows Virtual Desktop](app-attach-image-prep.md)
- [Configurer un partage de fichiers pour l’attachement d’application MSIX](app-attach-file-share.md)

Si vous avez des questions sur l’attachement d’application MSIX, consultez notre [FAQ sur l’attachement d’application](app-attach-faq.md) et le [Glossaire Attachement d’application](app-attach-glossary.md).
