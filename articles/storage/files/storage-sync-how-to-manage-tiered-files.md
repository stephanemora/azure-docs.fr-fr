---
title: Comment gérer des fichiers hiérarchisés Azure File Sync | Microsoft Docs
description: Conseils et commandlets PowerShell pour vous aider à gérer les fichiers hiérarchisés
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204213"
---
# <a name="how-to-manage-tiered-files"></a>Guide pratique pour gérer les fichiers hiérarchisés

Cet article fournit des conseils pour les utilisateurs qui ont des questions en lien avec la gestion des fichiers hiérarchisés. Pour des questions conceptuelles sur la hiérarchisation Cloud, consultez [FAQ Azure Files](storage-files-faq.md).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Comment vérifier si vos fichiers sont en cours de hiérarchisation ?

Si les fichiers doivent être hiérarchisés par ensemble, les stratégies sont évaluées une fois par heure. Vous pouvez rencontrer deux situations lors de la création d’un point de terminaison de serveur :

Lorsque vous ajoutez un nouveau point de terminaison de serveur pour la première fois, il existe souvent des fichiers à cet emplacement de serveur. Ils doivent être chargés en premier, avant que la hiérarchisation Cloud puisse commencer. La stratégie d’espace libre du volume ne commence pas à fonctionner tant que le chargement initial de tous les fichiers n’est pas terminé. Toutefois, la stratégie de date facultative commence à fonctionner sur la base d’un fichier individuel, dès qu’un fichier a été chargé. L’intervalle d’une heure s’applique également dans ce cas de figure. 

Lorsque vous ajoutez un nouveau point de terminaison de serveur, vous pouvez connecter un emplacement de serveur vide à un partage de fichiers Azure contenant vos données. Si vous choisissez de télécharger l’espace de noms et de rappeler le contenu pendant le téléchargement initial vers votre serveur, une fois l’espace de noms indisponible, les fichiers sont renommés en fonction de l’horodatage de la dernière modification. Et ce, jusqu’à ce que la stratégie d’espace de volume libre et la stratégie de date (en option) sont atteintes.

