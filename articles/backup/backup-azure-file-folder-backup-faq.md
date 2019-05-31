---
title: Questions courantes lors de la sauvegarde des fichiers et dossiers avec sauvegarde Azure
description: Traite des questions courantes sur la sauvegarde de fichiers et dossiers avec sauvegarde Azure.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: dacurwin
ms.openlocfilehash: 1ac3cdecc79cafb9ea2697cca3c87b2ebe083d40
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254865"
---
# <a name="common-questions-about-backing-up-files-and-folders"></a>Questions courantes sur la sauvegarde de fichiers et dossiers 

Cet article contient des réponses aux questions les plus fréquentes sont nombreux sauvegarde des fichiers et dossiers avec l’Agent Microsoft Azure Recovery Services (MARS) dans le [sauvegarde Azure](backup-overview.md) service.

## <a name="general"></a>Généralités

### <a name="why-does-the-mars-agent-need-net-framework-452-or-higher"></a>Pourquoi l’agent MARS doit-il .NET framework 4.5.2 ou version ultérieure ?

Nouvelles fonctionnalités disponibles dans [restauration instantanée](backup-azure-restore-windows-server.md#use-instant-restore-to-recover-data-to-the-same-machine) a besoin de .NET Framework 4.5.2 ou version ultérieure.

## <a name="configure-backups"></a>Configurer des sauvegardes

### <a name="where-can-i-download-the-latest-version-of-the-mars-agent"></a>Où puis-je télécharger la dernière version de l’agent MARS ? 
Le dernier agent MARS utilisé lors de la sauvegarde des machines Windows Server, System Center DPM et serveur de sauvegarde Microsoft Azure est disponible pour [télécharger](https://aka.ms/azurebackup_agent). 

### <a name="how-long-are-vault-credentials-valid"></a>Combien de temps sont les informations d’identification de coffre valide ?
Les informations d’identification du coffre expirent au bout de 48 heures. Si le fichier d’informations d’identification expire, retéléchargez le fichier à partir du portail Azure.

### <a name="from-what-drives-can-i-back-up-files-and-folders"></a>À partir de quels lecteurs puis-je sauvegarder des fichiers et dossiers ? 

Vous ne pouvez pas sauvegarder les types de disques et volumes suivants :

* Média amovible : toutes les sources d’éléments de sauvegarde doivent être déclarées fixes.
* Volumes en lecture seule : le volume doit être accessible en écriture pour que le service VSS puisse fonctionner.
* Volumes hors connexion : le volume doit être en ligne pour que le service VSS puisse fonctionner.
* Partages réseau : le volume doit être local sur le serveur à sauvegarder à l’aide de la sauvegarde en ligne.
* Volumes protégés par BitLocker : le volume doit être déverrouillé pour que la sauvegarde soit possible.
* Identification du système de fichiers : NTFS est le seul système de fichiers pris en charge.

### <a name="what-file-and-folder-types-are-supported"></a>Quels types de fichiers et dossiers sont pris en charge ?

Les types suivants sont pris en charge :

* Chiffré
* Compressé
* Partiellement alloué
* Compressé + partiellement alloué
* Liens physiques : non pris en charge, ignorés
* Point d’analyse : non pris en charge, ignoré
* Chiffré + partiellement alloué : non pris en charge, ignoré
* Flux compressé : non pris en charge, ignoré
* Points de réanalyse, y compris les liens DFS et les points de jonction


### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-an-azure-vm"></a>Puis-je utiliser l’agent MARS pour sauvegarder des fichiers et dossiers sur une machine virtuelle Azure ?  
Oui. Azure Backup fournit une sauvegarde au niveau de la machine virtuelle pour les machines virtuelles Azure à l’aide de l’extension de machine virtuelle pour l’agent de machine virtuelle Azure. Si vous souhaitez sauvegarder des fichiers et dossiers sur le système d’exploitation Windows invité sur la machine virtuelle, vous pouvez installer l’agent MARS pour ce faire. 

### <a name="can-i-use-the-mars-agent-to-back-up-files-and-folders-on-temporary-storage-for-the-azure-vm"></a>Puis-je utiliser l’agent MARS pour sauvegarder les fichiers et dossiers sur le stockage temporaire pour la machine virtuelle Azure ? 
Oui. Installez l’agent MARS et sauvegarder les fichiers et dossiers sur le système d’exploitation invité Windows vers un stockage temporaire. -Tâches sauvegarde échouent sur le stockage temporaire de données seront effacées.
- Si les données de stockage temporaire sont supprimées, vous pouvez uniquement restaurer vers le stockage non volatile.

### <a name="how-do-i-register-a-server-to-another-region"></a>Comment inscrire un serveur vers une autre région ?

Données de sauvegarde sont envoyées au centre de données du coffre dans lequel le serveur est inscrit. Pour modifier le centre de données le plus simple consiste à désinstaller et réinstaller l’agent, puis inscrire l’ordinateur dans un coffre dans la région que vous avez besoin

### <a name="does-the-mars-agent-support-windows-server-2012-deduplication"></a>La prise en charge de l’agent MARS Windows Server 2012 la déduplication ?
Oui. L’agent MARS convertit les données dédupliquées en données normales lorsqu’il prépare l’opération de sauvegarde. Il optimise les données pour la sauvegarde, chiffre les données puis envoie ensuite les données chiffrées dans le coffre.

## <a name="manage-backups"></a>Gestion des sauvegardes

### <a name="what-happens-if-i-rename-a-windows-machine-configured-for-backup"></a>Que se passe-t-il si je renomme un ordinateur Windows configuré pour la sauvegarde ?

Lorsque vous renommez un ordinateur Windows, toutes les sauvegardes actuellement configurées sont arrêtées.

- Vous devez inscrire le nouveau nom de l’ordinateur auprès du coffre de sauvegarde.
- Lorsque vous enregistrez le nouveau nom auprès du coffre, la première opération est un *complète* sauvegarde.
- Si vous avez besoin récupérer les données sauvegardées dans le coffre avec l’ancien nom de serveur, utilisez l’option Restaurer vers un autre emplacement dans l’Assistant Récupération de données. [Plus d’informations](backup-azure-restore-windows-server.md#use-instant-restore-to-restore-data-to-an-alternate-machine) 

### <a name="what-is-the-maximum-file-path-length-for-backup"></a>Qu’est la longueur de chemin d’accès de fichier maximale pour la sauvegarde ?
L’agent MARS s’appuie sur NTFS et utilise la spécification de longueur de chemin d’accès limitée par le [Windows API](/windows/desktop/FileIO/naming-a-file#fully_qualified_vs._relative_paths). Si les fichiers que vous souhaitez protéger sont plus longs que la valeur autorisée, sauvegardez le dossier parent ou le lecteur de disque.  

### <a name="what-characters-are-allowed-in-file-paths"></a>Quels caractères sont autorisés dans les chemins d’accès de fichier ?

L’agent MARS s’appuie sur NTFS et permet de [pris en charge les caractères](/windows/desktop/FileIO/naming-a-file#naming_conventions) dans les noms de chemins.

### <a name="the-warning-azure-backups-have-not-been-configured-for-this-server-appears"></a>L’avertissement « Sauvegardes Azure n’a pas ont été configurées pour ce serveur » s’affiche.
Cet avertissement peut apparaître même si vous avez configuré une stratégie de sauvegarde, lorsque les paramètres de planification de sauvegarde stockés sur le serveur local ne sont pas les mêmes que les paramètres stockés dans le coffre de sauvegarde.
- Lorsque le serveur ou les paramètres ont été restaurés à un état correct connu, les planifications de sauvegarde peuvent être désynchronisées.
- Si vous recevez cet avertissement, [configurer](backup-azure-manage-windows-server.md) la stratégie de sauvegarde à nouveau et sauvegarde d’une demande d’exécution pour resynchroniser le serveur local avec Azure.


## <a name="manage-the-backup-cache-folder"></a>Gérer le dossier du cache de sauvegarde

### <a name="whats-the-minimum-size-requirement-for-the-cache-folder"></a>Quelle est la taille minimale requise du dossier du cache ?
La taille du dossier du cache détermine la quantité de données que vous sauvegardez.
- Les volumes de dossier de cache doivent avoir un espace libre égal au moins 5 à 10 % de la taille totale des données de sauvegarde.
- Si le volume possède moins de 5 % d’espace libre, augmentez la taille du volume, ou déplacez le dossier du cache vers un volume avec suffisamment d’espace.
- Si vous sauvegardez l’état du système Windows, vous devez 30 à 35 Go d’espace libre dans le volume contenant le dossier du cache supplémentaires
### <a name="how-do-i-change-the-cache-location-for-the-mars-agent"></a>Comment modifier l’emplacement du cache pour l’agent MARS ?


1. Exécutez cette commande dans une invite de commandes avec élévation de privilèges pour arrêter le moteur de sauvegarde :

    ```PS C:\> Net stop obengine```

2. Ne déplacez pas les fichiers. Au lieu de cela, copiez le dossier d’espace de cache à un autre lecteur disposant de suffisamment d’espace.
3. Mettre à jour les entrées de Registre suivantes avec le chemin d’accès du nouveau dossier de cache.<br/>

    | Chemin d’accès au Registre | Clé de Registre | Valeur |
    | --- | --- | --- |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Emplacement du nouveau dossier de cache* |
    | `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Emplacement du nouveau dossier de cache* |

4. Redémarrez le moteur de sauvegarde à une invite de commandes avec élévation de privilèges :

    ```PS C:\> Net start obengine```

5. Une fois la sauvegarde terminée avec succès à l’aide du nouvel emplacement, vous pouvez supprimer le dossier du cache d’origine.


### <a name="where-should-the-cache-folder-be-located"></a>Où le dossier du cache doit se trouver ?

Les emplacements suivants pour le dossier du cache ne sont pas recommandés :

* Média amovible/partage réseau : le dossier du cache doit être local sur le serveur nécessitant une sauvegarde à l’aide de la sauvegarde en ligne. Les emplacements réseau ou les médias amovibles comme les lecteurs USB ne sont pas pris en charge.
* Volumes hors connexion : le dossier du cache doit être en ligne pour la sauvegarde attendue avec l’agent de sauvegarde Azure

### <a name="are-there-any-attributes-of-the-cache-folder-that-arent-supported"></a>Existe-t-il des attributs du dossier du cache qui ne sont pas pris en charge ?
Les attributs suivants ou leurs combinaisons ne sont pas pris en charge pour le dossier du cache :

* Chiffré
* Dédupliqué
* Compressé
* Partiellement alloué
* Point d’analyse

Le dossier du cache et les métadonnées du disque dur virtuel ne possèdent les attributs nécessaires pour l’agent de sauvegarde Azure.

### <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-for-backup"></a>Existe-t-il un moyen d’ajuster la quantité de bande passante utilisée pour la sauvegarde ?
 
Oui, vous pouvez utiliser la **modifier les propriétés** option dans l’agent MARS pour ajuster la bande passante et le minutage. [En savoir plus](backup-configure-vault.md#enable-network-throttling)**.

## <a name="restore"></a>Restore

### <a name="what-happens-if-i-cancel-an-ongoing-restore-job"></a>Que se passe-t-il si j’annule un travail de restauration en cours ?

Si un travail de restauration en cours est annulé, le processus de restauration s’arrête. Tous les fichiers restaurés avant l’annulation restent en destination configuré (emplacement d’origine ou à un autre), sans les restaurations.


## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](tutorial-backup-windows-server-to-azure.md) comment sauvegarder une machine Windows.
