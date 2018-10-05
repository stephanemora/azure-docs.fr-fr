---
title: Copier des données sur votre Microsoft Azure Data Box | Microsoft Docs
description: Découvrez comment copier des données sur votre Azure Data Box.
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
ms.date: 09/25/2018
ms.author: alkohli
ms.openlocfilehash: 9bc84a9b08c4cfbdf7f24416c923e0dbd7076556
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47161928"
---
# <a name="tutorial-copy-data-to-azure-data-box"></a>Didacticiel : Copier des données sur Azure Data Box 

Ce didacticiel explique comment vous connecter à votre ordinateur hôte et copier des données à partir de cet ordinateur à l’aide de l’interface utilisateur web locale, puis préparer l’expédition de Data Box.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Se connecter à Data Box
> * Copier des données sur Data Box
> * Préparer l’expédition de Data Box

## <a name="prerequisites"></a>Prérequis

Avant de commencer, assurez-vous que :

1. Vous avez suivi le didacticiel [Tutorial: Set up Azure Data Box](data-box-deploy-set-up.md) (Didacticiel : Configurer Azure Data Box)
2. Vous avez reçu votre Data Box et que l’état de la commande dans le portail est **Remis**
3. Vous disposez d’un ordinateur hôte contenant les données que vous souhaitez copier sur Data Box Votre ordinateur hôte doit
    - Exécuter un [système d’exploitation pris en charge](data-box-system-requirements.md)
    - Connectez-vous à un réseau haut débit. Nous vous recommandons vivement d’utiliser au minimum une connexion 10 GbE. Si vous ne disposez pas d’une connexion 10 GbE, vous pouvez utiliser une liaison de données 1 GbE. Cependant, les vitesses de copie seront affectées. 

## <a name="connect-to-data-box"></a>Se connecter à Data Box

Selon le compte de stockage sélectionné, Data Box crée jusqu’à :
- Trois partages pour chaque compte de stockage associé pour GPv1 et GPv2.
- Un partage pour un compte de stockage Premium ou Blob 

Sous les partages d’objet blob de blocs et d’objet blob de pages, les entités de premier niveau sont des conteneurs et les entités de second niveau sont des blobs. Sous les partages Azure Files, les entités de premier niveau sont des partages et les entités de second niveau sont des fichiers.

Considérez l'exemple suivant. 

- Compte de stockage : *Mystoracct*
- Partage pour l’objet blob de blocs : *Mystoracct_BlockBlob/my-container/blob*
- Partage pour l’objet blob de pages : *Mystoracct_PageBlob/my-container/blob*
- Partage pour le fichier : *Mystoracct_AzFile/my-share*

Les étapes de connexion et de copie peuvent varier selon que votre Data Box est connecté à un ordinateur hôte Windows Server ou à un hôte Linux.

### <a name="connect-via-smb"></a>Se connecter à l’aide d’une session SMB 

Si vous utilisez un ordinateur hôte Windows Server, procédez comme suit pour vous connecter au Data Box.

