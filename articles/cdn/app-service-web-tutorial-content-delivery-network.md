---
title: 'Didacticiel : Ajouter Azure CDN à une application web Azure App Service | Microsoft Docs'
description: Dans ce didacticiel, vous allez apprendre à ajouter un réseau de distribution de contenu (CDN) à une application web Azure App Service pour mettre en cache et distribuer vos fichiers statiques à partir des serveurs situés à proximité de vos clients, partout dans le monde.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: d782f5bf30160d85d7d9ef7a00190551f9a9843a
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/14/2018
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Didacticiel : Ajouter Azure CDN à une application web Azure App Service

Ce didacticiel montre comment ajouter Azure CDN à une [application web dans Azure App Service](../app-service/app-service-web-overview.md). Le [réseau de distribution de contenu (CDN) Azure](cdn-overview.md) met en cache le contenu web statique à des emplacements stratégiques afin de fournir un débit maximal pour la distribution de contenu aux utilisateurs. Azure CDN réduit également la charge du serveur sur votre application web. 

Voici la page d’accueil de l’exemple de site HTML statique que vous allez utiliser :

![Page d’accueil de l’exemple d’application](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page.png)

Ce que vous allez apprendre :

> [!div class="checklist"]
> * Créer un point de terminaison CDN.
> * Actualiser les ressources mises en cache.
> * Utiliser des chaînes de requête pour contrôler les versions mises en cache.
> * Utiliser un domaine personnalisé pour le point de terminaison CDN.

## <a name="prerequisites"></a>Prérequis


Pour suivre ce didacticiel :

