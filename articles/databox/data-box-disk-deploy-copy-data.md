---
title: Copier des données sur votre disque Microsoft Azure Data Box | Microsoft Docs
description: Utilisez ce didacticiel pour apprendre à copier des données sur votre disque Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 7bc8b3ba415f8fe701098a9fa7e51d60ffb9df4e
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092451"
---
# <a name="tutorial-copy-data-to-azure-data-box-disk-and-verify"></a>Didacticiel : Copier des données sur un disque Azure Data Box et procéder à une vérification

Ce didacticiel explique comment copier des données à partir de votre ordinateur hôte, puis générer les sommes de contrôle pour vérifier l’intégrité des données.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Copier des données sur un disque Data Box
> * Vérifier les données

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :
- Vous avez terminé le [Didacticiel : Installer et configurer votre disque Azure Data Box](data-box-disk-deploy-set-up.md).
- Vos disques sont déverrouillés et connectés à un ordinateur client.
- Votre ordinateur client utilisé pour copier des données sur les disques doit exécuter un [système d’exploitation pris en charge](data-box-disk-system-requirements.md).


## <a name="copy-data-to-disks"></a>Copier des données sur des disques

Procédez comme suit pour vous connecter et copier des données à partir de votre ordinateur vers le disque Data Box.

1. Affichez le contenu du disque déverrouillé. 

    ![Afficher le contenu du disque](media/data-box-disk-deploy-copy-data/data-box-disk-content.png)
 
2. Copiez les données qui doivent être importées en tant qu’objets blob de blocs dans le dossier BlockBlob. De même,copiez des données telles que VHD/VHDX vers le dossier PageBlob. 

    Un conteneur est créé dans le compte de stockage Azure de chaque sous-dossier sous les dossiers BlockBlob et PageBlob. Tous les fichiers sous les dossiers BlockBlob et PageBlob sont copiés dans un conteneur par défaut `$root` dans le compte de stockage Azure. Tous les fichiers présents dans le conteneur `$root` sont systématiquement chargés en tant qu’objets blob de blocs.

    Si les fichiers et les dossiers existent dans le répertoire racine, vous devez les déplacer vers un autre dossier avant de commencer à copier des données.

    Suivez les conventions de dénomination Azure pour les noms de conteneur et d’objet blob.

    #### <a name="azure-naming-conventions-for-container-and-blob-names"></a>Conventions d’affectation de noms Azure pour les noms de conteneur et d’objet blob
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

### <a name="split-and-copy-data-to-disks"></a>Fractionner et copier des données sur plusieurs disques

Vous pouvez exécuter cette procédure facultative lorsque vous utilisez plusieurs disques et que vous disposez d’un jeu de données volumineux qui doit être fractionné et copié sur la totalité des disques. L’outil Data Box Split Copy vous permet de fractionner et copier les données sur un ordinateur Windows.

1. L’outil Data Box Split Copy doit avoir été téléchargé et extrait dans un dossier local de votre ordinateur Windows. Cet outil a été téléchargé lorsque vous avez téléchargé l’ensemble d’outils Data Box Disk pour Windows.
2. Ouvrez l’Explorateur de fichiers. Notez la lettre de lecteur de la source de données et les lettres de lecteur attribuées à Data Box Disk. 

     ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-1.png)
 
3. Identifiez les données sources à copier. Par exemple, dans ce cas précis :
    - Les données d’objet blob de blocs ci-après ont été identifiées.

         ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-2.png)    

    - Les données d’objet blob de pages ci-après ont été identifiées.

         ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-3.png)
 
4. Accédez au dossier dans lequel le logiciel a été extrait. Recherchez le fichier SampleConfig.json dans ce dossier. Il s’agit d’un fichier en lecture seule que vous pouvez modifier et enregistrer.

   ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-4.png)
 
