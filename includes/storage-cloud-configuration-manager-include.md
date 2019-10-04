---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 11626dd95064cf972a845e5c37f930b9e49c57e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077039"
---
La [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) que vous avez référencée ci-dessus fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analyse les paramètres de configuration, que l’application cliente soit exécutée sur le Bureau, sur un appareil mobile, dans une machine virtuelle Azure ou dans un service cloud Azure.

Pour référencer le package CloudConfigurationManager, ajoutez la directive `using` suivante :

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Voici un exemple indiquant comment récupérer une chaîne de connexion d’un fichier de configuration :

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API comme la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.
