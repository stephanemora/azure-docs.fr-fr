---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: a9d7f4f77d91abc88ea348e71a3d9c471b26a273
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/16/2021
ms.locfileid: "103621989"
---
L’émulateur prend en charge un unique compte fixe et une clé d’authentification connue pour l’authentification par clé partagée. Ce compte et cette clé sont les seules informations d’identification de la clé partagée dont l’utilisation est autorisée avec l’émulateur. Il s'agit de :

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> La clé d’authentification prise en charge par l’émulateur est destinée uniquement au test de la fonctionnalité du code d’authentification client. Elle n'offre aucune fonction de sécurité. Vous ne pouvez pas utiliser votre compte de stockage de production ni votre clé avec l’émulateur. Vous ne devez pas utiliser le compte de développement avec des données de production.
>
> L’émulateur ne prend en charge que la connexion via le protocole HTTP. Toutefois, HTTPS est le protocole recommandé pour l’accès aux ressources dans un compte de Stockage Azure de production.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Connexion au compte de l’émulateur à l’aide du raccourci

Le moyen le plus simple de vous connecter à l’émulateur à partir de votre application est de configurer une chaîne de connexion faisant référence au raccourci `UseDevelopmentStorage=true` dans le fichier de configuration de votre application. Ce raccourci est l’équivalent de la chaîne de connexion complète pour l’émulateur, qui spécifie le nom du compte, la clé de compte et les points de terminaison de l’émulateur pour chacun des services Stockage Azure :

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
```

L’extrait de code .NET suivant montre comment utiliser le raccourci à partir d’une méthode qui prend une chaîne de connexion. Par exemple, le constructeur [BlobContainerClient(String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) prend une chaîne de connexion.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Vérifiez que l’émulateur est en cours d’exécution avant d’appeler le code dans l’extrait de code.
