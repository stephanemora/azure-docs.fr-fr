---
title: 'Démarrage rapide : Créer une application C# ASP.NET Core'
description: Découvrez comment exécuter des applications web dans Azure App Service en déployant votre première application ASP.NET Core.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 11/23/2020
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18, contperf-fy21q1
zone_pivot_groups: app-service-platform-windows-linux
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2a789b4ca1261c79e8e6eb93a4ed44e7e8e9272e
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214233"
---
# <a name="quickstart-create-an-aspnet-core-web-app-in-azure"></a>Démarrage rapide : Créer une application web ASP.NET Core dans Azure

::: zone pivot="platform-windows"  

Dans ce guide de démarrage rapide, vous allez découvrir comment créer et déployer votre première application web ASP.NET Core sur <abbr title="Service HTTP pour l’hébergement d’applications web, d’API REST et d’applications back-end mobiles.">Azure App Service</abbr>. App Service prend en charge les applications .NET 5.0.

Une fois que vous avez terminé, vous disposez d’un <abbr title="Conteneur logique pour des ressources Azure associées que vous pouvez gérer en tant qu’unité.">resource group</abbr>, constitué d’un <abbr title="Plan qui spécifie l’emplacement, la taille et les fonctionnalités de la batterie de serveurs web qui héberge votre application.">Plan App Service</abbr> et d’une <abbr title="Représentation de votre application web, qui contient le code de votre application, les noms d’hôte DNS, les certificats et les ressources associées.">application App Service</abbr> avec un exemple d’application ASP.NET Core déployée.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

- **Procurez-vous un compte Azure** avec un <abbr title="Structure organisationnelle de base dans laquelle vous gérez les ressources dans Azure, généralement associée à une personne ou à un service au sein d’une organisation.">abonnement</abbr>. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet/).
- **Installez <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>** avec la charge de travail **Développement ASP.NET et web**.

<details>
<summary>Vous avez déjà Visual Studio 2019 ?</summary>
Si vous avez déjà installé Visual Studio 2019 :

<ul>
<li><strong>Installez les dernières mises à jour</strong> dans Visual Studio en sélectionnant <strong>Aide</strong> &gt; <strong>Rechercher les mises à jour</strong>. Les dernières mises à jour contiennent le kit SDK .NET 5.0.</li>
<li><strong>Ajoutez la charge de travail</strong> en sélectionnant <strong>Outils</strong> &gt; <strong>Obtenir des outils et des fonctionnalités</strong>.</li>
</ul>
</details>

<hr/> 

## <a name="2-create-an-aspnet-core-web-app"></a>2. Créez une application web ASP.NET Core

1. Ouvrez Visual Studio et sélectionnez **Créer un projet**.

1. Dans **Créer un projet**, sélectionnez **Application web ASP.NET Core** et vérifiez que **C#** est listé dans les langages de ce choix, puis sélectionnez **Suivant**.

1. Dans **Configurer votre nouveau projet**, nommez votre projet d’application web *myFirstAzureWebApp*, puis sélectionnez **Créer**.

   ![Configurer votre projet d’application web](./media/quickstart-dotnetcore/configure-web-app-project.png)

1. Pour une application .NET 5.0, sélectionnez **ASP.NET Core 5.0** dans la liste déroulante. Sinon, utilisez la valeur par défaut.

1. Vous pouvez déployer n’importe quel type d’application web ASP.NET Core dans Azure, mais pour ce guide de démarrage rapide, choisissez le modèle **Application web ASP.NET Core**. Vérifiez que l’option **Authentification** est définie sur **Aucune authentification**, et qu’aucune autre option n’est sélectionnée. Sélectionnez ensuite **Create** (Créer).

   ![Créer une application web ASP.NET Core](./media/quickstart-dotnetcore/create-aspnet-core-web-app-5.png) 
   
1. Dans le menu Visual Studio, sélectionnez **Déboguer** > **Démarrer sans débogage** pour exécuter votre application web localement.

   ![Application web exécutée en local](./media/quickstart-dotnetcore/web-app-running-locally.png)

<hr/> 

## <a name="3-publish-your-web-app"></a>3. Publier votre application web

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp**, puis sélectionnez **Publier**. 

1. Dans **Publier**, sélectionnez **Azure** et cliquez sur **Suivant**.

