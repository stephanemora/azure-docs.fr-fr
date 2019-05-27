---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66114623"
---
La [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) que vous avez référencée ci-dessus fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analyse les paramètres de configuration, que l’application cliente soit exécutée sur le Bureau, sur un appareil mobile, dans une machine virtuelle Azure ou dans un service cloud Azure.

Pour référencer le package CloudConfigurationManager, ajoutez la directive `using` suivante :

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

Voici un exemple indiquant comment récupérer une chaîne de connexion d’un fichier de configuration :

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API comme la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