Vous pouvez déterminer de plusieurs façons si un fichier a été hiérarchisé sur votre partage de fichiers Azure :
    
   *  **Vérifier les attributs du fichier.**
     Cliquez avec le bouton droit sur le fichier, accédez à **Détails**, puis faites défiler vers le bas jusqu’à la propriété **Attributs**. Un fichier hiérarchisé a les attributs suivants définis :     
        
        | Lettre de l’attribut | Attribut | Définition |
        |:----------------:|-----------|------------|
        | Un | Archivage | Indique que le fichier doit être sauvegardé par un logiciel de sauvegarde. Cet attribut est toujours défini, que le fichier soit hiérarchisé ou entièrement stocké sur le disque. |
        | P | Fichier partiellement alloué | Indique qu’il s’agit d’un fichier partiellement alloué. Un fichier partiellement alloué est un type de fichier spécial offert par NTFS, qui est efficace quand le flux sur disque du fichier est pratiquement vide. Azure File Sync utilise des fichiers partiellement alloués, car un fichier est soit entièrement hiérarchisé, soit partiellement rappelé. Dans un fichier entièrement hiérarchisé, le flux de fichier est stocké dans le cloud. Dans un fichier partiellement rappelé, cette partie du fichier est déjà sur le disque. Cela peut se produire quand des fichiers sont partiellement lus par des applications telles que des lecteurs multimédias ou des utilitaires de compression. Si un fichier est entièrement rappelé sur le disque, Azure File Sync le convertit d’un fichier partiellement alloué en fichier normal. Cet attribut est défini uniquement sur Windows Server 2016 et les versions antérieures.|
        | M | Rappeler en cas d’accès aux données | Indique que les données du fichier ne sont pas intégralement présentes sur le stockage local. La lecture du fichier entraîne l’extraction d’au moins une partie du contenu du fichier à partir d’un partage de fichiers Azure auquel le point de terminaison de serveur est connecté. Cet attribut est défini uniquement sur Windows Server 2019. |
        | L | Point d’analyse | Indique que le fichier a un point d’analyse. Un point d’analyse est un pointeur spécial destiné à être utilisé par un filtre de système de fichiers. Azure File Sync utilise des points d’analyse pour définir dans le filtre de système de fichiers d’Azure File Sync (StorageSync.sys) à quel endroit du cloud le fichier est stocké. Cela permet de bénéficier d’un accès aisé. Les utilisateurs n’ont pas besoin de savoir qu’Azure File Sync est utilisé, ni comment obtenir l’accès au fichier dans votre partage de fichiers Azure. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime le point d’analyse du fichier. |
        | O | Hors connexion | Indique qu’une partie ou la totalité du contenu du fichier n’est pas stockée sur le disque. Quand un fichier est entièrement rappelé, la synchronisation de fichiers Azure supprime cet attribut. |

        ![Boîte de dialogue Propriétés d’un fichier dans laquelle l’onglet Détails est sélectionné](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Vous pouvez également voir les attributs de tous les fichiers d’un dossier en ajoutant le champ **Attributs** à l’affichage sous forme de tableau de l’Explorateur de fichiers. Pour ce faire, cliquez avec le bouton droit sur une colonne (par exemple, **Taille**), sélectionnez **Autres**, puis sélectionnez **Attributs** dans la liste déroulante.
        
        > [!NOTE]
        > Tous ces attributs sont également visibles pour les fichiers partiellement rappelés.
        
   * **Utiliser `fsutil` pour rechercher les points d’analyse sur un fichier.**
       Comme indiqué dans l’option précédente, un fichier hiérarchisé a toujours un point d’analyse défini. Un point d’analyse permet au pilote de filtre du système de fichiers Azure File Sync (StorageSync.sys) de récupérer du contenu à partir de partages de fichiers Azure qui n’est pas stocké localement sur le serveur. 

       Pour vérifier si un fichier a un point d’analyse, dans une fenêtre d’invite de commandes avec élévation de privilèges ou une fenêtre PowerShell, exécutez l’utilitaire `fsutil` :

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Si le fichier comporte un point d’analyse, vous pouvez vous attendre à voir s’afficher le message **Valeur de la balise d’analyse : 0x8000001e**. Cette valeur hexadécimale est la valeur de point d’analyse détenue par Azure File Sync. La sortie contient également les données d’analyse qui représentent le chemin du fichier dans votre partage de fichiers Azure.
        
        > [!WARNING]  
        > La commande d’utilitaire `fsutil reparsepoint` permet également de supprimer un point d’analyse. N’exécutez pas cette commande, sauf si l’équipe d’ingénierie Azure File Sync vous le demande. L’exécution de cette commande peut entraîner une perte de données. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Comment exclure des applications du suivi du dernier accès de la hiérarchisation Cloud

Avec la version 11.1 de l’agent Azure File Sync, vous pouvez désormais exclure des applications du dernier suivi des accès. Quand une application accède à un fichier, l’heure du dernier accès au fichier est mise à jour dans la base de données de hiérarchisation cloud. Les applications qui analysent le système de fichiers, telles que les antivirus, ont pour effet que tous les fichiers ont la même heure de dernier accès, ce qui a un impact sur leur hiérarchisation.

Pour exclure des applications du suivi de l’heure du dernier accès, ajoutez le nom du processus au paramètre de registre HeatTrackingProcessNameExclusionList qui se trouve sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Exemple : reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Par défaut, les processus de déduplication des données et de gestion de ressources pour serveur de fichiers (FSRM) sont exclus. Les modifications apportées à la liste d’exclusions de processus sont prises en compte par le système toutes les 5 minutes.

## <a name="how-to-access-the-heat-store"></a>Comment accéder à l’accumulateur de chaleur

La hiérarchisation Cloud utilise l’heure du dernier accès et la fréquence d’accès d’un fichier pour déterminer les fichiers à hiérarchiser. Le pilote de filtre de la hiérarchisation cloud (storagesync.sys) effectue le suivi de l’heure du dernier accès et enregistre les informations dans le magasin chaud de la hiérarchisation cloud. Vous pouvez récupérer l’accumulateur de chaleur et l’enregistrer dans un fichier CSV à l’aide d’une cmdlet PowerShell serveur-locale.

Il existe un accumulateur de chaleur unique pour tous les fichiers qui se trouvent sur le même volume. L’accumulateur de chaleur peut devenir très volumineux. Si vous devez uniquement récupérer le nombre d’éléments « le plus froid », utiliser la limite et un nombre. Envisagez également de filtrer par un sous-chemin d’accès et la racine du volume.

- Importez le module PowerShell : `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- ESPACE LIBRE DU VOLUME : pour connaître l’ordre dans lequel les fichiers seront hiérarchisés à l’aide de la stratégie d’espace libre du volume : `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- STRATÉGIE DE DATE : pour connaître l’ordre dans lequel les fichiers seront hiérarchisés à l’aide de la stratégie de date : `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Rechercher les informations de l’accumulateur de chaleur pour un fichier particulier : `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Afficher tous les fichiers dans l’ordre décroissant en fonction du dernier accès : `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Consultez l’ordre selon lequel les fichiers à plusieurs niveaux seront rappelés par le rappel en arrière-plan ou le rappel à la demande via PowerShell : `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Comment faire pour imposer la hiérarchisation d’un fichier ou répertoire

> [!NOTE]
> Lorsque vous sélectionnez un répertoire à hiérarchiser, seuls les fichiers qui se trouvent actuellement dans le répertoire sont hiérarchisés. Les fichiers créés après cette heure ne sont pas automatiquement hiérarchisés.

Quand elle est activée, la fonctionnalité de hiérarchisation cloud hiérarchise automatiquement les fichiers en fonction de la date du dernier accès et de la dernière modification afin d’atteindre le pourcentage d’espace libre du volume spécifié sur le point de terminaison cloud. Parfois, cependant, vous pouvez être amené à imposer une hiérarchisation manuelle d’un fichier. Cette opération peut s’avérer utile si vous enregistrez un fichier volumineux que vous n’envisagez pas de réutiliser pendant un certain temps, et que vous souhaitez dédier dans l’immédiat l’espace libre sur le volume à d’autres fichiers et dossiers. Vous pouvez forcer la hiérarchisation à l’aide des commandes PowerShell suivantes :

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Comment rappeler un fichier hiérarchisé sur le disque

Pour rappeler un fichier sur le disque, le plus simple consiste à l’ouvrir. Le filtre de système de fichiers d’Azure File Sync (StorageSync.sys) télécharge le fichier à partir de votre partage de fichiers Azure de façon fluide, sans aucune intervention de votre part. Pour les types de fichiers qui peuvent être partiellement lus ou transmis, comme les fichiers multimédias ou .zip, l’ouverture d’un fichier n’entraîne pas le téléchargement du fichier entier.

Pour vous assurer qu’un fichier est entièrement téléchargé sur le disque local, vous devez utiliser PowerShell pour forcer le rappel complet d’un fichier. Cette option peut être utile si vous souhaitez rappeler plusieurs fichiers en même temps (comme tous les fichiers d’un dossier). Ouvrez une session PowerShell sur le nœud de serveur sur lequel Azure File Sync est installé, puis exécutez les commandes PowerShell suivantes :
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Paramètres facultatifs :
* `-Order CloudTieringPolicy` rappellera d’abord les fichiers les plus récemment modifiés ou ayant été consultés, et est autorisée par la stratégie de hiérarchisation actuelle. 
    * Si la stratégie d’espace libre du volume est configurée, les fichiers sont rappelés jusqu’à ce que le paramètre de stratégie d’espace libre du volume soit atteint. Par exemple, si le paramètre de stratégie volume libre est de 20 %, le rappel s’arrête une fois que l’espace libre du volume atteint 20 %.  
    * Si la stratégie d’espace libre et de date du volume est configurée, les fichiers sont rappelés jusqu’à ce que le paramètre de stratégie d’espace libre et de date du volume soit atteint. Par exemple, si le paramètre de stratégie d’espace libre du volume est de 20 % et que la stratégie de date est de 7 jours, le rappel s’arrêtera une fois que l’espace libre du volume atteindra 20 % ou lorsque tous les fichiers consultés ou modifiés dans les 7 jours sont enregistrés en local.
* `-ThreadCount` détermine le nombre de fichiers rappelés en parallèle.
* `-PerFileRetryCount` détermine la fréquence à laquelle un rappel portant sur une fichier actuellement bloqué est tenté.
* `-PerFileRetryDelaySeconds` détermine le délai, en secondes, entre les tentatives de rappel, et doit systématiquement être utilisé avec le paramètre précédent.

Exemple :
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Si le volume local qui héberge le serveur n’a pas assez d’espace libre pour rappeler toutes les données hiérarchisées, l’applet de commande `Invoke-StorageSyncFileRecall` échoue.  

> [!Note]
> Pour rappeler les fichiers qui ont été hiérarchisés, la bande passante réseau doit être d'au moins 1 Mbps. Si la bande passante du réseau est inférieure à 1 Mbps, les fichiers peuvent ne pas se rappeler avec une erreur de délai d'attente.

## <a name="next-steps"></a>Étapes suivantes
* [FAQ sur les fichiers Azure](storage-files-faq.md)
