---
title: Informations de référence sur les données de supervision du stockage Azure | Microsoft Docs
description: Informations de référence sur les journaux et les métriques pour la supervision des données de stockage Azure.
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 481406b02d7d864dd16ac42918ae1aa2dea0b145
ms.sourcegitcommit: 1f48ad3c83467a6ffac4e23093ef288fea592eb5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/29/2020
ms.locfileid: "84195225"
---
# <a name="azure-storage-monitoring-data-reference"></a>Informations de référence sur les données de supervision du stockage Azure

Consultez [Supervision du stockage Azure](monitor-storage.md) pour plus d’informations sur la collecte et l’analyse des données de supervision du stockage Azure.

## <a name="metrics"></a>Mesures

Les tableaux suivants répertorient les métriques de plateforme collectées pour le stockage Azure. 

### <a name="capacity-metrics"></a>Métriques de capacité

Les valeurs de mesures de capacité sont envoyées à Azure Monitor toutes les heures. Les valeurs sont actualisées tous les jours. Le fragment de temps définit l’intervalle de temps pour lequel des valeurs de mesures sont présentées. Le fragment de temps pris en charge pour toutes les mesures de capacité est d’une heure (PT1H).

Stockage Azure fournit les mesures de capacité suivantes dans Azure Monitor.

#### <a name="account-level"></a>Niveau du compte

| Métrique | Description |
| ------------------- | ----------------- |
| UsedCapacity | Quantité de stockage utilisée par le compte de stockage. Pour les comptes de stockage standard, il s’agit de la somme de la capacité utilisée par les objets blob, tables, fichiers et files d’attente. Pour les comptes de stockage Premium et les comptes de stockage Blob, elle équivaut à BlobCapacity. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

#### <a name="blob-storage"></a>Stockage d'objets blob

