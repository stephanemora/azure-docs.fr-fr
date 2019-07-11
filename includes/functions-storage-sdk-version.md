---
title: Fichier Include
description: Fichier Include
services: functions
author: ggailey777
manager: cfowler
ms.service: azure-functions
ms.topic: include
ms.date: 05/17/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: c2fff707dcaafac69efcad3dbf33446a7b797396
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608183"
---
### <a name="azure-storage-sdk-version-in-functions-1x"></a>Version du kit SDK Stockage Azure dans Functions 1.x

Dans Functions 1.x, les déclencheurs et les liaisons de stockage utilisent la version 7.2.1 du kit SDK Stockage Azure (package NuGet [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1)). Si vous référencez une version différente du kit SDK Stockage Azure et établissez une liaison avec un type du kit SDK Stockage Azure dans votre signature de fonction, le runtime de Functions peut signaler ne pas pouvoir établir de liaison avec ce type. La solution consiste à s’assurer que votre projet référence [WindowsAzure.Storage 7.2.1](https://www.nuget.org/packages/WindowsAzure.Storage/7.2.1).
