---
title: Fichier Include
description: Fichier Include
services: functions
author: tdykstra
manager: cfowler
ms.service: functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 97c56b07833f7d93541bb0b3747889f5a50a8203
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34675273"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Version du kit SDK Stockage Azure dans Functions 1.x

Dans Functions 1.x, les déclencheurs et les liaisons de stockage utilisent la version 7.2.1 du kit SDK Stockage Azure (package NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Si vous référencez une version différente du kit SDK Stockage Azure et établissez une liaison avec un type du kit SDK Stockage Azure dans votre signature de fonction, le runtime de Functions peut signaler ne pas pouvoir établir de liaison avec ce type. La solution consiste à s’assurer que votre projet référence [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
