---
title: Journalisation d’Analytique de stockage Azure
description: Découvrez comment journaliser les informations sur les requêtes effectuées sur le stockage Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 03/11/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: a77cf20be30361abf6590dbd53bdb07c327eb9d8
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204989"
---
# <a name="azure-storage-analytics-logging"></a>Journalisation analytique du stockage Azure

Storage Analytics enregistre des informations détaillées sur les demandes ayant réussi ou échoué pour un service de stockage. Ces informations peuvent servir à analyser des demandes individuelles et à diagnostiquer les problèmes au niveau d'un service de stockage. Les demandes sont enregistrées sur la base du meilleur effort.

 Journalisation d’Analytique de stockage n’est pas activée par défaut pour votre compte de stockage. Vous pouvez l’activer dans le [portail Azure](https://portal.azure.com/). Pour plus d’informations, consultez l’article [Surveillance d’un compte de stockage dans le portail Azure](/azure/storage/storage-monitor-storage-account). Vous pouvez également activer Storage Analytics par programmation via l'API REST ou la bibliothèque cliente. Utilisez le [Get Blob Service Properties](https://docs.microsoft.com/rest/api/storageservices/Blob-Service-REST-API), [Get Queue Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Queue-Service-Properties), et [Get Table Service Properties](https://docs.microsoft.com/rest/api/storageservices/Get-Table-Service-Properties) operations pour activer l’Analytique de stockage pour chaque service.

 Les entrées de journal sont créées uniquement si des demandes sont effectuées sur le point de terminaison de service. Par exemple, si un compte de stockage a une activité dans son point de terminaison Blob, mais pas dans ses points de terminaison de Table ou de la file d’attente, seuls des journaux relatifs au service Blob seront créés.

> [!NOTE]
>  Journalisation du stockage Analytique est actuellement disponible uniquement pour les services Blob, file d’attente et Table. Toutefois, compte de stockage premium n’est pas pris en charge.

## <a name="requests-logged-in-logging"></a>Requêtes consignées lors de la journalisation
### <a name="logging-authenticated-requests"></a>Enregistrement des demandes authentifiées

 Les types de demandes authentifiées suivants sont enregistrés :

- Demandes ayant réussi
- Demandes ayant échoué, y compris les erreurs de délai d’expiration, limitation, réseau, autorisation et autres erreurs
- Demandes à l’aide d’une Signature d’accès partagé (SAS) ou le OAuth, y compris les demandes ayant réussi et ayant échoués
- Demandes de données d’analyse

  Les demandes effectuées par Storage Analytics lui-même, telles que la création ou la suppression d'un journal, ne sont pas enregistrées. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de Storage Analytics](/rest/api/storageservices/storage-analytics-log-format).

### <a name="logging-anonymous-requests"></a>Journalisation des demandes anonymes

 Les types de demandes anonymes suivants sont enregistrés :

- Demandes ayant réussi
- Erreurs de serveur
- Erreurs de délai d’expiration pour le client et serveur
- Demandes GET ayant échoué avec le code d’erreur 304 (non modifié)

  Aucune autre demande anonyme ayant échoué n'est enregistrée. La liste complète des données enregistrées est disponible dans les rubriques [Opérations et messages d’état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) et [Format de journal de Storage Analytics](/rest/api/storageservices/storage-analytics-log-format).

## <a name="how-logs-are-stored"></a>Mode de stockage des journaux d’activité

Tous les journaux sont stockés dans des objets BLOB de blocs dans un conteneur nommé `$logs`, qui est automatiquement créé lorsque Storage Analytique est activé pour un compte de stockage. Le `$logs` conteneur se trouve dans l’espace de noms d’objets blob du compte de stockage, par exemple : `http://<accountname>.blob.core.windows.net/$logs`. Ce conteneur ne peut pas être supprimé une fois Storage Analytics activé, mais son contenu peut l'être. Si vous utilisez votre outil de consultation du stockage pour accéder directement au conteneur, vous verrez tous les objets BLOB contenant vos données de journalisation.

> [!NOTE]
>  Le `$logs` conteneur n’est pas affiché lors d’une opération de liste de conteneurs, tels que l’opération de conteneurs de la liste. Vous devez y accéder directement. Par exemple, vous pouvez utiliser l’opération répertorier des objets BLOB pour accéder aux objets BLOB dans le `$logs` conteneur.

À mesure que des demandes sont enregistrées, Storage Analytics télécharge les résultats intermédiaires en tant que blocs. Périodiquement, Storage Analytics valide ces blocs et les rend accessibles sous forme d'objets blob. Il peut prendre jusqu'à une heure pour les données de journal s’affichent dans les objets BLOB dans le **$logs** conteneur étant donné que la fréquence à laquelle le service de stockage vide les enregistreurs de journal. Il peut exister des enregistrements en double pour les journaux d’activité créés dans la même heure. Vous pouvez déterminer si un enregistrement est un doublon en vérifiant les nombres **RequestId** et **Operation**.

Si vous avez un volume élevé de données de journal avec plusieurs fichiers pour chaque heure, vous pouvez utiliser les métadonnées d’objets blob pour déterminer les données que le journal contient en examinant les champs de métadonnées d’objet blob. Ceci est également utile car il peut parfois y avoir un délai tandis que les données sont écrites dans les fichiers journaux : les métadonnées de blob donne une indication plus précise du contenu blob que le nom d’objet blob.

La plupart des outils de navigation de stockage vous permettre d’afficher les métadonnées d’objets BLOB ; Vous pouvez également lire ces informations à l’aide de PowerShell ou par programmation. L’extrait de code PowerShell suivant est un exemple de filtrage de la liste d’objets BLOB de journal par nom pour spécifier une heure et par les métadonnées pour identifier uniquement les journaux qui contiennent des **écrire** operations.  

 ```powershell
 Get-AzureStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Pour plus d’informations sur la liste d’objets BLOB par programmation, consultez [l’énumération des ressources d’objet Blob](https://msdn.microsoft.com/library/azure/hh452233.aspx) et [définir et extraire les propriétés et métadonnées de ressources Blob](https://msdn.microsoft.com/library/azure/dd179404.aspx).  

### <a name="log-naming-conventions"></a>Conventions d'appellation de journal

 Chaque journal est écrit dans le format suivant :

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 Le tableau suivant décrit chaque attribut dans le nom du journal :

|Attribut|Description|
|---------------|-----------------|
|`<service-name>`|Nom du service de stockage. Par exemple : `blob`, `table`, ou `queue`|
|`YYYY`|L’année à quatre chiffres pour le journal. Par exemple : `2011`|
|`MM`|Mois à deux chiffres pour le journal. Par exemple : `07`|
|`DD`|Le jour à deux chiffres pour le journal. Par exemple : `31`|
|`hh`|L’heure à deux chiffres qui indique l’heure de début pour les journaux, dans les 24 heures UTC mettre en forme. Par exemple : `18`|
|`mm`|Nombre à deux chiffres qui indique la minute de début pour les journaux. **Remarque :**  Cette valeur est non pris en charge dans la version actuelle de l’Analytique de stockage, et sa valeur sera toujours `00`.|
|`<counter>`|Compteur de base zéro à six chiffres qui indique le nombre d'objets blob de journal générés pour le service de stockage durant une période d'une heure. Ce compteur commence à `000000`. Par exemple : `000001`|

 Voici un exemple complet de nom de journal qui combine les exemples ci-dessus :

 `blob/2011/07/31/1800/000001.log`

 Voici un exemple d’URI qui peut être utilisé pour accéder au journal ci-dessus :

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Lorsqu'une demande de stockage est enregistrée, le nom du journal qui en résulte correspond à l'heure de fin de l'opération demandée. Par exemple, si une demande GetBlob se termine à 18 h 30 le 31/7/2011, le journal est écrit avec le préfixe suivant : `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Métadonnées du journal

 Tous les objets blob de journal sont stockés avec des métadonnées qui peuvent être utilisées pour identifier les données de journalisation contenues dans l'objet blob. Le tableau suivant décrit chaque attribut de métadonnées :

|Attribut|Description|
|---------------|-----------------|
|`LogType`|Décrit si le journal contient des informations relatives aux opérations de lecture, écriture ou suppression. Cette valeur peut inclure un type ou une combinaison des trois, séparés par des virgules.<br /><br /> Exemple 1 : `write`<br /><br /> Exemple 2 : `read,write`<br /><br /> Exemple 3 : `read,write,delete`|
|`StartTime`|La première heure d’une entrée dans le journal, sous la forme de `YYYY-MM-DDThh:mm:ssZ`. Par exemple : `2011-07-31T18:21:46Z`|
|`EndTime`|La dernière heure d’une entrée dans le journal, sous la forme de `YYYY-MM-DDThh:mm:ssZ`. Par exemple : `2011-07-31T18:22:09Z`|
|`LogVersion`|Version du format du journal.|

 La liste suivante présente un exemple de métadonnées complètes utilisant les exemples ci-dessus :

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Activer la journalisation du stockage

Vous pouvez activer la journalisation de stockage avec le portail Azure, PowerShell et SDK de stockage.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Activer la journalisation du stockage à l’aide du portail Azure  

Dans le portail Azure, utilisez le **les paramètres de diagnostic (classique)** panneau pour contrôler Storage Logging, accessible à partir de la **Monitoring (classique)** section d’un compte de stockage **Panneau de Menu** .

Vous pouvez spécifier les services de stockage que vous souhaitez ouvrir une session et la période de rétention (en jours) pour les données journalisées.  

### <a name="enable-storage-logging-using-powershell"></a>Activer la journalisation du stockage à l’aide de PowerShell  

 Vous pouvez utiliser PowerShell sur votre ordinateur local pour configurer Storage Logging dans votre compte de stockage à l’aide de l’applet de commande Azure PowerShell **Get-AzureStorageServiceLoggingProperty** pour récupérer les paramètres actuels et l’applet de commande  **Set-AzureStorageServiceLoggingProperty** pour modifier les paramètres actuels.  

 Les applets de commande qui contrôlent Storage Logging utilisent un **LoggingOperations** paramètre qui est une chaîne contenant une liste séparée par des virgules des types de demande pour vous connecter. Les trois types de demande possibles sont **lire**, **écrire**, et **supprimer**. Pour désactiver la journalisation, utilisez la valeur **aucun** pour le **LoggingOperations** paramètre.  

 La commande suivante active la journalisation pour la lecture, écriture et suppression des demandes dans le service de file d’attente dans votre compte de stockage par défaut avec la rétention de cinq jours :  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 La commande suivante désactive la journalisation du service de table dans votre compte de stockage par défaut :  

```powershell
Set-AzureStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Pour plus d’informations sur la configuration des applets de commande Azure PowerShell avec votre abonnement Azure et sur la sélection du compte de stockage par défaut à utiliser, consultez : [Guide pratique pour installer et configurer Azure PowerShell](https://azure.microsoft.com/documentation/articles/install-configure-powershell/).  

### <a name="enable-storage-logging-programmatically"></a>Activez la journalisation par programme  

 Outre l’utilisation du portail Azure ou les applets de commande Azure PowerShell pour contrôler Storage Logging, vous pouvez également utiliser une des API de stockage Azure. Par exemple, si vous utilisez un langage .NET, vous pouvez utiliser la bibliothèque cliente de stockage.  

 Les classes **CloudBlobClient**, **CloudQueueClient**, et **CloudTableClient** contiennent toutes des méthodes telles que **SetServiceProperties** et **SetServicePropertiesAsync** qui prennent un **ServiceProperties** objet en tant que paramètre. Vous pouvez utiliser la **ServiceProperties** objet pour configurer Storage Logging. Par exemple, ce qui suit C# extrait de code montre comment modifier ce qui est enregistré et la période de rétention pour la journalisation de la file d’attente :  

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

 Pour plus d’informations sur l’utilisation d’un langage .NET pour configurer Storage Logging, consultez [référence de bibliothèque cliente de stockage](https://msdn.microsoft.com/library/azure/dn261237.aspx).  

 Pour obtenir des informations générales sur la configuration de journalisation du stockage à l’aide de l’API REST, consultez [activation et configuration d’une Analytique de stockage](https://msdn.microsoft.com/library/azure/hh360996.aspx).  

## <a name="download-storage-logging-log-data"></a>Télécharger les données du journal de journalisation du stockage

 Pour afficher et analyser vos données de journal, vous devez télécharger les objets BLOB qui contiennent les données de journal que vous vous intéressez à un ordinateur local. De nombreux outils de consultation du stockage permettent de télécharger des objets BLOB à partir de votre compte de stockage ; Vous pouvez également utiliser l’équipe de stockage Azure fourni l’outil de copie de ligne de commande Azure (**AzCopy**) pour télécharger vos données de journal.  

 Veillez à que télécharger les données de journal que vous êtes intéressé et vous éviter de télécharger les mêmes données plusieurs fois :  

-   Utiliser la date et la convention de nommage de temps pour les objets BLOB contenant les données du journal pour suivre les objets BLOB vous avez déjà téléchargé pour analyse afin d’éviter de télécharger une nouvelle les mêmes données plusieurs fois.  

-   Utiliser les métadonnées sur les objets BLOB contenant des données de journal pour identifier la période spécifique pour lequel l’objet blob conserve les données de journal pour identifier le blob exact, que vous devez télécharger.  

> [!NOTE]
>  AzCopy fait partie du SDK Azure, mais vous pouvez toujours télécharger la dernière version à partir de [ https://aka.ms/AzCopy ](https://aka.ms/AzCopy). Par défaut, AzCopy est installé dans le dossier **C:\Program Files (x86) \Microsoft SDKs\Windows Azure\AzCopy**, vous devez ajouter ce dossier à votre chemin avant d’essayer d’exécuter l’outil dans une invite de commandes ou une fenêtre PowerShell.  

 L’exemple suivant montre comment vous pouvez télécharger les données du journal pour le service de file d’attente pour les heures, en commençant à 09 h, 10 h et 11 h 00 le 20 mai 2014. Le **/S** paramètre entraîne AzCopy générer une structure de dossier local basée sur les dates et heures dans les noms de fichier journal ; la **/V** paramètre entraîne AzCopy produire une sortie détaillée ; le **/Y** paramètre permet à AzCopy de remplacer tous les fichiers locaux. Remplacez **< yourstorageaccount\>**  avec le nom de votre compte de stockage et la remplacez **< yourstoragekey par\>**  avec votre clé de compte de stockage.  

```
AzCopy 'http://<yourstorageaccount>.blob.core.windows.net/$logs/queue'  'C:\Logs\Storage' '2014/05/20/09' '2014/05/20/10' '2014/05/20/11' /sourceKey:<yourstoragekey> /S /V /Y  
```  

 AzCopy comprend aussi d’autres paramètres utiles qui contrôlent comment elle définit l’heure de dernière modification sur les fichiers téléchargés, et préciser s’il doit télécharger les fichiers plus anciens ou plus récents que les fichiers qui existent déjà sur votre ordinateur local. Vous pouvez également l’exécuter en mode de redémarrage. Pour plus d’informations, consultez l’aide en exécutant le **AzCopy / ?** commande.  

 Pour obtenir un exemple montrant comment télécharger vos données de journal par programmation, consultez le blog [journalisation du stockage Windows Azure : Utilisation des journaux pour suivre les demandes de stockage](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/08/03/windows-azure-storage-logging-using-logs-to-track-storage-requests.aspx) et recherchez le mot « DumpLogs » sur la page.  

 Lorsque vous avez téléchargé vos données de journal, vous pouvez afficher les entrées de journal dans les fichiers. Ces fichiers journaux emploient un format de texte délimité que de nombreux journaux lecture outils sont en mesure d’analyser, dont Microsoft Message Analyzer (pour plus d’informations, consultez le guide [surveillance, diagnostic et résolution des problèmes de Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). D’autres outils possèdent des fonctionnalités différentes pour la mise en forme, de filtrage, de tri et la recherche de contenu de vos fichiers journaux. Pour plus d’informations sur le format de fichier journal de journalisation du stockage et le contenu, consultez [le Format de journal Analytique stockage](/rest/api/storageservices/storage-analytics-log-format) et [les Messages d’état et les opérations de journalisation de stockage Analytique](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Étapes suivantes

* [Format du journal de l’analyse de stockage](/rest/api/storageservices/storage-analytics-log-format)
* [Opérations et messages d'état enregistrés Storage Analytics](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Métriques d’Analytique de stockage (classique)](storage-analytics-metrics.md)
