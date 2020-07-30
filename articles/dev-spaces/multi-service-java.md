---
title: 'Exécution de plusieurs services dépendants : Java et Visual Studio Code'
services: azure-dev-spaces
ms.date: 11/21/2018
ms.topic: tutorial
description: Ce tutoriel vous montre comment utiliser Azure Dev Spaces et Visual Studio Code pour déboguer une application Java multiservice sur Azure Kubernetes Service.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs, Helm, service Mesh, routage du service Mesh, kubectl, k8s
ms.custom: devx-track-java
ms.openlocfilehash: 95bcc0b25ba937b8db96f006adf80226dee99968
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374250"
---
# <a name="running-multiple-dependent-services-java-and-visual-studio-code-with-azure-dev-spaces"></a>Exécution de plusieurs services dépendants : Java et Visual Studio Code avec Azure Dev Spaces

Dans ce tutoriel, vous allez apprendre à développer des applications multiservices à l’aide d’Azure Dev Spaces, et découvrir certains des avantages de Dev Spaces.

## <a name="call-a-service-running-in-a-separate-container"></a>Appeler un service en cours d’exécution dans un conteneur distinct

Dans cette section, vous créez un deuxième service `mywebapi` et demander à `webfrontend` de l’appeler. Chaque service s’exécutera dans un conteneur distinct. Ensuite, vous allez effectuer le débogage dans les deux conteneurs.

![Plusieurs conteneurs](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Télécharger l’exemple de code pour *mywebapi*
Pour des questions de temps, nous allons télécharger un exemple de code à partir d’un référentiel GitHub. Accédez à https://github.com/Azure/dev-spaces et sélectionnez **Clone or Download** (Cloner ou Télécharger) pour télécharger depuis le référentiel GitHub. Le code de cette section se trouve dans `samples/java/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Exécuter *mywebapi*
1. Ouvrez le dossier `mywebapi` dans une *fenêtre VS Code distincte*.
1. Ouvrez la **Palette de commandes** (à partir du menu **Affichage | Palette de commandes**) et utilisez la saisie semi-automatique pour taper et sélectionnez cette commande : `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.
1. Appuyez sur F5, et attendez la création et le déploiement du service. Un message semblable à celui-ci s’affiche dans la console de débogage quand le service est prêt :

    ```cmd
    2019-03-11 17:02:35.935  INFO 216 --- [           main] com.ms.sample.mywebapi.Application       : Started Application in 8.164 seconds (JVM running for 9.272)
    ```

1. L’URL du point de terminaison doit ressembler à `http://localhost:<portnumber>`. **Conseil : La barre d’état de VS Code devient orange et affiche une URL sur laquelle vous pouvez cliquer.** Le conteneur a l’air de s’exécuter en local, mais en réalité, il s’exécute dans notre espace de développement dans Azure. L’adresse localhost est utilisée car `mywebapi` n’a pas défini de points de terminaison publics et est accessible uniquement à partir de l’instance Kubernetes. Pour des raisons pratiques et pour faciliter l’interaction avec le service privé à partir de votre ordinateur local, Azure Dev Spaces crée un tunnel SSH temporaire vers le conteneur en cours d’exécution dans Azure.
1. Lorsque `mywebapi` est prêt, ouvrez votre navigateur à l’adresse localhost.
1. Si toutes les étapes se sont déroulées correctement, vous pouvez voir une réponse du service `mywebapi`.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Effectuer une requête à partir de *webfrontend* sur *mywebapi*
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
1. Définissez un point d’arrêt dans la méthode `index()` du projet `mywebapi`, à la [ligne 19 de `Application.java`](https://github.com/Azure/dev-spaces/blob/master/samples/java/getting-started/mywebapi/src/main/java/com/ms/sample/mywebapi/Application.java#L19)
1. Dans le projet `webfrontend`, définissez un point d’arrêt juste avant l’envoi d’une requête GET à `mywebapi`, sur la ligne commençant par `try`.
1. Appuyez sur F5 dans le projet `webfrontend` (ou redémarrez le débogueur s’il est actuellement en cours d’exécution).
1. Appelez l’application web et parcourez le code dans les deux services.
1. Dans l’application web, la page About affichera un message concaténé par les deux services : « Hello from webfrontend and Hello from mywebapi. »

### <a name="well-done"></a>C’est terminé !
Vous disposez maintenant d’une application à plusieurs conteneurs où chaque conteneur peut être développé et déployé séparément.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [En savoir plus sur le développement en équipe dans Dev Spaces](team-development-java.md)