5. Modifiez le fichier SampleConfig.json.
 
    - Fournissez un nom de travail. Cette opération crée un dossier dans Data Box Disk qui devient le conteneur dans le compte de stockage Azure associé à ces disques. Le nom du travail doit respecter les conventions d’affectation de noms de conteneur Azure. 
    - Fournissez un chemin d’accès source en notant le format du chemin dans le fichier SampleConfig.json. 
    - Entrez les lettres de lecteur correspondant aux disques cibles. Les données sont copiées sur les différents disques à partir du chemin d’accès source.
    - Fournissez un chemin d’accès pour les fichiers journaux. Par défaut, ces fichiers sont envoyés au répertoire dans lequel se trouve le fichier .exe.

     ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-5.png)

6. Pour valider le format de fichier, accédez à JSONlint. Enregistrez le fichier sous le nom ConfigFile.json. 

     ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-6.png)
 
7. Ouvrez une fenêtre d’invite de commandes. 

8. Exécutez le fichier DataBoxDiskSplitCopy.exe. type

    `DataBoxDiskSplitCopy.exe PrepImport /config:<Your-config-file-name.json>`

     ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-7.png)
 
9. Appuyez sur Entrée pour poursuivre le script.

    ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-8.png)
  
10. Une fois le jeu de données fractionné et copié, le résumé de l’outil Split Copy pour la session de copie vous est présenté. Voici un exemple de sortie obtenue.

    ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-9.png)
 
11. Vérifiez que les données ont été fractionnées entre les disques cibles. 
 
    ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-10.png)
    ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-11.png)
     
    Si vous examinez davantage le contenu du lecteur n:, vous pouvez constater que deux sous-dossiers ont été créés et correspondent aux données d’objet blob de blocs et d’objet blob de pages.
    
     ![Fractionnement des données de copie ](media/data-box-disk-deploy-copy-data/split-copy-12.png)

12. Si la session de copie échoue, vous pouvez récupérer et reprendre l’opération en utilisant la commande suivante :

    `DataBoxDiskSplitCopy.exe PrepImport /config:<configFile.json> /ResumeSession`


Une fois la copie des données terminée, l’étape suivante consiste à valider les données. 


## <a name="validate-data"></a>Valider les données 

Pour vérifier les données, procédez comme suit.

1. Exécutez le fichier `DataBoxDiskValidation.cmd` pour la validation des sommes de contrôle dans le dossier *AzureImportExport* de votre lecteur. 
    
    ![Sortie de l’outil de validation Data Box Disk](media/data-box-disk-deploy-copy-data/data-box-disk-validation-tool-output.png)

2. Choisissez l’option appropriée. **Nous vous recommandons de toujours valider les fichiers et générer des sommes de contrôle en sélectionnant l’option 2**. Selon la taille de vos données, cette étape peut prendre un certain temps. Une fois le script exécuté, quittez la fenêtre de commandes. Si des erreurs se produisent pendant la validation et la génération des sommes de contrôle, vous en êtes averti, et un lien d’accès aux journaux des erreurs vous est également fourni.

    ![Sortie de la somme de contrôle](media/data-box-disk-deploy-copy-data/data-box-disk-checksum-output.png)

    > [!TIP]
    > - Réinitialisez l’outil entre deux exécutions.
    > - Utilisez l’option 1 pour valider les fichiers en traitant uniquement les jeux de données volumineux contenant de petits fichiers (de quelques Ko). Dans ce cas, la génération des sommes de contrôle peut prendre beaucoup de temps et ralentir considérablement les performances.

3. Si vous utilisez plusieurs disques, exécutez la commande pour chaque disque.

## <a name="next-steps"></a>Étapes suivantes

Ce didacticiel vous a apporté des connaissances sur les disques Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Copier des données sur un disque Data Box
> * Vérifier l’intégrité des données

Passez au didacticiel suivant pour savoir comment renvoyer le disque Data Box et vérifier le chargement des données dans Azure.

> [!div class="nextstepaction"]
> [Envoyer votre Azure Data Box à Microsoft](./data-box-disk-deploy-picked-up.md)

