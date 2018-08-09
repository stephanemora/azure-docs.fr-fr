---
title: Résoudre les problèmes d’Azure File Sync | Microsoft Docs
description: Découvrez comment résoudre les problèmes courants avec Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: jeffpatt
ms.component: files
ms.openlocfilehash: 1475e1955a282581c66235c13d4dbe7153735a35
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526740"
---
# <a name="troubleshoot-azure-file-sync"></a>Résoudre les problèmes de synchronisation de fichiers Azure
Utilisez Azure File Sync pour centraliser les partages de fichiers de votre organisation dans Azure Files tout en conservant la flexibilité, le niveau de performance et la compatibilité d’un serveur de fichiers local. Azure File Sync transforme Windows Server en un cache rapide de votre partage de fichiers Azure. Vous pouvez utiliser tout protocole disponible dans Windows Server pour accéder à vos données localement, notamment SMB, NFS et FTPS. Vous pouvez avoir autant de caches que nécessaire dans le monde entier.

Cet article est destiné à vous aider à dépanner et à résoudre les problèmes que vous pouvez rencontrer avec le déploiement d’Azure File Sync. Nous vous y expliquons également comment collecter des journaux du système qui sont utiles pour analyser les problèmes rencontrés de manière plus approfondie. Si vous ne trouvez pas de réponse à votre question ici, vous pouvez nous joindre par le biais des méthodes suivantes (par ordre de priorité) :

1. [Forum du Stockage Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)
3. Support Microsoft Pour créer une demande de support, dans le portail Azure, sous l’onglet **Aide**, sélectionnez le bouton **Aide et support**, puis **Nouvelle demande de support**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Je rencontre un problème avec Azure File Sync sur mon serveur (synchronisation, hiérarchisation cloud, etc.). Dois-je supprimer et recréer mon point de terminaison de serveur ?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Installation de l’agent et inscription du serveur
<a id="agent-installation-failures"></a>**Résoudre les problèmes d’installation de l’agent**  
Si l’installation de l’agent Azure File Sync échoue, à partir d’une invite de commandes avec élévation de privilèges, exécutez la commande suivante pour activer la journalisation durant l’installation de l’agent :

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Examinez le fichier installer.log pour déterminer la cause de l’échec de l’installation.

<a id="agent-installation-on-DC"></a>**L’installation de l’agent échoue sur le contrôleur de domaine Active Directory**  
Si vous essayez d’installer l’agent de synchronisation sur un contrôleur de domaine Active Directory où le propriétaire du rôle de contrôleur de domaine principal est sur Windows Server 2008 R2 ou un système d’exploitation antérieur, l’agent de synchronisation peut échouer.

Pour résoudre cela, transférez le rôle de contrôleur de domaine principal à un autre contrôleur de domaine avec Windows Server 2012 R2 ou une version plus récente, puis synchronisez.

<a id="server-registration-missing"></a>**Le serveur n’est pas listé sous Serveurs inscrits sur le Portail Azure**  
Si un serveur n’est pas listé sous **Serveurs inscrits** pour un service de synchronisation de stockage :
1. Connectez-vous au serveur que vous souhaitez inscrire.
2. Ouvrez l’Explorateur de fichiers, puis accédez au répertoire d’installation de l’agent de synchronisation de stockage (l’emplacement par défaut est C:\Program Files\Azure\StorageSyncAgent). 
3. Exécutez ServerRegistration.exe, puis effectuez l’Assistant pour inscrire le serveur auprès d’un service de synchronisation de stockage.

<a id="server-already-registered"></a>**L’inscription du serveur affiche le message suivant pendant l’installation de l’agent Azure File Sync : « Ce serveur est déjà inscrit ».** 

![Capture d’écran de la boîte de dialogue d’inscription du serveur avec le message d’erreur « Ce serveur est déjà inscrit »](media/storage-sync-files-troubleshoot/server-registration-1.png)

