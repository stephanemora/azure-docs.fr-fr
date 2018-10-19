---
title: Débogage d’applications dans un conteneur Docker local | Microsoft Docs
description: Découvrez comment modifier une application qui s’exécute dans un conteneur Docker local et actualiser le conteneur via la modification et l’actualisation ainsi que la définition de points d’arrêt pour le débogage
services: container-service
author: ghogen
manager: douge
ms.assetid: 480e3062-aae7-48ef-9701-e4f9ea041382
ms.service: multiple
ms.topic: article
ms.workload: multiple
ms.date: 09/11/2018
ms.author: ghogen
ms.openlocfilehash: 0f3f323cb4486c06f6f18de4c695efaf8dce4d99
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715718"
---
# <a name="debugging-apps-in-a-local-docker-container"></a>Débogage d’applications dans un conteneur Docker local
## <a name="overview"></a>Vue d’ensemble
Visual Studio 2017 fournit un moyen cohérent de développer sur un conteneur Docker et de valider votre application localement.
Vous n’avez pas besoin de redémarrer le conteneur chaque fois que vous modifiez le code.
Cet article illustre comment utiliser la fonctionnalité Modifier et actualiser pour démarrer une application web ASP.NET Core dans un conteneur Docker local, apporter les modifications nécessaires, puis actualiser le navigateur pour afficher ces modifications.
Cet article vous montre également comment définir des points d’arrêt pour le débogage.

## <a name="prerequisites"></a>Prérequis
Les outils suivants doivent être installés.

* [Visual Studio 2017](https://www.visualstudio.com/downloads/) avec installation de la charge de travail Développement web.

Pour exécuter des conteneurs de Docker localement, vous aurez besoin d’un client Docker local.
Vous pouvez utiliser la [Boîte à outils Docker](https://www.docker.com/products/docker-toolbox), qui nécessite la désactivation d’Hyper-V, ou [Docker pour Windows](https://www.docker.com/get-docker) qui utilise Hyper-V et nécessite Windows 10.

Si vous utilisez la Boîte à outils Docker, vous devrez [Configurer le client Docker](vs-azure-tools-docker-setup.md)

## <a name="1-create-a-web-app"></a>1. Créer une application web
[!INCLUDE [create-aspnet5-app](../includes/create-aspnet5-app.md)]

## <a name="2-edit-your-code-and-refresh"></a>2. Modifier votre code et actualiser
Pour effectuer une itération rapide des modifications, vous pouvez lancer votre application au sein d’un conteneur et continuer à apporter des modifications, en les observant comme vous le feriez avec IIS Express.

1. Définissez la configuration de solution sur `Debug`, puis appuyez sur **&lt;CTRL + F5 >** pour créer votre image de Docker et l’exécuter localement.

    Une fois l’image de conteneur créée et exécutée dans un conteneur Docker, Visual Studio lance l’application web dans votre navigateur par défaut.
    Si vous utilisez le navigateur Microsoft Edge ou rencontrez des erreurs, consultez la section [Résolution des problèmes](vs-azure-tools-docker-troubleshooting-docker-errors.md) .
2. Accédez à la page À propos, à laquelle nous allons apporter nos modifications.
3. Revenez à Visual Studio et ouvrez `Views\Home\About.cshtml`.
4. Ajoutez le contenu HTML suivant à la fin du fichier et enregistrez les modifications.

    ```
    <h1>Hello from a Docker Container!</h1>
    ```
5. Dans la fenêtre de sortie, lorsque le build .NET est terminé et que vous voyez ces lignes, revenez sur votre navigateur et actualisez la page À propos.

   ```
   Now listening on: http://*:80
   Application started. Press Ctrl+C to shut down
   ```

6. Vos modifications ont été appliquées !

## <a name="3-debug-with-breakpoints"></a>3. Déboguer à l’aide de points d’arrêt
Les modifications nécessitent souvent une inspection plus poussée, en exploitant les fonctionnalités de débogage de Visual Studio

1. Revenez à Visual Studio et ouvrez `About.cshtml.cs`
2. Remplacez le contenu de la méthode OnGet() par ce qui suit :

   ```cs
       Message = "Your application description page from within a Container";
   ```

3. Définissez un point d’arrêt à gauche de la ligne de code.
4. Appuyez sur **&lt;F5>** pour démarrer le débogage.
5. Accédez à la page À propos de pour atteindre votre point d’arrêt.
6. Basculez sur Visual Studio pour afficher le point d’arrêt, puis inspectez la valeur du message.

   ![][2]

## <a name="summary"></a>Résumé
Avec la prise en charge de Docker dans Visual Studio 2017, vous pouvez obtenir la productivité du travail local tout en conservant le réalisme de production du développement au sein d’un conteneur Docker.

## <a name="troubleshooting"></a>Résolution de problèmes
[Résolution des problèmes de développement avec Docker pour Visual Studio](vs-azure-tools-docker-troubleshooting-docker-errors.md)

## <a name="more-about-docker-with-visual-studio-windows-and-azure"></a>En savoir plus sur Docker avec Visual Studio, Windows et Azure
* [Développement de conteneur avec Visual Studio](/visualstudio/containers) : une page de hub de développement de conteneur
* [Intégration de Docker pour Azure Pipelines](http://aka.ms/dockertoolsforvsts) : créer et déployer des conteneurs Docker
* [Outils Docker pour Visual Studio Code](http://aka.ms/dockertoolsforvscode) : services linguistiques pour la modification de fichiers Docker, avec plus de scénarios E2E à venir
* [Informations sur le conteneur Windows](http://aka.ms/containers): informations sur Windows Server et Nano Server
* [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/) - [Documentation Azure Kubernetes Service](/azure/aks)

## <a name="various-docker-tools"></a>Divers outils Docker
[D’excellents outils Docker (blog de Steve Lasker)](https://blogs.msdn.microsoft.com/stevelasker/2016/03/25/some-great-docker-tools/)

## <a name="good-articles"></a>Articles intéressants
[Introduction aux microservices de NGINX](https://www.nginx.com/blog/introduction-to-microservices/)

## <a name="presentations"></a>Présentations
* [Steve Lasker : Visual Studio Live Las Vegas 2016 - E2E Docker](https://github.com/SteveLasker/Presentations/blob/master/VSLive2016/Vegas/)
* [Introduction à ASP.NET Core build 2016 - Démonstration Where You At](https://channel9.msdn.com/Events/Build/2016/B810)
* [Développement d’applications .NET dans des conteneurs, Channel 9](https://blogs.msdn.microsoft.com/stevelasker/2016/02/19/developing-asp-net-apps-in-docker-containers/)

[2]: ./media/vs-azure-tools-docker-edit-and-refresh/breakpoint.png
