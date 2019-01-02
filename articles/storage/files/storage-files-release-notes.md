---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Notes de publication de l’agent Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 12/10/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 5bea4c655e9a8970d8d0d946827cc3e46e7efa7a
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255157"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes de l’agent Azure File Sync sont prises en charge :

| Jalon | Numéro de version de l’agent | Date de lancement | Statut |
|----|----------------------|--------------|------------------|
| Correctif cumulatif de décembre – [KB4459990](https://support.microsoft.com/help/4459990)| 4.2.0.0 | 10 décembre 2018 | Prise en charge (version recommandée) |
| Correctif cumulatif de décembre | 4.1.0.0 | 4 décembre 2018 | Pris en charge |
| Version V4 | 4.0.1.0 | 13 novembre 2018 | Pris en charge |
| Correctif cumulatif de septembre | 3.3.0.0 | 24 septembre 2018 | Pris en charge |
| Correctif cumulatif d’août | 3.2.0.0 | 15 août 2018 | Pris en charge |
| Disponibilité générale | 3.1.0.0 | 19 juillet 2018 | Pris en charge |
| Correctif cumulatif de juin | 3.0.13.0 | 29 juin 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Actualiser 2 | 3.0.12.0 | 22 mai 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif d’avril | 2.3.0.0 | 8 mai 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif de mars | 2.2.0.0 | 12 mars 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif de février | 2.1.0.0 | 28 février 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Actualisation 1 | 2.0.11.0 | 8 février 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif de janvier | 1.4.0.0 | 8 janvier 2018 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif de novembre | 1.3.0.0 | 30 novembre 2017 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Correctif cumulatif d’octobre | 1.2.0.0 | 31 octobre 2017 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |
| Version préliminaire initiale | 1.1.0.0 | 26 septembre 2017 | Non pris en charge : la version de l’agent a expiré le 1er octobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

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
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande AzureRM PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.
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
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé. Pour mettre à jour le nom de serveur dans le portail, désinscrivez et réinscrivez le serveur.

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
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande AzureRM PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning#evaluation-tool) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2016 et Windows Server 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.
- Le service Storage Sync Agent (FileSyncSvc) ne prend pas en charge les points de terminaison serveur situés sur un volume dont le répertoire d’informations de volume système (SVI) est compressé. Cette configuration produit des résultats inattendus.

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
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé. Pour mettre à jour le nom de serveur dans le portail, désinscrivez et réinscrivez le serveur.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement à l’intérieur du locataire Azure AD existant. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

    > [!Note]  
    > Azure File Sync ne prend pas en charge le déplacement de l’abonnement vers un autre locataire Azure AD.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
