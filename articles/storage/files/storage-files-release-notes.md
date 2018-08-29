---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Notes de publication de l’agent Azure File Sync.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 08/21/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 3cd178333ee0d8d92db08fb08cbd02b05112f58b
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/22/2018
ms.locfileid: "42445020"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions suivantes de l’agent Azure File Sync sont prises en charge :

| Jalon | Numéro de version de l’agent | Date de lancement | Statut |
|----|----------------------|--------------|------------------|
| Correctif cumulatif d’août | 3.2.0.0 | 15 août 2018 | Prise en charge (version recommandée) |
| Disponibilité générale | 3.1.0.0 | 19 juillet 2018 | Pris en charge |
| Correctif cumulatif de juin | 3.0.13.0 | 29 juin 2018 | La version de l’agent expire le 4 septembre 2018 |
| Actualiser 2 | 3.0.12.0 | 22 mai 2018 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif d’avril | 2.3.0.0 | 8 mai 2018 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif de mars | 2.2.0.0 | 12 mars 2018 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif de février | 2.1.0.0 | 28 février 2018 | La version de l’agent expire le 4 septembre 2018 |
| Actualisation 1 | 2.0.11.0 | 8 février 2018 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif de janvier | 1.4.0.0 | 8 janvier 2018 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif de novembre | 1.3.0.0 | 30 novembre 2017 | La version de l’agent expire le 4 septembre 2018 |
| Correctif cumulatif d’octobre | 1.2.0.0 | 31 octobre 2017 | La version de l’agent expire le 4 septembre 2018 |
| Version préliminaire initiale | 1.1.0.0 | 26 septembre 2017 | La version de l’agent expire le 4 septembre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-3200"></a>Agent version 3.2.0.0
Les notes de publication suivantes concernent la version 3.2.0.0 de l’agent Azure File Sync (mise en production le 15 août 2018). Ces notes s’ajoutent aux notes de publication répertoriées pour la version 3.1.0.0.

Cette mise en production inclut le correctif suivant :
- La synchronisation échoue avec une erreur de mémoire insuffisante (0x8007000e) due à une fuite de mémoire

## <a name="agent-version-3100"></a>Version 3.1.0.0 de l’agent
Les notes de publication suivantes concernent la version 3.1.0.0 de l’agent Azure File Sync (mise en production le 19 juillet 2018).

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
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources ou un autre abonnement. Si le compte de stockage est déplacé, vous devez donner à Hybrid File Sync Service l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#troubleshoot-rbac)).

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.

## <a name="agent-version-30130"></a>Version 3.0.13.0 de l’agent
Les notes de publication suivantes concernent la version 3.0.13.0 de l’agent Azure File Sync mise en production le 29 juin 2018. Ces notes s’ajoutent aux notes de publication répertoriées pour la version 3.0.12.0.

Cette mise en production inclut le correctif suivant :
- La synchronisation échoue si un serveur est ajouté à un groupe de synchronisation existant s’il existe des points d’analyse dans l’emplacement du point de terminaison de serveur sur le serveur.

## <a name="agent-version-30120"></a>Version 3.0.12.0 de l’agent
Les notes de publication suivantes concernent la version 3.0.12.0 de l’agent Azure File Sync (mise en production le 22 mai 2018).

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
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
- Les fichiers à plusieurs niveaux sont inutilisables si les fichiers ne sont pas rappelés avant la suppression du point de terminaison.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.

## <a name="agent-version-2300"></a>Version 2.3.0.0 de l’agent
Les notes de publication suivantes concernent la version 2.3.0.0 de l’agent Azure File Sync mise en production le 8 mai 2018. Ces notes s’ajoutent aux notes de publication répertoriées pour la version 2.0.11.0.

Cette mise en production inclut les correctifs suivants :
- Les mises à jour de l’agent peuvent bloquer si le pilote de filtre de hiérarchisation cloud n’est pas déchargé.
- Les performances de synchronisation peuvent diminuer lors de la synchronisation d’un grand nombre de fichiers.

