---
title: Journalisation Azure Storage Analytics
description: Utilisez Storage Analytics pour consigner des détails sur les demandes de stockage Azure. Consultez les demandes journalisées, la façon dont les journaux sont stockés, comment activer la journalisation du stockage, et bien plus encore.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: d48ab6223485807400b6749bcf72691261405495
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108134552"
---
# <a name="azure-storage-analytics-logging"></a>Journalisation Azure Storage Analytics

Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort. Cela signifie que la plupart des requêtes génèrent un enregistrement dans le journal, mais l’exhaustivité et la chronologie des journaux de Storage Analytics ne sont pas garanties. 

> [!NOTE]
> Nous vous recommandons d’utiliser les journaux Stockage Azure disponibles dans Azure Monitor au lieu des journaux Storage Analytics. Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Cette préversion active les journaux des objets blob (qui incluent Azure Data Lake Storage Gen2), les fichiers, les files d’attente et les tables. Pour plus d’informations, consultez l’un des articles suivants :
>
> - [Supervision du Stockage Blob Azure](../blobs/monitor-blob-storage.md)
> - [Supervision d’Azure Files](../files/storage-files-monitoring.md)
> - [Surveiller le service Stockage File d'attente](../queues/monitor-queue-storage.md)
> - [Supervision du stockage Table Azure](../tables/monitor-table-storage.md)

 La journalisation Storage Analytics n’est pas activée par défaut pour votre compte de stockage. Vous pouvez l'activer sur le [portail Azure](https://portal.azure.com/), ou à l'aide de PowerShell ou Azure CLI. Pour obtenir des instructions pas à pas, consultez [Activer et gérer les journaux Azure Storage Analytics (classique)](manage-storage-analytics-logs.md). 

Vous pouvez également activer les journaux Storage Analytics par programmation via l'API REST ou la bibliothèque de client. Utilisez les opérations [Get Blob Service Properties](/rest/api/storageservices/Blob-Service-REST-API), [Get Queue Service Properties](/rest/api/storageservices/Get-Queue-Service-Properties) et [Get Table Service Properties](/rest/api/storageservices/Get-Table-Service-Properties) pour activer Storage Analytics pour chaque service. Pour consulter un exemple d'activation des journaux Storage Analytics à l'aide de .NET, consultez [Activer les journaux](manage-storage-analytics-logs.md).

 Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison Blob, mais pas dans ses points de terminaison de Table ou de File d’attente, seuls des journaux d’activité relatifs au service BLOB seront créés.

> [!NOTE]
>  La journalisation Storage Analytics est disponible pour les services Blob, File d’attente et de Table. La journalisation Storage Analytics est également disponible pour les comptes [BlockBlobStorage](./storage-account-create.md) avec des performances Premium. Toutefois, elle n’est pas disponible pour les comptes v2 à usage général avec des performances Premium.

## <a name="requests-logged-in-logging"></a>Requêtes consignées lors de la journalisation
### <a name="logging-authenticated-requests"></a>Enregistrement des demandes authentifiées

 Les types de demandes authentifiées suivants sont enregistrés :

- Demandes ayant réussi
- Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs
- Requêtes utilisant une signature d’accès partagé (SAS) ou OAuth, y compris les requêtes ayant réussi et ayant échoué
- Demandes de données d’analyse

  Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de Storage Analytics](/rest/api/storageservices/storage-analytics-log-format).

### <a name="logging-anonymous-requests"></a>Journalisation des demandes anonymes

 Les types de demandes anonymes suivants sont enregistrés :

- Demandes ayant réussi
- Erreurs de serveur
- Erreurs de délai d’expiration pour le client et le serveur
- Demandes GET ayant échoué avec le code d’erreur 304 (non modifié)

  Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de Storage Analytics](/rest/api/storageservices/storage-analytics-log-format).

## <a name="how-logs-are-stored"></a>Mode de stockage des journaux d’activité

Tous les journaux d’activité sont stockés dans des objets blob de blocs dans un conteneur nommé `$logs`, qui est automatiquement créé lorsque Storage Analytics est activé pour un compte de stockage. Le conteneur `http://<accountname>.blob.core.windows.net/$logs` se trouve dans l’espace de noms d’objets blob du compte de stockage, par exemple : `$logs`. Ce conteneur ne peut pas être supprimé une fois Storage Analytics activé, mais son contenu peut l'être. Si vous utilisez votre outil de navigation du stockage pour accéder directement au conteneur, vous verrez tous les objets Blob contenant vos données de journalisation.

> [!NOTE]
>  Le conteneur `$logs` ne s’affiche pas lorsqu’une opération d’énumération des conteneurs est en cours, comme l’opération Répertorier les conteneurs. Vous devez y accéder directement. Par exemple, vous pouvez utiliser l’opération Liste des blobs pour accéder aux objets blob dans le conteneur `$logs`.

