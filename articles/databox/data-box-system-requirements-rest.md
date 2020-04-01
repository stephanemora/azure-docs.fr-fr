---
title: Exigences relatives au stockage Blob Microsoft Azure Data Box | Microsoft Docs
description: Familiarisez-vous avec les versions des API, des kits de développement logiciel (SDK) et des bibliothèques clientes prises en charge pour le stockage Blob Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 71e0ebf7d7851ae65a6fba67a1695d755fd98bb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436492"
---
# <a name="azure-data-box-blob-storage-requirements"></a>Exigences relatives au stockage Blob Azure Data Box

Cet article liste les versions des API Azure, des bibliothèques de client Azure et des outils pris en charge avec le stockage Blob Data Box. Le stockage Blob Data Box fournit des fonctionnalités de gestion des objets blob avec une sémantique Azure cohérente. Cet article récapitule également les différences connues entre le stockage Blob Azure Data Box et les services de stockage Azure.

Nous vous recommandons de lire attentivement les informations suivantes avant de vous connecter au stockage Blob Data Box, puis d'y revenir par la suite si nécessaire.


## <a name="storage-differences"></a>différences entre les stockages

|     Fonctionnalité                                             |     Stockage Azure                                     |     Stockage Blob Data Box |
|---------------------------------------------------------|-------------------------------------------------------|---------------------------|
|    Présentation du stockage de fichiers                                   |    Partages de fichiers SMB sur le cloud pris en charge              |    Non pris en charge      |
|    Service Encryption pour les données au repos                  |    Chiffrement AES 256 bits                             |    Chiffrement AES 256 bits |
|    Type de compte de stockage                                 |    Comptes de stockage à usage général et comptes de stockage d’objets blob Azure    |    Comptes de stockage à usage général v1 uniquement|
|    Nom de l’objet blob                                            |    1 024 caractères (2 048 octets)                     |    880 caractères (1 760 octets)|
|    Taille maximale des objets blob de blocs                              |    4,75 To (100 Mo X 50 000 blocs)                   |    4,75 To (100 Mo x 50 000 blocs) pour Azure Data Box v 1.8 et versions ultérieures.|
|    Taille maximale des objets blob de pages                               |    8 To                                               |    1 To                   |
|    Taille de page d’un objet blob de pages                                  |    512 octets                                          |    4 Ko                   |

## <a name="supported-api-versions"></a>Versions d'API prises en charge

Les versions suivantes des API du service de stockage Azure sont prises en charge avec le stockage Blob Data Box :

Azure Data Box 1.8 et versions ultérieures

- [2017-11-09](/rest/api/storageservices/version-2017-11-09)
- [2017-07-29](/rest/api/storageservices/version-2017-07-29)
- [2017-04-17](/rest/api/storageservices/version-2017-04-17)
- [2016-05-31](/rest/api/storageservices/version-2016-05-31)
- [2015-12-11](/rest/api/storageservices/version-2015-12-11)
- [2015-07-08](/rest/api/storageservices/version-2015-07-08)
- [2015-04-05](/rest/api/storageservices/version-2015-04-05) |
## <a name="supported-azure-client-libraries"></a>Bibliothèques clientes Azure prises en charge

Des bibliothèques clientes spécifiques, de même que des exigences spécifiques en matière de suffixe de point de terminaison, s'appliquent au stockage Blob Data Box. Les points de terminaison du stockage Blob Data Box n’ont pas de parité complète avec la dernière version de l’API REST du stockage Blob Azure. Consultez la liste des [versions prises en charge pour Azure Data Box v 1.8 et versions ultérieures](#supported-api-versions). Par conséquent, pour les bibliothèques clientes de stockage, vous devez connaître la version compatible avec l’API REST.

### <a name="azure-data-box-18-onwards"></a>Azure Data Box 1.8 et versions ultérieures