- [Installez Git](https://git-scm.com/)
- [Installation d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>Créer l’application web

Pour créer l’application web que vous allez utiliser, suivez les instructions de l’article [Créer une application web HTML statique dans Azure](../app-service/app-service-web-get-started-html.md) jusqu’à la fin de l’étape **Accéder à l’application**.

### <a name="have-a-custom-domain-ready"></a>Nécessité d’avoir un nom de domaine

Pour suivre l’étape relative au domaine personnalisé de ce didacticiel, vous devez posséder un domaine personnalisé et avoir accès à votre registre DNS pour votre fournisseur de domaine. Par exemple, pour ajouter des entrées DNS pour contoso.com et www.contoso.com, vous devez disposer d’un accès et pouvoir configurer les paramètres DNS du domaine racine contoso.com.

Si vous n’avez pas encore de nom de domaine, suivez le [didacticiel sur le domaine App Service](../app-service/custom-dns-web-site-buydomains-web-app.md) pour acheter un domaine à l’aide du portail Azure. 

## <a name="log-in-to-the-azure-portal"></a>Se connecter au portail Azure.

Ouvrez un navigateur et accédez au [portail Azure](https://portal.azure.com).

## <a name="create-a-cdn-profile-and-endpoint"></a>Création d’un profil CDN et d’un point de terminaison

Dans le volet de navigation gauche, sélectionnez **App Services**, puis sélectionnez l’application que vous avez créée à la rubrique [Créer une application web HTML statique dans Azure en 5 minutes](../app-service/app-service-web-get-started-html.md).

![Sélection de l’application App Service dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-select-app-services.png)

Dans la page **App Service**, dans la section **Paramètres**, sélectionnez **Mise en réseau > Configurer Azure CDN pour votre application**.

![Sélection de CDN dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-select-cdn.png)

Dans la page **Azure Content Delivery Network**, fournissez les paramètres du **Nouveau point de terminaison**, comme spécifié dans le tableau.

![Création d’un profil et d’un point de terminaison dans le portail](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint.png)

| Paramètre | Valeur suggérée | Description |
| ------- | --------------- | ----------- |
| **Profil CDN** | myCDNProfile | Sélectionnez **Créer** pour créer un profil CDN. Un profil CDN est une collection de points de terminaison CDN possédant le même niveau tarifaire. |
| **Niveau tarifaire** | Standard Akamai | Le [niveau tarifaire](cdn-features.md) spécifie le fournisseur et les fonctionnalités disponibles. Ce didacticiel utilise *Akamai standard*. |
| **Nom du point de terminaison CDN** | N’importe quel nom qui est unique dans le domaine azureedge.net | Vous accédez à vos ressources mises en cache au niveau du *\<Nom_Point_Terminaison>.azureedge.net* du domaine.

Sélectionnez **Créer**.

Azure crée le profil et le point de terminaison. Le nouveau point de terminaison s’affiche dans la liste **Points de terminaison**, et lorsqu’il est configuré, son état est **Exécution en cours**.

![Nouveau point de terminaison dans la liste](media/app-service-web-tutorial-content-delivery-network/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>Test du point de terminaison CDN

Si vous avez sélectionné le niveau tarifaire Verizon, il faut généralement compter environ 90 minutes pour la propagation du point de terminaison. Pour Akamai, la propagation prend quelques minutes.

L’application fournie à titre d’exemple comprend un fichier *index.html* et des dossiers *css*, *img* et *js* contenant d’autres ressources statiques. Les chemins d’accès de contenu pour tous ces fichiers sont identiques au point de terminaison CDN. Par exemple, les deux URL suivantes accèdent au fichier *bootstrap.css* dans le dossier *css* :

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

Dans un navigateur, accédez à l’URL suivante :

```
http://<endpointname>.azureedge.net/index.html
```

![Page d’accueil de l’exemple d’application traitée à partir du CDN](media/app-service-web-tutorial-content-delivery-network/sample-app-home-page-cdn.png)

 Vous consultez la page que vous avez exécutée précédemment dans une application web Azure. Azure CDN a récupéré les ressources de l’application web d’origine et les traite à partir du point de terminaison CDN.

Pour vous assurer que cette page est mise en cache dans le CDN, actualisez la page. Deux demandes pour la même ressource sont parfois nécessaires pour que le CDN mette en cache le contenu demandé.

Pour plus d’informations sur la création de profils et de points de terminaison Azure CDN, consultez [Prise en main d’Azure CDN](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>Vidage du CDN

Le CDN actualise régulièrement ses ressources à partir de l’application web d’origine en fonction de la configuration de la durée de vie (TTL). La valeur TTL par défaut est de sept jours.

Dans certains cas, vous devrez peut-être actualiser le CDN avant l’expiration de la durée de vie. C’est par exemple le cas lorsque vous déployez un contenu mis à jour dans l’application web. Pour déclencher une actualisation, videz manuellement les ressources du CDN. 

Dans cette section du didacticiel, vous déployez une modification dans l’application web et videz le CDN pour le forcer à actualiser son cache.

### <a name="deploy-a-change-to-the-web-app"></a>Déploiement d’une modification dans l’application web

Ouvrez le fichier *index.html* et ajoutez *- V2* au titre H1, comme illustré dans l’exemple suivant : 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Validez votre modification et déployez-la dans l’application web.

```bash
git commit -am "version 2"
git push azure master
```

Une fois le déploiement terminé, accédez à l’URL de l’application web pour constater la modification.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 dans le titre de l’application web](media/app-service-web-tutorial-content-delivery-network/v2-in-web-app-title.png)

Si vous ouvrez la page d’accueil à partir de l’URL du point de terminaison CDN, vous ne pourrez pas visualiser cette modification, car la version mise en cache dans le CDN n’a pas encore expiré. 

```
http://<endpointname>.azureedge.net/index.html
```

![Pas de V2 dans le titre du CDN](media/app-service-web-tutorial-content-delivery-network/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>Vidage du CDN dans le portail

Pour forcer le CDN à mettre à jour sa version mise en cache, videz le CDN.

Dans le portail de navigation gauche, sélectionnez **Groupes de ressources**, puis sélectionnez le groupe de ressources que vous avez créé pour votre application web (myResourceGroup).

![Sélection du groupe de ressources](media/app-service-web-tutorial-content-delivery-network/portal-select-group.png)

Dans la liste des ressources, sélectionnez votre point de terminaison CDN.

![Sélection du point de terminaison](media/app-service-web-tutorial-content-delivery-network/portal-select-endpoint.png)

En haut de la page **Point de terminaison**, sélectionnez **Vider**.

![Sélection de l’option Vider](media/app-service-web-tutorial-content-delivery-network/portal-select-purge.png)

Entrez les chemins d’accès au contenu que vous souhaitez vider. Vous pouvez transmettre un chemin d’accès complet pour vider un fichier individuel ou un segment de chemin d’accès pour vider et actualiser tout le contenu d’un dossier. Étant donné que vous avez modifié *index.html*, assurez-vous que ce fichier se trouve dans l’un des chemins d’accès.

En bas de la page, sélectionnez **Vider**.

![Vidage de la page](media/app-service-web-tutorial-content-delivery-network/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>Vérification de la mise à jour du CDN

Patientez jusqu’à la fin du traitement de la demande de vidage. Quelques minutes sont habituellement nécessaires. Pour afficher l’état actuel, sélectionnez l’icône représentant une cloche en haut de la page. 

![Notification de vidage](media/app-service-web-tutorial-content-delivery-network/portal-purge-notification.png)

Si vous accédez au fichier *index.html* à partir de l’URL du point de terminaison CDN, vous pourrez voir la valeur *V2* que vous avez ajoutée au titre sur la page d’accueil, ce qui indique que le cache du CDN a bien été actualisé.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 dans le titre du CDN](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title.png)

Pour plus d’informations, consultez [Purger un point de terminaison CDN Azure](../cdn/cdn-purge-endpoint.md). 

## <a name="use-query-strings-to-version-content"></a>Utiliser des chaînes de requête pour contrôler la version du contenu

Azure CDN offre les options de comportement de mise en cache suivantes :

* Ignorer les chaînes de requête
* Contourner la mise en cache pour les chaînes de requête
* Mettre en cache chaque URL unique 

La première option est celle par défaut, ce qui signifie qu’il n’existe qu’une seule version mise en cache d’une ressource, quelle que soit la chaîne de requête dans l’URL. 

Dans cette section du didacticiel, vous modifiez le comportement de mise en cache pour mettre en cache chaque URL unique.

### <a name="change-the-cache-behavior"></a>Modification du comportement de mise en cache

Dans la page **Point de terminaison CDN** du portail Azure, sélectionnez **Cache**.

Sélectionnez **Mettre en cache chaque URL unique** dans la liste déroulante **Comportement de mise en cache des chaînes de requête**.

Sélectionnez **Enregistrer**.

![Sélection du comportement de mise en cache de chaîne de requête](media/app-service-web-tutorial-content-delivery-network/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Vérification de la mise en cache séparée des URL uniques

Dans un navigateur, accédez à la page d’accueil au niveau du point de terminaison CDN, puis insérez une chaîne de requête : 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Azure CDN renvoie le contenu de l’application web actuelle, qui inclut *V2* dans le titre. 

Pour vous assurer que cette page est mise en cache dans le CDN, actualisez la page. 

Ouvrez *index.html*, changez *V2* en *V3*, puis déployez la modification. 

```bash
git commit -am "version 3"
git push azure master
```

Dans un navigateur, accédez à l’URL du point de terminaison CDN avec une nouvelle chaîne de requête, par exemple `q=2`. Azure CDN obtient le fichier *index.html* actuel et affiche *V3*. Toutefois, si vous naviguez vers le point de terminaison CDN avec la chaîne de requête `q=1`, c’est la valeur *V2* qui s’affiche.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 dans le titre dans le CDN, chaîne de requête 2](media/app-service-web-tutorial-content-delivery-network/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 dans le titre dans le CDN, chaîne de requête 1](media/app-service-web-tutorial-content-delivery-network/v2-in-cdn-title-qs1.png)

Cette sortie montre que chaque chaîne de requête est traitée différemment :

* Comme la chaîne de requête q=1 a été utilisée précédemment, le contenu mis en cache est retourné (V2).
* Comme la chaîne de requête q=2 est nouvelle, le contenu le plus récent de l’application web est récupéré et retourné (V3).

Pour plus d’informations, consultez [Contrôle du comportement de mise en cache du CDN Azure avec des chaînes de requête](../cdn/cdn-query-string.md).

## <a name="map-a-custom-domain-to-a-cdn-endpoint"></a>Mappage d’un domaine personnalisé à un point de terminaison CDN

Vous allez mapper votre domaine personnalisé à votre point de terminaison CDN en créant un enregistrement CNAME. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source à un domaine cible. Par exemple, vous pouvez mapper cdn.contoso.com ou static.contoso.com à contoso.azureedge.net.

Si vous n’avez pas de domaine personnalisé, suivez le [didacticiel sur le domaine App Service](../app-service/custom-dns-web-site-buydomains-web-app.md) pour acheter un domaine à l’aide du portail Azure. 

### <a name="find-the-hostname-to-use-with-the-cname"></a>Recherche du nom d’hôte à utiliser avec l’enregistrement CNAME

Dans la page **Point de terminaison** du portail Azure, vérifiez que **Vue d’ensemble** est sélectionné dans le volet de navigation gauche, puis cliquez sur le bouton **+ Domaine personnalisé** en haut de la page.

![Sélection de l’ajout d’un domaine personnalisé](media/app-service-web-tutorial-content-delivery-network/portal-select-add-domain.png)

Dans la page **Ajouter un domaine personnalisé**, vous visualisez le nom d’hôte du point de terminaison à utiliser pour la création d’un enregistrement CNAME. Le nom d’hôte est dérivé de votre URL de point de terminaison CDN : **&lt;Nom_Point_Terminaison>.azureedge.net**. 

![Ajout d’une page de domaine](media/app-service-web-tutorial-content-delivery-network/portal-add-domain.png)

### <a name="configure-the-cname-with-your-domain-registrar"></a>Configuration de l’enregistrement CNAME avec votre bureau d’enregistrement de domaines

Accédez au site web de votre bureau d'enregistrement de domaines et recherchez la section de la création d'enregistrements DNS. Par exemple, cette section peut être nommée **Nom de domaine**, **DNS** ou **Gestion des serveurs de noms**.

Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots CNAME, Alias ou Sous-domaines.

Créez un enregistrement CNAME qui mappe votre sous-domaine choisi (par exemple, **static** ou **cdn**) au **nom d’hôte du point de terminaison** affiché précédemment dans le portail. 

### <a name="enter-the-custom-domain-in-azure"></a>Saisie du domaine personnalisé dans Azure

Revenez à la page **Ajouter un domaine personnalisé**, puis entrez votre domaine personnalisé, dont le sous-domaine, dans la boîte de dialogue. Par exemple, entrez *cdn.contoso.com*.   
   
Azure vérifie que l’enregistrement CNAME existe pour le nom de domaine que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé est validé.

La propagation de l’enregistrement CNAME aux serveurs de noms sur Internet peut prendre du temps. Si votre domaine n’est pas validé immédiatement, patientez quelques minutes, puis réessayez.

### <a name="test-the-custom-domain"></a>Test du domaine personnalisé

Dans un navigateur, accédez au fichier *index.html* à l’aide de votre domaine personnalisé (par exemple, cdn.contoso.com/index.html) pour vérifier que le résultat est identique à celui que vous obtenez lorsque vous accédez directement à &lt;endpointname&gt;azureedge.net/index.html.

![Page d’accueil d’exemple d’application utilisant l’URL du domaine personnalisé](media/app-service-web-tutorial-content-delivery-network/home-page-custom-domain.png)

Pour plus d’informations, consultez [Mapper du contenu Azure CDN à un domaine personnalisé](../cdn/cdn-map-content-to-custom-domain.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Étapes suivantes

Vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un point de terminaison CDN.
> * Actualiser les ressources mises en cache.
> * Utiliser des chaînes de requête pour contrôler les versions mises en cache.
> * Utiliser un domaine personnalisé pour le point de terminaison CDN.

Découvrez comment optimiser les performances du réseau CDN dans les articles suivants :

> [!div class="nextstepaction"]
> [Tutoriel : Ajouter un domaine personnalisé à un point de terminaison Azure CDN](cdn-map-content-to-custom-domain.md)


