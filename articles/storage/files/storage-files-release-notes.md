---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Notes de publication de l’agent Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: cc919cabab94e078b8a212feec40047639a36341
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452913"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes de l’agent Azure File Sync sont prises en charge :

| Jalon | Numéro de version de l’agent | Date de publication | Statut |
|----|----------------------|--------------|------------------|
| Correctif cumulatif de décembre 2019 – [KB4522360](https://support.microsoft.com/help/4522360)| 9.1.0.0 | 12 décembre 2019 | Prise en charge |
| Version V9 - [KB4522359](https://support.microsoft.com/help/4522359)| 9.0.0.0 | 2 décembre 2019 | Prise en charge |
| Version V8 - [KB4511224](https://support.microsoft.com/help/4511224)| 8.0.0.0 | 8 octobre 2019 | Prise en charge |
| Correctif cumulatif de juillet 2019 : [KB4490497](https://support.microsoft.com/help/4490497)| 7.2.0.0 | 24 juillet 2019 | Prise en charge |
| Correctif cumulatif de juillet 2019 : [KB4490496](https://support.microsoft.com/help/4490496)| 7.1.0.0 | 12 juillet 2019 | Prise en charge |
| Publication V7 – [KB4490495](https://support.microsoft.com/help/4490495)| 7.0.0.0 | 19 juin 2019 | Prise en charge |
| Correctif cumulatif de juin 2019 – [KB4489739](https://support.microsoft.com/help/4489739)| 6.3.0.0 | 27 juin 2019 | Prise en charge |
| Correctif cumulatif de juin 2019 – [KB4489738](https://support.microsoft.com/help/4489738)| 6.2.0.0 | 13 juin 2019 | Prise en charge |
| Correctif cumulatif de juin 2019 – [KB4489737](https://support.microsoft.com/help/4489737)| 6.1.0.0 | 7 mai 2019 | Prise en charge |
| Publication V6 – [KB4489736](https://support.microsoft.com/help/4489736)| 6.0.0.0 | 21 avril 2019 | Prise en charge |
| Correctif cumulatif d’avril 2019 – [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 avril 2019 | Prise en charge |
| Correctif cumulatif de mars 2019 – [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 mars 2019 | Prise en charge |
| Version V5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 février 2019 | Prise en charge |
| Version V4 | 4.0.1.0 - 4.3.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 6 novembre 2019 |
| mise en production V3 | 3.1.0.0 - 3.4.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 19 août 2019 |
| Agents antérieurs à la disponibilité générale | 1.1.0.0 - 3.0.13.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 1er octobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-9100"></a>Agent version 9.1.0.0
Les notes de publication suivantes concernent la version 9.1.0.0 de l’agent Azure File Sync publiée le 12 décembre 2019. Ces notes s’ajoutent aux notes de publication de la version 9.0.0.0.

Problème résolu dans cette version :  
- La synchronisation échoue avec l’une des erreurs suivantes après la mise à niveau vers l’agent Azure File Sync version 9.0 :
    - 0x8e5e044e (JET_errWriteConflict)
    - 0x8e5e0450 (JET_errInvalidSesid)
    - 0x8e5e0442 (JET_errInstanceUnavailable)

## <a name="agent-version-9000"></a>Agent version 9.0.0.0
Les notes de publication suivantes concernent la version 9.0.0.0 de l’agent Azure File Sync (publiée le 2 décembre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Prise en charge de la restauration libre-service
    - Les utilisateurs peuvent désormais restaurer leurs fichiers à l’aide de la fonctionnalité « version précédente ». Avant la version v9, la fonctionnalité « version précédente » n’était pas prise en charge sur les volumes pour lesquels la hiérarchisation cloud était activée. Cette fonctionnalité doit être activée séparément pour chaque volume sur lequel existe un point de terminaison pour lequel la hiérarchisation cloud est activée. Pour plus d'informations, consultez les rubriques suivantes :  
[Restauration libre-service via Versions précédentes et VSS (Volume Shadow Copy Service)](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service). 
 
- Prise en charge de tailles de partage de fichiers plus importantes 
    - Azure File Sync prend désormais en charge jusqu’à 64 Tio et 100 millions de fichiers dans un seul espace de noms qui se synchronise.  
 
- Prise en charge de la déduplication des données sur Server 2019 
    - La déduplication des données est désormais prise en charge avec la hiérarchisation cloud activée sur Windows Server 2019. Pour que la déduplication des données soit prise en charge sur les volumes pour lesquels la hiérarchisation cloud est activée, Windows Update [KB4520062](https://support.microsoft.com/help/4520062) doit être installé. 
 
- Amélioration de la taille minimale d’un fichier à hiérarchiser 
    - La taille minimale d’un fichier à hiérarchiser est désormais basée sur le double de la taille de cluster du système de fichiers. Par exemple, par défaut, la taille de cluster du système de fichiers NTFS est de 4 Ko ; ainsi, la taille de fichier minimale pour un fichier à hiérarchiser est de 8 Ko. 
 
- Applet de commande de test de connectivité réseau 
    - Dans le cadre de la configuration d’Azure File Sync, plusieurs points de terminaison de service doivent être contactés. Ils ont chacun leur propre nom DNS qui doit être accessible au serveur. Ces URL sont également propres à la région dans laquelle un serveur est inscrit. Une fois qu’un serveur est inscrit, l’applet de commande de test de connectivité (PowerShell et l’utilitaire d’inscription du serveur) peut être utilisée pour tester les communications avec toutes les URL propres à ce serveur. Cette applet de commande peut aider à résoudre les problèmes qui se produisent quand une communication incomplète empêche le serveur de collaborer pleinement avec Azure File Sync et peut être utilisée pour affiner les configurations de proxy et de pare-feu.  
 
        Pour exécuter le test de connectivité réseau, exécutez les commandes PowerShell suivantes : 
 
        Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"  
        Test-StorageSyncNetworkConnectivity
 
- Supprimer l’amélioration du point de terminaison de serveur quand la hiérarchisation cloud est activée 
    - Comme auparavant, la suppression d’un point de terminaison de serveur n’entraîne pas la suppression de fichiers dans le partage de fichiers Azure. Toutefois, le comportement des points d’analyse sur le serveur local a changé. Les points d’analyse (pointeurs vers des fichiers qui ne sont pas locaux sur le serveur) sont désormais supprimés lors de la suppression d’un point de terminaison de serveur. Les fichiers entièrement mis en cache sont conservés sur le serveur. Cette amélioration a été apportée pour éviter l’existence de [fichiers hiérarchisés orphelins](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) lors de la suppression d’un point de terminaison de serveur. Si le point de terminaison de serveur est recréé, les points d’analyse des fichiers hiérarchisés sont recréés sur le serveur.  
 
- Améliorations des performances et de la fiabilité 
    - Réduction des échecs de rappel. La taille de rappel est désormais automatiquement ajustée en fonction de la bande passante réseau. 
    - Amélioration des performances de téléchargement lors de l’ajout d’un nouveau serveur à un groupe de synchronisation. 
    - Réduction de la proportion de fichiers non synchronisés en raison de conflits de contrainte. 
    - Les fichiers ne sont pas hiérarchisés ou sont rappelés de manière inattendue dans certains scénarios si le chemin du point de terminaison de serveur est un point de montage de volume.
    
### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](storage-sync-files-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Les fichiers à plusieurs niveaux sont inaccessibles si les fichiers ne sont pas rappelés avant la suppression du point de terminaison. Pour restaurer l’accès aux fichiers, recréez le point de terminaison de serveur. Si 30 jours se sont écoulés depuis que le point de terminaison de serveur a été supprimé ou si le point de terminaison cloud a été supprimé, les fichiers hiérarchisés non rappelés sont inutilisables. Pour en savoir plus, consultez [Les fichiers hiérarchisés ne sont pas accessibles sur le serveur après la suppression d’un point de terminaison de serveur](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-8000"></a>Version 8.0.0.0 de l’agent
Les notes de publication suivantes concernent la version 8.0.0.0 de l’agent Azure File Sync (mise en production le 8 octobre 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Améliorations apportées aux performances de restauration
    - Délais de récupération plus rapides pour la récupération effectuée par le biais de Sauvegarde Azure. Les fichiers restaurés seront synchronisés beaucoup plus rapidement sur les serveurs Azure File Sync. 
- Amélioration de l’expérience du portail de hiérarchisation cloud  
    - Si vous avez des fichiers hiérarchisés pour lesquels les rappels échouent, vous pouvez désormais afficher les erreurs de rappel dans les propriétés du point de terminaison de serveur. De plus, l’intégrité du point de terminaison de serveur affiche désormais une erreur et des étapes d’atténuation des risques si le pilote de filtre de hiérarchisation cloud n’est pas chargé sur le serveur.
- Simplification de l’installation de l’agent
    - Le module PowerShell Az\AzureRM n’est plus nécessaire pour inscrire le serveur, ce qui simplifie et accélère l’installation.
- Améliorations diverses au niveau des performances et de la fiabilité

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](storage-sync-files-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Les fichiers à plusieurs niveaux sont inaccessibles si les fichiers ne sont pas rappelés avant la suppression du point de terminaison. Pour restaurer l’accès aux fichiers, recréez le point de terminaison de serveur. Si 30 jours se sont écoulés depuis que le point de terminaison de serveur a été supprimé ou si le point de terminaison cloud a été supprimé, les fichiers hiérarchisés non rappelés sont inutilisables. Pour en savoir plus, consultez [Les fichiers hiérarchisés ne sont pas accessibles sur le serveur après la suppression d’un point de terminaison de serveur](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint).
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-7200"></a>Agent version 7.2.0.0
Les notes de publication suivantes concernent la version 7.2.0.0 de l’agent Azure File Sync (mise en production le 24 juillet 2019). Ces notes s’ajoutent aux notes de publication de la version 7.0.0.0.

Liste des problèmes résolus dans cette version :  
- L’agent de synchronisation de stockage (FileSyncSvc) se bloque si la configuration du proxy est nulle.
- Le point de terminaison du serveur démarre BCDR (erreur 0x80c80257 - ECS_E_BCDR_IN_PROGRESS) si plusieurs points de terminaison sur le serveur portent le même nom.
- Améliorations de la fiabilité de la hiérarchisation cloud.

## <a name="agent-version-7100"></a>Agent version 7.1.0.0
Les notes de publication suivantes concernent la version 7.1.0.0 de l’agent Azure File Sync (mise en production le 12 juillet 2019). Ces notes s’ajoutent aux notes de publication de la version 7.0.0.0.

Liste des problèmes résolus dans cette version :  
- L’accès à un emplacement de point de terminaison de serveur via SMB est lent sur Windows Server 2012 R2. 
- Utilisation accrue du processeur après l’installation de l’agent Azure File Sync v6.
- Améliorations de la télémétrie de hiérarchisation cloud.
- Améliorations diverses de la fiabilité de la hiérarchisation cloud et de la synchronisation.

## <a name="agent-version-7000"></a>Agent version 7.0.0.0
Les notes de publication suivantes concernent la version 7.0.0.0 de l’agent Azure File Sync (publiée le 19 juin 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Prise en charge de tailles de partage de fichiers plus importantes
    - Avec la préversion des partages de fichiers Azure plus volumineux, nous augmentons nos limites de prise en charge de la synchronisation de fichiers. Dans le cadre de cette première étape, Azure File Sync prend désormais en charge jusqu’à 25 To et 50 millions de fichiers dans un seul espace de noms qui se synchronise. Pour demander à disposer de la préversion du partage de fichiers volumineux, remplissez ce formulaire https://aka.ms/azurefilesatscalesurvey. 
- Prise en charge du paramètre de pare-feu et de réseau virtuel sur les comptes de stockage
    - Azure File Sync prend désormais en charge le paramètre de pare-feu et de réseau virtuel sur les comptes de stockage. Pour configurer votre déploiement de manière à utiliser le paramètre de pare-feu et de réseau virtuel, consultez [Configurer les paramètres de pare-feu et de réseau virtuel](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=azure-portal#configure-firewall-and-virtual-network-settings).
- Cmdlet PowerShell pour synchroniser immédiatement les fichiers modifiés dans le partage de fichiers Azure
    - Pour synchroniser immédiatement les fichiers modifiés dans le partage de fichiers Azure, vous pouvez utiliser la cmdlet PowerShell Invoke-AzStorageSyncChangeDetection afin de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Cette cmdlet est destinée aux scénarios dans lesquels un certain type de processus automatisé apporte des modifications au partage de fichiers Azure ou dans lesquels les modifications sont apportées par un administrateur (comme le déplacement de fichiers et de répertoires dans le partage). Pour les changements d’utilisateurs finaux, il est recommandé d’installer l’agent Azure File Sync dans une machine virtuelle IaaS et de demander aux utilisateurs finaux d’accéder au partage de fichiers via la machine virtuelle IaaS. Ainsi, toutes les modifications sont rapidement synchronisées avec d’autres agents, sans qu’il soit nécessaire d’utiliser la cmdlet Invoke-AzStorageSyncChangeDetection. Pour plus d’informations, consultez la documentation [Invoke-AzStorageSyncChangeDetection](https://docs.microsoft.com/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
- Expérience améliorée du portail en présence de fichiers non synchronisés
    - Si certains de vos fichiers ne sont pas synchronisés, nous faisons maintenant la distinction entre les erreurs temporaires et les erreurs persistantes dans le portail. Les erreurs temporaires se corrigent généralement sans intervention de l’administrateur. Par exemple, un fichier en cours d’utilisation ne sera synchronisé qu'une fois le descripteur de fichier fermé. Pour les erreurs persistantes, nous affichons maintenant le nombre de fichiers concernés par chaque erreur. Le nombre d’erreurs persistantes s'affiche également dans la colonne des fichiers non synchronisés de tous les points de terminaison de serveur d'un groupe de synchronisation.
- Amélioration de la restauration au niveau fichier de Sauvegarde Azure
    - Les fichiers restaurés à l’aide de Sauvegarde Azure sont désormais détectés et synchronisés plus rapidement sur le point de terminaison de serveur.
- Amélioration de la fiabilité de la cmdlet de rappel de hiérarchisation cloud 
    - L’applet de commande Invoke-StorageSyncFileRecall permet désormais aux clients de spécifier le nombre de nouvelles tentatives par fichier et le délai de nouvelle tentative par fichier, à l’instar de robocopy. Auparavant, cette applet de commande rappellait tous les fichiers hiérarchisés sous un chemin donné dans un ordre aléatoire. Avec le nouveau paramètre -Order, cette applet de commande rappelle tout d’abord les données les plus récentes et respecte la stratégie de hiérarchisation cloud (arrêter le rappel si la stratégie de date est satisfaite ou si l’espace libre du volume est atteint, selon ce qui se produit en premier).
- Prise en charge du protocole TLS 1.2 uniquement (les versions 1.0 et 1.1 du protocole TLS sont désactivées)
    - Azure File Sync prend désormais en charge l’utilisation du protocole TLS 1.2 uniquement sur les serveurs pour lesquels les versions 1.0 et 1.1 du protocole TLS sont désactivées. Avant cette amélioration, l’inscription du serveur échouait si le protocole TLS 1.0 ou 1.1 était désactivé sur le serveur.
- Diverses améliorations des performances et de la fiabilité pour la synchronisation et la hiérarchisation cloud
    - Cette publication comprend plusieurs améliorations des performances et de la fiabilité. Certaines d’entre elles visent à rendre plus efficace la hiérarchisation cloud, et Azure File Sync dans son ensemble fonctionne mieux dans les situations où vous avez une planification définie de limitation de la bande passante.

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](storage-sync-files-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Les fichiers à plusieurs niveaux sont inaccessibles si les fichiers ne sont pas rappelés avant la suppression du point de terminaison. Pour restaurer l’accès aux fichiers, recréez le point de terminaison de serveur. Si 30 jours se sont écoulés depuis que le point de terminaison de serveur a été supprimé ou si le point de terminaison cloud a été supprimé, les fichiers hiérarchisés non rappelés sont inutilisables.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.

## <a name="agent-version-6300"></a>Version 6.3.0.0 de l’agent
Les notes de publication suivantes concernent la version 6.3.0.0 de l’agent Azure File Sync publiée le 27 juin 2019. Ces notes s’ajoutent aux notes de publication de la version 6.0.0.0.

Liste des problèmes résolus dans cette version :  
- L’accès à un emplacement de point de terminaison de serveur via SMB est lent sur Windows Server 2012 R2 
- Utilisation accrue du processeur après l’installation de l’agent Azure File Sync v6
- Améliorations de la télémétrie de hiérarchisation cloud

## <a name="agent-version-6200"></a>Version 6.2.0.0 de l’agent
Les notes de publication suivantes concernent la version 6.2.0.0 de l’agent Azure File Sync publié le 13 juin 2019. Ces notes s’ajoutent aux notes de publication de la version 6.0.0.0.

Liste des problèmes résolus dans cette version :  
- Après avoir créé un point de terminaison de serveur, une utilisation élevée du processeur peut se produire lorsque le rappel en arrière-plan télécharge des fichiers sur le serveur
- La synchronisation et la hiérarchisation des opérations cloud peuvent échouer avec l’erreur ECS_E_SERVER_CREDENTIAL_NEEDED en raison de l’expiration du jeton
- Le rappel d’un fichier peut échouer si l’URL pour télécharger le fichier contient des caractères réservés 

## <a name="agent-version-6100"></a>Version 6.1.0.0 de l’agent
Les notes de publication suivantes concernent la version 6.1.0.0 de l’agent Azure File Sync publiée le 6 mai 2019. Ces notes s’ajoutent aux notes de publication de la version 6.0.0.0.

Liste des problèmes résolus dans cette version :  
- Windows Admin Center n’affiche pas la version de l’agent et la configuration du point de terminaison du serveur sur les serveurs sur lesquels la version 6.0 de l’agent Azure File Sync est installée.

## <a name="agent-version-6000"></a>Version 6.0.0.0 de l’agent
Les notes de publication suivantes concernent la version 6.0.0.0 de l’agent Azure File Sync (publiée le 22 avril 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Prise en charge de la mise à jour automatique de l’agent
  - Nous avons tenu compte de vos commentaires et ajouté une fonctionnalité de mise à jour automatique à l’agent du serveur Azure File Sync. Pour plus d’informations, voir [Stratégie de mise à jour de l’agent Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#azure-file-sync-agent-update-policy).
- Prise en charge des listes de contrôle d’accès de partage de fichiers Azure
  - Azure File Sync a toujours pris en charge la synchronisation des listes de contrôle d’accès entre les points de terminaison de serveur, mais ces dernières n’étaient pas synchronisées sur le point de terminaison cloud (partage de fichiers Azure). Cette version ajoute la prise en charge de la synchronisation des listes de contrôle d’accès entre le serveur et les points de terminaison cloud.
- Sessions de synchronisation de chargement et téléchargement en parallèle pour un point de terminaison de serveur 
  - Les points de terminaison de serveur prennent désormais en charge le chargement et le téléchargement simultanés de fichiers. Il ne faut plus attendre qu’un téléchargement se termine avant de charger des fichiers sur le partage de fichiers Azure. 
- Nouvelles cmdlets de hiérarchisation cloud pour obtenir le volume et l’état de hiérarchisation
  - Deux nouvelles cmdlets PowerShell locales sur le serveur peuvent désormais être utilisées pour obtenir des informations sur la cmdlet et le rappel de fichiers. Elles rendent disponibles les informations de journalisation de deux canaux d’événement sur le serveur :
    - La cmdlet Get-StorageSyncFileTieringResult répertorie tous les fichiers et leurs chemins d’accès non hiérarchisés, ainsi que la raison pour laquelle ils ne le sont pas.
    - La cmdlet Get-StorageSyncFileRecallResult signale tous les événements de rappel de fichier. Elle répertorie tous les fichiers rappelés et leur chemin d’accès, ainsi que la réussite ou l’échec de ce rappel.
  - Par défaut, les deux canaux d’événement peuvent stocker jusqu’à 1 Mo chacun. Vous pouvez cependant augmenter la quantité de fichiers signalés en augmentant la taille de canal d’événement.
- Prise en charge du mode FIPS
  - Azure File Sync prend désormais en charge l’activation du mode FIPS sur des serveurs sur lesquels l’agent Azure File Sync est installé.
    - Avant d’activer le mode FIPS sur votre serveur, installez l’agent Azure File Sync et le [module PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) sur votre serveur. Si le mode FIPS est déjà activé sur le serveur, [téléchargez manuellement](/powershell/scripting/gallery/how-to/working-with-packages/manual-download) le [module PackageManagement](https://www.powershellgallery.com/packages/PackageManagement/1.1.7.2) sur votre serveur.
- Améliorations diverses de la fiabilité de la hiérarchisation cloud et de la synchronisation

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](storage-sync-files-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Les fichiers à plusieurs niveaux sont inaccessibles si les fichiers ne sont pas rappelés avant la suppression du point de terminaison. Pour restaurer l’accès aux fichiers, recréez le point de terminaison de serveur. Si 30 jours se sont écoulés depuis que le point de terminaison de serveur a été supprimé ou si le point de terminaison cloud a été supprimé, les fichiers hiérarchisés non rappelés sont inutilisables.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.

## <a name="agent-version-5200"></a>Version 5.2.0.0 de l’agent
Les notes de publication suivantes concernent la version 5.2.0.0 de l’agent Azure File Sync publiée le 4 avril 2019. Ces notes s’ajoutent aux notes de publication de la version 5.0.2.0.

Liste des problèmes résolus dans cette version :  
- Amélioration de la fiabilité des fonctionnalités de transfert de données hors connexion et de la reprise de transfert de données
- Améliorations de la télémétrie de synchronisation

## <a name="agent-version-5100"></a>Version 5.1.0.0 de l’agent
Les notes de publication suivantes concernent la version 5.1.0.0 de l’agent Azure File Sync publiée le 7 mars 2019. Ces notes s’ajoutent aux notes de publication de la version 5.0.2.0.

Liste des problèmes résolus dans cette version :  
- La synchronisation des fichiers peut échouer avec l’erreur 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) si l’énumération de la modification échoue sur le serveur
- Si une session ou un fichier de synchronisation reçoit une erreur 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), la synchronisation va désormais relancer l’opération
- La synchronisation des fichiers peut échouer avec l’erreur 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE)
- Le rappel de fichiers peut entraîner une utilisation importante de la mémoire
- Améliorations de la télémétrie de hiérarchisation cloud 

## <a name="agent-version-5020"></a>Agent version 5.0.2.0
Les notes de publication suivantes concernent la version 5.0.2.0 de l’agent Azure File Sync (publiée le 12 février 2019).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Prise en charge du cloud Azure Government
  - Nous avons ajouté la prise en charge en préversion du cloud Azure Government, qui exige un abonnement sur liste verte et un téléchargement d’agent spécial auprès de Microsoft. Pour demander l’accès à la préversion, envoyez-nous directement un e-mail à l’adresse [AzureFiles@microsoft.com](mailto:AzureFiles@microsoft.com).
- Pris en charge de la déduplication des données
    - La déduplication des données est maintenant entièrement prise en charge avec la hiérarchisation cloud sur Windows Server 2016 et 2019. Le fait d’activer la déduplication sur un volume pour lequel la hiérarchisation cloud est activée permet de mettre en cache plus de fichiers en local sans avoir à approvisionner davantage de stockage.
- Prise en charge du transfert de données hors connexion (par exemple, par Data Box)
    - Migrez facilement de gros volumes de données dans Azure File Sync suivant la méthode de votre choix. Vous pouvez choisir Azure Data Box, AzCopy et même des services de migration tiers. Il n’est pas nécessaire d’utiliser une grande quantité de bande passante pour placer des données dans Azure : avec Data Box, il vous suffit de les envoyer ! Pour plus d'informations, voir [Documents sur le transfert de données hors connexion](https://aka.ms/AFS/OfflineDataTransfer).
- Amélioration des performances de synchronisation
    - Les clients disposant de plusieurs points de terminaison de serveur sur le même volume ont pu rencontrer des problèmes de lenteur de la synchronisation avant cette version. Azure File Sync crée une capture instantanée VSS temporaire une fois par jour sur le serveur pour synchroniser les fichiers qui ont des descripteurs ouverts. La synchronisation gère maintenant plusieurs points de terminaison de serveur sur un volume lorsqu'une session de synchronisation VSS est active. Il n’est plus nécessaire d’attendre la fin d’une session de synchronisation VSS pour reprendre la synchronisation d’autres points de terminaison de serveur sur le volume.
- Amélioration du monitoring sur le portail
    - Des graphiques ont été ajoutés sur le portail du service de synchronisation du stockage :
        - nombre de fichiers synchronisés ;
        - taille des données transférées ;
        - nombre de fichiers non synchronisés ;
        - taille des données rappelées ;
        - état de connectivité du serveur.
    - Pour plus d’informations, voir [Effectuer le monitoring d’Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring).
- Amélioration de la scalabilité et de la fiabilité
    - Le nombre maximal d’objets système de fichiers (répertoires et fichiers) dans un répertoire est passé à 1 000 000, contre 200 000 auparavant.
    - En cas d’interruption d’un transfert de fichiers volumineux, la synchronisation tente de reprendre le transfert de données, plutôt que de les retransmettre. 

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.
- Le mode FIPS n’est pas pris en charge et doit être désactivé. 

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Fichiers avec caractères non pris en charge. Reportez-vous au [Guide de résolution des problèmes](storage-sync-files-troubleshoot.md#handling-unsupported-characters) pour consulter la liste des caractères non pris en charge.
- Fichiers ou répertoires se terminant par un point.
- Chemins de plus de 2 048 caractères.
- La partie liste de contrôle d’accès discrétionnaire (DACL) d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie liste de contrôle d’accès système (SACL) d’un descripteur de sécurité qui est utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données.

    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoint"></a>Point de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Les fichiers à plusieurs niveaux sont inaccessibles si les fichiers ne sont pas rappelés avant la suppression du point de terminaison. Pour restaurer l’accès aux fichiers, recréez le point de terminaison de serveur. Si 30 jours se sont écoulés depuis que le point de terminaison de serveur a été supprimé ou si le point de terminaison cloud a été supprimé, les fichiers hiérarchisés non rappelés sont inutilisables.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
