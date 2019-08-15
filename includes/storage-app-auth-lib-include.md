---
title: Fichier Include
description: Fichier Include
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2019
ms.locfileid: "69011993"
---
La bibliothèque de client [Microsoft Azure App Authentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) pour .NET (préversion) simplifie le processus d’acquisition et de renouvellement d’un jeton à partir de votre code. La bibliothèque de client App Authentication gère l’authentification de manière automatique. Elle utilise les informations d’identification du développeur pour l’authentification au cours du développement local. Il est plus sûr d’utiliser les informations d’identification du développeur pendant le développement local, car vous n’avez pas besoin de créer des informations d’identification Azure AD ou de partager des informations d’identification entre les développeurs. Lorsque la solution est déployée ultérieurement vers Azure, la bibliothèque bascule automatiquement vers l’utilisation des informations d’identification de l’application.

Pour utiliser la bibliothèque App Authentication dans une application de stockage Azure, installez le dernier package en préversion à partir de [Nuget](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), ainsi que la dernière version de la [bibliothèque de client commune de stockage Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) et la [bibliothèque de client de stockage d’objets blob Azure pour .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Ajoutez les instructions **using** suivantes à votre code :

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
