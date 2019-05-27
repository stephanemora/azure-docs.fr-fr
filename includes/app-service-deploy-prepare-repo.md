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
ms.openlocfilehash: 7ec4028c319749b6a3da019e1d320d3937e9c4b2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/23/2019
ms.locfileid: "66133180"
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
| Python | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| Tâches web | _\<job_name>/run.\<extension>_ sous _App\_Data/jobs/continuous_ (pour les WebJobs continus) ou _App\_Data/jobs/triggered_ (pour les WebJobs déclenchés). Pour plus d’informations, consultez [Kudu WebJobs documentation](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Fonctions | Consultez [Déploiement continu pour Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Pour personnaliser votre déploiement, vous pouvez inclure un fichier _.deployment_ dans la racine du dépôt. Pour plus d’informations, consultez [personnaliser déploiements](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) et [script de déploiement personnalisé](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Si vous développez dans Visual Studio, laissez [Visual Studio créer un dépôt pour vous](/azure/devops/repos/git/creatingrepo?view=vsts&tabs=visual-studio). Le projet est immédiatement prêt à être déployé à l’aide de Git.
>
>

