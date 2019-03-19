---
title: Qu’est-il arrivé à mon projet ASP.NET ? | Microsoft Docs
description: Décrit ce qui se passe après que l’ajout de stockage Azure à un projet ASP.NET à l’aide de Visual Studio des services connectés
services: storage
author: ghogen
manager: douge
ms.assetid: e1fe1b6d-4e3d-476d-8b2f-f7ade050515e
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: e0e065b23581f297ee4ae2288a6e437da461a19f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998355"
---
# <a name="what-happened-to-my-aspnet-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il arrivé à mon projet ASP.NET (service connecté Azure Storage de Visual Studio) ?
## <a name="references-added"></a>Références ajoutées
Le package NuGet Azure Storage a été ajouté à votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.Configuration**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier web.config de votre projet, un élément a été créé avec la clé et la chaîne de connexion du compte de stockage sélectionné.

Pour plus d'informations, consultez la page [ASP.NET](https://www.asp.net).

