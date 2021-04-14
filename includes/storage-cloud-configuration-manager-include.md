---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026643"
---
La [Bibliothèque Microsoft Azure Configuration Manager pour .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) que vous avez référencée ci-dessus fournit une classe pour l’analyse d’une chaîne de connexion à partir d’un fichier de configuration. La classe [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) analyse les paramètres de configuration. Elle analyse les paramètres des applications clientes qui s’exécutent sur le Bureau, sur un appareil mobile, sur une machine virtuelle Azure ou dans un service cloud Azure.

Pour référencer le package `CloudConfigurationManager`, ajoutez les directives `using` suivantes :

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

L’utilisation d’Azure Configuration Manager est facultative. Vous pouvez également utiliser une API, par exemple la [classe ConfigurationManager](/dotnet/api/system.configuration.configurationmanager)de .NET Framework.