## <a name="agent-version-2200"></a>Version 2.2.0.0 de l’agent
Les notes de publication suivantes concernent la version 2.2.0.0 de l’agent Azure File Sync publiée le 12 mars 2018.  Ces notes s’ajoutent aux notes de publication répertoriées pour les versions 2.1.0.0 et 2.0.11.0

L’installation de la version 2.1.0.0 pour certains clients échoue en raison de FileSyncSvc qui ne s’arrête pas. Cette mise à jour résout ce problème.

## <a name="agent-version-2100"></a>Agent version 2.1.0.0
Les notes de publication suivantes concernent la version 2.1.0.0 de l’agent Azure File Sync mise en production le 28 février 2018. Ces notes s’ajoutent aux notes de publication répertoriées pour la version 2.0.11.0.

Cette mise en production inclut les changements suivants :
- Améliorations de la gestion du basculement du cluster.
- Améliorations afin de permettre la gestion fiable de fichiers hiérarchisés.
- Prise en charge de l’installation de l’agent sur des machines de contrôleur de domaine ajoutées à un environnement de domaine Windows Server 2008 R2.
- Corrigé lors de cette mise en production : génération de diagnostics excessifs sur des serveurs comprenant de nombreux fichiers.
- Améliorations de la gestion des erreurs lors d’échecs de session.
- Améliorations de la gestion des erreurs lors de problèmes de transfert de fichiers.
- Dans cette mise en production, l’intervalle par défaut pour exécuter la hiérarchisation cloud lorsqu’elle est activée sur un serveur de point de terminaison est passé à 1 heure. 
- Problème bloquant temporaire : déplacer des ressources Azure File Sync (service de synchronisation de stockage) vers un nouvel abonnement Azure.