Ce message s’affiche si le serveur a déjà été inscrit auprès d’un service de synchronisation de stockage. Pour désinscrire le serveur du service de synchronisation de stockage actuel et l’inscrire ensuite auprès d’un nouveau service de synchronisation de stockage, suivez les étapes décrites dans [Désinscrire un serveur d’Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Si le serveur n’est pas répertorié sous **Serveurs inscrits** dans le service de synchronisation de stockage, sur le serveur à désinscrire, exécutez les commandes PowerShell suivantes :

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Si le serveur fait partie d’un cluster, vous pouvez utiliser le paramètre facultatif *Reset-StorageSyncServer -CleanClusterRegistration* pour annuler aussi l’inscription du cluster.

<a id="web-site-not-trusted"></a>**Quand j’inscris un serveur, je vois de nombreuses réponses indiquant que le site web n’est pas approuvé. Pourquoi ?**  
Ce problème se produit quand la stratégie **Sécurité renforcée d’Internet Explorer** est activée pendant l’inscription du serveur. Pour plus d’informations sur la façon de désactiver correctement la stratégie de **Sécurité renforcée d’Internet Explorer**, consultez [Préparer Windows Server pour une utilisation avec Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) et [Déployer Azure File Sync](storage-sync-files-deployment-guide.md).

## <a name="sync-group-management"></a>Gestion du groupe de synchronisation
<a id="cloud-endpoint-using-share"></a>**La création du point de terminaison cloud échoue, avec cette erreur : « Le partage de fichiers Azure spécifié est déjà en cours d’utilisation par un autre point de terminaison cloud »**  
Ce problème se produit si le partage de fichiers Azure est déjà en cours d’utilisation par un autre point de terminaison cloud. 

Si vous voyez ce message et que le partage de fichiers Azure n’est pas en cours d’utilisation par un point de terminaison cloud, effectuez les étapes suivantes pour supprimer les métadonnées Azure File Sync sur le partage de fichiers Azure :

> [!Warning]  
> La suppression des métadonnées sur un partage de fichiers Azure en cours d’utilisation par un point de terminaison cloud entraîne l’échec des opérations Azure File Sync. 

1. Dans le portail Azure, accédez au partage de fichiers Azure.  
2. Cliquez avec le bouton droit sur le partage de fichiers Azure, puis sélectionnez **Modifier les métadonnées**.
3. Cliquez avec le bouton droit sur **SyncService**, puis sélectionnez **Supprimer**.

<a id="cloud-endpoint-authfailed"></a>**La création du point de terminaison cloud échoue, avec cette erreur : « AuthorizationFailed »**  
Ce problème se produit si votre compte d’utilisateur ne dispose pas des droits suffisants pour créer un point de terminaison cloud. 

Pour créer un point de terminaison cloud, votre compte d’utilisateur doit disposer des autorisations Microsoft suivantes :  
* Lecture : Obtenir la définition de rôle
* Écriture : Créer ou mettre à jour la définition de rôle personnalisée
* Lecture : Obtenir l’attribution de rôle
* Écriture : Créer l’attribution de rôle

Les rôles intégrés suivants ont les autorisations Microsoft nécessaires :  
* Propriétaire
* Administrateur de l'accès utilisateur

Pour déterminer si votre rôle de compte d’utilisateur a les autorisations nécessaires :  
1. Dans le portail Azure, sélectionnez **Groupes de ressources**.
2. Sélectionnez le groupe de ressources dans lequel se trouve le compte de stockage, puis sélectionnez **Contrôle d’accès (IAM)**.
3. Sélectionnez le **rôle** (par exemple, Propriétaire ou Contributeur) pour votre compte d’utilisateur.
4. Dans la liste **Fournisseur de ressources**, sélectionnez **Autorisation Microsoft**. 
    * **Attribution de rôle** doit avoir les autorisations **Lecture** et **Écriture**.
    * **Définition de rôle** doit avoir les autorisations **Lecture** et **Écriture**.

<a id="server-endpoint-createjobfailed"></a>**La création du point de terminaison de serveur a échoué avec l’erreur : « MgmtServerJobFailed » (Code d’erreur :-2134375898)**  
Ce problème se produit si le chemin du point de terminaison de serveur se trouve sur le volume système et que la hiérarchisation cloud est activée. La hiérarchisation cloud n’est pas prise en charge sur le volume système. Pour créer un point de terminaison de serveur sur le volume système, désactivez la hiérarchisation cloud quand vous créez le point de terminaison de serveur.

<a id="server-endpoint-deletejobexpired"></a>**La suppression du point de terminaison de serveur échoue avec cette erreur : « MgmtServerJobExpired »**                
Ce problème se produit si le serveur est hors connexion ou n’a pas de connectivité réseau. Si le serveur n’est plus disponible, désinscrivez le serveur dans le portail pour supprimer les points de terminaison de serveur. Pour supprimer les points de terminaison de serveur, suivez les étapes décrites dans [Désinscrire un serveur dans Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

<a id="server-endpoint-provisioningfailed"></a>**Impossible d’ouvrir la page de propriétés du point de terminaison serveur ou de mettre à jour de la stratégie de hiérarchisation du cloud**  
Ce problème peut se produire si une opération de gestion sur le point de terminaison serveur échoue. Si la page de propriétés de point de terminaison serveur ne s’ouvre pas dans le portail Azure, la mise à jour du point de terminaison serveur à l’aide des commandes PowerShell à partir du serveur peut résoudre ce problème. 

```PowerShell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzureRmStorageSyncServerEndpoint `
    -SubscriptionId mysubguid `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup

# Update the free space percent policy for the server endpoint
Set-AzureRmStorageSyncServerEndpoint `
    -Id serverendpointid `
    -CloudTiering true `
    -VolumeFreeSpacePercent 60
```

## <a name="sync"></a>Synchronisation
<a id="afs-change-detection"></a>**Après avoir créé un fichier directement dans mon partage de fichiers Azure sur SMB ou par le biais du portail, combien de temps faut-il pour synchroniser le fichier sur les serveurs du groupe de synchronisation ?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**L’intégrité du point de terminaison de serveur est en état d’attente pendant plusieurs heures**  
Ce problème peut se produire si vous créez un point de terminaison cloud et que vous utilisez un partage de fichiers Azure contenant des données. Le travail d’énumération de modifications qui analyse les modifications dans le partage de fichiers Azure doit être terminé avant que les fichiers puissent être synchronisés entre le cloud et les points de terminaison serveur. La durée d’exécution du travail dépend de la taille de l’espace de noms dans le partage de fichiers Azure. L’intégrité du point de terminaison de serveur doit se mettre à jour une fois que le travail d’énumération des modifications est terminé.

### <a id="broken-sync"></a>Comment surveiller l’intégrité de synchronisation ?
# <a name="portaltabportal1"></a>[Portail](#tab/portal1)
Au sein de chaque groupe de synchronisation, vous pouvez zoomer sur ses points de terminaison serveur individuel pour afficher l’état des dernières sessions de synchronisation terminées. Une colonne de contrôle d’intégrité verte et des fichiers non synchronisés à la valeur 0 indiquent que la synchronisation fonctionne comme prévu. Si ce n’est pas le cas, voir ci-dessous pour obtenir la liste des erreurs de synchronisation courantes et savoir comment gérer les fichiers qui ne synchronisent pas. 

![Capture d’écran du portail Azure](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="servertabserver"></a>[Serveur](#tab/server)
Accédez aux enregistrements de données de télémétrie du serveur, qui se trouve dans l’observateur d'événements à `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`. Événement 9102 correspond à une session de synchronisation terminée; Pour obtenir le dernier état de synchronisation, recherchez l’événement le plus récent avec ID 9102. SyncDirection vous indique si cette session a été chargée ou téléchargée. Si la valeur HResult est 0, la session de synchronisation a réussi. Une valeur HResult différente de zéro indique une erreur pendant la synchronisation; voir ci-dessous pour obtenir la liste d’erreurs courantes. Si le PerItemErrorCount est supérieur à 0, cela signifie que certains fichiers ou dossiers non pas synchronisés correctement. Il est possible d’avoir une valeur HResult égale à 0, mais un PerItemErrorCount supérieur à 0.

Vous trouverez ci-dessous un exemple d’un chargement réussi. Par souci de concision, uniquement certaines des valeurs contenues dans chaque événement 9102 sont répertoriées ci-dessous. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

À l’inverse, un téléchargement échoué peut ressembler à ceci :

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Parfois, les sessions de synchronisation échouent globalement ou ont un PerItemErrorCount différent de zéro, mais progressent toujours, avec des synchronisations de fichiers réussies. Ceci peut être observé dans les champs Applied* (AppliedFileCount AppliedDirCount, AppliedTombstoneCount et AppliedSizeBytes), qui vous indiquent le niveau d’avancement de la session. Si vous voyez plusieurs sessions de synchronisation consécutives échouer tout en ayant un nombre d’Applied* croissant, alors vous devriez laisser à la synchronisation le temps de réessayer avant d’ouvrir un ticket de support.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Comment surveiller la progression d’une session en cours de synchronisation ?
# <a name="portaltabportal1"></a>[Portail](#tab/portal1)
Au sein de votre groupe de synchronisation, accédez au point de terminaison de serveur en question et examinez la section Activité de synchronisation pour voir le nombre de fichiers chargés ou téléchargés dans la session de synchronisation en cours. Notez que cet état sera retardé d’environ 5 minutes, et si votre session de synchronisation est suffisamment petite pour être effectuée pendant cette période, il peut ne pas être signalé dans le portail. 

# <a name="servertabserver"></a>[Serveur](#tab/server)
Recherchez les événements 9302 les plus récents dans les enregistrements de données de télémétrie sur le serveur (dans l’observateur d’événements, accédez aux enregistrements des Applications and Services\Microsoft\FileSync\Agent\Telemetry). Cet événement indique l’état de la session de synchronisation en cours. TotalItemCount indique combien de fichiers doivent être synchronisés, AppliedItemCount le nombre de fichiers qui ont été synchronisés jusqu’ici et PerItemErrorCount le nombre de fichiers qui ne parviennent pas à se synchroniser (voir ci-dessous pour savoir comment gérer cette opération).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Comment savoir si mes serveurs sont synchronisés entre eux ?
# <a name="portaltabportal1"></a>[Portail](#tab/portal1)
Pour chaque serveur dans un groupe de synchronisation donné, vérifiez que :
- Les horodatages de la dernière tentative de synchronisation pour le chargement téléchargement sont récents.
- L’état est affiché en vert pour le chargement et le téléchargement.
- Le champ de l’activité de synchronisation présente très peu ou pas de fichiers restant à synchroniser.
- Le champ des fichiers non synchronisés est à 0 pour le chargement et le téléchargement.

# <a name="servertabserver"></a>[Serveur](#tab/server)
Examinez les sessions de synchronisation terminées, qui sont marquées par des événements 9102 dans les données de télémétrie de l’événement pour chaque serveur (dans l’observateur d’événements, accédez à `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry`). 

1. Sur un serveur donné, vous souhaitez vous assurer que les dernières sessions de chargement et téléchargement se sont terminées correctement. Pour ce faire, vérifiez que le HResult et PerItemErrorCount sont à 0 pour le chargement et le téléchargement (le champ SyncDirection indique si une session donnée est une session de chargement ou téléchargement). Notez que si vous ne voyez pas une session de synchronisation effectuée récemment, il est probable qu’une session de synchronisation soit en cours, ce qui est normal si vous venez d’ajouter ou de modifier une grande quantité de données.
2. Lorsqu’un serveur est complètement à jour avec le cloud et ne comporte aucune modification pour la synchronisation dans les deux sens, vous verrez des sessions de synchronisation vide. Ceux-ci sont indiqués par des événements de chargement et de téléchargement dans lesquels tous les champs Sync* (SyncFileCount, SyncDirCount, SyncTombstoneCount, SyncTombstoneCount et SyncSizeBytes) sont à zéro, ce qui signifie qu’il n’y avait rien à synchroniser. Notez que ces sessions de synchronisation vide ne peuvent pas survenir sur les serveurs à forte activité car il y a toujours quelque chose de nouveau à synchroniser. S’il n’existe aucune activité de synchronisation, elles doivent se produire toutes les 30 minutes. 
3. Si tous les serveurs sont à jour avec le cloud, ce qui signifie que leur chargement récent et les sessions de téléchargement sont des sessions de synchronisation vide, vous pouvez affirmer avec une certitude raisonnable que l’ensemble du système est synchronisé. 
    
Notez que si vous avez effectué des modifications directement dans votre partage de fichiers Azure, Azure File Sync ne détectera pas cette modification jusqu’à ce que les énumérations de modifications s’exécutent, ce qui se produit une fois toutes les 24 heures. Il est possible qu’un serveur indique qu’il est à jour avec le cloud alors qu’il lui manque en fait des modifications récentes apportées directement dans le partage de fichiers Azure. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Comment puis-je voir s’il existe des fichiers ou dossiers qui ne sont pas synchronisés ?
Si votre PerItemErrorCount sur le serveur ou le nombre de fichiers non synchronisés dans le portail est supérieur à 0 pour une session de synchronisation donnée, cela signifie que certains éléments ne sont pas synchronisés. Les fichiers et les dossiers peuvent avoir des caractéristiques qui empêchent leur synchronisation. Ces caractéristiques peuvent être persistantes et nécessitent une action explicite pour reprendre la synchronisation, par exemple la suppression des caractères non pris en charge à partir du nom du fichier ou du dossier. Elles peuvent aussi être temporaires, ce qui signifie que le fichier ou le dossier reprendra automatiquement la synchronisation ; par exemple, des fichiers avec des handles ouverts qui reprennent automatiquement la synchronisation quand le fichier est fermé. Lorsque le moteur Azure File Sync détecte ce type de problème, un journal des erreurs est généré, il peut être analysé pour répertorier les éléments qui ne sont pas en train de se synchroniser correctement.

Pour afficher ces erreurs, exécutez le script PowerShell **FileSyncErrorsReport.ps1** (situé dans le répertoire d’installation de l’agent Azure File Sync) pour identifier les fichiers qui ont échoué lors de la synchronisation en raison de descripteurs ouverts, de caractères non pris en charge, ou d’autres problèmes. Le champ ItemPath vous indique l’emplacement du fichier en relation avec le répertoire racine de synchronisation. Consultez la liste des erreurs de synchronisation courantes ci-dessous pour les étapes de correction.

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Résolution des problèmes par les erreurs de synchronisation de fichier/répertoire
**Journal ItemResults : erreurs de synchronisation par élément**  
| HRESULT | HRESULT (décimal) | Chaîne d’erreur | Problème | Correction |
|---------|-------------------|--------------|-------|-------------|
| 0x80c80065 | -2134376347 | ECS_E_DATA_TRANSFER_BLOCKED | Le fichier a produit des erreurs persistantes pendant la synchronisation et par conséquent, et ne sera donc pas synchronisé plus d’une fois par jour. Vous trouverez l’erreur sous-jacente dans un journal des événements antérieurs. | Dans les agents R2 (2.0) et versions ultérieures, l’erreur d’origine plutôt que celle-ci est exposée. Mettez à niveau vers le dernier agent pour voir l’erreur sous-jacente, ou consultez les journaux d’événements antérieurs pour trouver la cause de l’erreur d’origine. |
| 0x7b | 123 | ERROR_INVALID_NAME | Le nom de répertoire est non valide. | Renommez le fichier ou le répertoire en question. Consultez [Directives d’affectation de noms Azure Files](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) et la liste des caractères non pris en charge ci-dessous. |
| 0x8007007b | -2147024773 | STIERR_INVALID_DEVICE_NAME | Le nom de répertoire est non valide. | Renommez le fichier ou le répertoire en question. Consultez [Directives d’affectation de noms Azure Files](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names) et la liste des caractères non pris en charge ci-dessous. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Un fichier a changé, mais la modification n’a pas encore été détectée par la synchronisation. La synchronisation sera rétablie une fois cette modification détectée. | Aucune action requise. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Un fichier ne peut pas être synchronisé, car il est en cours d’utilisation. Le fichier sera synchronisé lorsqu’il ne sera plus en cours d’utilisation. | Aucune action requise. Azure File Sync crée une capture instantanée temporaire VSS une fois par jour sur le serveur pour synchroniser les fichiers qui ont des descripteurs ouverts. |
| 0x20 | 32 | ERROR_SHARING_VIOLATION | Un fichier ne peut pas être synchronisé, car il est en cours d’utilisation. Le fichier sera synchronisé lorsqu’il ne sera plus en cours d’utilisation. | Aucune action requise. |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Une modification de fichier ou de répertoire ne peut pas encore être synchronisée, car un dossier dépendant n’est pas encore synchronisé. Cet élément sera synchronisé une fois que les modifications dépendantes seront synchronisées. | Aucune action requise. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Un fichier a été modifié pendant la synchronisation, par conséquent, il doit être synchronisé à nouveau. | Aucune action requise. |

#### <a name="handling-unsupported-characters"></a>Gestion des caractères non pris en charge
Si le script PowerShell **FileSyncErrorsReport.ps1** montre les défaillances dues à des caractères non pris en charge (codes d’erreur 0x7b et 0x8007007b), vous devriez supprimer ou renommer les caractères en cause à partir des fichiers respectifs. PowerShell imprimera probablement ces caractères en tant que points d’interrogation ou rectangles vides dans la mesure où la plupart de ces caractères n’ont aucun codage visuel standard.

Le tableau ci-dessous contient tous les caractères unicode qu’Azure File Sync ne prend pas en charge.

| Jeu de caractères | Nombre de caractères |
|---------------|-----------------|
| <ul><li>0x0000009D (osc commande de système d’exploitation)</li><li>0x00000090 (dcs chaîne de commande d’appareils)</li><li>0x0000008F (ss3 remplacement unique trois)</li><li>0x00000081 (préréglage haut octet)</li><li>0x0000007F (suppr Supprimer)</li><li>0x0000008D (ri interligne inversé)</li></ul> | 6. |
| <ul><li>0x0000200F (marque droite à gauche)</li><li>0x0000200E (marque gauche à droite)</li><li>0x0000202E (forçage droite à gauche)</li><li>0x0000202D (forçage de gauche à droite)</li><li>0x0000202C (dépilement de formatage directionnel)</li><li>0x0000202B (imbrication de droite à gauche)</li><li>0x0000202A (imbrication de gauche à droite)</li></ul> | 7 |
| 0x0000FDD0 - 0x0000FDEF (formulaire de présentation arabe-a) | 32 |
| 0x0000FFF0 - 0x0000FFFF (caractères spéciaux) | 16 |
| <ul><li>0x0001FFFE - 0x0001FFFF = 2 (type non caractère)</li><li>0x0002FFFE - 0x0002FFFF = 2 (type non caractère)</li><li>0x0003FFFE - 0x0003FFFF = 2 (type non caractère)</li><li>0x0004FFFE - 0x0004FFFF = 2 (type non caractère)</li><li>0x0005FFFE - 0x0005FFFF = 2 (type non caractère)</li><li>0x0006FFFE - 0x0006FFFF = 2 (type non caractère)</li><li>0x0007FFFE - 0x0007FFFF = 2 (type non caractère)</li><li>0x0008FFFE - 0x0008FFFF = 2 (type non caractère)</li><li>0x0009FFFE - 0x0009FFFF = 2 (type non caractère)</li><li>0x000AFFFE - 0x000AFFFF = 2 (type non caractère)</li><li>0x000BFFFE - 0x000BFFFF = 2 (type non caractère)</li><li>0x000CFFFE - 0x000CFFFF = 2 (type non caractère)</li><li>0x000DFFFE - 0x000DFFFF = 2 (type non caractère)</li><li>0x000EFFFE - 0x000EFFFF = 2 (type non caractère)</li><li>0x000FFFFE - 0x000FFFFF = 2 (zone d’utilisation privée supplémentaire)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Erreurs de synchronisation courantes
<a id="-2147023673"></a>**La session de synchronisation a été annulée.**  
| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (décimal)** | -2147023673 | 
| **Chaîne d’erreur** | ERROR_CANCELLED |
| **Correction requise** | Non  |

Les sessions de synchronisation peuvent échouer pour diverses raisons, comme la mise à jour ou le redémarrage en cours du serveur, des captures instantanées VSS, etc. Bien que cette erreur semble nécessiter un suivi, il est possible d’ignorer cette erreur, sauf si elle persiste pendant une période de plusieurs heures.

<a id="-2147012889"></a>**Impossible d’établir une connexion avec le service.**    
| | |
|-|-|
| **HRESULT** | 0x80072ee7 |
| **HRESULT (décimal)** | -2147012889 | 
| **Chaîne d’erreur** | WININET_E_NAME_NOT_RESOLVED |
| **Correction requise** | Oui |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**La requête de l’utilisateur a été limitée par le service.**  
| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (décimal)** | -2134376372 |
| **Chaîne d’erreur** | ECS_E_USER_REQUEST_THROTTLED |
| **Correction requise** | Non  |

Aucune action n’est requise; le serveur essayera à nouveau. Si cette erreur persiste pendant plus de quelques heures, créez une demande de support.

<a id="-2134364065"></a>**La synchronisation ne peut pas accéder au partage de fichiers Azure spécifié dans le point de terminaison de cloud.**  
| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (décimal)** | -2134364065 |
| **Chaîne d’erreur** | ECS_E_CANNOT_ACCESS_EXTERNAL_STORAGE_ACCOUNT |
| **Correction requise** | Oui |

Cette erreur se produit parce que l’agent Azure File Sync ne peut pas accéder au partage de fichiers Azure, ce qui peut être dû au fait que le partage de fichiers Azure ou le compte de stockage qui l’héberge n’existent plus. Vous pouvez résoudre cette erreur en passant par les étapes suivantes :

1. [Vérifiez l’existence du compte de stockage.](#troubleshoot-storage-account)
2. [Vérifiez que le compte de stockage ne contienne aucune règle de réseau.](#troubleshoot-network-rules)
3. [Assurez-vous de l’existence du partage de fichiers Azure.](#troubleshoot-azure-file-share)
4. [Vérifiez qu’Azure File Sync a accès au compte de stockage.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Le nom du compte de stockage utilisé n’a pas pu être résolu.**  
| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (décimal)** | -2134364064 |
| **Chaîne d’erreur** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Correction requise** | Oui |

1. Vérifiez que vous pouvez résoudre le nom DNS de stockage à partir du serveur.

    ```PowerShell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Vérifiez l’existence du compte de stockage.](#troubleshoot-storage-account)
3. [Vérifiez que le compte de stockage ne contienne aucune règle de réseau.](#troubleshoot-network-rules)

<a id="-1906441138"></a>**La synchronisation a échoué en raison d’un problème avec la base de données de synchronisation.**  
| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (décimal)** | -1906441138 |
| **Chaîne d’erreur** | JET_errWriteConflict |
| **Correction requise** | Oui |

Cette erreur se produit lorsqu’il existe un problème avec la base de données interne utilisée par Azure File Sync. Lorsque ce problème se produit créez une demande de support et nous vous contacterons pour vous aider à résoudre ce problème.

<a id="-2134351810"></a>**La limite de stockage de partage de fichiers Azure a été atteinte.**  
| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (décimal)** | -2134351810 |
| **Chaîne d’erreur** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Correction requise** | Oui |

Cette erreur se produit lorsque la limite de stockage de partage de fichiers Azure a été atteinte, ce qui peut se produire si un quota est appliqué pour un partage de fichiers Azure ou si l’utilisation dépasse les limites d’un partage de fichiers Azure. Pour plus d’informations, consultez les [limites actuelles pour un partage de fichiers Azure](storage-files-scale-targets.md).

1. Accédez au groupe de synchronisation au sein du service de synchronisation de stockage.
2. Sélectionnez le point de terminaison de cloud au sein du groupe de synchronisation.
3. Notez le nom de partage de fichiers Azure dans le volet ouvert.
4. Sélectionnez le compte de stockage associé. Si ce lien échoue, le compte de stockage référencé a été supprimé.

    ![Une capture d’écran montrant le volet d’informations de point de terminaison de cloud avec un lien vers le compte de stockage.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Sélectionnez **Fichiers** pour afficher la liste des partages de fichiers.
6. Cliquez sur les points de suspension à la fin de la ligne pour le partage de fichiers Azure référencé par le point de terminaison de cloud.
7. Vérifiez que l’**Utilisation** est inférieure au **Quota**. Remarque : à moins qu’un autre quota n’ait été spécifié, le quota correspondra à la taille maximale [du partage de fichiers Azure](storage-files-scale-targets.md).

    ![Une capture d’écran des propriétés de partage de fichiers Azure.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Si le partage est plein et si un quota n’est pas défini, il est possible de corriger ce problème en faisant de chaque sous-dossier du point de terminaison de serveur en cours son propre point de terminaison de serveur dans leurs propres groupes de synchronisation distincts. De cette manière, chaque sous-dossier se synchronisera avec des partages de fichiers Azure individuels.

<a id="-2134351824"></a>**Impossible de trouver le partage de fichiers Azure.**  
| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (décimal)** | -2134351824 |
| **Chaîne d’erreur** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Correction requise** | Oui |

Cette erreur se produit lorsque le partage de fichiers Azure n’est pas accessible. Pour résoudre des problèmes :

1. [Vérifiez l’existence du compte de stockage.](#troubleshoot-storage-account)
2. [Assurez-vous de l’existence du partage de fichiers Azure.](#troubleshoot-azure-file-share)

Si le partage de fichiers Azure a été supprimé, vous devez créer un nouveau partage de fichiers, puis recréer le groupe de synchronisation. 

<a id="-2134364042"></a>**La synchronisation est en pause pendant la suspension de cet abonnement Azure.**  
| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (décimal)** | -2134364042 |
| **Chaîne d’erreur** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Correction requise** | Oui |

Cette erreur se produit lorsque l’abonnement Azure est suspendu. La synchronisation est réactivée lors de la restauration de l’abonnement Azure. Consultez [Pourquoi mon abonnement Azure est-il désactivé et comment puis-je le réactiver ?](../../billing/billing-subscription-become-disable.md) pour plus d’informations.

<a id="-2134364052"></a>**Le compte de stockage comporte un pare-feu ou des réseaux virtuels configurés.**  
| | |
|-|-|
| **HRESULT** | 0x80c8306c |
| **HRESULT (décimal)** | -2134364052 |
| **Chaîne d’erreur** | ECS_E_MGMT_STORAGEACLSNOTSUPPORTED |
| **Correction requise** | Oui |

Cette erreur se produit lorsque le partage de fichiers Azure est inaccessible en raison d’un pare-feu de compte de stockage ou parce que le compte de stockage appartient à un réseau virtuel. Azure File Sync ne prend pas encore en charge cette fonctionnalité. Pour résoudre des problèmes :

1. [Vérifiez l’existence du compte de stockage.](#troubleshoot-storage-account)
2. [Vérifiez que le compte de stockage ne contienne aucune règle de réseau.](#troubleshoot-network-rules)

Supprimez ces règles pour résoudre ce problème. 

<a id="-2134375911"></a>**La synchronisation a échoué en raison d’un problème avec la base de données de synchronisation.**  
| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (décimal)** | -2134375911 |
| **Chaîne d’erreur** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Correction requise** | Non  |

Cette erreur se résout généralement d’elle-même et peut se produire s’il existe :

* Un nombre élevé de changements apportés aux fichiers sur les serveurs du groupe de synchronisation.
* Un grand nombre d’erreurs sur les fichiers et répertoires individuels.

Si cette erreur persiste pendant plusieurs heures, créez une demande de support et nous vous contacterons pour vous aider à résoudre ce problème.

<a id="-2146762487"></a>**Échec de connexion sécurisée au serveur. Le service de cloud a reçu un certificat inattendu.**  
| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (décimal)** | -2146762487 |
| **Chaîne d’erreur** | CERT_E_UNTRUSTEDROOT |
| **Correction requise** | Oui |

Cette erreur peut se produire si votre organisation utilise un proxy de terminaison SSL ou si une entité malveillante intercepte le trafic entre votre serveur et le service Azure File Sync. Si vous êtes certain que cela est prévu (car votre organisation utilise un certificat proxy de terminaison SSL), vous pouvez ignorer la vérification de certificat avec un remplacement du registre.

1. Créez la valeur de Registre SkipVerifyingPinnedRootCertificate.

    ```PowerShell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Redémarrez le service de synchronisation sur le serveur inscrit.

    ```PowerShell
    Restart-Service -Name FileSyncSvc -Force
    ```

En définissant cette valeur de Registre, l’agent Azure File Sync accepte n’importe quel certificat SSL approuvé localement lors du transfert de données entre le serveur et le service cloud.

<a id="-2147012894"></a>**Impossible d’établir une connexion avec le service.**  
| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (décimal)** | -2147012894 |
| **Chaîne d’erreur** | WININET_E_TIMEOUT |
| **Correction requise** | Oui |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**La synchronisation a échoué en raison d’un problème d’authentification.**  
| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (décimal)** | -2134375680 |
| **Chaîne d’erreur** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Correction requise** | Oui |

Cette erreur se produit généralement parce que l’heure du serveur est incorrecte ou que le certificat utilisé pour l’authentification a expiré. Si l’heure du serveur est correcte, procédez comme suit pour supprimer le certificat arrivé à expiration (s’il est arrivé à expiration) et réinitialiser l’état de l’inscription du serveur :

1. Ouvrez le composant logiciel enfichable MMC de certificats, sélectionnez Compte d’ordinateur et accédez à Certificates (Ordinateur Local)\Personal\Certificates.
2. Supprimer le certificat d’authentification client s’il a expiré et fermez le composant logiciel enfichable MMC de certificats.
3. Ouvrez Regedit et supprimez la clé ServerSetting dans le registre : HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync\ServerSetting
4. Exécutez les commandes PowerShell suivantes :

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Reset-StorageSyncServer
    ```

5. Réinscrivez le serveur en exécutant ServerRegistration.exe (l’emplacement par défaut est C:\Program Files\Azure\StorageSyncAgent).

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Le volume où se trouve le point de terminaison de serveur est faible sur l’espace disque.**  
| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (décimal)** | -1906441711 |
| **Chaîne d’erreur** | JET_errLogDiskFull |
| **Correction requise** | Oui |
| | |
| **HRESULT** | 0x80c8031a |
| **HRESULT (décimal)** | -2134375654 |
| **Chaîne d’erreur** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Correction requise** | Oui |

Cette erreur se produit parce que le volume est saturé. Cette erreur se produit souvent parce que les fichiers situés à l’extérieur du point de terminaison de serveur occupent de l’espace sur le volume. Libérer de l’espace sur le volume en ajoutant des points de terminaison serveur supplémentaires, déplacer les fichiers vers un autre volume ou augmenter la taille du volume sur lequel se trouve le point d’extrémité du serveur.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Le service n’est pas encore prêt pour la synchronisation avec ce point de terminaison de serveur.**  
| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (décimal)** | -2134364145 |
| **Chaîne d’erreur** | ECS_E_REPLICA_NOT_READY |
| **Correction requise** | Non  |

Cette erreur se produit parce qu’il y a des changements sur le partage de fichier Azure directement et que la détection des changements est en cours. La synchronisation commence une fois la détection de modification terminée.

[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**La synchronisation a échoué en raison de problèmes avec de nombreux fichiers individuels.**  
| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (décimal)** | -2134364145 |
| **Chaîne d’erreur** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Correction requise** | Oui |
| | |
| **HRESULT** | 0x80c8021c |
| **HRESULT (décimal)** | -2134375908 |
| **Chaîne d’erreur** | ECS_E_SYNC_METADATA_KNOWLEGE_LIMIT_REACHED |
| **Correction requise** | Oui |
| | |
| **HRESULT** | 0x80c80253 |
| **HRESULT (décimal)** | -2134375853 |
| **Chaîne d’erreur** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Correction requise** | Oui |

Dans les cas où il y a beaucoup d’erreurs de synchronisation par fichier, les sessions de synchronisation peuvent commencer à échouer. Pour corriger cet état, consultez [Résolution des erreurs de synchronisation par fichier/répertoire](#troubleshooting-per-file-directory-sync-errors).

> [!NOTE]
> Azure File Sync crée une capture instantanée VSS temporaire une fois par jour sur le serveur pour synchroniser les fichiers qui ont des descripteurs ouverts.

<a id="-2134376423"></a>**La synchronisation a échoué en raison d’un problème avec le chemin du point de terminaison de serveur.**  
| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (décimal)** | -2134376423 |
| **Chaîne d’erreur** | ECS_E_SYNC_INVALID_PATH |
| **Correction requise** | Oui |

Vérifiez que le chemin d’accès existe, qu’il se trouve sur un volume NTFS local et n’est pas un point d’analyse ou un point de terminaison de serveur existant.

<a id="-2134376373"></a>**Le service est actuellement indisponible.**  
| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (décimal)** | -2134376373 |
| **Chaîne d’erreur** | ECS_E_SERVICE_UNAVAILABLE |
| **Correction requise** | Non  |

Cette erreur se produit parce que le service Azure File Sync n’est pas disponible. Cette erreur se résoudra automatiquement lorsque le service Azure File Sync sera à nouveau disponible.

<a id="-2134375922"></a>**La synchronisation a échoué en raison d’un problème temporaire avec la base de données de synchronisation.**  
| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (décimal)** | -2134375922 |
| **Chaîne d’erreur** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Correction requise** | Non  |

Cette erreur se produit en raison d’un problème interne avec la base de données de synchronisation. Cette erreur se résoudra automatiquement lors de la prochaine tentative de synchronisation d’Azure File Sync. Si cette erreur persiste pendant une période prolongée, créez une demande de support et nous vous contacterons pour vous aider à résoudre ce problème.

### <a name="common-troubleshooting-steps"></a>Ouvrir les étapes de résolution des problèmes
<a id="troubleshoot-storage-account"></a>**Vérifiez l’existence du compte de stockage.**  
# <a name="portaltabportal"></a>[Portail](#tab/portal)
1. Accédez au groupe de synchronisation au sein du service de synchronisation de stockage.
2. Sélectionnez le point de terminaison de cloud au sein du groupe de synchronisation.
3. Notez le nom de partage de fichiers Azure dans le volet ouvert.
4. Sélectionnez le compte de stockage associé. Si ce lien échoue, le compte de stockage référencé a été supprimé.
    ![Une capture d’écran montrant le volet d’informations de point de terminaison de cloud avec un lien vers le compte de stockage.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
# Variables for you to populate based on your configuration
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# Log into the Azure account and put the returned account information
# in a reference variable.
$acctInfo = Connect-AzureRmAccount

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzureRMAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzureRmLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = [System.String[]]@()
Get-AzureRmResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    –SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzureRmStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.DisplayName
}

if ($storageSyncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzureRmStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.DisplayName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzureRmStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $storageSyncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    Write-Host "The storage account referenced in the cloud endpoint does not exist."
}
```
---

<a id="troubleshoot-network-rules"></a>**Vérifiez que le compte de stockage ne contienne aucune règle réseau.**  
# <a name="portaltabportal"></a>[Portail](#tab/portal)
1. Une fois dans le compte de stockage, sélectionnez **Pare-feu et réseaux virtuels** sur le côté gauche du compte de stockage.
2. Dans le compte de stockage, **autoriser l’accès à partir de tous les réseaux** la case d’option doit être sélectionnée.
    ![Une capture d’écran montrant les règles de pare-feu et de réseau de compte stockage désactivés.](media/storage-sync-files-troubleshoot/file-share-inaccessible-2.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
if ($storageAccount.NetworkRuleSet.DefaultAction -ne 
    [Microsoft.Azure.Commands.Management.Storage.Models.PSNetWorkRuleDefaultActionEnum]::Allow) {
    Write-Host ("The storage account referenced contains network " + `
        "rules which are not currently supported by Azure File Sync.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Assurez vous de l’existence du partage de fichiers Azure.**  
# <a name="portaltabportal"></a>[Portail](#tab/portal)
1. Cliquez sur **Aperçu** sur la table des matières de gauche pour revenir à la page principale de compte de stockage.
2. Sélectionnez **Fichiers** pour afficher la liste des partages de fichiers.
3. Vérifiez que le partage de fichiers référencé par le point de terminaison de du cloud apparaît dans la liste des partages de fichiers (vous auriez dû le noter à l’étape 1 ci-dessus).

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell
$fileShare = Get-AzureStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.StorageAccountShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    Write-Host "The Azure file share referenced by the cloud endpoint does not exist"
}
```
---

<a id="troubleshoot-rbac"></a>**Vérifiez qu’Azure File Sync a accès au compte de stockage.**  
# <a name="portaltabportal"></a>[Portail](#tab/portal)
1. Cliquez sur **contrôle d’accès (IAM)** sur la table des matières de gauche pour accéder à la liste des utilisateurs et des applications (*principaux de service*) qui ont accès à votre compte de stockage.
2. Vérifiez que **Hybrid File Sync Service** apparaît dans la liste avec le rôle **Lecteur et Accès aux données**. 

    ![Une capture d’écran du principal de service Hybrid File Sync Service dans l’onglet contrôle d’accès du compte de stockage](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
```PowerShell    
$foundSyncPrincipal = $false
Get-AzureRmRoleAssignment -Scope $storageAccount.Id | ForEach-Object { 
    if ($_.DisplayName -eq "Hybrid File Sync Service") {
        $foundSyncPrincipal = $true
        if ($_.RoleDefinitionName -ne "Reader and Data Access") {
            Write-Host ("The storage account has the Azure File Sync " + `
                "service principal authorized to do something other than access the data " + `
                "within the referenced Azure file share.")
        }

        break
    }
}

if (!$foundSyncPrincipal) {
    Write-Host ("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Comment empêcher les utilisateurs de créer des fichiers contenant des caractères non pris en charge sur le serveur ?
Vous pouvez utiliser [File Server Resource Manager (FSRM) File Screens](https://docs.microsoft.com/windows-server/storage/fsrm/file-screening-management) pour empêcher les fichiers contenant dans leurs noms des caractères non pris en charge d’être créés sur le serveur. Vous pouvez avoir à le faire en utilisant PowerShell car la plupart des caractères non pris en charge ne sont pas imprimables et vous devez donc caster leurs représentations hexadécimales en premier lieu comme caractères.

Tout d’abord créez un groupe de fichiers FSRM à l’aide de la [cmdlet New-FsrmFileGroup](https://docs.microsoft.com/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). Cet exemple définit le groupe comme ne contenant que deux des caractères non pris en charge, mais vous pouvez inclure autant de caractères que nécessaire dans votre groupe de fichiers.

```PowerShell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

Une fois que vous avez défini un groupe de fichiers FSRM, vous pourrez créer un filtre de fichiers FSRM à l’aide de la cmdlet New-FsrmFileScreen.

```PowerShell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Notez que les écrans de fichier doivent uniquement servir à bloquer la création de caractères non pris en charge par Azure File Sync. Si les filtres de fichiers sont utilisés dans d’autres scénarios, la synchronisation tentera en permanence de télécharger les fichiers à partir du partage de fichiers Azure sur le serveur et sera bloquée en raison de l’écran de fichier, ce qui entraînera une grande suite de données. 

## <a name="cloud-tiering"></a>Hiérarchisation cloud 
Il existe deux chemins d’accès dédiés aux défaillances dans la hiérarchisation cloud :

- La hiérarchisation des fichiers peut être mise en échec, auquel cas la tentative d’Azure File Sync de hiérarchiser un fichier sur Azure Files est elle aussi avortée.
- Le rappel des fichiers peut être mis en échec, ce qui signifie que le filtre du système de fichiers Azure File Sync (StorageSync.sys) ne parvient pas à télécharger des données lorsqu’un utilisateur tente d’accéder à un fichier hiérarchisé.

Il existe deux classes principales de défaillances pouvant se produire par le biais des deux chemins d’accès dédiés :

- Défaillances de stockage cloud
    - *Problèmes de disponibilité du service de stockage temporaire*. Pour plus d’informations, consultez [Contrat de niveau de service (SLA) pour le stockage Azure](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Inaccessibilité du partage de fichiers Azure*. Cet échec se produit généralement lorsque vous supprimez un partage de fichiers Azure qui est toujours un point de terminaison de cloud d’un groupe de synchronisation.
    - *Inaccessibilité d’un compte de stockage*. Cet échec se produit généralement lorsque vous supprimez un compte de stockage comportant un partage de fichiers Azure qui est un point de terminaison cloud d’un groupe de synchronisation. 
- Défaillances de serveur 
    - *Défaut de chargement du filtre du système de fichiers Azure File Sync (StorageSync.sys)*. Pour répondre aux requêtes de hiérarchisation/de rappel, il est nécessaire que le filtre du système de fichiers Azure File Sync soit chargé. Ce défaut de chargement peut s’expliquer de différentes manières. La raison la plus courante est le déchargement manuel par un administrateur. Le filtre du système de fichiers Azure File Sync doit être chargé à tout moment pour qu’un bon fonctionnement d’Azure File Sync soit garanti.
    - *Défaut, corruption ou endommagement de point d’analyse*. Un point d’analyse est une structure de données spécifique d’un fichier qui est composée de deux parties distinctes :
        1. Une balise d’analyse, qui indique au système d’exploitation que le filtre du système de fichiers Azure File Sync (StorageSync.sys) doit éventuellement effectuer une action sur les E/S du fichier. 
        2. Les données d’analyse, qui indiquent au filtre du système de fichiers l’URI du fichier sur le point de terminaison associé du cloud (le partage de fichiers Azure). 
        
        La raison la plus courante de la corruption d’un point d’analyse est la tentative par un administrateur de modifier la balise ou ses données. 
    - *Problèmes de connectivité réseau*. Pour hiérarchiser ou rappeler un fichier, le serveur doit disposer d’une connectivité Internet.

Les sections suivantes vous indiquent comment résoudre les problèmes de hiérarchisation cloud et déterminer si un problème est lié au stockage cloud ou au serveur.

<a id="monitor-tiering-activity"></a>**Comment surveiller l’activité de hiérarchisation sur un serveur**  
Pour surveiller l’activité de hiérarchisation sur un serveur, utilisez les ID d’événement 9002, 9003, 9016 et 9029 dans le journal des événements des données de télémétrie (situé sous Applications and Services\Microsoft\FileSync\Agent in Event Viewer).

- L’ID d’événement 9002 fournit des statistiques de dédoublement pour un point de terminaison de serveur. Par exemple, TotalGhostedFileCount, SpaceReclaimedMB, etc.

- L’ID d’événement 9003 fournit la distribution des erreurs de distribution pour un point de terminaison de serveur. Par exemple, le nombre total d’erreurs, le code d’erreur, etc. Remarque, un événement est enregistré par code d’erreur.

- L’ID d’événement 9016 fournit des résultats de dédoublement pour un volume. Par exemple, le pourcentage d’espace libre, le nombre de fichiers dédoublé dans la session, le nombre d’échec de dédoublement de fichiers, etc.

- L’ID d’événement 9029 fournit des informations sur les sessions de dédoublement. Par exemple, le nombre de fichiers tentés dans la session, le nombre de fichiers hiérarchisés dans la session, le nombre de fichiers déjà hiérarchisés, etc.

<a id="monitor-recall-activity"></a>**Comment surveiller l’activité de rappel sur un serveur**  
Pour surveiller l’activité de rappel sur un serveur, utilisez les ID d’événement 9005, 9006, 9007 dans le journal des événements de télémétrie (situé sous Applications and Services\Microsoft\FileSync\Agent in Event Viewer). Remarque : ces événements sont enregistrés toutes les heures.

- L’ID d’événement 9005 fournit une fiabilité de rappel pour un point de terminaison de serveur. Par exemple, le nombre de total des fichiers uniques consultés, le nombre total des fichiers uniques dont l’accès a échoué, etc.

- L’ID d’événement 9006 fournit la distribution des erreurs de rappel pour un point de terminaison de serveur. Par exemple, le nombre total d’échecs de requête, le code d’erreur, etc. Remarque : un événement est enregistré par code d’erreur.

- L’ID d’événement 9007 fournit une performance de rappel pour un point de terminaison de serveur. Par exemple, TotalRecallIOSize, TotalRecallTimeTaken, etc.

<a id="files-fail-tiering"></a>**Résoudre les problèmes de hiérarchisation de fichiers**  
Si la hiérarchisation des fichiers dans Azure Files échoue :

1. Dans l’observateur d’événements, examinez les journaux d’événements de télémétrie, des opérations et de diagnostic, situés sous Applications and Services\Microsoft\FileSync\Agent. 
    1. Vérifiez que le ou les fichiers se trouvent dans le partage de fichiers Azure.

    > [!NOTE]
    > Un fichier doit être synchronisé dans un partage de fichiers Azure pour pouvoir ensuite être hiérarchisé.

    2. Vérifiez la connectivité Internet du serveur. 
    3. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
        - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

> [!NOTE]
> Un d’ID d’événement 9003 est enregistré une fois par heure dans le journal d’événements de télémétrie si un fichier ne parvient pas à hiérarchiser (un événement est enregistré par code d’erreur). Les journaux d’événements des opérations et de diagnostic doivent être utilisés si les informations supplémentaires sont nécessaires pour diagnostiquer un problème.

<a id="files-fail-recall"></a>**Résoudre les problèmes de rappel de fichiers**  
Si le rappel de fichiers échoue :
1. Dans l’observateur d’événements, examinez les journaux d’événements de télémétrie, des opérations et de diagnostic, situés sous Applications and Services\Microsoft\FileSync\Agent.
    1. Vérifiez que le ou les fichiers se trouvent dans le partage de fichiers Azure.
    2. Vérifiez la connectivité Internet du serveur. 
    3. Ouvrez le composant logiciel enfichable MMC des services et vérifiez que le service de l’Agent de synchronisation du stockage (FileSyncSvc) est en cours d’exécution.
    4. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
        - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

> [!NOTE]
> Un d’ID d’événement 9006 est enregistré une fois par heure dans le journal d’événements de télémétrie si un fichier ne parvient pas à rappeler (un événement est enregistré par code d’erreur). Les journaux d’événements des opérations et de diagnostic doivent être utilisés si les informations supplémentaires sont nécessaires pour diagnostiquer un problème.

<a id="files-unexpectedly-recalled"></a>**Résoudre les problèmes de rappel de fichiers inattendu sur un serveur**  
Les antivirus, applications de sauvegarde et autres applications qui lisent un grand nombre de fichiers provoquent des rappels inattendus, sauf s’ils ont été configurés pour ignorer la lecture du contenu des fichiers hors connexion. Ignorer les fichiers hors connexion pour les produits qui prennent en charge cette option permet d’éviter des rappels inattendus pendant les opérations telles que les analyses antivirus ou les travaux de sauvegarde.

Contactez votre éditeur de logiciel pour savoir comment configurer la solution de façon à ignorer la lecture des fichiers hors connexion.

Des rappels inattendus peuvent également se produire dans d’autres scénarios, par exemple, quand vous parcourez des fichiers dans l’Explorateur de fichiers. L’ouverture d’un dossier contenant des fichiers cloud hiérarchisés dans l’Explorateur de fichiers sur le serveur peut provoquer des rappels inattendus. Le risque est d’autant plus grand si une solution antivirus est activée sur le serveur.

## <a name="general-troubleshooting"></a>Résolution générale des problèmes
Si vous rencontrez des problèmes avec Azure File Sync sur un serveur, commencez par effectuer les étapes suivantes :
1. Dans l’observateur d’événements, examinez les journaux d’événements de télémétrie, des opérations et de diagnostic.
    - Les problèmes de synchronisation, de hiérarchisation et de rappel sont enregistrés dans les journaux d’événements de télémétrie, de diagnostic et d’exploitation sous Applications and services\Microsoft\FileSync\Agent.
    - Les problèmes liés à la gestion d’un serveur (par exemple, les paramètres de configuration) sont enregistrés dans ces journaux sous Applications et Services\Microsoft\FileSync\Management.
2. Vérifiez que le service Azure File Sync est en cours d’exécution sur le serveur :
    - Ouvrez le composant logiciel enfichable MMC des services et vérifiez que le service Storage Sync Agent (FileSyncSvc) est en cours d’exécution.
3. Vérifiez que les pilotes de filtre Azure File Sync (StorageSync.sys et StorageSyncGuard.sys) sont en cours d’exécution :
    - À partir d’une invite de commandes avec élévation de privilèges, exécutez `fltmc`. Vérifiez que les pilotes de filtre du système de fichiers StorageSync.sys et StorageSyncGuard.sys sont répertoriés.

Si le problème n’est toujours pas résolu, exécutez l’outil AFSDiag :
1. Créez un répertoire où la sortie AFSDiag doit être enregistrée (par exemple, C:\Output).
2. Ouvrez une fenêtre PowerShell avec élévation de privilèges, puis exécutez les commandes suivantes (appuyez sur Entrée après chaque commande) :

    ```PowerShell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. Pour le niveau de trace du mode noyau d’Azure File Sync, entrez **1** (sauf indication contraire, pour créer des traces plus détaillées), puis appuyez sur Entrée.
4. Pour le niveau de trace du mode utilisateur d’Azure File Sync, entrez **1** (sauf indication contraire, pour créer des traces plus détaillées), puis appuyez sur Entrée.
5. Reproduisez le problème. Quand vous avez terminé, entrez **D**.
6. Un fichier .zip contenant les journaux et les fichiers de trace est enregistré dans le répertoire de sortie que vous avez spécifié.

## <a name="see-also"></a>Voir aussi
- [Forum Aux Questions Azure Files](storage-files-faq.md)
- [Résoudre les problèmes liés à Azure Files sous Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Résoudre les problèmes liés à Azure Files dans Linux](storage-troubleshoot-linux-file-connection-problems.md)
