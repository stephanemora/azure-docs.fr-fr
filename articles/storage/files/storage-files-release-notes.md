---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Notes de publication de l’agent Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 4/4/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: e709ccee9dfcc6b6931df86b5dd38c7255baefdb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59792571"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes de l’agent Azure File Sync sont prises en charge :

| Jalon | Numéro de version de l’agent | Date de lancement | Statut |
|----|----------------------|--------------|------------------|
| Avril 2019 correctif cumulatif - [KB4481061](https://support.microsoft.com/help/4481061)| 5.2.0.0 | 4 avril 2019 | Prise en charge (version recommandée) |
| Mars 2019 correctif cumulatif - [KB4481060](https://support.microsoft.com/help/4481060)| 5.1.0.0 | 7 mars 2019 | Pris en charge |
| Version V5 – [KB4459989](https://support.microsoft.com/help/4459989)| 5.0.2.0 | 12 février 2019 | Pris en charge |
| Correctif cumulatif de janvier 2019 - [KB4481059](https://support.microsoft.com/help/4481059)| 4.3.0.0 | 14 janvier 2019 | Pris en charge |
| Correctif cumulatif de décembre 2018 – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 décembre 2018 | Pris en charge |
| Correctif cumulatif de décembre 2018 | 4.1.0.0 | 4 décembre 2018 | Pris en charge |
| Version V4 | 4.0.1.0 | 13 novembre 2018 | Pris en charge |
| Correctif cumulatif de septembre 2018 | 3.3.0.0 | 24 septembre 2018 | Pris en charge – La version de l’agent expirera le 19 juillet 2019 |
| Correctif cumulatif d’août 2018 | 3.2.0.0 | 15 août 2018 | Pris en charge – La version de l’agent expirera le 19 juillet 2019 |
| Disponibilité générale | 3.1.0.0 | 19 juillet 2018 | Pris en charge – La version de l’agent expirera le 19 juillet 2019 |
| Agents ayant expiré | 1.1.0.0 - 3.0.13.0 | S.O. | Non pris en charge : les versions de l’agent ont expiré le 1er octobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-5200"></a>Version de l’agent 5.2.0.0
Les notes de publication suivantes concernent la version 5.2.0.0 de l’agent Azure File Sync publiée le 4 avril 2019. Ces notes s’ajoutent les notes de publication répertoriées pour la version 5.0.2.0.

Liste des problèmes résolus dans cette version :  
- Améliorations de la fiabilité pour le transfert de données hors connexion et de transfert de données reprendre des fonctionnalités
- Améliorations de la télémétrie synchronisation

## <a name="agent-version-5100"></a>Version de l’agent 5.1.0.0
Les notes de publication suivantes concernent la version 5.1.0.0 de l’agent Azure File Sync publiée le 7 mars 2019. Ces notes s’ajoutent les notes de publication répertoriées pour la version 5.0.2.0.

Liste des problèmes résolus dans cette version :  
- Synchronisation avec l’erreur 0x80c8031d (ECS_E_CONCURRENCY_CHECK_FAILED) si l’énumération des modifications échoue sur le serveur de fichiers peuvent échouer
- Si un fichier ou une session de synchronisation reçoit une erreur 0x80072f78 (WININET_E_INVALID_SERVER_RESPONSE), sync maintenant réessaiera l’opération
- Fichiers de synchronisation avec l’erreur 0x80c80203 (ECS_E_SYNC_INVALID_STAGED_FILE) peuvent échouer
- L’utilisation élevée de la mémoire peut se produire lors du rappel de fichiers
- Améliorations de la télémétrie hiérarchisation de cloud 

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
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) du guide de planification. 

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

## <a name="agent-version-4300"></a>Version 4.3.0.0 de l’agent
Les notes de publication suivantes concernent la version 4.3.0.0 de l’agent Azure File Sync (mis en production le 14 janvier 2019). Ces notes s’ajoutent aux notes de publication de la version 4.0.1.0.

Liste des problèmes résolus dans cette version :  
- Les fichiers ne sont pas hiérarchisées après la mise à niveau de l’agent Azure File Sync vers la version 4.x.
- AfsUpdater.exe est désormais pris en charge sur Windows Server 2019.
- Améliorations diverses de la fiabilité pour la synchronisation. 

## <a name="agent-version-4200"></a>Version 4.2.0.0 de l’agent
Les notes de publication suivantes concernent la version 4.2.0.0 de l’agent Azure File Sync, publiée le 10 décembre 2018. Ces notes s’ajoutent aux notes de publication de la version 4.0.1.0.

Liste des problèmes résolus dans cette version :  
- Une erreur d’arrêt 0x3B ou 0x1E se produit parfois lors de la création d’un instantané VSS.  
- Une fuite de mémoire se produit parfois lorsque la hiérarchisation cloud est activée.  

## <a name="agent-version-4100"></a>Version 4.1.0.0 de l’agent
Les notes de publication suivantes concernent la version 4.1.0.0 de l’agent Azure File Sync, publiée le 4 décembre 2018. Ces notes s’ajoutent aux notes de publication de la version 4.0.1.0.

Liste des problèmes résolus dans cette version :  
- Le serveur cesse parfois de répondre en raison d’une fuite de mémoire de hiérarchisation du cloud.  
- L’installation de l’agent échoue avec l’erreur suivante : Erreur 1921. Impossible d’arrêter le service « Storage Sync Agent » (FileSyncSvc).  Vérifiez que vous disposez de privilèges suffisants pour arrêter les services système.  
- Le service Storage Sync Agent (FileSyncSvc) se bloque parfois lorsque l’utilisation de la mémoire est élevée.  
- Améliorations diverses de la fiabilité de la hiérarchisation cloud et de la synchronisation.

## <a name="agent-version-4010"></a>Version 4.0.1.0 de l'agent
Les notes de publication suivantes concernent la version 4.0.1.0 de l’agent Azure File Sync (mise en production le 13 novembre 2018).

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.
- Le mode FIPS n’est pas pris en charge et doit être désactivé. 
- Une erreur 0x3B ou 0x1E peut se produire lors de la création d’un instantané VSS.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Les filtres de fichiers FSRM peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’agent Azure File Sync doit être installé après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.

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
- Le paramètre de stratégie de hiérarchisation cloud basée sur la date permet de spécifier les fichiers qui doivent être mis en cache en cas d'accès dans un nombre de jours spécifié. Pour plus d’informations, consultez [Vue d’ensemble de la hiérarchisation cloud](https://docs.microsoft.com/azure/storage/files/storage-sync-cloud-tiering#afs-force-tiering).
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.

## <a name="agent-version-3300"></a>Version de l’agent 3.3.0.0
Les notes de publication suivantes concernent la version 3.3.0.0 de l’agent Azure File Sync mise en production le 24 septembre 2018. Ces notes s’ajoutent aux notes de publication répertoriées pour la version 3.1.0.0.

Liste des problèmes résolus dans cette version :
- L’état du serveur inscrit est « Apparaît hors connexion » après la mise à niveau de l’agent Azure File Sync vers la version 3.1 ou 3.2.
- Le service Agent de synchronisation de stockage (FileSyncSvc) tombe en panne en raison de fichiers dont les chemins d’accès sont longs.
- Échec de l’inscription du serveur avec l’erreur : Impossible de charger le fichier ou l’assembly Kailani.Afs.StorageSyncProtocol.V3.

## <a name="agent-version-3200"></a>Agent version 3.2.0.0
Les notes de publication suivantes concernent la version 3.2.0.0 de l’agent Azure File Sync (mise en production le 15 août 2018). Ces notes s’ajoutent aux notes de publication répertoriées pour la version 3.1.0.0.

Cette mise en production inclut le correctif suivant :
- La synchronisation échoue avec une erreur de mémoire insuffisante (0x8007000e) due à une fuite de mémoire

## <a name="agent-version-3100"></a>Version 3.1.0.0 de l’agent
Les notes de publication suivantes concernent la version 3.1.0.0 de l’agent Azure File Sync (mise en production le 19 juillet 2018).

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2016 et Windows Server 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.
- Le mode FIPS n’est pas pris en charge et doit être désactivé. 

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- N’utilisez pas les Outils de gestion de ressources pour serveur de fichiers (FSRM) ou d’autres filtres de fichiers. Les filtres de fichiers peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- L’exécution de sysprep sur un serveur sur lequel l’agent Azure File Sync est installé n’est pas prise en charge et peut produire des résultats inattendus. L’installation de l’agent et l’inscription du serveur doivent être effectués après avoir déployé l’image du serveur et terminé la mini-configuration de sysprep.
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.

### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
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
- Les fichiers à plusieurs niveaux sont inutilisables si les fichiers ne sont pas rappelés avant la suppression du point de terminaison.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
