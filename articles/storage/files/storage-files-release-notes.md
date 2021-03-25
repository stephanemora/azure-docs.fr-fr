---
title: Notes de publication de l’agent Azure File Sync | Microsoft Docs
description: Lisez les notes de publication de l’agent Azure File Sync, qui vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files.
services: storage
author: wmgries
ms.service: storage
ms.topic: conceptual
ms.date: 3/3/2021
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 5549fc3b63b76c6158ae7399e6d94a43d2d4f28f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102435186"
---
# <a name="release-notes-for-the-azure-file-sync-agent"></a>Notes de publication de l’agent Azure File Sync
Azure File Sync vous permet de centraliser les partages de fichiers de votre organisation dans Azure Files sans perdre la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Il transforme vos installations Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement (notamment SMB, NFS et FTPS). Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article fournit les notes de publication concernant les versions prises en charge de l’agent Azure File Sync.

## <a name="supported-versions"></a>Versions prises en charge
Les versions prises en charge de l'agent Azure File Sync sont les suivantes :

| Jalon | Numéro de version de l’agent | Date de publication | Statut |
|----|----------------------|--------------|------------------|
| Version V11.2 – [KB4539952](https://support.microsoft.com/topic/azure-file-sync-agent-v11-2-release-february-2021-c956eaf0-cd8e-4511-98c0-e5a1f2c84048)| 11.2.0.0 | 2 février 2021 | Prise en charge |
| Mise en production V 11.1 – [KB4539951](https://support.microsoft.com/help/4539951)| 11.1.0.0 | 4 novembre 2020 | Prise en charge |
| Version V10.1 : [KB4522411](https://support.microsoft.com/help/4522411)| 10.1.0.0 | 5 juin 2020 | Prise en charge - La version de l'agent expirera le 7 juin 2021 |
| Correctif cumulatif de mai 2020 : [KB4522412](https://support.microsoft.com/help/4522412)| 10.0.2.0 | 19 mai 2020 | Prise en charge - La version de l'agent expirera le 7 juin 2021 |
| Version V10 : [KB4522409](https://support.microsoft.com/help/4522409)| 10.0.0.0 | 9 avril 2020 | Prise en charge - La version de l'agent expirera le 7 juin 2021 |

## <a name="unsupported-versions"></a>Versions non prises en charge
Les versions suivantes de l'agent Azure File Sync ont expiré et ne sont plus prises en charge :

| Jalon | Numéro de version de l’agent | Date de publication | Statut |
|----|----------------------|--------------|------------------|
| Version V9 | 9.0.0.0 - 9.1.0.0 | N/A | Non prise en charge : la version de l'agent a expiré le 16 février 2021 |
| Version 8 | 8.0.0.0 | N/A | Non pris en charge : la version de l'agent a expiré le 12 janvier 2021 |
| Version V7 | 7.0.0.0 - 7.2.0.0 | N/A | Non pris en charge - Les versions de l'agent ont expiré le 1er septembre 2020 |
| Version V6 | 6.0.0.0 - 6.3.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 21 avril 2020 |
| Version V5 | 5.0.2.0 à 5.2.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 18 mars 2020 |
| Version V4 | 4.0.1.0 - 4.3.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 6 novembre 2019 |
| mise en production V3 | 3.1.0.0 - 3.4.0.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 19 août 2019 |
| Agents antérieurs à la disponibilité générale | 1.1.0.0 - 3.0.13.0 | N/A | Non pris en charge : les versions de l’agent ont expiré le 1er octobre 2018 |

### <a name="azure-file-sync-agent-update-policy"></a>Stratégie de mise à jour de l’agent Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="agent-version-11200"></a>Version de l’agent 11.2.0.0
Les notes de publication suivantes concernent la version 11.2.0.0 de l’agent Azure File Sync publiée le 2 février 2021. Ces notes s’ajoutent aux notes de publication de la version 11.1.0.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus 
- Si une session de synchronisation est annulée en raison d'un nombre élevé d'erreurs par élément, la synchronisation peut passer par un rapprochement lorsqu'une nouvelle session démarre si le service Azure File Sync détermine qu'une session de synchronisation personnalisée est nécessaire pour corriger les erreurs par élément.
- L’inscription d’un serveur à l’aide de la cmdlet Register-AzStorageSyncServer peut échouer avec l’erreur « Exception non prise en charge ».
- Nouvelle cmdlet PowerShell (Add-StorageSyncAllowedServerEndpointPath) pour configurer les chemins d’accès aux points de terminaison de serveur autorisés sur un serveur. Cette cmdlet est utile pour les scénarios dans lesquels le déploiement Azure File Sync est managé par un fournisseur de solutions cloud (CSP) ou un fournisseur de services, quand le client souhaite configurer des chemins d’accès aux points de terminaison de serveur autorisés sur un serveur. Lors de la création d’un point de terminaison de serveur, si le chemin d’accès spécifié n’est pas dans la liste d’autorisation, la création du point de terminaison de serveur échoue. Notez qu’il s’agit d’une fonctionnalité facultative et que tous les chemins d’accès pris en charge sont autorisés par défaut lors de la création d’un point de terminaison de serveur.  

    
    - Pour ajouter un chemin d’accès autorisé au point de terminaison de serveur, exécutez les commandes PowerShell suivantes sur le serveur :

    ```powershell
    Import-Module 'C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll' -verbose
    Add-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  

    - Pour obtenir la liste des chemins d’accès pris en charge, exécutez la commande PowerShell suivante :
    
    ```powershell
    Get-StorageSyncAllowedServerEndpointPath
    ```     
    - Pour supprimer un lien, exécutez la commande PowerShell suivante :
    
    ```powershell
    Remove-StorageSyncAllowedServerEndpointPath -Path <path>
    ```  
## <a name="agent-version-11100"></a>Version de l’agent 11.1.0.0
Les notes de publication suivantes concernent la version 11.1.0.0 de l’agent Azure File Sync (mise en production le 4 novembre 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus
- Nouveaux modes de hiérarchisation cloud pour contrôler le téléchargement initial et le rappel proactif
    - Mode de téléchargement initial : vous pouvez maintenant choisir la manière dont vous souhaitez que vos fichiers soient téléchargés initialement sur votre nouveau point de terminaison de serveur. Vous voulez que tous vos fichiers soient hiérarchisés ou que le plus grand nombre possible de fichiers soient téléchargés sur votre serveur en fonction du dernier horodateur de modification ? Vous pouvez le faire. Vous ne pouvez pas utiliser une hiérarchisation cloud ? Vous pouvez désormais choisir d’éviter les fichiers hiérarchisés sur votre système. Pour plus d’informations, consultez la section [Créer un point de terminaison de serveur](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#create-a-server-endpoint) dans la documentation Déployer Azure File Sync.
    - Mode de rappel proactif : chaque fois qu’un fichier est créé ou modifié, vous pouvez le rappeler de manière proactive sur les serveurs que vous spécifiez dans le même groupe de synchronisation. Cela a pour effet de rendre le fichier disponible en vue de son utilisation sur chaque serveur que vous avez spécifié. Vous avez des équipes disséminées dans le monde qui travaillent sur les mêmes données ? Activez le rappel proactif afin que, quand une équipe arrive le matin, tous les fichiers mis à jour par une autre équipe opérant dans un fuseau horaire différent soient téléchargés et prêts à l’emploi. Pour plus d’informations, consultez la section [Rappeler de manière proactive les fichiers nouveaux et modifiés à partir d’un partage de fichiers Azure](./storage-sync-files-deployment-guide.md?tabs=azure-portal%252cproactive-portal#proactively-recall-new-and-changed-files-from-an-azure-file-share) de la documentation Déployer Azure File Sync.

- Exclure des applications du suivi de l’heure du dernier accès de la hiérarchisation cloud Vous pouvez désormais exclure des applications du suivi de l’heure du dernier accès. Quand une application accède à un fichier, l’heure du dernier accès au fichier est mise à jour dans la base de données de hiérarchisation cloud. Les applications qui analysent le système de fichiers, telles que les antivirus, ont pour effet que tous les fichiers ont la même heure de dernier accès, ce qui a un impact sur leur hiérarchisation.

    Pour exclure des applications du suivi de l’heure du dernier accès, ajoutez le nom du processus au paramètre de registre HeatTrackingProcessNameExclusionList qui se trouve dans HKEY_LOCAL_MACHINE \SOFTWARE\Microsoft\Azure\StorageSync.

    Exemple : reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

    > [!Note]  
    > Les processus de déduplication des données et de Gestionnaire de ressources du serveur de fichiers (FSRM) sont exclus par défaut (codés en dur), et la liste d’exclusion de processus est actualisée toutes les 5 minutes.

- Améliorations diverses au niveau des performances et de la fiabilité
    - Amélioration des performances de détection des modifications pour détecter les fichiers modifiés dans le partage de fichiers Azure.
    - Amélioration des performances de chargement de la synchronisation.
    - Le chargement initial est maintenant effectué à partir d’un instantané VSS qui réduit les erreurs par élément et les échecs de session de synchronisation.
    - Améliorations de la fiabilité de synchronisation pour certains modèles d’E/S.
    - Correction d’un bogue pour empêcher la base de données de synchronisation de remonter dans le temps sur des clusters de basculement en cas de basculement.
    - Amélioration des performances de rappel lors de l’accès à un fichier hiérarchisé.

### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](./storage-sync-files-planning.md#evaluation-cmdlet) du guide de planification. 

### <a name="agent-installation-and-server-configuration"></a>Installation de l’agent et configuration du serveur
Pour plus d’informations sur l’installation et la configuration de l’agent Azure File Sync avec Windows Server, consultez [Planification d’un déploiement Azure File Sync](storage-sync-files-planning.md) et [Guide pratique pour déployer Azure File Sync](storage-sync-files-deployment-guide.md).

- Le package d’installation de l’agent doit être installé avec des autorisations (administrateur) élevées.
- L’agent n’est pas pris en charge par l’option de déploiement de Nano Server.
- L’agent est uniquement pris en charge sur Windows Server 2019, Windows Server 2016 et Windows Server 2012 R2.
- 2 Gio de mémoire nécessaires pour l’agent. Si le serveur s’exécute sur une machine virtuelle où la mémoire dynamique est activée, la machine virtuelle doit être configurée avec un minimum de 2 048 Mio de mémoire. Pour plus d'informations, consultez [Ressources système recommandées](./storage-sync-files-planning.md#recommended-system-resources).
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
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
    > [!Warning]  
    > Le commutateur Robocopy /B n'est pas pris en charge avec Azure File Sync. L'utilisation du commutateur Robocopy /B avec un point de terminaison de serveur Azure File Sync comme source peut entraîner une corruption de fichiers.

## <a name="agent-version-10100"></a>Version de l’agent 10.1.0.0
Les notes de publication suivantes concernent la version 10.1.0.0 de l’agent Azure File Sync publié le 5 juin 2020. Ces notes s’ajoutent aux notes de publication listées pour les versions 10.0.0.0 et 10.0.2.0.

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Prise en charge d’un point de terminaison privé Azure
    - Le trafic de synchronisation vers le service de synchronisation de stockage peut désormais être envoyé à un point de terminaison privé. Ceci permet le tunneling sur une connexion ExpressRoute ou VPN. Pour plus d’informations, consultez [Configuration des points de terminaison réseau Azure File Sync](./storage-sync-files-networking-endpoints.md).
- La métrique Fichiers synchronisés affiche désormais la progression pendant l’exécution d’une longue synchronisation, au lieu de l’indiquer seulement à la fin.
- Diverses améliorations de la fiabilité pour l’installation de l’agent, la hiérarchisation cloud, la synchronisation et la télémétrie

## <a name="agent-version-10020"></a>Version de l’agent 10.0.2.0
Les notes de publication suivantes concernent la version 10.0.2.0 de l’agent Azure File Sync publiée le 19 mai 2020. Ces notes s’ajoutent aux notes de publication de la version 10.0.0.0.

Problème résolu dans cette version :  
- L’agent de synchronisation de stockage (FileSyncSvc) se bloque fréquemment après l’installation de l’agent Azure File Sync v10.

> [!Note]  
>Cette version n’a pas été transmise aux serveurs qui sont configurés pour être mis à jour automatiquement lorsqu’une nouvelle version est disponible. Pour installer cette mise à jour, utilisez Microsoft Update ou le Catalogue Microsoft Update (consultez [KB4522412](https://support.microsoft.com/help/4522412) pour obtenir des instructions d’installation).

## <a name="agent-version-10000"></a>Version de l’agent 10.0.0.0
Les notes de publication suivantes concernent la version 10.0.0.0 de l’agent Azure File Sync (publiée le 9 avril 2020).

### <a name="improvements-and-issues-that-are-fixed"></a>Améliorations et problèmes résolus

- Amélioration de la progression de la synchronisation dans le portail
    - Avec la version V10 de l’agent, le portail Azure va bientôt commencer à afficher le type de session de synchronisation en cours d’exécution. Par exemple, téléchargement initial, téléchargement normal, rappel en arrière-plan (cas de récupération d’urgence rapide) et cas similaires. 

- Amélioration de l’expérience du portail de hiérarchisation cloud
    - Si vous avez des fichiers dont la hiérarchisation ou le rappel échouent, vous pouvez désormais afficher les erreurs de hiérarchisation dans les propriétés du point de terminaison de serveur.
    - Des informations supplémentaires sur la hiérarchisation cloud sont disponibles pour un point de terminaison du serveur :
        - Taille du cache local
        - Efficacité d’utilisation du cache
        - Détails de la stratégie de hiérarchisation cloud : taille du volume, espace libre actuel ou heure du dernier accès au fichier le plus ancien du cache local.
    - Ces modifications seront fournies dans le portail Azure peu après la mise en production de la version initiale de l’agent V10.

- Prise en charge du déplacement du service de synchronisation du stockage et/ou du compte de stockage vers un autre locataire Azure Active Directory
    - Azure File Sync prend désormais en charge le déplacement du service de synchronisation du stockage et/ou du compte de stockage vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD.
    
- Améliorations diverses au niveau des performances et de la fiabilité
    - La détection des modifications sur le partage de fichiers Azure peut échouer si les règles du réseau virtuel (VNET) et du pare-feu sont configurées sur le compte de stockage.
    - Réduction de la consommation de mémoire associée au rappel. 
    - Amélioration des performances lors de l’utilisation de l’applet de commande [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection).
    - Autres améliorations diverses de la fiabilité. 
    
### <a name="evaluation-tool"></a>Outil d’évaluation
Avant de déployer l’agent Azure File Sync, vous devez évaluer s’il est compatible avec votre système à l’aide de l’outil d’évaluation Azure File Sync. Cet outil est une applet de commande Azure PowerShell qui recherche les problèmes potentiels liés à votre système de fichiers et à votre jeu de données, comme des caractères non pris en charge ou une version de système d’exploitation non prise en charge. Pour des instructions d’installation et d’utilisation, voir la section [Outil d’évaluation](./storage-sync-files-planning.md#evaluation-cmdlet) du guide de planification. 

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
- La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.
- Le clustering de basculement est pris en charge uniquement avec les disques en cluster, pas avec les volumes partagés de cluster (CSV).
- Un point de terminaison de serveur ne peut pas être imbriqué. Il peut coexister sur le même volume en parallèle avec un autre point de terminaison.
- Ne stockez pas de système d’exploitation ni de fichier de pagination d’application au sein d’un emplacement de point de terminaison de serveur.
- Le nom de serveur dans le portail n’est pas mis à jour si le serveur est renommé.

### <a name="cloud-endpoint"></a>Point de terminaison cloud
- Azure File Sync prend en charge les modifications directes dans le partage de fichiers Azure. Toutefois, les modifications apportées au partage de fichiers Azure doivent d’abord être détectées par un travail de détection des modifications Azure File Sync. Un travail de détection des modifications est lancé pour un point de terminaison cloud une fois toutes les 24 heures. Pour synchroniser immédiatement les fichiers qui ont été modifiés dans le partage de fichiers Azure, le cmdlet PowerShell [Invoke-AzStorageSyncChangeDetection](/powershell/module/az.storagesync/invoke-azstoragesyncchangedetection) permet de lancer manuellement la détection des modifications apportées au partage de fichiers Azure. Par ailleurs, les modifications apportées à un partage de fichiers Azure via le protocole REST ne mettent pas à jour l’heure de dernière modification de SMB et ne sont pas visibles comme des modifications par la synchronisation.
- Le service de synchronisation de stockage et/ou le compte de stockage peuvent être déplacés vers un autre groupe de ressources, un autre abonnement ou un autre locataire Azure AD. Une fois le service de synchronisation de stockage ou le compte de stockage déplacé, vous devez donner à l’application Microsoft.StorageSync l’accès au compte de stockage (consultez [Vérifiez qu’Azure File Sync a accès au compte de stockage](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > Lors de la création du point de terminaison cloud, le service de synchronisation du stockage et le compte de stockage doivent se trouver dans le même locataire Azure AD. Une fois le point de terminaison cloud créé, le service de synchronisation du stockage et le compte de stockage peuvent être déplacés vers des locataires Azure AD différents.

### <a name="cloud-tiering"></a>Hiérarchisation cloud
- Si un fichier hiérarchisé est copié vers un autre emplacement à l’aide de Robocopy, le fichier résultant n’est pas hiérarchisé. L’attribut hors connexion peut être défini car Robocopy inclut cet attribut de façon erronée dans les opérations de copie.
- Lors de la copie de fichiers à l’aide de Robocopy, utilisez l’option /MIR pour conserver les horodatages des fichiers. Les plus anciens fichiers sont ainsi hiérarchisés plus tôt que les derniers fichiers utilisés.