À mesure que des demandes sont enregistrées, Storage Analytics télécharge les résultats intermédiaires en tant que blocs. Périodiquement, Storage Analytics valide ces blocs et les rend accessibles sous forme d'objets blob. Une heure peut être nécessaire avant l’affichage des données de journaux dans les objets Blob du conteneur **$logs** en raison de la fréquence à laquelle le service de stockage vide les enregistreurs de journal. Il peut exister des enregistrements en double pour les journaux d’activité créés dans la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant les nombres **RequestId** et **Operation**.

Si vous avez un volume élevé de données de journal avec plusieurs fichiers pour chaque heure, vous pouvez utiliser les métadonnées d’objets Blob pour déterminer les données que le journal contient en examinant les champs de métadonnées d’objet Blob. Ceci est également utile car il peut parfois y avoir un délai d’écriture des données dans les fichiers journaux : les métadonnées d’objet Blob indiquent plus précisément le contenu d’objet Blob que le nom d’objet Blob.

La plupart des outils de navigation du stockage vous permettent d’afficher les métadonnées d’objets Blob. Vous pouvez également lire ces informations à l’aide de PowerShell ou par programmation. L’extrait de code PowerShell suivant est un exemple de filtrage de la liste d’objets Blob de journal par nom pour spécifier une heure et par métadonnées pour identifier uniquement les journaux qui contiennent des opérations d’**écriture**.  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'blob/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Pour plus d’informations sur la liste d’objets Blob par programmation, consultez [Énumération de ressources d’objet Blob](/rest/api/storageservices/Enumerating-Blob-Resources) et [Définition et récupération de propriétés et métadonnées de ressources d’objets Blob](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Conventions d'appellation de journal

 Chaque journal est écrit au format suivant :

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Le tableau suivant décrit chaque attribut du nom du journal :

|Attribut|Description|
|---------------|-----------------|
|`<service-name>`|Nom du service de stockage. Par exemple : `blob`, `table` ou `queue`|
|`YYYY`|Année à quatre chiffres pour le journal. Par exemple : `2011`|
|`MM`|Mois à deux chiffres pour le journal. Par exemple : `07`|
|`DD`|Jour à deux chiffres pour le journal. Par exemple : `31`|
|`hh`|Heure à deux chiffres qui indique l’heure de début pour les journaux d’activité, au format UTC 24 heures. Par exemple : `18`|
|`mm`|Nombre à deux chiffres qui indique la minute de début pour les journaux d’activité. **Remarque :**  Cette valeur n’étant pas prise en charge dans la version actuelle de Storage Analytics, elle est toujours égale à `00`.|
|`<counter>`|Compteur de base zéro à six chiffres qui indique le nombre d'objets blob de journal générés pour le service de stockage durant une période d'une heure. Ce compteur commence à `000000`. Par exemple : `000001`|

 Voici un exemple complet de nom de journal qui combine les exemples ci-dessus :

 `blob/2011/07/31/1800/000001.log`

 Voici un exemple d’URI qui peut être utilisé pour accéder au journal ci-dessus :

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Lorsqu'une demande de stockage est enregistrée, le nom du journal qui en résulte correspond à l'heure de fin de l'opération demandée. Par exemple, si une requête GetBlob se termine à 18h30 le 31 juillet 2011, le journal est écrit avec le préfixe suivant : `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Métadonnées du journal

 Tous les objets blob de journal sont stockés avec des métadonnées qui peuvent être utilisées pour identifier les données de journalisation contenues dans l'objet blob. Le tableau suivant décrit chaque attribut de métadonnées :

|Attribut|Description|
|---------------|-----------------|
|`LogType`|Décrit si le journal contient des informations relatives aux opérations de lecture, écriture ou suppression. Cette valeur peut inclure un type ou une combinaison des trois, séparés par des virgules.<br /><br /> Exemple 1 : `write`<br /><br /> Exemple 2 : `read,write`<br /><br /> Exemple 3 : `read,write,delete`|
|`StartTime`|Heure la plus antérieure d’une entrée dans le journal, au format `YYYY-MM-DDThh:mm:ssZ`. Par exemple : `2011-07-31T18:21:46Z`|
|`EndTime`|Heure la plus récente d’une entrée dans le journal, au format `YYYY-MM-DDThh:mm:ssZ`. Par exemple : `2011-07-31T18:22:09Z`|
|`LogVersion`|Version du format du journal.|

 La liste suivante présente un exemple de métadonnées complètes utilisant les exemples ci-dessus :

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`


## <a name="next-steps"></a>Étapes suivantes

* [Activer et gérer les journaux Azure Storage Analytics (classique)](manage-storage-analytics-logs.md)
* [Format du journal de l’analyse de stockage](/rest/api/storageservices/storage-analytics-log-format)
* [Opérations et messages d'état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métriques Storage Analytics (classiques)](storage-analytics-metrics.md)