1. La première étape consiste à vous authentifier et à démarrer une session. Accédez à la page **Connect and copy** (Connexion et copie). Cliquez sur **Get credentials** (Obtenir les informations d’identification) pour obtenir les informations d’identification requises pour accéder aux partages associés à votre compte de stockage. 

    ![Obtenir les informations d’identification du partage 1](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Dans la boîte de dialogue Access share and copy data (Accéder au partage et copier les données), copiez les valeurs **Nom d’utilisateur** et **Mot de passe** correspondant au partage. Cliquez sur **OK**.
    
    ![Obtenir les informations d’identification du partage 1](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Accédez aux partages associés à votre compte de stockage (Mystoracct dans l’exemple suivant). Utilisez le chemin d’accès `\\<IP of the device>\ShareName` pour accéder aux partages. Selon votre format de données, connectez-vous aux partages (utilisez le nom de partage) à l’adresse suivante : 
    - *\\<IP address of the device>\Mystoracct_Blob*
    - *\\<IP address of the device>\Mystoracct_Page*
    - *\\<IP address of the device>\Mystoracct_AzFile*
    
    Pour vous connecter aux partages à partir de votre ordinateur hôte, ouvrez une fenêtre de commande.  À l’invite de commandes, tapez :

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Lorsque vous y êtes invité, entrez le mot de passe du partage. L’exemple suivant illustre la connexion à un partage à l’aide de la commande précédente.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.172\devicemanagertest1_BlockBlob /u:devicemanagertest1
    Enter the password for 'devicemanagertest1' to connect to '10.126.76.172':
    The command completed successfully.
    ```

4. Appuyez sur Windows + R. Dans la fenêtre **Exécuter**, spécifiez `\\<device IP address>`. Cliquez sur **OK**. L’Explorateur de fichiers s’affiche.
    
    ![Se connecter au partage à l’aide de l’Explorateur de fichiers 2](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

5. À présent, vous devriez voir les partages sous forme de dossiers. Créez un dossier pour les fichiers que vous souhaitez copier (dans ce cas, templates). Il arrive que les dossiers soient marqués d’une croix grise. Cette croix n’indique pas une condition d’erreur. L’application marque les dossiers pour permettre d’en suivre l’état.
    
    ![Se connecter au partage à l’aide de l’Explorateur de fichiers 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) ![Se connecter au partage à l’aide de l’Explorateur de fichiers 2](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png) 

### <a name="connect-via-nfs"></a>Se connecter via NFS 

Si vous utilisez un ordinateur hôte Linux, procédez comme suit afin de configurer Data Box pour autoriser l’accès aux clients NFS.

1. Indiquez les adresses IP des clients autorisés pouvant accéder au partage. Dans l’interface utilisateur web locale, accédez à la page **Connect and copy** (Connexion et copie). Sous **Paramètres NFS**, cliquez sur **Accès au client NFS**. 

    ![Configurer l’accès au client NFS 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Indiquez l’adresse IP du client NFS, puis cliquez sur **Ajouter**. Vous pouvez configurer un accès à plusieurs clients NFS en répétant cette étape. Cliquez sur **OK**.

    ![Configurer l’accès au client NFS 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Assurez-vous qu’une [version prise en charge](data-box-system-requirements.md) du client NFS est installée sur l’ordinateur hôte Linux. Utilisez la version spécifiquement adaptée à votre distribution Linux. 

3. Une fois le client NFS installé, utilisez la commande suivante pour monter le partage NFS sur votre appareil Data Box :

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    L’exemple suivant montre comment se connecter à un partage Data Box via NFS. L’adresse IP de l’appareil Data Box est `10.161.23.130`. Le partage `Mystoracct_Blob` est monté sur la machine virtuelle ubuntuVM avec le point de montage `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Copier des données sur Data Box

Une fois que vous êtes connecté aux partages Data Box, l’étape suivante consiste à copier les données. Avant de copier les données, veillez à passer en revue les considérations suivantes :

- Assurez-vous que les données sont copiées vers des partages compatibles avec le format des données. Par exemple, les données d’objet blob de blocs doivent être copiées dans le partage des objets blob de blocs. Si le format des données ne correspond pas au type de partage, les données ne pourront pas être chargées dans Azure.
-  Lorsque vous copiez des données, vérifiez que la taille des données est conforme aux limites de taille spécifiées dans l’article [Azure storage and Data Box limits](data-box-limits.md) (Limitations relatives au Stockage Azure et à Data Box). 
- Si les données, qui sont en cours de chargement par Data Box, sont chargées simultanément par d’autres applications en dehors de Data Box, cela peut entraîner l’échec du chargement ou des corruptions de données.
- Nous vous recommandons de ne pas utiliser SMB et NFS simultanément et de ne pas copier les mêmes données vers la même destination finale sur Azure. En effet, le résultat final ne pourrait être déterminé.

### <a name="copy-data-via-smb"></a>Copier des données via SMB

Après vous être connecté au partage SMB, lancez une copie de données. 

Vous pouvez utiliser n’importe quel outil de copie de fichier SMB, comme Robocopy, pour copier vos données. Plusieurs travaux de copie peuvent être lancés simultanément à l’aide de Robocopy. Utilisez la commande suivante :
    
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile> 
  
 Les attributs sont décrits dans le tableau suivant.
    
|Attribut  |Description  |
|---------|---------|
|/e     |Copie les sous-répertoires, y compris les répertoires vides.         |
|/r:     |Spécifie le nombre de nouvelles tentatives en cas d’échec de la copie.         |
|/w:     |Spécifie le délai d’attente entre les tentatives, en secondes.         |
|/is     |Inclut les mêmes fichiers.         |
|/nfl     |Indique que les noms de fichier ne doivent pas être consignés.         |
|/ndl    |Indique que les noms de répertoire ne doivent pas être consignés.        |
|/np     |Spécifie que la progression de l’opération de copie (le nombre de fichiers ou répertoires copiés jusqu’ici) n’est pas affichée. L’affichage de la progression réduit considérablement les performances.         |
|/MT     | Utilise le multithreading (32 ou 64 threads recommandés). Cette option n’est pas utilisée avec les fichiers chiffrés. Vous devrez peut-être séparer les fichiers chiffrés et non chiffrés. Toutefois, une copie monothread réduit considérablement les performances.           |
|/fft     | Utilisé pour réduire la granularité de l’horodatage pour n’importe quel système de fichiers.        |
|/b     | Copie les fichiers en mode de sauvegarde.        |
|/z    | Copie les fichiers en mode de redémarrage. Utilisez cette option si l’environnement est instable. La journalisation supplémentaire effectuée par cette option réduit le débit.      |
| /zb     | Utilise le mode de redémarrage. Si l’accès est refusé, cette option utilise le mode de sauvegarde. La création de points de contrôle par cette option réduit le débit.         |
|/efsraw     | Copie tous les fichiers chiffrés en mode EFS brut. Utilisez cette option avec des fichiers chiffrés uniquement.         |
|log+:<LogFile>| Ajoute la sortie au fichier journal existant.|    
 
L’exemple suivant affiche la sortie de la commande robocopy pour copier les fichiers sur Data Box.
    
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
    
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
    
        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\
    
        Files : *.*
    
        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60
    
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
    C:\Users>
       

Pour optimiser les performances, utilisez les paramètres robocopy suivants lors de la copie des données.

|    Plateforme    |    Principalement des fichiers de petite taille (< 512 Ko)                           |    Principalement des fichiers de taille moyenne (entre 512 Ko et 1 Mo)                      |    Principalement des fichiers volumineux (> 1 Mo)                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 sessions Robocopy <br> 16 threads par session    |    3 sessions Robocopy <br> 16 threads par session    |    2 sessions Robocopy <br> 24 threads par session    |  |


Pour plus d’informations sur la commande Robocopy, consultez [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Robocopy et quelques exemples).

Ouvrez le dossier cible pour afficher et vérifier les fichiers copiés. Si vous rencontrez des erreurs au cours du processus de copie, téléchargez les fichiers d’erreurs pour résoudre les problèmes.

Pour garantir l’intégrité des données, la somme de contrôle est calculée par le biais d’une fonction inline lors de la copie des données. Une fois la copie terminée, vérifiez l’espace utilisé et l’espace libre sur votre appareil.
    
   ![Vérifier l’espace libre et l’espace utilisé sur le tableau de bord](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

### <a name="copy-data-via-nfs"></a>Copier des données via NFS

Si vous utilisez un ordinateur hôte Linux, utilisez un utilitaire de copie similaire à Robocopy. Plusieurs solutions alternatives sont disponibles pour Linux, par exemple, [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) et [Ultracopier](https://ultracopier.first-world.info/).  

La commande `cp` constitue l’une des meilleures options pour copier un répertoire. Pour plus d’informations sur son utilisation, reportez-vous aux [pages man sur cp](http://man7.org/linux/man-pages/man1/cp.1.html).

Si vous utilisez l’option rsync pour une copie multithread, suivez ces instructions :

 - Installez le package **CIFS Utils** ou **NFS Utils** selon le système de fichiers utilisé par votre client Linux.

    `sudo apt-get install cifs-utils` `sudo apt-get install nfs-utils`

 -  Installez **Rsync** et **Parallel** (selon la version distribuée de Linux).

    `sudo apt-get install rsync`
    `sudo apt-get install parallel` 

 - Créez un point de montage.

    `sudo mkdir /mnt/databox`

 - Montez le volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

 - Faites une mise en miroir de la structure de répertoires du dossier.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

 - Copiez les fichiers. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     où j spécifie le nombre de parallélisations et X = le nombre de copies parallèles

     Nous vous recommandons de commencer avec 16 copies parallèles et d’augmenter le nombre de threads selon les ressources disponibles.

## <a name="prepare-to-ship"></a>Préparer l’expédition

La dernière étape consiste à préparer l’appareil pour l’expédition. Pour cela, tous les partages de l’appareil sont mis hors connexion. Les partages ne seront plus accessibles lorsque vous aurez commencé à préparer l’appareil pour l’expédition.
1. Accédez à la page **Prepare to ship** (Préparer l’expédition) et cliquez sur **Démarrer la préparation**. 
   
    ![Préparer l’expédition 1](media/data-box-deploy-copy-data/prepare-to-ship1.png)

2. Par défaut, les sommes de contrôle sont calculées par le biais d’une fonction inline pendant la préparation de l’expédition. Le calcul des sommes de contrôle peut prendre un certain temps selon la taille de vos données. Cliquez sur **Démarrer la préparation**.
    1. Lors de la préparation de l’expédition, les partages de l’appareil sont mis hors connexion et l’appareil est verrouillé.
        
        ![Préparer l’expédition 1](media/data-box-deploy-copy-data/prepare-to-ship2.png) 
   
    2. Une fois terminée la préparation de l’appareil, son état devient *Ready to ship* (Prêt pour expédition). 
        
        ![Préparer l’expédition 1](media/data-box-deploy-copy-data/prepare-to-ship3.png)

    3. Téléchargez la liste des fichiers (manifeste) qui ont été copiés dans le cadre de ce processus. Vous pourrez utiliser cette liste ultérieurement pour vérifier les fichiers téléchargés vers Azure.
        
        ![Préparer l’expédition 1](media/data-box-deploy-copy-data/prepare-to-ship4.png)

3. Arrêtez l’appareil. Accédez à la page **Shut down or restart** (Arrêter ou redémarrer) et cliquez sur **Arrêter**. Lorsque vous êtes invité à confirmer l’opération, cliquez sur **OK** pour continuer.
4. Débranchez les câbles. L’étape suivante consiste à expédier l’appareil à Microsoft.

 
<!--## Appendix - robocopy parameters

This section describes the robocopy parameters used when copying the data to optimize the performance.

|    Platform    |    Mostly small files < 512 KB                           |    Mostly medium  files 512 KB-1 MB                      |    Mostly large files > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|---|
|    Data Box         |    2 Robocopy sessions <br> 16 threads per sessions    |    3 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 24 threads per sessions    |  |
|    Data Box Heavy     |    6 Robocopy sessions <br> 24 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |    6 Robocopy sessions <br> 16 threads per sessions    |   
|    Data Box Disk         |    4 Robocopy sessions <br> 16 threads per sessions             |    2 Robocopy sessions <br> 16 threads per sessions    |    2 Robocopy sessions <br> 16 threads per sessions    |   
-->

## <a name="next-steps"></a>Étapes suivantes

Ce tutoriel vous a apporté des connaissances concernant Azure Data Box, notamment concernant les points suivants :

> [!div class="checklist"]
> * Se connecter à Data Box
> * Copier des données sur Data Box
> * Préparer l’expédition de Data Box

Passez au didacticiel suivant pour découvrir comment préparer et copier des données sur votre Data Box.

> [!div class="nextstepaction"]
> [Ship your Azure Data Box to Microsoft](./data-box-deploy-picked-up.md) (Expédier votre Azure Data Box à Microsoft)

