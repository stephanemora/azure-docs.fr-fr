---
title: Didacticiel - Déboguer une application web de maillage Azure Service Fabric | Microsoft Docs
description: Dans ce didacticiel, vous allez déboguer une application de maillage Azure Service Fabric s’exécutant dans votre cluster local.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e39e5861c0606c43a869fe02a7de2dc9b6f489ea
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125651"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Didacticiel : Déboguer une application web de maillage Service Fabric

Deuxième d’une série, ce didacticiel vous montre comment déboguer une application web de maillage Azure Service Fabric dans votre cluster de développement local.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

> [!div class="checklist"]
> * Que se passe-t-il lorsque vous créez une application de maillage Azure Service Fabric
> * Comment définir un point d’arrêt pour observer un appel de service à service

Cette série de tutoriels vous montre comment effectuer les opérations suivantes :
> [!div class="checklist"]
> * [Créer une application de maillage Service Fabric](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Déboguer l’application localement
> * [Publier l’application dans Azure](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Prérequis

Avant de commencer ce tutoriel :

* Si vous n’avez pas d’abonnement Azure, vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

* Assurez-vous que vous avez [configuré votre environnement de développement](service-fabric-mesh-howto-setup-developer-environment-sdk.md) qui inclut l’installation du runtime Service Fabric, du Kit de développement logiciel (SDK), de Docker et de Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Télécharger l’exemple d’application de tâches

Si vous n’avez pas créer l’exemple d’application de tâches dans la [première partie de cette série de didacticiels](service-fabric-mesh-tutorial-create-dotnetcore.md), vous pouvez le télécharger. Dans une fenêtre Commande, exécutez la commande suivante pour cloner le référentiel de l’exemple d’application sur votre ordinateur local.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

L’application se trouve dans le répertoire `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Générer et déboguer l’application dans votre cluster local

Une image Docker est automatiquement générée et déployée dans votre cluster local dès que votre projet est chargé. Ce processus peut prendre un certain temps. Pour suivre la progression dans le volet **Sortie** de Visual Studio, sélectionnez **Outils Service Fabric** dans le menu déroulant **Afficher la sortie à partir de :** du volet Sortie.

Appuyez sur **F5** pour compiler et exécuter votre service localement. Lorsque le projet est exécuté et débogué localement, Visual Studio va :

* Vérifier que Docker pour Windows est en cours d’exécution et configuré pour utiliser Windows comme système d’exploitation du conteneur.
* Télécharger toute image de base Docker manquante. Cette opération peut prendre un certain temps.
* Générer (ou régénérer) l’image Docker utilisée pour héberger votre projet de code.
* Déployer et exécuter le conteneur dans le cluster de développement Service Fabric local.
* Exécuter vos services et accéder à tous les points d’arrêt que vous avez définis.

Une fois que le déploiement local est terminé et que Visual Studio exécute votre application, une fenêtre de navigateur s’ouvre sur un exemple de page web par défaut.

**Conseils de débogage**

* Si vous obtenez l’erreur **No Service Fabric local cluster is running** (Aucun cluster Service Fabric local n’est en cours d’exécution), assurez-vous que Service Local Cluster Manager (SLCM) est en cours d’exécution, cliquez sur l’icône SLCM dans la barre des tâches, puis cliquez sur **Démarrer le cluster local**. Une fois le cluster démarré, revenez à Visual Studio et appuyez sur la touche **F5**.
* Si vous obtenez une erreur **404** lorsque l’application démarre, cela signifie probablement que vos variables d’environnement dans le fichier **service.yaml** ne sont pas correctes. Assurez-vous que les valeurs `AppName`, `ApiHostPort` et `ServiceName` sont correctement définies conformément aux instructions de l’article [Définition des variables d’environnement](#set-environment-variables).
* Si vous obtenez des erreurs de build dans le fichier **service.yaml**, assurez-vous que des espaces, et non des tabulations, sont utilisés pour mettre en retrait les lignes.

### <a name="debug-in-visual-studio"></a>Déboguer dans Visual Studio

Pour déboguer une application de maillage Service Fabric dans Visual Studio, vous utilisez un cluster de développement Service Fabric local. Pour voir comment des éléments de tâche sont récupérés à partir du service back-end, effectuez un débogage dans la méthode OnGet().
1. Dans le projet **WebFrontEnd**, ouvrez **Pages** > **Index.cshtml** > **Index.cshtml.cs** et définissez un point d’arrêt dans la méthode **Get** (ligne 17).
2. Dans le projet **ToDoService**, ouvrez **TodoController.cs** et définissez un point d’arrêt dans la méthode **OnGet** (ligne 15).
3. Accédez à votre navigateur et actualisez la page. Vous avez atteint le point d’arrêt dans la méthode `OnGet()` du service web frontal. Vous pouvez examiner la variable `backendUrl` pour voir comment les variables d’environnement que vous avez définies dans le fichier **service.yaml** forment l’URL utilisée pour contacter le service back-end.
4. Survolez (F10) l’appel `client.GetAsync(backendUrl).GetAwaiter().GetResult())` pour atteindre le point d'arrêt `Get()` du contrôleur. Dans cette méthode, vous pouvez voir comment la liste des éléments de tâche est récupérée à partir de la liste en mémoire.
5. Lorsque vous avez terminé, arrêtez le débogage de votre projet dans Visual Studio en appuyant sur **Maj+F5**.
 
## <a name="next-steps"></a>Étapes suivantes

Cette partie du didacticiel vous a permis de répondre aux questions suivantes :

> [!div class="checklist"]
> * Que se passe-t-il lorsque vous créez une application de maillage Azure Service Fabric
> * Comment définir un point d’arrêt pour observer un appel de service à service

Passez au tutoriel suivant :
> [!div class="nextstepaction"]
> [Publier une application web de maillage Service Fabric](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)