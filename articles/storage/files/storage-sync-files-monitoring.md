---
title: Surveiller Azure File Sync | Microsoft Docs
description: Comment surveiller Azure File Sync.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/28/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0232a0c6526d6dcdfec86dedec437c71e7e21080
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85515206"
---
# <a name="monitor-azure-file-sync"></a>Superviser Azure File Sync

Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article explique comment surveiller votre déploiement Azure File Sync à l’aide d’Azure Monitor, de Storage Sync Service et de Windows Server.

Les options de surveillance suivantes sont actuellement disponibles.

## <a name="azure-monitor"></a>Azure Monitor

Utilisez [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) pour afficher les métriques et configurer des alertes pour la synchronisation, la hiérarchisation cloud et la connectivité du serveur.  

### <a name="metrics"></a>Mesures

Les métriques pour Azure File Sync sont activées par défaut et sont envoyées à Azure Monitor toutes les 15 minutes.

Pour afficher les métriques Azure File Sync dans Azure Monitor, sélectionnez le type de ressource **Services de synchronisation de stockage**.

Les métriques suivantes pour Azure File Sync sont disponibles dans Azure Monitor :

| Nom de métrique | Description |
|-|-|
| Octets synchronisés | Taille des données transférées (chargement et téléchargement).<br><br>Unité : Octets<br>Type d’agrégation : SUM<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| Rappel de hiérarchisation cloud | Taille des données rappelées.<br><br>**Remarque** : cette métrique sera supprimée ultérieurement. Utilisez la métrique de taille de rappel de la hiérarchisation cloud pour surveiller la taille des données rappelées.<br><br>Unité : Octets<br>Type d’agrégation : SUM<br>Dimension applicable : Nom du serveur |
| Taille de rappel de la hiérarchisation cloud | Taille des données rappelées.<br><br>Unité : Octets<br>Type d’agrégation : SUM<br>Dimension applicable : Nom du serveur, nom du groupe de synchronisation |
| Taille de rappel de hiérarchisation cloud par application | Taille des données rappelées par application.<br><br>Unité : Octets<br>Type d’agrégation : SUM<br>Dimension applicable : Nom de l’application, nom du serveur, nom du groupe de synchronisation |
| Débit de rappel de la hiérarchisation cloud | Taille de débit de rappel des données.<br><br>Unité : Octets<br>Type d’agrégation : SUM<br>Dimension applicable : Nom du serveur, nom du groupe de synchronisation |
| Fichiers ne se synchronisant pas | Nombre de fichiers qui ne peuvent pas être synchronisés.<br><br>Unité : Count<br>Type d’agrégation : SUM<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| Fichiers synchronisés | Nombre de fichiers transférés (chargement et téléchargement).<br><br>Unité : Count<br>Type d’agrégation : SUM<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |
| État du serveur en ligne | Nombre de pulsations reçues du serveur.<br><br>Unité : Count<br>Type d’agrégation : Maximale<br>Dimension applicable : Nom du serveur |
| Résultat de session de synchronisation | Résultat de session de synchronisation (1 = session de synchronisation réussie ; 0 = session de synchronisation ayant échoué)<br><br>Unité : Count<br>Types d’agrégation : Maximale<br>Dimensions applicables : Nom du point de terminaison de serveur, sens de la synchronisation, nom du groupe de synchronisation |

### <a name="alerts"></a>Alertes

