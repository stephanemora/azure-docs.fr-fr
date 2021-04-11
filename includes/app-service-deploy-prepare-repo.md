---
title: Fichier include
description: Fichier include
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 93bfb515c46413400ecd47105de378c7b677cecd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564954"
---
## <a name="prepare-your-repository"></a>Préparer votre dépôt

Pour obtenir des builds automatiques auprès du serveur de builds Azure App Service, vérifiez que la racine de votre référentiel contient les fichiers appropriés de votre projet.

| Runtime | Fichiers du répertoire racine |
|-|-|
| ASP.NET (Windows uniquement) | _*.sln_, _*.csproj_ ou _default.aspx_ |
| ASP.NET Core | _*.sln_ ou _*.csproj_ |
| PHP | _index.php_ |
| Ruby (Linux uniquement) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ ou _package.json_ avec un script de démarrage |
| Python | _\*.py_, _requirements.txt_ ou _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ ou _iisstart.htm_ |
| WebJobs | _\<job_name>/run.\<extension>_ sous _App\_Data/jobs/continuous_ pour les WebJobs continus ou _App\_Data/jobs/triggered_ pour les WebJobs déclenchés. Pour plus d’informations, consultez la [documentation Kudu relative aux WebJobs](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Fonctions | Consultez [Déploiement continu pour Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Pour personnaliser votre déploiement, vous pouvez inclure un fichier *.deployment* dans la racine du dépôt. Pour plus d’informations, consultez [Personnaliser les déploiements](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) et [Personnaliser un script de déploiement](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Si vous développez dans Visual Studio, laissez [Visual Studio créer un dépôt pour vous](/azure/devops/repos/git/creatingrepo?tabs=visual-studio). Le projet est immédiatement prêt à être déployé à l’aide de Git.
>

