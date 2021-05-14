---
title: Déployer une application à partir de la source vers Azure Red Hat OpenShift
description: Découvrir la procédure de déploiement d’une application sur Azure Red Hat OpenShift à partir du code source à l’aide de la stratégie de génération S2I (source-image)
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, s2i, source-image
ms.openlocfilehash: 270e5d915e2a77cdb4377a616abc97a836a09710
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107558604"
---
# <a name="deploy-an-application-from-source-to-azure-red-hat-openshift"></a>Déployer une application à partir de la source vers Azure Red Hat OpenShift

Dans cet article, vous déployez une application sur un cluster Azure Red Hat OpenShift à partir du code source à l’aide d’un build source-image (S2I). Le processus du build [source-image (S2I)](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html#builds-strategy-s2i-build_understanding-image-builds) permet de générer des images de conteneur reproductibles à partir de code source. S2I génère des images prêtes à être exécutées en injectant du code source dans une image conteneur et en permettant au conteneur de préparer le code source pour l’exécution. Vous pouvez faire en sorte qu’OpenShift génère une application à partir de la source pour la déployer. Vous ne devez donc pas construire de conteneur manuellement à chaque modification. OpenShift peut ensuite créer et déployer automatiquement de nouvelles versions lorsqu’il est informé de modifications du code source.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

## <a name="create-a-project"></a>Créer un projet

Pour créer un projet appelé `demoproject`, exécutez la commande suivante :

```azurecli-interactive
oc new-project demoproject
```

La sortie doit ressembler à celle-ci :

```output
Now using project "demoproject" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

## <a name="launch-the-web-console"></a>Lancer la console web

Pour découvrir l’URL de la console web du cluster, exécutez la commande suivante :

```azurecli-interactive
 az aro show \
    --name <cluster name> \
    --resource-group <resource group> \
    --query "consoleProfile.url" -o tsv
```

L’URL doit ressembler à celle-ci.

```output
https://console-openshift-console.apps.wzy5hg7x.eastus.aroapp.io/
```

Lancez l’URL de la console dans un navigateur et connectez-vous à l’aide des informations d’identification `kubeadmin`.

:::image type="content" source="media/login.png" alt-text="Écran de connexion Azure Red Hat OpenShift":::

Basculez vers la perspective *Développeur* au lieu de la perspective *Administrateur* dans le menu latéral gauche, puis sélectionnez `demoproject` dans la liste des projets. Vous devez ensuite être sur la page *Topologie* du projet.

:::image type="content" source="media/s2i/project-topology.png" alt-text="Topologie de projet Azure Red Hat OpenShift":::

Dans la mesure où le projet est vide, aucune charge de travail ne doit être trouvée et les différentes options de déploiement d’une application vous seront présentées.

## <a name="deploying-using-the-web-console"></a>Déploiement à l’aide de la console web

À partir des options présentées pour le déploiement d’une application, sélectionnez *À partir de Git*. Vous êtes redirigé vers la page *Importer à partir de Git*. Utilisez `https://github.com/sclorg/django-ex.git` comme **URL du référentiel Git**. L’exemple d’application web est implémenté à l’aide du langage de programmation Python.

:::image type="content" source="media/s2i/from-git.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git":::

> [!NOTE]
> OpenShift détecte qu’il s’agit d’un projet Python et sélectionne l’image de générateur appropriée.

Faites défiler jusqu’à *Options avancées* et vérifiez que l’option **Créer un itinéraire vers l’application** est cochée. Cette action permet de créer un itinéraire OpenShift, un moyen d’exposer un service en lui donnant un nom d’hôte accessible de l’extérieur.

:::image type="content" source="media/s2i/from-git-route.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Configuration d’itinéraire":::

Lorsque vous êtes prêt, en bas de la page, cliquez sur **Créer**. Cela vous permet de créer des ressources pour gérer la création et le déploiement de l’application. Vous êtes ensuite redirigé vers la vue d’ensemble de la topologie pour le projet.

:::image type="content" source="media/s2i/demo-app-topology.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Topologie":::

La vue d’ensemble de la topologie fournit une représentation visuelle de l’application que vous avez déployée. Cette vue vous permet de consulter la structure globale de l’application.

Vous pouvez cliquer sur l’icône Git pour vous connecter au référentiel Git à partir duquel le code source de l’application a été créé. L’icône affichée en bas à gauche montre l’état du build de l’application. Cliquez sur cette icône pour accéder à la section des détails du build. Si l’application a exposé des itinéraires, vous pouvez cliquer sur l’icône en haut à droite pour ouvrir l’URL de l’itinéraire de l’application qui a été créée.

Pendant que l’application effectue un scale up ou down, démarre les lancements et recrée des pods, la représentation de l’application dans la vue de la topologie est animée pour vous offrir une vue en temps réel de ce qu’il se passe.

Cliquez sur l’icône d’application pour afficher plus de détails, comme indiqué ci-dessous.

:::image type="content" source="media/s2i/demo-app-details.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Détails":::

## <a name="viewing-the-builder-logs"></a>Affichage des journaux du générateur

Une fois le build démarré, cliquez sur le lien *Voir les journaux* affiché dans le panneau *Ressources*.

:::image type="content" source="media/s2i/demo-app-build-logs.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Générer des journaux":::

Cela vous permettra de surveiller la progression du build lors de son exécution. L’image du générateur, Python dans ce cas, injecte le code source de l’application dans l’image finale avant de l’envoyer dans le registre de l’image interne OpenShift. La génération est terminée lorsque vous voyez un message final indiquant que l’envoi (push) a réussi.

## <a name="accessing-the-application"></a>Accès à l’application

Une fois la génération de l’image d’application terminée, celle-ci est déployée.

Cliquez sur *Topologie* dans la barre de menus de gauche pour revenir à la vue de la topologie du projet. Lorsque vous avez créé l’application à l’aide de la console web, un *itinéraire* a été créé automatiquement pour l’application et il est exposé en dehors du cluster. L’URL qui peut permettre d’accéder à l’application à partir d’un navigateur web était visible sous l’onglet *Ressources* de l’application que vous avez consultée précédemment.

Dans la vue de la topologie, vous pouvez accéder à l’URL de l’application déployée en cliquant sur l’icône en haut à droite de l’anneau. Une fois le déploiement terminé, cliquez sur l’icône pour voir l’application que vous avez déployée.

:::image type="content" source="media/s2i/demo-app-browse.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Parcourir l’application":::

## <a name="deploying-using-the-command-line"></a>Déploiement avec la ligne de commande

Vous avez appris à déployer une application à l’aide de la console web, ce qui permet désormais de déployer la même application web, mais cette fois-ci à l’aide de l'outil en ligne de commande `oc`.

Exécutez la commande suivante pour supprimer le projet et recommencer :

```azurecli-interactive
oc delete project demoproject
```

Vous devez obtenir un message de confirmation indiquant que `demoproject` a été supprimé.

```output
project.project.openshift.io "demoproject" deleted
```

Pour recréer `demoproject`, exécutez :

```azurecli-interactive
oc new-project demoproject
```

Dans le projet, créez une application à partir de la source sur GitHub en spécifiant le générateur S2I pour la dernière version de Python fournie.

```azurecli-interactive
oc new-app python:latest~https://github.com/sclorg/django-ex.git
```

Cette valeur doit afficher une sortie semblable à celle-ci :

```output
--> Found image 8ec6f0d (4 weeks old) in image stream "openshift/python" under tag "latest" for "python:latest"

    Python 3.8
    ----------
   [...]

    Tags: builder, python, python38, python-38, rh-python38

    * A source build using source code from https://github.com/sclorg/django-ex.git will be created
      * The resulting image will be pushed to image stream tag "django-ex:latest"
      * Use 'oc start-build' to trigger a new build
    * This image will be deployed in deployment config "django-ex"
    * Port 8080/tcp will be load balanced by service "django-ex"
      * Other containers can access this service through the hostname "django-ex"

--> Creating resources ...
    imagestream.image.openshift.io "django-ex" created
    buildconfig.build.openshift.io "django-ex" created
    deploymentconfig.apps.openshift.io "django-ex" created
    service "django-ex" created
--> Success
    Build scheduled, use 'oc logs -f bc/django-ex' to track its progress.
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/django-ex'
    Run 'oc status' to view your app.
```

OpenShift va utiliser le nom du référentiel Git comme nom de l’application. Passez en revue l’état de la création et du déploiement en exécutant :

```azurecli-interactive
oc status
```

Une fois la création et le déploiement terminés, vous devez voir une sortie semblable à la suivante.

```output
In project demoproject on server https://api.wzy5hg7x.eastus.aroapp.io:6443

svc/django-ex - 172.30.200.50:8080
  dc/django-ex deploys istag/django-ex:latest <-
    bc/django-ex source builds https://github.com/sclorg/django-ex.git on openshift/python:latest
    deployment #1 deployed about a minute ago - 1 pod


2 infos identified, use 'oc status --suggest' to see details.
```

Pour exposer l’application en dehors du cluster OpenShift, vous devez créer un itinéraire en exécutant :

```azurecli-interactive
oc expose service/django-ex
```

Vous devez obtenir une confirmation.

```output
route.route.openshift.io/django-ex exposed
```

Récupérez l’URL en exécutant :

```azurecli-interactive
oc get route django-ex
```

Vous devez récupérer le nom d’hôte affecté à l’itinéraire que vous pouvez utiliser pour parcourir l’application déployée.

```output
NAME        HOST/PORT                                              PATH   SERVICES    PORT       TERMINATION   WILDCARD
django-ex   django-ex-demoproject.apps.wzy5hg7x.eastus.aroapp.io          django-ex   8080-tcp                 None
```

## <a name="triggering-a-new-binary-build"></a>Déclenchement d’un nouveau build binaire

Au fur et à mesure que vous utilisez l’application, vous souhaiterez probablement apporter des modifications et les voir déployées. Vous pouvez facilement configurer un webhook qui déclenche une nouvelle création et un déploiement avec chaque validation de code. Toutefois, ce n’est peut-être pas souhaitable, car vous aimeriez parfois voir les modifications sans devoir envoyer (push) chaque modification de code au référentiel.

Dans les cas où vous effectuez rapidement une itération sur les modifications, vous pouvez utiliser ce que l’on appelle un build binaire. Pour illustrer ce scénario, clonez le référentiel Git pour l’application en local en exécutant :

```azurecli-interactive
git clone https://github.com/sclorg/django-ex.git
```

Cette opération crée un sous-répertoire `django-ex` contenant le code source de l’application :

```output
Cloning into 'django-ex'...
remote: Enumerating objects: 980, done.
remote: Total 980 (delta 0), reused 0 (delta 0), pack-reused 980
Receiving objects: 100% (980/980), 276.23 KiB | 4.85 MiB/s, done.
Resolving deltas: 100% (434/434), done.
```

Basculez vers le sous-répertoire :

```azurecli-interactive
cd django-ex
```

Ouvrez l’éditeur Azure Cloud Shell intégré :

```azurecli-interactive
code welcome/templates/welcome/index.html
```

Faites défiler vers le bas et modifiez la ligne qui indique `Welcome to your Django application on OpenShift` en `Welcome to Azure Red Hat OpenShift`. Enregistrez le fichier et fermez l’éditeur via le menu `...` en haut à droite.

:::image type="content" source="media/s2i/cloudshell-editor.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Modifier l’application dans l’éditeur Azure Cloud Shell":::

Démarrez un nouveau build en exécutant la commande :

```azurecli-interactive
oc start-build django-ex --from-dir=. --wait
```

Lorsque vous transmettez l'indicateur `--from-dir=.`, la ligne de commande OpenShift charge le code source à partir du répertoire spécifié, puis lance les processus de création et de déploiement. Vous devez obtenir une sortie similaire à la suivante et, après quelques minutes, le build doit être terminé.

```output
Uploading directory "." as binary input for the build ...
.
Uploading finished
build.build.openshift.io/django-ex-2 started
```

Si vous actualisez le navigateur avec l’application, vous devez voir le titre mis à jour.

:::image type="content" source="media/s2i/demo-app-browse-updated.png" alt-text="Projet Azure Red Hat OpenShift provenant de Git - Parcourir l’application mise à jour":::

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé avec l’application, vous pouvez exécuter la commande suivante pour supprimer le projet :

```azurecli-interactive
oc delete project demoproject
```

Vous pouvez également supprimer le cluster en suivant les instructions figurant dans [Tutoriel : Supprimer un cluster Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à :
> [!div class="checklist"]
>
> * Créer un projet
> * Déployer une application à partir de code source à l’aide de la console web
> * Déployer une application à partir de code source à l’aide de la ligne de commande OpenShift
> * Déclencher un build binaire à l’aide de la ligne de commande OpenShift

En savoir plus sur les procédures de création et de déploiement d’applications à l’aide de [stratégies de génération](https://docs.openshift.com/container-platform/4.6/builds/understanding-image-builds.html) source-image ou autres.