Pour configurer des alertes dans Azure Monitor, sélectionnez le service de synchronisation de stockage, puis sélectionnez la [métrique Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#metrics) à utiliser pour l’alerte.  

Le tableau suivant répertorie quelques exemples de scénarios destinés à la surveillance ainsi que la métrique appropriée à utiliser pour l’alerte :

| Scénario | Mesure à utiliser pour l’alerte |
|-|-|
| L’intégrité du point de terminaison de serveur dans le portail = erreur | Résultat de session de synchronisation |
| La synchronisation des fichiers avec le serveur ou le point de terminaison cloud échoue | Fichiers ne se synchronisant pas |
| Le serveur inscrit ne parvient pas à communiquer avec le service de synchronisation de stockage | État du serveur en ligne |
| La taille de rappel de la hiérarchisation cloud a dépassé 500 Gio en une journée  | Taille de rappel de la hiérarchisation cloud |

Pour en savoir plus sur la configuration des alertes dans Azure Monitor, consultez [Vue d’ensemble des alertes dans Microsoft Azure]( https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

## <a name="storage-sync-service"></a>Service de synchronisation de stockage

Pour afficher l'intégrité du serveur inscrit ainsi que l'intégrité et les métriques du point de terminaison du serveur, accédez au service de synchronisation de stockage sur le portail Azure. Vous pouvez consulter l'intégrité du serveur inscrit dans le panneau **Serveurs enregistrés** et l'intégrité du point de terminaison du serveur dans le panneau **Groupes de synchronisation**.

### <a name="registered-server-health"></a>Intégrité du serveur inscrit

- Si l'état du **serveur inscrit** est défini sur **En ligne**, cela signifie que le serveur communique avec le service.
- Si l'état du **serveur inscrit** est défini sur **Apparaît hors connexion**, vérifiez que le processus de surveillance de la synchronisation du stockage (AzureStorageSyncMonitor.exe) est en cours d'exécution sur le serveur. Si le serveur se trouve derrière un pare-feu ou un proxy, consultez [cet article](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) pour configurer le pare-feu et le proxy.

### <a name="server-endpoint-health"></a>Intégrité du point de terminaison de serveur

- L’intégrité du point de terminaison de serveur dans le portail est basée sur les événements de synchronisation qui sont enregistrés dans le journal d’événements de télémétrie sur le serveur (ID 9102 et 9302). Si une session de synchronisation échoue à cause d'une erreur transitoire, telle qu'une erreur annulée, la synchronisation peut apparaître comme saine sur le portail tant que la session de synchronisation progresse. L'ID d'événement 9302 est utilisé pour déterminer si les fichiers sont appliqués. Pour plus d'informations, consultez [Intégrité de la synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) et [Progression de la synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).
- Si le portail affiche une erreur de synchronisation parce que la synchronisation ne progresse pas, consultez la [documentation de dépannage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

### <a name="metric-charts"></a>Graphiques de métrique

- Les graphiques de métrique suivants sont visibles sur le portail du service de synchronisation de stockage :

  | Nom de métrique | Description | Nom du panneau |
  |-|-|-|
  | Octets synchronisés | Taille des données transférées (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | Rappel de hiérarchisation cloud | taille des données rappelées ; | Serveurs inscrits |
  | Fichiers ne se synchronisant pas | Nombre de fichiers qui ne peuvent pas être synchronisés | Point de terminaison de serveur |
  | Fichiers synchronisés | Nombre de fichiers transférés (chargement et téléchargement) | Groupe de synchronisation, point de terminaison du serveur |
  | État du serveur en ligne | Nombre de pulsations reçues du serveur | Serveurs inscrits |

- Pour en savoir plus, consultez [Azure Monitor](https://docs.microsoft.com/azure/storage/files/storage-sync-files-monitoring#azure-monitor).

  > [!Note]  
  > Un intervalle de temps de 24 heures s'applique aux graphiques du portail du service de synchronisation de stockage. Pour afficher d'autres intervalles de temps ou dimensions, utilisez Azure Monitor.

## <a name="windows-server"></a>Windows Server

Sur Windows Server, vous pouvez afficher la hiérarchisation cloud, le serveur inscrit et l'intégrité de la synchronisation.

### <a name="event-logs"></a>Journaux d’événements

Utilisez le journal des événements de télémétrie sur le serveur pour surveiller l’intégrité de hiérarchisation cloud, de synchronisation et de serveur inscrit. Le journal des événements de télémétrie se trouve dans l'observateur d'événements, sous *Applications and Services\Microsoft\FileSync\Agent*.

Intégrité de la synchronisation :

- L'ID d'événement 9102 est enregistré au terme de la session de synchronisation. Utilisez cet événement pour déterminer si les sessions de synchronisation aboutissent (**HResult = 0**) et s'il existe des erreurs de synchronisation par élément. Pour plus d'informations, consultez la documentation [Intégrité de la synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) et [Erreurs par élément](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

  > [!Note]  
  > Parfois, les sessions de synchronisation échouent totalement ou présentent un champ PerItemErrorCount différent de zéro. Pourtant, elles progressent toujours et la synchronisation de certains fichiers aboutit. Ceci est visible dans les champs de type Applied, comme AppliedFileCount, AppliedDirCount, AppliedTombstoneCount et AppliedSizeBytes. Ces champs indiquent dans quelle mesure la session a abouti. Si vous voyez plusieurs sessions de synchronisation consécutives échouer tout en ayant un nombre Applied croissant, laissez à la synchronisation le temps de réessayer avant d'ouvrir un ticket de support.

- L’ID d’événement 9302 est enregistré toutes les 5 à 10 minutes s’il existe une session de synchronisation active. Utilisez cet événement pour déterminer si la session de synchronisation actuelle progresse (**AppliedItemCount > 0**). Si la synchronisation ne progresse pas, la session de synchronisation peut finir par échouer, et un ID d'événement 9102 est enregistré avec l'erreur. Pour plus d'informations, consultez la [documentation consacrée à la progression de la synchronisation](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session).

Intégrité du serveur inscrit :

- L’ID d’événement 9301 est enregistré toutes les 30 secondes quand un serveur interroge le service pour des travaux. Si GetNextJob se termine avec **status = 0**, cela signifie que le serveur est en mesure de communiquer avec le service. Si GetNextJob se termine avec une erreur, consultez la [documentation de dépannage](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) pour obtenir des conseils.

Intégrité de la hiérarchisation cloud :

- Pour surveiller l'activité de hiérarchisation sur un serveur, utilisez les ID d'événement 9003, 9016 et 9029 dans le journal des événements de télémétrie. Celui-ci se trouve dans l'observateur d'événements, sous *Applications and Services\Microsoft\FileSync\Agent*.

  - L’ID d’événement 9003 fournit la distribution des erreurs de distribution pour un point de terminaison de serveur. Par exemple : le nombre total d'erreurs et le code d'erreur. Un événement est enregistré par code d'erreur.
  - L’ID d’événement 9016 fournit des résultats de dédoublement pour un volume. Par exemple : le pourcentage d'espace libre, le nombre de fichiers dupliqués au cours de la session et le nombre d'échecs de duplication de fichiers.
  - L’ID d’événement 9029 fournit des informations sur les sessions de duplication d’un point de terminaison de serveur. Par exemple : le nombre de fichiers tentés au cours de la session, le nombre de fichiers hiérarchisés au cours de la session et le nombre de fichiers déjà hiérarchisés.
  
- Pour surveiller l'activité de rappel sur un serveur, utilisez les ID d'événement 9005, 9006, 9009 et 9059 dans le journal des événements de télémétrie. Celui-ci se trouve dans l'observateur d'événements, sous *Applications and Services\Microsoft\FileSync\Agent*.

  - L’ID d’événement 9005 fournit une fiabilité de rappel pour un point de terminaison de serveur. Par exemple : le nombre de total de fichiers uniques consultés et le nombre total de fichiers uniques dont l'accès a échoué.
  - L’ID d’événement 9006 fournit la distribution des erreurs de rappel pour un point de terminaison de serveur. Par exemple : le nombre total de demandes ayant échoué et le code d'erreur. Un événement est enregistré par code d'erreur.
  - L’ID d’événement 9009 fournit des informations sur les sessions de rappel d’un point de terminaison de serveur. Par exemple : DurationSeconds, CountFilesRecallSucceeded et CountFilesRecallFailed.
  - L’ID d’événement 9059 fournit la distribution des rappels d’application pour un point de terminaison de serveur. Par exemple : ShareId, Application Name et TotalEgressNetworkBytes.

### <a name="performance-counters"></a>Compteurs de performance

Utilisez les compteurs de performances d’Azure File Sync sur le serveur pour surveiller l’activité de synchronisation.

Pour afficher les compteurs de performances Azure File Sync sur le serveur, ouvrez l'Analyseur de performances (Perfmon.exe). Les compteurs se trouvent sous les objets **AFS Bytes Transferred** et **AFS Sync Operations**.

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