1. Vos options varient si vous êtes déjà connecté à Azure et si vous avez un compte Visual Studio lié à un compte Azure. Sélectionnez **Ajouter un compte** ou **Connexion** pour vous connecter à votre abonnement Azure. Si vous êtes déjà connecté, sélectionnez le compte souhaité.

   ![Connexion à Azure](./media/quickstart-dotnetcore/sign-in-azure-vs2019.png)

1. À droite de **Instances d’App Service**, cliquez sur **+** .

   ![Nouvelle application App Service](./media/quickstart-dotnetcore/publish-new-app-service.png)

1. Pour **Abonnement**, acceptez l’abonnement qui est listé ou sélectionnez-en un nouveau dans la liste déroulante.

1. Pour **Groupe de ressources**, sélectionnez **Nouveau**. Dans **Nouveau nom du groupe de ressources**, entrez *myResourceGroup*, puis sélectionnez **OK**. 

1. Pour **Plan d’hébergement**, sélectionnez **Nouveau**. 

1. Dans la boîte de dialogue **Plan d’hébergement : Créer nouveau**, entrez les valeurs spécifiées dans le tableau suivant :

   | Paramètre  | Valeur suggérée |
   | -------- | --------------- |
   | **Plan d’hébergement**  | *myFirstAzureWebAppPlan* |
   | **Lieu**      | *Europe Ouest* |
   | **Taille**          | *Gratuit* |
   
   ![Créer un plan d’hébergement](./media/quickstart-dotnetcore/create-new-hosting-plan-vs2019.png)

1. Dans **Nom**, entrez un nom d’application unique.

    <details>
        <summary>Quels caractères puis-je utiliser ?</summary>
        Les caractères valides sont a-z, A-Z, 0-9 et -. Vous pouvez accepter le nom unique généré automatiquement. L’URL de l’application web est http://<code>&lt;app-name&gt;.azurewebsites.net</code>, où <code>&lt;app-name&gt;</code> est le nom de votre application.
    </details>

1. Sélectionnez **Créer** pour créer les ressources Azure. 

   ![Créer les ressources d’application](./media/quickstart-dotnetcore/web-app-name-vs2019.png)

1. Attendez que l’Assistant ait terminé de créer les ressources Azure. Sélectionnez **Terminer** pour fermer l’Assistant.

1. Dans la page **Publier**, cliquez sur **Publier** pour déployer votre projet. 

    <details>
        <summary>Que fait Visual Studio ?</summary>
        Visual Studio génère, empaquète et publie l’application sur Azure, puis la démarre dans le navigateur par défaut.
    </details>

   ![Application web ASP.NET publiée en cours d’exécution dans Azure](./media/quickstart-dotnetcore/web-app-running-live.png)

<hr/> 

## <a name="4-update-the-app-and-redeploy"></a>4. Mise à jour de l’application et redéploiement

1. Dans l’**Explorateur de solutions**, sous votre projet, ouvrez **Pages** > **Index.cshtml**.

