---
title: Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ? | Microsoft Docs
description: Décrit ce qui s’est produit dans un projet WebJob Azure après vous être connecté à un compte de stockage à l’aide des services connectés de Visual Studio
services: storage
author: ghogen
manager: jillfra
ms.assetid: 36ae7ff7-c22c-47eb-b220-049d61618c74
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ms.openlocfilehash: c54214e0b919ddaa60403a1c986a144100143577
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/15/2019
ms.locfileid: "69510480"
---
# <a name="what-happened-to-my-webjob-project-visual-studio-azure-storage-connected-service"></a>Qu’est-il arrivé à mon projet de tâche web (service connecté Azure Storage de Visual Studio) ?
## <a name="references-added"></a>Références ajoutées
Le package NuGet Azure Storage a été ajouté ou mis à jour dans votre projet Visual Studio.  
Ce package ajoute les références .NET suivantes :

* **Microsoft.Data.Edm**
* **Microsoft.Data.OData**
* **Microsoft.Data.Services.Client**
* **Microsoft.WindowsAzure.ConfigurationManager**
* **Microsoft.WindowsAzure.Storage**
* **Newtonsoft.Json**
* **System.Data**
* **System.Spatial**

## <a name="connection-string-for-azure-storage-added"></a>Chaîne de connexion pour Azure Storage ajoutée
Dans le fichier App.config de votre projet, les entrées **AzureWebJobsStorage** et **AzureWebJobsDashboard** ont été mises à jour avec la chaîne de connexion et la clé du compte de stockage sélectionné.

Pour plus d’informations, consultez [Ressources de documentation relatives à Azure WebJobs](https://go.microsoft.com/fwlink/?linkid=390226).

