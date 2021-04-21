---
title: 'Démarrage rapide : Créer une application web HTML statique'
description: Déployez votre premier programme Hello World HTML dans Azure App Service en quelques minutes. Vous effectuez le déploiement avec Git, qui figure parmi les nombreuses façons de déployer vers App Service.
author: msangapu-msft
ms.assetid: 60495cc5-6963-4bf0-8174-52786d226c26
ms.topic: quickstart
ms.date: 08/23/2019
ms.author: msangapu
ms.custom: mvc, cli-validate, seodec18
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 603d8e642cd2e88beec6ae34094a2c6c43d179ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768899"
---
# <a name="create-a-static-html-web-app-in-azure"></a>Créer une application web HTML statique dans Azure

Ce guide de démarrage rapide vous montre comment déployer un site HTML + CSS de base sur <abbr title="Service HTTP pour l’hébergement d’applications web, d’API REST et d’applications back-end mobiles.">Azure App Service</abbr>. Vous allez effectuer ce démarrage rapide dans [Cloud Shell](../cloud-shell/overview.md), mais vous pouvez également exécuter ces commandes localement avec [Azure CLI](/cli/azure/install-azure-cli).

## <a name="1-prepare-your-environment"></a>1. Préparation de votre environnement

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Dans [Cloud Shell](../cloud-shell/overview.md), créez un répertoire de démarrage rapide, puis accédez à celui-ci.

```bash
mkdir quickstart

cd $HOME/quickstart
```

Exécutez ensuite la commande suivante pour cloner le référentiel de l’exemple d’application sur votre répertoire de démarrage rapide.

```bash
git clone https://github.com/Azure-Samples/html-docs-hello-world.git
```
<hr/>

## <a name="2-create-a-web-app"></a>2. Créer une application web

Passez au répertoire qui contient l’exemple de code et exécutez la commande `az webapp up`. **Remplacez** `<app-name>` par un nom global unique.

```bash
cd html-docs-hello-world

az webapp up --location westeurope --name <app_name> --html
```

<details>
<summary>Dépannage</summary>
<ul>
<li>Si la commande <code>az</code> n’est pas reconnue, vérifiez qu’Azure CLI est installé, comme décrit dans <a href="#1-prepare-your-environment">Préparer votre environnement</a>.</li>
<li>Remplacez <code>&lt;app-name&gt;</code> par un nom unique sur l’ensemble d’Azure (<em>les caractères valides sont <code>a-z</code>, <code>0-9</code> et <code>-</code></em>). Un bon modèle consiste à utiliser une combinaison du nom de votre société et d’un identificateur d’application.</li>
<li>L’argument <code>--sku F1</code> crée l’application web sur le niveau tarifaire Gratuit. Omettez cet argument pour utiliser un niveau Premium plus rapide, ce qui entraîne un coût horaire.</li>
<li>L’argument <code>--html</code> indique de traiter tout le contenu du dossier en tant que contenu statique et de désactiver l’automatisation de la génération.</li>
<li>Vous pouvez éventuellement inclure l’argument <code>--location &lt;location-name&gt;</code> où <code>&lt;location-name&gt;</code> est une région Azure disponible. Vous pouvez récupérer une liste de régions autorisées pour votre compte Azure en exécutant la commande <a href="/cli/azure/appservice#az_appservice_list_locations"><code>az account list-locations</code></a>.</li>
</ul>
</details>

La commande peut prendre quelques minutes. 

<details>
<summary>Que fait <code>az webapp up</code> ?</summary>
<p>La commande <code>az webapp up</code> exécute les actions suivantes :</p>
<ul>
<li>Créer un groupe de ressources par défaut</li>
<li>Créer un plan App Service par défaut.</li>
<li><a href="/cli/azure/webapp#az_webapp_create">Créez une application App Service</a> avec le nom spécifié.</li>
<li><a href="/azure/app-service/deploy-zip">Déployez les fichiers zip</a> à partir du répertoire de travail actif sur l’application web.</li>
<li>Lors de son exécution, elle fournit des messages sur la création de ressources, la journalisation et le déploiement du fichier ZIP.</li>
</ul>

Quand elle se termine, elle affiche des informations similaires à l’exemple suivant :

```output
{
  "app_url": "https://&lt;app_name&gt;.azurewebsites.net",
  "location": "westeurope",
  "name": "&lt;app_name&gt;",
  "os": "Windows",
  "resourcegroup": "appsvc_rg_Windows_westeurope",
  "serverfarm": "appsvc_asp_Windows_westeurope",
  "sku": "FREE",
  "src_path": "/home/&lt;username&gt;/quickstart/html-docs-hello-world ",
  &lt; JSON data removed for brevity. &gt;
}
```

</details>

Vous aurez besoin de la valeur de `resourceGroup` pour [nettoyer les ressources](#6-clean-up-resources) ultérieurement.

<hr/>

## <a name="3-browse-to-the-app"></a>3. Accéder à l’application

Dans un navigateur, accédez à l’URL de l’application : `http://<app_name>.azurewebsites.net`.

La page s’exécute comme une application web Azure App Service.

![Page d’accueil de l’exemple d’application](media/quickstart-html/hello-world-in-browser-az.png)

<hr/>

## <a name="4-update-and-redeploy-the-app"></a>4. Mise à jour et redéploiement de l’application

Dans Cloud Shell, **tapez** `nano index.html` pour ouvrir l’éditeur de texte nano. 

Dans la balise de titre `<h1>`, remplacez « Azure App Service - Sample Static HTML Site » par « Azure App Service ».

![Nano index.html](media/quickstart-html/nano-index-html.png)

**Enregistrez** vos modifications en utilisant la commande `^O`.

**Quittez**  nano en utilisant la commande `^X`.

Redéployez l’application avec la commande `az webapp up`.

```bash
az webapp up --html
```

Revenez à la fenêtre du navigateur que vous avez ouverte à l’étape **Accéder à l’application**.

**Actualisez** la page.

![Mise à jour de la page d’accueil de l’exemple d’application](media/quickstart-html/hello-azure-in-browser-az.png)

<hr/>

## <a name="5-manage-your-new-azure-app"></a>5. Gérer votre nouvelle application Azure

**Accédez** au [portail Azure](https://portal.azure.com). 

**Recherchez** et **sélectionnez** **App Services**.

![Sélectionner App Services dans le portail Azure](./media/quickstart-html/portal0.png)

**Sélectionnez** le nom de votre application Azure.

![Navigation au sein du portail pour accéder à l’application Azure](./media/quickstart-html/portal1.png)

Vous voyez apparaître la page Vue d’ensemble de votre application web. Ici, vous pouvez également des tâches de gestion de base (parcourir, arrêter, démarrer, redémarrer et supprimer des éléments, par exemple).

![Panneau App Service sur le portail Azure](./media/quickstart-html/portal2.png)

Le menu de gauche fournit différentes pages vous permettant de configurer votre application.

<hr/>

## <a name="6-clean-up-resources"></a>6. Nettoyer les ressources

Au cours des étapes précédentes, vous avez créé des ressources Azure au sein d’un groupe de ressources. Si vous ne pensez pas avoir besoin de ces ressources à l’avenir, supprimez le groupe de ressources en exécutant la commande suivante dans Cloud Shell. Souvenez-vous que le nom du groupe de ressources a été généré automatiquement pour vous à l’étape [créer une application web](#2-create-a-web-app).

```bash
az group delete --name appsvc_rg_Windows_westeurope
```

L’exécution de cette commande peut prendre une minute.

<hr/>

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Mapper un domaine personnalisé](app-service-web-tutorial-custom-domain-uiex.md)
