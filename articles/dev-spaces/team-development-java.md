---
title: Développement en équipe avec Azure Dev Spaces à l’aide de Java et VS Code| Microsoft Docs
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.subservice: azds-kubernetes
author: stepro
ms.author: stephpr
ms.date: 08/01/2018
ms.topic: tutorial
description: Développement Kubernetes rapide avec des conteneurs et des microservices sur Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
manager: mmontwil
ms.openlocfilehash: 4019bc3eb1a0071abc330cb7fe6127eeca856cc0
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55476364"
---
# <a name="team-development-with-azure-dev-spaces"></a>Développement en équipe avec les espaces Azure Dev Spaces

Dans ce tutoriel, vous allez apprendre à utiliser plusieurs espaces de développement pour travailler simultanément dans différents environnements de développement, en conservant les travaux des espaces de développement distincts dans le même cluster.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous créez un deuxième service `mywebapi` et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous effectuerez le débogage dans les deux conteneurs.

![Plusieurs conteneurs](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Ce dernier sera prêt lorsque vous verrez la barre de débogage VS Code s’afficher.
1. L’URL du point de terminaison doit ressembler à http://localhost:\<portnumber\>. **Conseil : La barre d’état de VS Code affichera une URL sur laquelle vous pouvez cliquer.** Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans notre espace de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost.
1. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête de *webfrontend* à *mywebapi*
Nous allons maintenant écrire du code dans `webfrontend` qui envoie une requête à `mywebapi`.
1. Basculez vers la fenêtre VS Code pour `webfrontend`.
1. *Ajoutez* les instructions `import` suivantes après l’instruction `package` :

   ```java
   import java.io.*;
   import java.net.*;
   ```
1. *Remplacez* le code de la méthode greeting :

    ```java
    @RequestMapping(value = "/greeting", produces = "text/plain")
    public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
        URLConnection conn = new URL("http://mywebapi/").openConnection();
        conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
        {
            return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
        }
    }
    ```

L’exemple de code précédent transfère l’en-tête `azds-route-as` de la requête entrante à la requête sortante. Vous verrez ultérieurement de quelle façon cette fonction aide les équipes au niveau du développement collaboratif.

### <a name="debug-across-multiple-services"></a>Déboguer dans plusieurs services
1. À ce stade, `mywebapi` doit toujours être en cours d’exécution avec le débogueur joint. Si ce n’est pas le cas, appuyez sur F5 dans le projet `mywebapi`.
1. Définissez un point d’arrêt dans la méthode `index()` du projet `webapi`.
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi`, sur la ligne commençant par `try`.
1. Appuyez sur F5 dans le projet `webfrontend` (ou redémarrez le débogueur s’il est actuellement en cours d’exécution).
1. Appelez l’application web et parcourez le code dans les deux services.
1. Dans l’application web, la page About affichera un message concaténé par les deux services : « Hello from webfrontend and Hello from mywebapi. »

C’est terminé ! Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.

## <a name="learn-about-team-development"></a>En savoir plus sur le développement en équipe

[!INCLUDE [](../../includes/team-development-1.md)]

Découvrons-le en action. Accédez à la fenêtre VS Code pour `mywebapi` et modifiez le code de la méthode `String index()`, par exemple :

```java
@RequestMapping(value = "/", produces = "text/plain")
public String index() {
    return "Hello from mywebapi says something new";
}
```

[!INCLUDE [](../../includes/team-development-2.md)]

### <a name="well-done"></a>C’est terminé !
Vous êtes arrivé au terme du guide de démarrage ! Vous avez appris à effectuer les actions suivantes :

> [!div class="checklist"]
> * Configurer Azure Dev Spaces avec un cluster Kubernetes géré dans Azure.
> * Développer du code de façon itérative dans des conteneurs.
> * Développer indépendamment deux services distincts et utiliser la découverte des services DNS de Kubernetes pour appeler un autre service.
> * Développer et tester votre code de façon productive dans un environnement d’équipe.

Maintenant que vous avez exploré Azure Dev Spaces, [partagez votre espace de développement avec les membres de l’équipe](how-to/share-dev-spaces.md) et montrez-leur combien il est facile de collaborer.

## <a name="clean-up"></a>Nettoyer
Pour supprimer complètement une instance d’Azure Dev Spaces sur un cluster, dont tous les espaces de développement et les services en cours d’exécution qu’elle contient, utilisez la commande `az aks remove-dev-spaces`. N’oubliez pas que cette action est irréversible. Vous pouvez ajouter à nouveau la prise en charge d’Azure Dev Spaces sur le cluster, mais il sera comme si vous recommenciez. Vos anciens services et espaces ne sont pas restaurés.

L’exemple suivant répertorie les contrôleurs Azure Dev Spaces de votre abonnement actif, puis supprime le contrôleur Azure Dev Spaces qui est associé au cluster AKS « myaks » dans le groupe de ressources « myaks-rg ».

```cmd
    azds controller list
    az aks remove-dev-spaces --name myaks --resource-group myaks-rg
```
