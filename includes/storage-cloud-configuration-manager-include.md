---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165380"
---
La [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) que vous avez référencée ci-dessus fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration. La [classe CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) analyse les paramètres de configuration, que l’application cliente soit exécutée sur le Bureau, sur un appareil mobile, dans une machine virtuelle Azure ou dans un service cloud Azure.

Pour référencer le package CloudConfigurationManager, ajoutez la directive `using` suivante :

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Voici un exemple indiquant comment récupérer une chaîne de connexion d’un fichier de configuration :

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API comme la [classe ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) de .NET Framework.

