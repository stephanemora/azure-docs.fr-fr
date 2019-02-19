---
title: Surveiller Azure File Sync | Microsoft Docs
description: Comment surveiller Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/31/2019
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 5a0d02768b0fbd23e33d13c5e5c3fe84a41cdc52
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/14/2019
ms.locfileid: "56243652"
---
# <a name="monitor-azure-file-sync"></a>Superviser Azure File Sync

Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article décrit comment surveiller votre déploiement Azure File Sync à l’aide du portail Azure et de Windows Server.

Les options de surveillance suivantes sont actuellement disponibles :

## <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, vous pouvez afficher l’intégrité du serveur inscrit, l’intégrité du point de terminaison de serveur (intégrité de synchronisation) et des métriques.

### <a name="storage-sync-service"></a>Service de synchronisation de stockage

Pour afficher l'intégrité du serveur inscrit ainsi que l'intégrité et les métriques du point de terminaison du serveur, accédez au service de synchronisation de stockage sur le portail Azure. L’intégrité du serveur inscrit est visible dans le panneau Serveurs inscrits. L’intégrité du point de terminaison de serveur est visible dans le panneau Groupes de synchronisation.

Intégrité du serveur inscrit
- Si l’état du serveur inscrit est défini sur « En ligne », le serveur communique avec le service.
- Si l’état du serveur inscrit est défini sur « Apparaît hors connexion », vérifiez que le processus de surveillance de la synchronisation du stockage (AzureStorageSyncMonitor.exe) sur le serveur est en cours d’exécution. Si le serveur est derrière un pare-feu ou un proxy, configurez le pare-feu et le proxy conformément à cette [documentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy).

