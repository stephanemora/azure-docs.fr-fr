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
ms.openlocfilehash: 4ae17249903f317e7a75a3e6bc7c03292021c96a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57534631"
---
# <a name="monitor-azure-file-sync"></a>Superviser Azure File Sync

Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article décrit comment surveiller votre déploiement Azure File Sync à l’aide du portail Azure et Windows Server.

Les options de surveillance suivantes sont actuellement disponibles.

## <a name="azure-portal"></a>Portail Azure

Dans le portail Azure, vous pouvez afficher l’intégrité du serveur inscrit, l’intégrité du point de terminaison de serveur (intégrité de synchronisation) et des métriques.

### <a name="storage-sync-service"></a>Service de synchronisation de stockage

Pour afficher l’intégrité du serveur inscrit, l’intégrité du point de terminaison de serveur et de mesures, accédez au Service de synchronisation de stockage dans le portail Azure. Vous pouvez afficher l’intégrité du serveur inscrit dans le **serveurs inscrits** panneau et le serveur de contrôle d’intégrité de point de terminaison dans le **groupes de synchronisation** panneau.

Contrôle d’intégrité du serveur inscrit :

- Si le **Registered server** état est **Online**, le serveur communique avec le service.
- Si le **Registered server** état est **apparaît hors connexion**, vérifiez que le processus du moniteur de synchronisation de stockage (AzureStorageSyncMonitor.exe) sur le serveur est en cours d’exécution. Si le serveur est derrière un pare-feu ou un proxy, consultez [cet article](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) pour configurer le pare-feu et le proxy.

Intégrité du point de terminaison de serveur :