1. Remplacez l’intégralité de la balise `<div>` par le code suivant :

   ```html
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. Pour effectuer un redéploiement dans Azure, cliquez avec le bouton droit sur le projet **myFirstAzureWebApp** dans **l’Explorateur de solutions**, puis sélectionnez **Publier**.

1. Dans la page récapitulative intitulée **Publier**, sélectionnez **Publier**.

   <!-- ![Publish update to web app](./media/quickstart-dotnetcore/publish-update-to-web-app-vs2019.png) -->

    Une fois la publication terminée, Visual Studio lance un navigateur en accédant à l’URL de l’application web.

    ![Application web ASP.NET mise à jour en cours d’exécution dans Azure](./media/quickstart-dotnetcore/updated-web-app-running-live.png)

<hr/> 

## <a name="5-manage-the-azure-app"></a>5. Gérer l’application Azure

1. Accédez au [portail Azure](https://portal.azure.com), puis recherchez et sélectionnez **App Services**.

    ![Sélectionner App Services](./media/quickstart-dotnetcore/app-services.png)
    
1. Dans la page **App Services**, sélectionnez le nom de votre application web.

    :::image type="content" source="./media/quickstart-dotnetcore/select-app-service.png" alt-text="Capture d’écran de la page App Services avec un exemple d’application web sélectionnée.":::

1. La page **Vue d’ensemble** de votre application web contient des options de gestion de base, telles que parcourir, arrêter, démarrer, redémarrer et supprimer. Le menu de gauche fournit d’autres pages vous permettant de configurer votre application.

    ![App Service dans le portail Azure](./media/quickstart-dotnetcore/web-app-overview-page.png)
    
<hr/> 

## <a name="6-clean-up-resources"></a>6. Nettoyer les ressources

1. Dans le menu ou la **page d’accueil** du portail Azure, sélectionnez **Groupes de ressources**. Ensuite, dans la page **Groupes de ressources**, sélectionnez **myResourceGroup**.

1. Dans la page **myResourceGroup**, assurez-vous que les ressources répertoriées sont bien celles que vous souhaitez supprimer.

1. Sélectionnez **Supprimer le groupe de ressources**, tapez  **myResourceGroup** dans la zone de texte pour confirmer, puis sélectionnez **Supprimer**.

<hr/> 

## <a name="next-steps"></a>Étapes suivantes

Passez à l’article suivant pour savoir comment créer une application .NET Core et la connecter à une base de données SQL :

- [Build a .NET Core and SQL Database web app in Azure App Service](tutorial-dotnetcore-sqldb-app.md) (Créer une application web .NET Core et SQL Database dans Azure App Service)
- [Configurer une application ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end  

::: zone pivot="platform-linux"
Ce guide de démarrage rapide vous montre comment créer une application [.NET Core](/aspnet/core/) sur <abbr title="App Service sur Linux fournit un service d’hébergement web hautement scalable appliquant des mises à jour correctives automatiques à l’aide du système d’exploitation Linux.">App Service sur Linux</abbr>. Vous créez l’application à l’aide d’[Azure CLI](/cli/azure/get-started-with-azure-cli), puis vous utilisez Git pour déployer le code .NET Core sur l’application.

<hr/> 

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

- **Procurez-vous un compte Azure** avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/dotnet/).
- **Installez** la dernière version du <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">SDK .NET Core 3.1</a> ou du <a href="https://dotnet.microsoft.com/download/dotnet/5.0" target="_blank">SDK .NET 5.0</a>.
- **<a href="/cli/azure/install-azure-cli" target="_blank">Installez la dernière version d’Azure CLI</a>** .

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="2-create-the-app-locally"></a>2. Créer l’application en local

1. Exécutez `mkdir hellodotnetcore` pour créer le répertoire.

    ```bash
    mkdir hellodotnetcore
    ```

1. Exécutez `cd hellodotnetcore` pour changer de répertoire. 

    ```bash
    cd hellodotnetcore
    ```

1. Exécutez `dotnet new web` pour créer une application .NET Core.

    ```bash
    dotnet new web
    ```

<hr/> 

## <a name="3-run-the-app-locally"></a>3. Exécutez l’application localement.

1. Exécutez `dotnet run` pour voir comment elle se présente quand vous la déployez sur Azure.

    ```bash
    dotnet run
    ```
    
1. **Ouvrez un navigateur web** et accédez à l’application à l’adresse `http://localhost:5000`.

![Tester avec un navigateur](media/quickstart-dotnetcore/dotnet-browse-local.png)

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="4-sign-into-azure"></a>4. Connexion à Azure

Exécutez `az login` pour vous connecter à Azure.

```azurecli
az login
```

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="5-deploy-the-app"></a>5. Déployer l’application

1. **Exécutez** `az webapp up` dans votre dossier local. **Remplacez** <app-name> par un nom global unique.

    ```azurecli
    az webapp up --sku F1 --name <app-name> --os-type linux
    ```
    
    <details>
    <summary>Dépannage</summary>
    <ul>
    <li>Si la commande <code>az</code> n’est pas reconnue, vérifiez qu’Azure CLI est installé, comme décrit dans <a href="#1-prepare-your-environment">Préparer votre environnement</a>.</li>
    <li>Remplacez <code>&lt;app-name&gt;</code> par un nom unique sur l’ensemble d’Azure (<em>les caractères valides sont <code>a-z</code>, <code>0-9</code> et <code>-</code></em>). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.</li>
    <li>L’argument <code>--sku F1</code> crée l’application web sur le niveau tarifaire Gratuit. Omettez cet argument pour utiliser un niveau Premium plus rapide, ce qui entraîne un coût horaire.</li>
    <li>Vous pouvez éventuellement inclure l’argument <code>--location &lt;location-name&gt;</code> où <code>&lt;location-name&gt;</code> est une région Azure disponible. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande <a href="/cli/azure/appservice#az-appservice-list-locations"><code>az account list-locations</code></a>.</li>
    </ul>
    </details>
    
