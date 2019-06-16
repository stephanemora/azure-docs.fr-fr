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
ms.openlocfilehash: f8733ef907b8f31ace7ea72f705ba1b37d1adece
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66132385"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Version du kit SDK Stockage Azure dans Functions 1.x

Dans Functions 1.x, les déclencheurs et les liaisons de stockage utilisent la version 7.2.1 du kit SDK Stockage Azure (package NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Si vous référencez une version différente du kit SDK Stockage Azure et établissez une liaison avec un type du kit SDK Stockage Azure dans votre signature de fonction, le runtime de Functions peut signaler ne pas pouvoir établir de liaison avec ce type. La solution consiste à s’assurer que votre projet référence [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