- L’intégrité du point de terminaison de serveur dans le portail est basée sur les événements de synchronisation qui sont enregistrés dans le journal d’événements de télémétrie sur le serveur (ID 9102 et 9302). Si une session de synchronisation échoue en raison d’une erreur temporaire, tel qu’erreur annulée, la synchronisation peut apparaître saine dans le portail tant que la session de synchronisation actuel progresse dans le processus. ID d’événement 9302 est utilisé pour déterminer si les fichiers sont appliquées. Pour plus d’informations, consultez [d’intégrité de synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) et [synchroniser progression](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Si le portail affiche une erreur de synchronisation, car la synchronisation ne progresse pas, consultez le [résolution des problèmes de documentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

Métriques :

- Les métriques suivantes sont visibles sur le portail du service de synchronisation de stockage :

  | Nom de métrique | Description | Nom du panneau |
  |-|-|-|
  | Octets synchronisés | Taille des données transférées (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | Rappel de hiérarchisation cloud | Taille des données rappelées | Serveurs inscrits |
  | Fichiers ne se synchronisant pas | Nombre de fichiers qui ne peuvent pas être synchronisés | Point de terminaison de serveur |
  | Fichiers synchronisés | Nombre de fichiers transférés (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | État du serveur en ligne | Nombre de pulsations reçues du serveur | Serveurs inscrits |

- Pour plus d’informations, consultez [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Un intervalle de temps de 24 heures s'applique aux graphiques du portail du service de synchronisation de stockage. Pour afficher d'autres intervalles de temps ou dimensions, utilisez Azure Monitor.

### <a name="azure-monitor"></a>Azure Monitor

Utilisez [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pour surveiller la synchronisation, la hiérarchisation cloud et la connectivité du serveur. Les métriques pour Azure File Sync sont activées par défaut et sont envoyées à Azure Monitor toutes les 15 minutes.

Pour afficher les métriques d’Azure File Sync dans Azure Monitor, sélectionnez le **les Services de synchronisation de stockage** type de ressource.

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

Sur Windows Server, vous pouvez afficher la synchronisation cloud, serveur inscrit et l’intégrité de la synchronisation.

### <a name="event-logs"></a>Journaux d’événements

Utilisez le journal des événements de télémétrie sur le serveur pour surveiller l’intégrité de hiérarchisation cloud, de synchronisation et de serveur inscrit. Le journal des événements télémétrie se trouve dans l’Observateur d’événements sous *Applications et Services\Microsoft\FileSync\Agent*.

Contrôle d’intégrité de la synchronisation :

- ID d’événement 9102 enregistré après qu’une session de synchronisation se termine. Utilisez cet événement pour déterminer si les sessions de synchronisation sont réussites (**HResult = 0**) et s’il existe par élément des erreurs de synchronisation. Pour plus d’informations, consultez le [d’intégrité de synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) et [erreurs d’élément](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) documentation.

  > [!Note]  
  > Parfois, les sessions de synchronisation échouent ensemble ou ont un PerItemErrorCount différent de zéro. Toutefois, ils toujours progressent, et certains fichiers synchronisés avec succès. Vous pouvez le voir dans les champs appliqués comme AppliedFileCount, AppliedDirCount, AppliedTombstoneCount et AppliedSizeBytes. Ces champs indiquent la quantité de la session a réussi. Si vous voyez plusieurs sessions de synchronisation échoue dans une ligne, et ils ont un nombre croissant d’appliqué, à synchronisation de temps pour essayer à nouveau avant d’ouvrir un ticket de support.

- L’ID d’événement 9302 est enregistré toutes les 5 à 10 minutes s’il existe une session de synchronisation active. Utilisez cet événement pour déterminer si la session de synchronisation actuel progression (**AppliedItemCount > 0**). Si la synchronisation ne progresse pas, la session de synchronisation doit finit par échouer, et une 9102 d’ID d’événement est enregistré avec l’erreur. Pour plus d’informations, consultez le [synchroniser documentation cours](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Contrôle d’intégrité du serveur inscrit :

- L’ID d’événement 9301 est enregistré toutes les 30 secondes quand un serveur interroge le service pour des travaux. Si la fin de GetNextJob avec **état = 0**, le serveur est en mesure de communiquer avec le service. Si GetNextJob se termine avec une erreur, vérifiez le [résolution des problèmes de documentation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

Cloud hiérarchisation d’intégrité :

- Pour surveiller l’activité de hiérarchisation sur un serveur, utilisez Event ID 9003, 9016 et 9029 dans le journal d’événements de télémétrie, qui se trouve dans l’Observateur d’événements sous *Applications et Services\Microsoft\FileSync\Agent*.

  - L’ID d’événement 9003 fournit la distribution des erreurs de distribution pour un point de terminaison de serveur. Par exemple :  Nombre total d’erreurs et le code d’erreur. Un événement est consigné par code d’erreur.
  - L’ID d’événement 9016 fournit des résultats de dédoublement pour un volume. Par exemple :  Pourcentage d’espace libre est, nombre de fichiers dédoublé dans la session, et nombre de fichiers a échoué à fantômes.
  - L’ID d’événement 9029 fournit des informations sur les sessions de duplication d’un point de terminaison de serveur. Par exemple :  Nombre de fichiers a tenté de dans la session, le nombre de fichiers à plusieurs niveaux dans la session, et nombre de fichiers sont déjà à plusieurs niveaux.
  
- Pour surveiller l’activité de rappel sur un serveur, utilisez Event ID 9005, 9006, 9009 et 9059 dans le journal d’événements de télémétrie, qui se trouve dans l’Observateur d’événements sous *Applications et Services\Microsoft\FileSync\Agent*.

  - L’ID d’événement 9005 fournit une fiabilité de rappel pour un point de terminaison de serveur. Par exemple :  Nombre total de fichiers uniques accédés et nombre Total de fichiers uniques avec un accès ayant échoué.
  - L’ID d’événement 9006 fournit la distribution des erreurs de rappel pour un point de terminaison de serveur. Par exemple :  Nombre total de demandes ayant échoué et code d’erreur. Un événement est consigné par code d’erreur.
  - L’ID d’événement 9009 fournit des informations sur les sessions de rappel d’un point de terminaison de serveur. Par exemple :  DurationSeconds, CountFilesRecallSucceeded et CountFilesRecallFailed.
  - L’ID d’événement 9059 fournit la distribution des rappels d’application pour un point de terminaison de serveur. Par exemple :  ShareId, nom de l’Application et TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Compteurs de performances

Utilisez les compteurs de performances d’Azure File Sync sur le serveur pour surveiller l’activité de synchronisation.

Pour afficher les compteurs de performances d’Azure File Sync sur le serveur, ouvrez l’Analyseur de performances (Perfmon.exe). Vous pouvez trouver les compteurs sous le **AFS octets transférés** et **les opérations de synchronisation AFS** objets.

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