| Métrique | Description |
| ------------------- | ----------------- |
| BlobCapacity | Total de stockage d’objets blob utilisé dans le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 <br/> Dimensions : **BlobType** et **BlobTier** ([définition](#metrics-dimensions)) |
| BlobCount    | Nombre d’objets blob stockés dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 <br/> Dimensions : **BlobType** et **BlobTier** ([définition](#metrics-dimensions)) |
| ContainerCount    | Nombre de conteneurs dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| IndexCapacity     | Espace utilisé par l'index hiérarchique d'ADLS Gen2 <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

#### <a name="table-storage"></a>Stockage de tables

| Métrique | Description |
| ------------------- | ----------------- |
| TableCapacity | Quantité de stockage de tables utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| TableCount   | Nombre de tables dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| TableEntityCount | Nombre d’entités de table dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

#### <a name="queue-storage"></a>Stockage de files d'attente

| Métrique | Description |
| ------------------- | ----------------- |
| QueueCapacity | Quantité de stockage de files d’attente utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueCount   | Nombre de files d’attente dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| QueueMessageCount | Nombre de messages de file d’attente non expirés dans le compte de stockage. <br/><br/>Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

#### <a name="file-storage"></a>Stockage Fichier

| Métrique | Description |
| ------------------- | ----------------- |
| FileCapacity | Quantité de stockage de fichiers utilisée par le compte de stockage. <br/><br/> Unité : Octets <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| FileCount   | Nombre de fichiers dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |
| FileShareCount | Nombre de partages de fichiers dans le compte de stockage. <br/><br/> Unité : Count <br/> Type d’agrégation : Average <br/> Exemple de valeur : 1 024 |

### <a name="transaction-metrics"></a>Métriques de transaction

Des métriques de transaction sont émises lors de chacune des requêtes adressées à un compte de stockage entre Stockage Azure et Azure Monitor. En l'absence d'activité sur votre compte de stockage, il n'y aura pas de données sur les métriques de transaction au cours la période. Toutes les mesures de transaction sont disponibles au niveau du compte et au niveau du service (stockage d’objets blob, stockage de tables, fichiers Azure et stockage de files d’attente). Le fragment de temps définit l’intervalle de temps auquel des valeurs de mesures sont présentées. Les fragments de temps pris en charge pour toutes les mesures de transaction sont PT1H et PT1M.

Stockage Azure fournit les mesures de transaction suivantes dans Azure Monitor.

| Métrique | Description |
| ------------------- | ----------------- |
| Transactions | Nombre de requêtes envoyées à un service de stockage ou à l’opération API spécifiée. Ce nombre inclut les requêtes réussies et celles ayant échoué, ainsi que les requêtes qui ont généré des erreurs. <br/><br/> Unité : Count <br/> Type d’agrégation : Total <br/> Dimensions applicables : ResponseType, GeoType, ApiName et Authentication ([Définition](#metrics-dimensions))<br/> Exemple de valeur : 1 024 |
| Entrée | Quantité de données d’entrée. Ce nombre inclut les entrées d’un client externe dans Stockage Microsoft Azure ainsi que les entrées dans Azure. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Sortie | Quantité de données de sortie. Ce nombre inclut les sorties d’un client externe dans Stockage Microsoft Azure ainsi que les sorties dans Azure. Par conséquent, ce nombre ne reflète pas les sorties facturables. <br/><br/> Unité : Octets <br/> Type d’agrégation : Total <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessServerLatency | Durée moyenne utilisée pour traiter une requête réussie par Stockage Azure. Cette valeur n’inclut pas la latence réseau spécifiée dans SuccessE2ELatency. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| SuccessE2ELatency | Latence moyenne de bout en bout des requêtes réussies envoyées à un service de stockage ou à l’opération API spécifiée. Cette valeur inclut le temps de traitement requis au sein de Stockage Microsoft Azure pour lire la requête, envoyer la réponse et recevoir un accusé de réception de la réponse. <br/><br/> Unité : Millisecondes <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 1 024 |
| Disponibilité | Pourcentage de disponibilité pour le service de stockage ou l’opération API spécifiée. La disponibilité est calculée en prenant la valeur TotalBillableRequests puis en la divisant par le nombre de requêtes applicables, y compris les requêtes qui ont généré des erreurs inattendues. Toutes erreurs inattendues réduisent la disponibilité du service de stockage ou de l’opération API spécifiée. <br/><br/> Unité : Pourcentage <br/> Type d’agrégation : Average <br/> Dimensions applicables : GeoType, ApiName et Authentication ([Définition](#metrics-dimensions)) <br/> Exemple de valeur : 99,99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>Dimensions de mesures

Stockage Azure prend en charge les dimensions suivantes pour les mesures dans Azure Monitor.

| Nom de la dimension | Description |
| ------------------- | ----------------- |
| **BlobType** | Type d’objet blob pour les mesures d’objet Blob uniquement. Les valeurs prises en charge sont **BlockBlob**, **PageBlob**, et **Azure Data Lake Storage**. Append Blob est inclus dans BlockBlob. |
| **BlobTier** | Le Stockage Azure propose différents niveaux d’accès qui vous permettent de stocker vos objets blob de la manière la plus économique. Pour en savoir plus, consultez [Niveau du stockage Azure d’objets blob](../blobs/storage-blob-storage-tiers.md). Les valeurs prises en charge incluent : <br/> <li>**Hot** : Niveau de stockage chaud</li> <li>**Cool** : Niveau de stockage froid</li> <li>**Archivage** : Niveau de stockage archive</li> <li>**Premium** : Niveau Premium pour les objets blob de blocs</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60** : Types de niveau pour les objets blob de pages premium</li> <li>**Standard** : Type de niveau pour les objets blob de pages standard</li> <li>**Untiered** : Type de niveau pour un compte de stockage à usage général v1</li> |
| **GeoType** | Transaction du cluster principal ou secondaire. Les valeurs disponibles incluent **Principal** et **Secondaire**. S’applique au stockage Géo-redondant avec accès en lecture (RA-GRS) lors de la lecture d’objets à partir du locataire secondaire. |
| **ResponseType** | Type de réponse de transaction. Les valeurs disponibles incluent : <br/><br/> <li>**ServerOtherError**: toutes les autres erreurs côté serveur sauf celles décrites </li> <li>**ServerBusyError**: requête authentifiée qui a renvoyé un code d’état HTTP 503. </li> <li>**ServerTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Le délai d’expiration s’est produit en raison d’une erreur serveur. </li> <li>**AuthorizationError**: requête authentifiée qui a échoué en raison d’un accès aux données non autorisé ou d’un échec d’autorisation. </li> <li>**NetworkError**: requête authentifiée qui a échoué en raison d’erreurs réseau. Se produit généralement lorsqu’un client ferme une connexion avant la fin du délai d’expiration. </li><li>**ClientAccountBandwidthThrottlingError** : Une limitation de la bande passante est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](scalability-targets-standard-account.md).</li><li>**ClientAccountRequestThrottlingError** : Une limitation du taux de requêtes est appliquée à la requête pour dépassement des [limites de scalabilité du compte de stockage](scalability-targets-standard-account.md).<li>**ClientThrottlingError**: Autre erreur de limitation côté client. ClientAccountBandwidthThrottlingError et ClientAccountRequestThrottlingError sont exclus.</li> <li>**ClientTimeoutError**: requête authentifiée et arrivée à expiration, qui a renvoyé un code d’état HTTP 500. Si le délai d’expiration réseau du client ou le délai d’expiration de la requête est défini sur une valeur inférieure à ce qui est attendu par le service de stockage, il s’agit d’un délai d’expiration attendu. Sinon, il est signalé comme une erreur ServerTimeoutError. </li> <li>**ClientOtherError**: toutes les autres erreurs côté client sauf celles décrites. </li> <li>**Réussite** : requête réussie</li> <li> **SuccessWithThrottling** : Demande réussie lorsqu’un client SMB est ralenti lors des premières tentatives, mais qu’il réussit lors des suivantes.</li> |
| **ApiName** | Nom de l’opération. Par exemple : <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> Pour tous les noms d’opérations, voir [document](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
| **Authentification** | Type d’authentification utilisé dans les transactions. Les valeurs disponibles incluent : <br/> <li>**AccountKey** : la transaction est authentifiée avec la clé du compte de stockage.</li> <li>**SAS** : la transaction est authentifiée avec des signatures d’accès partagé.</li> <li>**OAuth** : la transaction est authentifiée avec des jetons d’accès OAuth.</li> <li>**Anonymous** : la transaction est demandée anonymement. Elle n’inclut pas les demandes préalables.</li> <li>**AnonymousPreflight** : la transaction est une requête préalable.</li> |

Pour les mesures prenant en charge des dimensions, vous devez spécifier la valeur de la dimension pour afficher les valeurs de mesures correspondantes. Par exemple, si vous examinez la valeur **Transactions** pour des réponses réussies, vous devez filtrer la dimension **ResponseType** avec **Success**. Si vous examinez la valeur **BlobCount** pour BlockBlob, vous devez filtrer la dimension **BlobType** avec **BlockBlob**.

## <a name="resource-logs-preview"></a>Journaux de ressources (préversion)

> [!NOTE]
> Les journaux de stockage Azure dans Azure Monitor sont en préversion publique et sont disponibles pour le test en préversion dans toutes les régions de cloud public. Pour vous inscrire dans la préversion, consultez [cette page](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u).  Cette préversion active les journaux pour les objets blob (y compris Azure Data Lake Storage Gen2), les fichiers, les files d’attente, les tables, les comptes de stockage Premium dans les comptes de stockage à usage général v1 et à usage général v2. Les comptes de stockage classiques ne sont pas pris en charge.

Le tableau suivant liste les propriétés des journaux de ressources de stockage Azure lorsqu’elles sont collectées dans les journaux Azure Monitor ou dans le Stockage Azure. Ces propriétés décrivent l’opération, le service et le type d’autorisation qui ont servi à effectuer l’opération.

### <a name="fields-that-describe-the-operation"></a>Champs qui décrivent l’opération

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| Propriété | Description |
|:--- |:---|
|**time** | Heure UTC (Universal Time Coordinated) à laquelle la demande a été reçue par le stockage. Par exemple : `2018/11/08 21:09:36.6900118`.|
|**resourceId** | ID de ressource du compte de stockage. Par exemple : `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | Catégorie de l’opération demandée. Par exemple : `StorageRead`, `StorageWrite` ou `StorageDelete`.|
|**operationName** | Type de l’opération REST qui a été exécutée. <br> Pour obtenir la liste complète des opérations, consultez la rubrique [Opérations journalisées et messages d’état de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). |
|**operationVersion** | Version du service de stockage qui a été spécifiée lorsque la demande a été effectuée. Elle équivaut à la valeur de l’en-tête **x-ms-version**. Par exemple : `2017-04-17`.|
|**schemaVersion** | Version de schéma du journal. Par exemple : `1.0`.|
|**statusCode** | Code d’état HTTP pour la demande. Si la demande est interrompue, cette valeur peut être définie sur `Unknown`. <br> Par exemple : `206` |
|**statusText** | Statut de l’opération demandée.  Pour obtenir la liste complète des messages d’état, consultez la rubrique [Opérations journalisées et messages d’état de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages). Dans la version 2017-04-17 et les versions ultérieures, le message d’état `ClientOtherError` n’est pas utilisé. À la place, ce champ contient un code d’erreur. Par exemple : `SASSuccess`  |
|**durationMs** | Durée totale, en millisecondes, pour exécuter l’opération demandée. Cette valeur inclut le temps nécessaire à la lecture de la demande entrante et à l’envoi de la réponse au demandeur. Par exemple : `12`.|
|**callerIpAddress** | Adresse IP du demandeur, numéro de port inclus. Par exemple : `192.100.0.102:4362`. |
|**correlationId** | ID utilisé pour corréler les journaux entre les ressources. Par exemple : `b99ba45e-a01e-0042-4ea6-772bbb000000`. |
|**location** | Emplacement du compte de stockage. Par exemple : `North Europe`. |
|**protocol**|Protocole utilisé dans l’opération. Par exemple : `HTTP`, `HTTPS`, `SMB` ou `NFS`|
| **uri** | URI (Uniform Resource Identifier) demandé. Par exemple : `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`. |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>Champs qui décrivent comment l’opération a été authentifiée

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| Propriété | Description |
|:--- |:---|
|**identity / type** | Type d’authentification qui a été utilisé pour effectuer la demande. Par exemple : `OAuth`, `SAS Key`, `Account Key` ou `Anonymous` |
|**identity / tokenHash**|Ce champ est réservé à un usage interne. |
|**authorization / action** | Action affectée à la demande. Par exemple : `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization / roleAssignmentId** | ID d’attribution de rôle. Par exemple : `4e2521b7-13be-4363-aeda-111111111111`.|
|**authorization / roleDefinitionId** | ID de définition de rôle. Par exemple : `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**principals / id** | ID du principal de sécurité. Par exemple : `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**principals / type** | Type du principal de sécurité. Par exemple : `ServicePrincipal`. |
|**requester / appID** | ID d’application Open Authorization (OAuth) utilisé comme demandeur. <br> Par exemple : `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**requester / audience** | Audience OAuth de la demande. Par exemple : `https://storage.azure.com`. |
|**requester / objectId** | ID d’objet OAuth du demandeur. Dans le cas de l’authentification Kerberos, représente l’identificateur d’objet de l’utilisateur authentifié Kerberos. Par exemple : `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**requester / tenantId** | ID de locataire OAuth de l’identité. Par exemple : `72f988bf-86f1-41af-91ab-222222222222`.|
|**requester / tokenIssuer** | Émetteur de jeton OAuth. Par exemple : `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**requester / upn** | Nom d’utilisateur principal (UPN) du demandeur. Par exemple : `someone@contoso.com`. |
|**requester / userName** | Ce champ est réservé à un usage interne.|

### <a name="fields-that-describe-the-service"></a>Champs qui décrivent le service

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| Propriété | Description |
|:--- |:---|
|**accountName** | Nom du compte de stockage. Par exemple : `mystorageaccount`.  |
|**requestUrl** | URL demandée. Par exemple : `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`.|
|**userAgentHeader** | Valeur d’**en-tête User-Agent**, entre guillemets. Par exemple : `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`.|
|**referrerHeader** | Valeur d’en-tête du **référent**. Par exemple : `http://contoso.com/about.html`.|
|**clientRequestId** | Valeur d’en-tête **x-ms-client-request-id** de la demande. Par exemple : `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`. |
|**etag** | Identificateur de l’ETag pour l’objet retourné, entre guillemets. Par exemple : `0x8D101F7E4B662C4`.  |
|**serverLatencyMs** | Durée totale, en millisecondes, pour exécuter l’opération demandée. Cette valeur n’inclut pas la latence réseau (le temps nécessaire à la lecture de la demande entrante et à l’envoi de la réponse au demandeur). Par exemple : `22`. |
|**serviceType** | Service associé à cette demande. Par exemple : `blob`, `table`, `files` ou `queue`. |
|**operationCount** | Nombre de chaque opération journalisée qui est impliquée dans la demande. Ce nombre commence par un index de `0`. Certaines demandes nécessitent plusieurs opérations, telles qu’une demande de copie d’un objet blob. La plupart des demandes effectuent une seule opération. Par exemple : `1`. |
|**requestHeaderSize** | Taille de l’en-tête de demande, en octets. Par exemple : `578`. <br>Si une demande n’aboutit pas, cette valeur peut être vide. |
|**requestBodySize** | Taille des paquets de demande, en octets, lus par le service de stockage. <br> Par exemple : `0`. <br>Si une demande n’aboutit pas, cette valeur peut être vide.  |
|**responseHeaderSize** | Taille de l’en-tête de réponse, en octets. Par exemple : `216`. <br>Si une demande n’aboutit pas, cette valeur peut être vide.  |
|**responseBodySize** | Taille des paquets de réponse écrits par le service de stockage, en octets. Si une demande n’aboutit pas, cette valeur peut être vide. Par exemple : `216`.  |
|**requestMd5** | Valeur de l’en-tête **Content-MD5** ou de l’en-tête **x-ms-content-md5** dans la demande. La valeur de hachage MD5 spécifiée dans ce champ représente le contenu dans la demande. Par exemple : `788815fd0198be0d275ad329cafd1830`. <br>Ce champ peut être vide.  |
|**serverMd5** | Valeur du hachage MD5 calculée par le service de stockage. Par exemple : `3228b3cf1069a5489b298446321f8521`. <br>Ce champ peut être vide.  |
|**lastModifiedTime** | Heure de dernière modification (LMT) de l’objet retourné.  Par exemple : `Tuesday, 09-Aug-11 21:13:26 GMT`. <br>Ce champ est vide pour les opérations qui peuvent retourner plusieurs objets. |
|**conditionsUsed** | Liste de paires clé-valeur séparées par des points-virgules qui représentent des conditions. Les conditions peuvent correspondre aux éléments suivants : <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> Par exemple : `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`. |
|**contentLengthHeader** | Valeur de l’en-tête Content-Length de la demande envoyée au service de stockage. Si la demande a réussi, cette valeur est égale à requestBodySize. Si une demande n’aboutit pas, cette valeur peut ne pas être égale à requestBodySize, ou être vide. |
|**tlsVersion** | Version TLS utilisée dans la connexion de la demande. Par exemple : `TLS 1.2`. |
|**smbTreeConnectID** | Identificateur SMB (Server Message Block) **treeConnectId** établi au moment de la connexion de l’arborescence. Par exemple : `0x3` |
|**smbPersistentHandleID** | ID de handle volatile issu d’une demande SMB2 CREATE qui survit aux reconnexions réseau.  Référencé dans [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 comme **SMB2_FILEID.Persistent**. Par exemple : `0x6003f` |
|**smbVolatileHandleID** | ID de handle volatile issu d’une demande SMB2 CREATE qui est recyclée sur les reconnexions réseau.  Référencé dans [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 comme **SMB2_FILEID.Volatile**. Par exemple : `0xFFFFFFFF00000065` |
|**smbMessageID** | **MessageId** relatif à la connexion. Par exemple : `0x3b165` |
|**smbCreditsConsumed** | Entrée ou sortie consommée par la demande, en unités de 64 000. Par exemple : `0x3` |
|**smbCommandDetail** | Plus d’informations sur cette demande spécifique plutôt que sur le type général de demande. Par exemple : `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | **FileId** associé au fichier ou au répertoire.  À peu près similaire à un identificateur NTFS FileId. Par exemple : `0x9223442405598953` |
|**smbSessionID** | Identificateur SMB2 **SessionId** établi au moment de la configuration de la session. Par exemple : `0x8530280128000049` |
|**smbCommandMajor  uint32** | Valeur dans **SMB2_HEADER.Command**. Actuellement, il s’agit d’un nombre compris entre 0 et 18 inclus. Par exemple : `0x6` |
|**smbCommandMinor** | Sous-classe de **SmbCommandMajor**, le cas échéant. Par exemple : `DirectoryCloseAndDelete` |

## <a name="see-also"></a>Voir aussi

- Consultez [Supervision du stockage Azure](monitor-storage.md) pour obtenir une description de la supervision du stockage Azure.
- Pour plus d’informations sur le monitoring des ressources Azure, voir [Monitoring des ressources Azure avec Azure Monitor](../../azure-monitor/insights/monitor-azure-resource.md).