## <a name="agent-version-20110"></a>Agent version 2.0.11.0
Les notes de publication suivantes concernent la version 2.0.11.0 de l’agent Azure File Sync (mise en production le 9 février 2018). 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2016 et Windows Server 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- Cette mise en production ajoute la prise en charge de DFS-R. Pour plus d’informations, consultez le [Guide de planification](storage-sync-files-planning.md#distributed-file-system-dfs).
- N’utilisez pas les Outils de gestion de ressources pour serveur de fichiers (FSRM) ou d’autres filtres de fichiers. Les filtres de fichiers peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- La duplication de serveurs inscrits (y compris le clonage de machines virtuelles) peut produire des résultats inattendus. En particulier, il se peut que la synchronisation ne converge jamais.
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
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison ne peut pas se trouver sur le volume système. Par exemple, C:\MonDossier n’est pas un chemin d’accès acceptable, sauf si C:\MonDossier est un point de montage.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Dans cette mise en production, la racine de synchronisation présente à la racine d’un volume est désormais prise en charge.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
- Dans cette mise en production, de nouveaux événements ont été ajoutés pour suivre le temps total d’exécution de la hiérarchisation cloud (EventID 9016), la progression du chargement de la synchronisation (EventID 9302) et les fichiers non synchronisés (EventID 9900).
- Amélioré dans cette version : 
- les performances rapides de synchronisation d’espace de noms de récupération d’urgence ont été considérablement améliorées.
- La suppression de nombres importants (plus de 10 000) de répertoires ne doit pas nécessairement être effectuée dans des lots avec v2 *.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Dans cette version, les nouveaux fichiers sont hiérarchisés dans un délai d’1 heure (contre 32 heures auparavant) sous réserve des paramètres de stratégie de hiérarchisation. Nous fournissons une cmdlet PowerShell pour hiérarchiser à la demande. Vous pouvez utiliser la cmdlet pour évaluer la hiérarchisation plus efficacement sans attendre le processus d’arrière-plan.
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
- Dans cette nouvelle version, les fichiers sont maintenant téléchargés en tant que fichiers hiérarchisés sur d’autres serveurs, dans la mesure où le fichier est nouveau ou a déjà été hiérarchisé.

## <a name="agent-version-1100"></a>Agent version 1.1.0.0
Les notes de publication suivantes concernent la version 1.1.0.0 de l’agent Azure File Sync (mise en production le 9 septembre 2017, préversion initiale). 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation (MSI) de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par les options de déploiement Windows Server Core ou Nano Server.
- L’agent est pris en charge uniquement sur Windows Server 2016 et 2012 R2.
- 2 Go de mémoire physique nécessaires pour l’agent.

### <a name="interoperability"></a>Interopérabilité
- Les antivirus, applications de sauvegarde et autres applications qui ont accès à des fichiers hiérarchisés peuvent provoquer des rappels indésirables, sauf s’ils respectent l’attribut hors connexion et ignorent la lecture du contenu de ces fichiers. Pour plus d’informations, consultez [Résoudre les problèmes liés à Azure File Sync](storage-sync-files-troubleshoot.md).
- N’utilisez pas FSRM ou autres filtres de fichiers. Les filtres de fichiers peuvent entraîner des échecs de synchronisation sans fin lorsque les fichiers sont bloqués en raison du filtre de fichier.
- La duplication de serveurs inscrits (y compris le clonage de machines virtuelles) peut produire des résultats inattendus. En particulier, il se peut que la synchronisation ne converge jamais.
- La déduplication des données et la hiérarchisation cloud ne sont pas prises en charge pour le même volume.
 
### <a name="sync-limitations"></a>Limitations de synchronisation
Les éléments suivants ne se synchronisent pas, mais le reste du système continue d’opérer normalement :
- Chemins de plus de 2 048 caractères.
- La partie DACL d’un descripteur de sécurité si elle est supérieure à 2 Ko. (Ce problème survient uniquement lorsque vous avez plus de 40 entrées de contrôle d’accès (ACE) sur un seul élément.)
- La partie SACL d’un descripteur de sécurité utilisée pour l’audit.
- Attributs étendus.
- Autres flux de données.
- Points d’analyse.
- Liens physiques.
- Si définie sur un serveur de fichiers, la compression n’est pas conservée lorsque les changements se synchronisent avec ce fichier depuis d’autres points de terminaison.
- Tous les fichiers chiffrés avec EFS (ou tout autre chiffrement de mode utilisateur) qui empêche le service de lire les données. 
    
    > [!Note]  
    > Azure File Sync chiffre toujours les données en transit. Les données sont toujours chiffrées au repos dans Azure.
 
### <a name="server-endpoints"></a>Points de terminaison de serveur
- Un point de terminaison de serveur ne peut être créé que sur un volume NTFS. ReFS, FAT, FAT32 et d’autres systèmes de fichiers ne sont actuellement pas pris en charge par Azure File Sync.
- Un point de terminaison ne peut pas se trouver sur le volume système. Par exemple, C:\MonDossier n’est pas un chemin d’accès acceptable, sauf si C:\MonDossier est un point de montage.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les CSV.
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- La suppression d’un grand nombre de répertoires (plus de 10 000) d’un serveur en une seule fois peut entraîner des échecs de synchronisation. Supprimez les répertoires par lots de moins de 10 000. Assurez-vous que la synchronisation des opérations de suppression réussisse avant de supprimer le lot suivant.
- Un point de terminaison de serveur à la racine d’un volume n’est actuellement pas pris en charge.
- Ne stockez pas un système d’exploitation ni ou un fichier de pagination d’application qui se trouve au sein d’un point de terminaison de serveur.
 
### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Afin de garantir que les fichiers peuvent être rappelés correctement, il se peut que le système ne hiérarchise pas automatiquement les fichiers nouveaux ou modifiés avant 32 heures. Ce processus inclut la première hiérarchisation après avoir configuré un nouveau point de terminaison de serveur. Nous fournissons une cmdlet PowerShell pour hiérarchiser à la demande. Vous pouvez utiliser la cmdlet pour évaluer la hiérarchisation plus efficacement sans attendre le processus d’arrière-plan.
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lorsque vous consultez les propriétés de fichier depuis un client SMB, l’attribut hors ligne peut sembler mal défini en raison de la mise en cache SMB des métadonnées du fichier.