1. Patientez jusqu’à la fin de l'exécution de la commande. Cette opération peut prendre quelques minutes et se termine par « Vous pouvez lancer l’application sur http://&lt;nom_application&gt;.azurewebsites.net ».

    <details>
    <summary>Que fait <code>az webapp up</code> ?</summary>
    <p>La commande <code>az webapp up</code> exécute les actions suivantes :</p>
    <ul>
    <li>Créer un groupe de ressources par défaut</li>
    <li>Créer un plan App Service par défaut.</li>
    <li><a href="/cli/azure/webapp#az-webapp-create">Créez une application App Service</a> avec le nom spécifié.</li>
    <li><a href="/azure/app-service/deploy-zip">Déployez les fichiers zip</a> à partir du répertoire de travail actif sur l’application web.</li>
    <li>Lors de son exécution, elle fournit des messages sur la création de ressources, la journalisation et le déploiement du fichier ZIP.</li>
    </ul>
    </details>
    
# <a name="net-core-31"></a>[.NET Core 3.1](#tab/netcore31)

![Exemple de sortie de la commande az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-3.1.png)

# <a name="net-50"></a>[.NET 5.0](#tab/net50)

![Exemple de sortie de la commande az webapp up](./media/quickstart-dotnetcore/az-webapp-up-output-5.0.png)

---

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="6-browse-to-the-app"></a>6. Accéder à l’application

**Accédez à l’application déployée** en utilisant votre navigateur web.

```bash
http://<app_name>.azurewebsites.net
```

![Exemple d’application s’exécutant dans Azure](media/quickstart-dotnetcore/dotnet-browse-azure.png)

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="7-update-and-redeploy-the-code"></a>7. Mettre à jour et redéployer le code

1. **Ouvrez le fichier _Startup.cs_**  dans le répertoire local. 

1. **Apportez une petite modification** au texte de l’appel de la méthode `context.Response.WriteAsync`.

    ```csharp
    await context.Response.WriteAsync("Hello Azure!");
    ```
    
1. **Enregistrez vos modifications**.

1. **Exécutez** `az webapp up` pour redéployer :

    ```azurecli
    az webapp up --os-type linux
    ```
    
    <details>
    <summary>Que fait cette fois <code>az webapp up</code> ?</summary>
    La première fois que vous avez exécuté la commande, elle a enregistré le nom de l’application, le groupe de ressources et le plan App Service dans le fichier <i>.azure/config</i> de la racine du projet. Quand vous l’exécutez à nouveau à partir de la racine du projet, elle utilise les valeurs enregistrées dans <i>.azure/config</i>, détecte que les ressources App Service existent déjà et effectue à nouveau le déploiement du fichier Zip.
    </details>
    
1. Une fois le déploiement terminé, **cliquez sur Actualiser** dans la fenêtre du navigateur qui s’est ouverte auparavant.

    ![Mise à jour de l’exemple d’application s’exécutant dans Azure](media/quickstart-dotnetcore/dotnet-browse-azure-updated.png)
    
[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="8-manage-your-new-azure-app"></a>8. Gérer votre nouvelle application Azure

1. Accédez au <a href="https://portal.azure.com" target="_blank">portail Azure</a>.

1. Dans le menu de gauche, cliquez sur **App Services**, puis sur le nom de votre application Azure.

    :::image type="content" source="./media/quickstart-dotnetcore/portal-app-service-list-up.png" alt-text="Capture d’écran de la page App Services montrant un exemple d’application Azure sélectionnée.":::

1. La page Vue d’ensemble est l’endroit où vous effectuez des tâches de gestion de base, comme parcourir, arrêter, démarrer, redémarrer et supprimer. Le menu de gauche fournit différentes pages vous permettant de configurer votre application. 

    ![Page App Service du Portail Azure](media/quickstart-dotnetcore/portal-app-overview-up.png)
    
<hr/> 

## <a name="9-clean-up-resources"></a>9. Nettoyer les ressources

**Exécutez** `az group delete --name myResourceGroup` pour supprimer le groupe de ressources.

```azurecli-interactive
az group delete --name myResourceGroup
```

[Vous rencontrez des problèmes ? Faites-le nous savoir.](https://aka.ms/DotNetAppServiceLinuxQuickStart)

<hr/> 

## <a name="next-steps"></a>Étapes suivantes

- [Tutoriel : application ASP.NET Core avec SQL Database](tutorial-dotnetcore-sqldb-app.md)
- [Configurer une application ASP.NET Core](configure-language-dotnetcore.md)

::: zone-end
