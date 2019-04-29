---
title: Fichier Include
description: Fichier Include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/05/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: df987d1e13cb5330842fbab41dae96b24b581ddb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765569"
---
## <a name="prepare-your-repository"></a>Préparer votre dépôt

Pour obtenir des builds automatiques à partir du serveur de build d’Azure App Service Kudu, assurez-vous que la racine de votre référentiel comprend les fichiers nécessaires dans votre projet.

| Runtime | Fichiers du répertoire racine |
|-|-|
| ASP.NET (Windows uniquement) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux uniquement) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ avec un script de démarrage |
| Python (Windows uniquement) | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ sous _App\_Data/jobs/continuous_ (pour les WebJobs continus) ou _App\_Data/jobs/triggered_ (pour les WebJobs déclenchés). Pour plus d’informations, consultez [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Fonctions | Consultez [Déploiement continu pour Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#continuous-deployment-requirements). |

Pour personnaliser votre déploiement, vous pouvez inclure un fichier _.deployment_ dans la racine du dépôt. Pour plus d’informations, consultez [personnaliser déploiements](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) et [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Si vous développez dans Visual Studio, laissez [Visual Studio créer un dépôt pour vous](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Le projet est immédiatement prêt à être déployé à l’aide de Git.
>
>