Intégrité du point de terminaison de serveur
- L’intégrité du point de terminaison de serveur dans le portail est basée sur les événements de synchronisation qui sont enregistrés dans le journal d’événements de télémétrie sur le serveur (ID 9102 et 9302). Si une session de synchronisation échoue en raison d’une erreur temporaire (par exemple, erreur annulée), la synchronisation peut toujours être affichée comme étant intègre dans le portail tant que la session de synchronisation actuelle est en cours (l’ID d’événement 9302 est utilisé pour déterminer si des fichiers sont appliqués). Pour plus d’informations, consultez la documentation suivante : [Comment surveiller l’intégrité de synchronisation ?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Comment surveiller la progression d’une session en cours de synchronisation ?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Si le portail affiche une erreur de synchronisation en raison d’une synchronisation qui ne progresse pas, consultez la [documentation de dépannage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

Mesures
- Les métriques suivantes sont visibles sur le portail du service de synchronisation de stockage :

  | Nom de métrique | Description | Panneau(x) du portail | 
  |-|-|-|
  | Octets synchronisés | Taille des données transférées (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | Rappel de hiérarchisation cloud | Taille des données rappelées | Serveurs inscrits |
  | Fichiers ne se synchronisant pas | Nombre de fichiers qui ne peuvent pas être synchronisés | Point de terminaison de serveur |
  | Fichiers synchronisés | Nombre de fichiers transférés (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | État du serveur en ligne | Nombre de pulsations reçues du serveur | Serveurs inscrits |

- Pour en savoir plus, consultez la section [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor). 

  > [!Note]  
  > Un intervalle de temps de 24 heures s'applique aux graphiques du portail du service de synchronisation de stockage. Pour afficher d'autres intervalles de temps ou dimensions, utilisez Azure Monitor.


### <a name="azure-monitor"></a>Azure Monitor

Utilisez [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pour surveiller la synchronisation, la hiérarchisation cloud et la connectivité du serveur. Les métriques pour Azure File Sync sont activées par défaut et sont envoyées à Azure Monitor toutes les 15 minutes.

Pour afficher les métriques Azure File Sync dans Azure Monitor, sélectionnez le type de ressource Services de synchronisation de stockage.

Les métriques suivantes pour Azure File Sync sont disponibles dans Azure Monitor :

| Nom de métrique | Description |
|-|-|
| Octets synchronisés | Taille des données transférées (chargement et téléchargement).<br><br>Unité : Octets<br>Type d’agrégation : Somme<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| Rappel de hiérarchisation cloud | Taille des données rappelées.<br><br>Unité : Octets<br>Type d’agrégation : Somme<br>Dimension applicable : Nom du serveur |
| Fichiers ne se synchronisant pas | Nombre de fichiers qui ne peuvent pas être synchronisés.<br><br>Unité : Nombre<br>Type d’agrégation : Somme<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| Fichiers synchronisés | Nombre de fichiers transférés (chargement et téléchargement).<br><br>Unité : Nombre<br>Type d’agrégation : Somme<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| État du serveur en ligne | Nombre de pulsations reçues du serveur.<br><br>Unité : Nombre<br>Type d’agrégation : Maximale<br>Dimension applicable : Nom du serveur |
| Résultat de session de synchronisation | Résultat de session de synchronisation (1 = session de synchronisation réussie ; 0 = session de synchronisation ayant échoué)<br><br>Unité : Nombre<br>Types d’agrégation : Maximale<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |

## <a name="windows-server"></a>Windows Server

Sur le serveur Windows Server, vous pouvez afficher la hiérarchisation cloud, le serveur inscrit et l’intégrité de synchronisation.

### <a name="event-logs"></a>Journaux d’événements

Utilisez le journal des événements de télémétrie sur le serveur pour surveiller l’intégrité de hiérarchisation cloud, de synchronisation et de serveur inscrit. Le journal d’événement de télémétrie se trouve sous Applications and Services\Microsoft\FileSync\Agent dans l’observateur d’événements.

Intégrité de synchronisation
- L’ID d’événement 9102 est enregistré une fois que la session de terminaison se termine. Cet événement doit être utilisé pour déterminer si les sessions de synchronisation se terminent correctement (HResult = 0) et s’il existe des erreurs de synchronisation par élément. Pour plus d’informations, consultez la documentation suivante : [Comment surveiller l’intégrité de synchronisation ?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Comment puis-je voir s’il existe des fichiers ou dossiers qui ne sont pas synchronisés ?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Parfois, les sessions de synchronisation échouent globalement ou ont un PerItemErrorCount différent de zéro, mais progressent toujours, avec des synchronisations de fichiers réussies. Ceci peut être observé dans les champs Applied (AppliedFileCount AppliedDirCount, AppliedTombstoneCount et AppliedSizeBytes), qui vous indiquent le niveau d’avancement de la session. Si vous voyez plusieurs sessions de synchronisation consécutives échouer tout en ayant un nombre Applied croissant, alors vous devriez laisser à la synchronisation le temps de réessayer avant d’ouvrir un ticket de support.

- L’ID d’événement 9302 est enregistré toutes les 5 à 10 minutes s’il existe une session de synchronisation active. Cet événement doit être utilisé pour déterminer si la session de synchronisation actuelle progresse (AppliedItemCount > 0). Si la synchronisation ne progresse pas, la session de synchronisation peut éventuellement échouer, et un ID d’événement 9102 est enregistré avec l’erreur. Pour plus d’informations, consultez la documentation suivante : [Comment surveiller la progression d’une session en cours de synchronisation ?](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Intégrité du serveur inscrit
- L’ID d’événement 9301 est enregistré toutes les 30 secondes quand un serveur interroge le service pour des travaux. Si GetNextJob se termine avec l’état = 0, le serveur est en mesure de communiquer avec le service. Si GetNextJob se termine avec une erreur, consultez la [documentation de dépannage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

Intégrité de hiérarchisation cloud
- Pour surveiller l’activité de hiérarchisation sur un serveur, utilisez les ID d’événement 9003, 9016 et 9029 dans le journal des événements de télémétrie (situé sous Applications and Services\Microsoft\FileSync\Agent in Event Viewer).

  - L’ID d’événement 9003 fournit la distribution des erreurs de distribution pour un point de terminaison de serveur. Par exemple, le nombre total d’erreurs, le code d’erreur, etc. Remarque, un événement est enregistré par code d’erreur.
  - L’ID d’événement 9016 fournit des résultats de dédoublement pour un volume. Par exemple, le pourcentage d’espace libre, le nombre de fichiers dédoublé dans la session, le nombre d’échec de dédoublement de fichiers, etc.
  - L’ID d’événement 9029 fournit des informations sur les sessions de duplication d’un point de terminaison de serveur. Par exemple, le nombre de fichiers tentés dans la session, le nombre de fichiers hiérarchisés dans la session, le nombre de fichiers déjà hiérarchisés, etc.
  
- Pour surveiller l’activité de rappel sur un serveur, utilisez les ID d’événement 9005, 9006, 9009 et 9059 dans le journal des événements de télémétrie (situé sous Applications and Services\Microsoft\FileSync\Agent in Event Viewer).

  - L’ID d’événement 9005 fournit une fiabilité de rappel pour un point de terminaison de serveur. Par exemple, le nombre de total des fichiers uniques consultés, le nombre total des fichiers uniques dont l’accès a échoué, etc.
  - L’ID d’événement 9006 fournit la distribution des erreurs de rappel pour un point de terminaison de serveur. Par exemple, le nombre total d’échecs de requête, le code d’erreur, etc. Remarque : un événement est enregistré par code d’erreur.
  - L’ID d’événement 9009 fournit des informations sur les sessions de rappel d’un point de terminaison de serveur. Par exemple, DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed, etc.
  - L’ID d’événement 9059 fournit la distribution des rappels d’application pour un point de terminaison de serveur. Par exemple, ShareId, Application Name et TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Compteurs de performances

Utilisez les compteurs de performances d’Azure File Sync sur le serveur pour surveiller l’activité de synchronisation.

Pour afficher les compteurs de performances de synchronisation d’Azure File Sync sur le serveur, lancez l’Analyseur de performances (Perfmon.exe) ; les compteurs se trouvent sous les objets AFS Bytes Transferred (Octets transférés AFS) et AFS Sync Operations (Opérations de synchronisation AFS).

Les compteurs de performances suivants pour Azure File Sync sont disponibles dans l’Analyseur de performances :

| Objet de performances\Nom de compteur | Description |
|-|-|
| AFS Bytes Transferred (Octets transférés AFS)\Downloaded Bytes/sec (Octets téléchargés/s) | Nombre d’octets téléchargés par seconde. |
| AFS Bytes Transferred (Octets transférés AFS)\Uploaded Bytes/sec (Octets chargés/s) | Nombre d’octets chargés par seconde. |
| AFS Bytes Transferred (Octets transférés AFS)\Total Bytes/sec (Total des octets/s) | Nombre total d’octets par seconde (chargement et téléchargement). |
| AFS Sync Operations (Opérations de synchronisation AFS)\Downloaded Sync Files/sec (Fichiers de synchronisation téléchargés/s) | Nombre de fichiers téléchargés par seconde. |
| AFS Sync Operations (Opérations de synchronisation AFS)\Uploaded Sync Files/sec (Fichiers de synchronisation chargés/s) | Nombre de fichiers chargés par seconde. |
| AFS Sync Operations (Opérations de synchronisation AFS)\Total Sync File Operations/sec (Total des opérations de fichier de synchronisation/s) | Nombre total de fichiers synchronisés (chargement et téléchargement). |

## <a name="next-steps"></a>Étapes suivantes
- [Planification d’un déploiement de synchronisation de fichiers Azure](storage-sync-files-planning.md)
- [Prenez en compte les paramètres de pare-feu et de proxy](storage-sync-files-firewall-and-proxy.md)
- [Déployer Azure File Sync](storage-sync-files-deployment-guide.md)
- [Résoudre les problèmes d’Azure File Sync](storage-sync-files-troubleshoot.md)
- [Forum Aux Questions Azure Files](storage-files-faq.md)