| Bibliothèque cliente     |Versions prises en charge du stockage Blob Data Box     | Lien   |     Spécification du point de terminaison      |
|--------------------|--------------------------------------------|--------|---------------------------------|
|    .NET                |    9.2.0                                           |    Package NuGet :   https://www.nuget.org/packages/WindowsAzure.Storage/9.2.0    <br>Version de GitHub :   https://github.com/Azure/azure-storage-net/releases/tag/v9.2.0                                                                                                                                                                                               |    Fichier app.config                 |
|    Java                |    7.0.0                                           |    Package Maven :   https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage/6.1.0   <br>Version de GitHub :   https://github.com/Azure/azure-storage-java/releases/tag/v7.0.0                                                                                                                                                                              |    Configuration de la chaîne de connexion         |
|    Node.js             |    2.8.3                                           |    Lien NPM :   https://www.npmjs.com/package/azure-storage   (Exécutez : `npm install azure-storage@2.7.0` )   <br>Version de GitHub :   https://github.com/Azure/azure-storage-node/releases/tag/v2.8.3                                                                                                                                                                        |    Déclaration d’instance de service    |
|    C++                 |    5.2.0                                           |    Package NuGet :   https://www.nuget.org/packages/wastorage.v140/5.2.0   <br>Version de GitHub :   https://github.com/Azure/azure-storage-cpp/releases/tag/v5.2.0                                                                                                                                                                                                     |    Configuration de la chaîne de connexion         |
|    PHP                 |    1.2.0                                           |    Version de GitHub :<br>Courant : https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-common   <br>Objet blob : https://github.com/Azure/azure-storage-php/releases/tag/v1.2.0-blob      <br>Installer via Composer (pour en savoir plus, voir les détails ci-dessous.)                                                                                                             |    Configuration de la chaîne de connexion         |
|    Python              |    1.1.0                                           |    Version de GitHub :<br>Courant :   https://github.com/Azure/azure-storage-python/releases/tag/v1.0.0-common <br>Objet blob :   https://github.com/Azure/azure-storage-python/releases/tag/v1.1.0-blob                                                                                                                                                                          |    Déclaration d’instance de service    |
|    Ruby                |    1.0.1                                           |    Package RubyGems :<br>Courant :   https://rubygems.org/gems/azure-storage-common/versions/1.0.1   <br>Objet blob : https://rubygems.org/gems/azure-storage-blob/versions/1.0.1         <br>Version de GitHub :<br>Courant : https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-common   <br>Objet blob : https://github.com/Azure/azure-storage-ruby/releases/tag/v1.0.1-blob          |    Configuration de la chaîne de connexion         |



### <a name="install-php-client-via-composer---current"></a>Installer le client PHP via Composer - actuel

Pour installer via Composer : (prenez blob pour exemple).
1. Créez un fichier nommé composer.json à la racine du projet avec le code suivant :

    ```
    {
    "require": {
    "Microsoft/azure-storage-blob":"1.2.0"
    }
    ```

2. Téléchargez `composer.phar` à la racine du projet.

3. Exécutez : php composer.phar install.

### <a name="endpoint-declaration"></a>Déclaration de point de terminaison

Un point de terminaison de stockage Blob Azure Data Box comprend deux parties : le nom d'une région et le domaine Data Box. Dans le SDK du stockage Blob Data Box, le point de terminaison par défaut est `\<serial no. of the device>.microsoftdatabox.com`.  Pour plus d'informations sur le point de terminaison du service d'objets blob, accédez à [Se connecter via le stockage Blob Data Box](data-box-deploy-copy-data-via-rest.md).
 
## <a name="examples"></a>Exemples

### <a name="net"></a>.NET

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans le fichier `app.config` :

```
<add key="StorageConnectionString"
value="DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;
EndpointSuffix=<<serial no. of the device>.microsoftdatabox.com  />
```

### <a name="java"></a>Java

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=<serial no. of the device>.microsoftdatabox.com ";
```

### <a name="nodejs"></a>Node.js

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans l'instance de déclaration :

```
var blobSvc = azure.createBlobService('myaccount', 'mykey',
'myaccount.blob. <serial no. of the device>.microsoftdatabox.com ');
```

### <a name="c"></a>C++

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;
AccountName=your_storage_account;
AccountKey=your_storage_account_key;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com "));
```

### <a name="php"></a>PHP

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
$connectionString = 'BlobEndpoint=http://<storage account name>.blob.<serial no. of the device>.microsoftdatabox.com /;
AccountName=<storage account name>;AccountKey=<storage account key>'
```

### <a name="python"></a>Python

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans l'instance de déclaration :

```
block_blob_service = BlockBlobService(account_name='myaccount',
account_key='mykey',
endpoint_suffix=’<serial no. of the device>.microsoftdatabox.com’)
```

### <a name="ruby"></a>Ruby

Pour le stockage Blob Data Box, le suffixe du point de terminaison est spécifié dans la configuration de la chaîne de connexion :

```
set
AZURE_STORAGE_CONNECTION_STRING=DefaultEndpointsProtocol=https;
AccountName=myaccount;
AccountKey=mykey;
EndpointSuffix=<serial no. of the device>.microsoftdatabox.com
```

## <a name="next-steps"></a>Étapes suivantes

* [Déployer votre Azure Data Box](data-box-deploy-ordered.md)
