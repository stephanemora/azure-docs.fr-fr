---
title: Copier des données sur votre disque Microsoft Azure Data Box | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à copier des données sur votre disque Azure Data Box
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
ms.date: 09/05/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: f25d0b3522658d5fcd4b34110cb03b624dd9e7b1
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2018
ms.locfileid: "43841503"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Didacticiel : Copier des données sur un disque Azure Data Box et procéder à une vérification

Ce didacticiel explique comment copier des données à partir de votre ordinateur hôte, puis générer les sommes de contrôle pour vérifier l’intégrité des données.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Copier des données sur un disque Data Box
> * Vérifier l’intégrité des données

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :
- Vous avez terminé le [Didacticiel : Installer et configurer votre disque Azure Data Box](data-box-disk-deploy-set-up.md).
- Vos disques sont décompressés et activés.
- Vous disposez d’un ordinateur hôte pour copier des données sur les disques. Votre ordinateur hôte doit
    - Exécuter un [système d’exploitation pris en charge](data-box-disk-system-requirements.md)
    - Avoir [Windows PowerShell 4 installé](https://www.microsoft.com/download/details.aspx?id=40855)
    - Avoir [.NET Framework 4.5 installé](https://www.microsoft.com/download/details.aspx?id=30653)


## <a name="copy-data-to-disks"></a>Copier des données sur des disques

Procédez comme suit pour vous connecter et copier des données à partir de votre ordinateur vers le disque Data Box.

1. Affichez le contenu du disque déverrouillé. 

    ![Afficher le contenu du disque](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copiez les données qui doivent être importées en tant qu’objets blob de blocs dans le dossier BlockBlob. De même,copiez des données telles que VHD/VHDX vers le dossier PageBlob. 

    Un conteneur est créé dans le compte de stockage Azure de chaque sous-dossier sous les dossiers BlockBlob et PageBlob. Tous les fichiers sous les dossiers BlockBlob et PageBlob sont copiés dans un conteneur par défaut `$root` dans le compte de stockage Azure. Tous les fichiers présents dans le conteneur `$root` sont systématiquement chargés en tant qu’objets blob de blocs.

    Si les fichiers et les dossiers existent dans le répertoire racine, vous devez les déplacer vers un autre dossier avant de commencer à copier des données.

    Suivez les conventions de dénomination Azure pour les noms de conteneur et d’objet blob.

    |Entité   |Conventions  |
    |---------|---------|
    |Noms de conteneur pour l’objet blob de blocs et l’objet blob de pages     |Doit commencer par une lettre ou un chiffre et peut comporter uniquement des lettres minuscules, des chiffres et des traits d’union (-). Chaque trait d’union (-) doit être immédiatement précédé et suivi par une lettre ou un chiffre. Les traits d’union consécutifs ne sont pas autorisés dans les noms. <br>Doit être un nom DNS valide dont la longueur est comprise entre 3 et 63 caractères.          |
    |Noms d’objet blob pour l’objet blob de blocs et l’objet blob de pages    |Les noms d’objet blob respectent la casse et peuvent contenir une combinaison de caractères. <br>Le nom d’objet blob doit comprendre entre 1 et 1 024 caractères.<br>Les caractères d’URL réservées doivent être correctement placés dans une séquence d’échappement.<br>Le nombre de segments de ligne comprenant le nom d’objet blob ne peut pas dépasser 254. Un segment de chemin représente la chaîne située entre des caractères délimiteurs consécutifs (par exemple, la barre oblique « / ») et correspondant au nom d’un répertoire virtuel.         |

    > [!IMPORTANT] 
    > Tous les conteneurs et objets blob doivent être conformes aux [conventions d’affectation de noms Azure](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions). Si ces règles ne sont pas respectées, le chargement des données vers Azure échoue.

3. Lorsque vous copiez des fichiers, assurez-vous qu’ils ne dépassent pas ~4,7 Tio pour les objets blob de blocs et ~ 8 Tio pour les objets blob de pages. 
4. Vous pouvez utiliser la fonction de glisser-déplacer dans l’Explorateur de fichiers pour copier les données. Vous pouvez également utiliser n’importe quel outil de copie de fichier SMB, comme Robocopy, pour copier vos données. Plusieurs travaux de copie peuvent être lancés simultanément à l’aide de la commande Robocopy suivante :

    `Robocopy <source> <destination>  * /MT:64 /E /R:1 /W:1 /NFL /NDL /FFT /Log:c:\RobocopyLog.txt` 
    
    Les paramètres et les options de la commande sont présentés sous forme de tableau tel que suit :
    
    |Paramètres/Options  |Description |
    |--------------------|------------|
    |Source            | Spécifie le chemin d’accès au répertoire source.        |
    |Destination       | Spécifie le chemin d’accès au répertoire de destination.        |
    |/E                  | Copie les sous-répertoires, y compris les répertoires vides. |
    |/MT[:N]             | Crée des copies multithread avec N threads où N est un nombre entier compris entre 1 et 128. <br>La valeur par défaut de N est 8.        |
    |/R: <N>             | Spécifie le nombre de nouvelles tentatives en cas d’échec de la copie. La valeur par défaut de N est 1 000 000 (un million de tentatives).        |
    |/W: <N>             | Spécifie le délai d’attente entre les tentatives, en secondes. La valeur par défaut de N est 30 (temps d’attente de 30 secondes).        |
    |/NFL                | Indique que les noms de fichier ne doivent pas être consignés.        |
    |/NDL                | Indique que les noms de répertoire ne doivent pas être consignés.        |
    |/FFT                | Calcule l’heure des fichiers FAT (à 2 secondes près).        |
    |/Log:<Log File>     | Écrit la sortie d’état dans le fichier journal (remplace le fichier journal existant).         |

    Plusieurs disques peuvent être utilisés en parallèle, avec plusieurs travaux s’exécutant sur chaque disque. 

6. Vérifiez l’état de l’opération de copie pendant l’exécution du travail. L’exemple suivant affiche la sortie de la commande robocopy pour copier les fichiers sur le disque Data Box.

    ```
    C:\Users>robocopy
        -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:53 PM
           Simple Usage :: ROBOCOPY source destination /MIR
    
                 source :: Source Directory (drive:\path or \\server\share\path).
            destination :: Destination Dir  (drive:\path or \\server\share\path).
                   /MIR :: Mirror a complete directory tree.
    
        For more usage information run ROBOCOPY /?    
    
    ****  /MIR can DELETE files as well as copy them !
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:64 /E /R:1 /W:1 /FFT 
    -------------------------------------------------------------------------------
       ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
      Started : Thursday, March 8, 2018 2:34:58 PM
       Source : C:\Git\azure-docs-pr\contributor-guide\
         Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
      Options : *.* /DCOPY:DA /COPY:DAT /MT:8 /R:1000000 /W:30
    
    ------------------------------------------------------------------------------
    
    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------
    
                   Total    Copied   Skipped  Mismatch    FAILED    Extras
        Dirs :         1         1         1         0         0         0
       Files :        17        17         0         0         0         0
       Bytes :     3.9 k     3.9 k         0         0         0         0
       Times :   0:00:05   0:00:00                       0:00:00   0:00:00
        
       Speed :                5620 Bytes/sec.
       Speed :               0.321 MegaBytes/min.
       Ended : Thursday, March 8, 2018 2:34:59 PM
        
    C:\Users>
    ```
 
    
7. Ouvrez le dossier cible pour afficher et vérifier les fichiers copiés. Si vous rencontrez des erreurs au cours du processus de copie, téléchargez les fichiers journaux pour résoudre les problèmes. L’emplacement des fichiers journaux est spécifié dans la commande robocopy.
 


> [!IMPORTANT]
> - C’est à vous de vous assurer que les données sont copiées vers des dossiers compatibles avec le format des données. Par exemple, les données d’objet blob de blocs doivent être copiées dans le dossier des objets blob de blocs. Si le format des données ne correspond pas au dossier (type de stockage), les données ne pourront pas être chargées dans Azure.
> -  Lorsque vous copiez des données, vérifiez que la taille des données est conforme aux limites de taille spécifiées dans l’article [Azure storage and Data Box Disk limits](data-box-disk-limits.md) (Limitations relatives au stockage Azure et aux disques Data Box). 
> - Si les données, qui sont en cours de chargement via Data Box Disk, sont chargées simultanément par d’autres applications en dehors de Data Box Disk, cela pourrait entraîner l’échec du téléchargement ou des corruptions de données.

## <a name="verify-data-integrity"></a>Vérifier l’intégrité des données

Pour vérifier l’intégrité des données, procédez comme suit.

1. Exécutez `AzureExpressDiskService.ps1` pour valider la somme de contrôle. Dans l’Explorateur de fichiers, accédez au dossier *AzureImportExport* du disque. Cliquez avec le bouton droit et sélectionnez **Exécuter avec PowerShell**. 

    ![Exécuter la somme de contrôle](media/data-box-disk-deploy-copy-data/data-box-disk-checksum.png)

2. Selon la taille de vos données, cette étape peut prendre un certain temps. Un résumé du processus de vérification de l’intégrité des données, ainsi que le temps d’exécution du processus, s’affichent une fois le script terminé. Vous pouvez appuyer sur **Entrée** pour quitter la fenêtre de commande.

    ![Sortie de la somme de contrôle](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

3. Si vous utilisez plusieurs disques, exécutez la commande pour chaque disque.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Copier des données sur un disque Data Box
> * Vérifier l’intégrité des données

Passez au didacticiel suivant pour savoir comment renvoyer le disque Data Box et vérifier le chargement des données dans Azure.

> [!div class="nextstepaction"]
> [Envoyer votre Azure Data Box à Microsoft](./data-box-disk-deploy-picked-up.md)

