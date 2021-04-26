---
title: Déployer une application sur Azure Red Hat OpenShift à l’aide d’OpenShift serverless
description: Apprenez à déployer une application sur Azure Red Hat OpenShift à l’aide d’OpenShift serverless
author: sabbour
ms.author: asabbour
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 4/5/2021
keywords: aro, openshift, red hat, serverless
ms.openlocfilehash: 6db87e752745e3df919c93b2ffc8144e5886b319
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532811"
---
# <a name="deploy-an-application-to-azure-red-hat-openshift-using-openshift-serverless"></a>Déployer une application sur Azure Red Hat OpenShift à l’aide d’OpenShift serverless

Dans cet article, vous déployez une application sur un cluster Azure Red Hat OpenShift à l’aide d’[OpenShift serverless](https://www.openshift.com/learn/topics/serverless). OpenShift serverless aide les développeurs à déployer et à exécuter des applications qui connaissent ensuite un scale-up ou sont mises à l’échelle à zéro à la demande, ce qui supprime la consommation des ressources lorsqu’elles ne sont pas utilisées.

Le code d’application peut être empaqueté dans un conteneur avec les runtimes appropriés et la fonctionnalité serverless démarre les conteneurs d’application lorsqu’ils sont déclenchés par un événement. Les applications peuvent être déclenchées par diverses sources d’événements, telles que les événements de vos propres applications, les services cloud de plusieurs fournisseurs, les systèmes SaaS (Software as a Service) et d’autres services.

La gestion de tous les aspects du déploiement d’un conteneur en mode serverless est directement intégrée à l’interface OpenShift. Les développeurs peuvent identifier visuellement les événements qui conduisent au lancement de leurs applications conteneurisées, avec plusieurs manières de modifier les paramètres d’événement. Les applications OpenShift serverless peuvent être intégrées à d’autres services OpenShift, tels que les pipelines OpenShift, le maillage de service et la surveillance, ce qui offre une expérience complète de développement et de déploiement d’applications serverless.

## <a name="before-you-begin"></a>Avant de commencer

[!INCLUDE [aro-howto-beforeyoubegin](includes/aro-howto-before-you-begin.md)]

### <a name="install-the-knative-command-line-tool-kn"></a>Installer l’outil en ligne de commande Knative (kn)

Vous pouvez télécharger la dernière version de l’interface CLI qui convient pour votre ordinateur à partir de <https://github.com/knative/client/releases/>

Si vous exécutez les commandes sur Azure Cloud Shell, téléchargez la dernière version de l’interface CLI Knative pour Linux.

```azurecli-interactive
cd ~
wget https://github.com/knative/client/releases/download/v0.22.0/kn-linux-amd64

mkdir knative
chmod +x kn-linux-amd64
mv kn-linux-amd64 knative/kn
echo 'export PATH=$PATH:~/knative' >> ~/.bashrc && source ~/.bashrc
```

### <a name="launch-the-web-console"></a>Lancer la console web

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

## <a name="install-the-openshift-serverless-operator"></a>Installer l’opérateur OpenShift serverless

Une fois que vous êtes connecté à la console web OpenShift, vérifiez que vous êtes dans la perspective *Administrateur*. Ouvrez le *hub d’opérateur* et recherchez l’opérateur **OpenShift serverless**, puis sélectionnez-le.

![Rechercher l’opérateur OpenShift serverless](media/serverless/serverless-operatorhub.png)

Ensuite, ouvrez la page d’installation de l’opérateur en cliquant sur **Installer**.

![Cliquer sur Installer pour installer l’opérateur](media/serverless/serverless-clickinstall.png)

Choisissez le *canal de mise à jour* approprié à votre version de cluster Azure Red Hat OpenShift et installez l’opérateur dans l'espace de noms `openshift-serverless`. Faites défiler et cliquez sur **Installer**.

![Page d'installation de l'opérateur](media/serverless/serverless-installpage.png)

Après quelques minutes, la page d’état doit indiquer que l’opérateur est installé et prêt à l’emploi. Cliquez sur le bouton **Afficher l’opérateur** pour continuer.

![L’opérateur est installé et prêt à l’emploi](media/serverless/serverless-installed.png)

## <a name="install-knative-serving"></a>Installer Knative Serving

Si vous utilisez Knative en amont, il est possible d’exécuter n’importe quel conteneur en mode serverless sur OpenShift serverless. Knative étend Kubernetes pour fournir un ensemble de composants pour le déploiement, l’exécution et la gestion d’applications modernes à l’aide de la méthodologie serverless.

### <a name="create-an-instance-of-the-knative-serving"></a>Créer une instance de Knative Serving

Pour basculer vers l'espace de noms `knative-serving`, cliquez sur la liste déroulante de projet en haut à gauche, puis, sous *API fournies*, cliquez sur **Créer une instance** dans la carte *Knative Serving*.

![Cliquer pour créer une instance Service Knative](media/serverless/serverless-createknativeserving.png)

Conservez les valeurs par défaut et faites défiler la page *Créer Knative Serving* pour cliquer sur le bouton **Créer**.

![Conserver les valeurs par défaut et cliquer sur créer](media/serverless/serverless-createknativeserving2.png)

Attendez que la colonne *État* indique **Prêt**. OpenShift serverless doit alors être installé et vous êtes prêt à créer un projet OpenShift serverless.

![Knative Serving prêt](media/serverless/serverless-createknativeserving3.png)

## <a name="create-a-serverless-project"></a>Créer un projet serverless

Pour créer un projet appelé `demoserverless`, exécutez la commande suivante :

```azurecli-interactive
oc new-project demoserverless
```

La sortie doit ressembler à celle-ci :

```output
Now using project "demoserverless" on server "https://api.wzy5hg7x.eastus.aroapp.io:6443".

You can add applications to this project with the 'new-app' command. For example, try:

    oc new-app django-psql-example

to build a new example application in Python. Or use kubectl to deploy a simple Kubernetes application:

    kubectl create deployment hello-node --image=gcr.io/hello-minikube-zero-install/hello-node
```

Basculez vers la perspective *Développeur* au lieu de la perspective *Administrateur* dans le menu latéral gauche, puis sélectionnez `demoserverless` dans la liste des projets. Vous devez ensuite être sur la page *Topologie* du projet.

![Topologie de projet Azure Red Hat OpenShift](media/serverless/serverless-topology.png)

## <a name="deploying-using-the-web-console"></a>Déploiement à l’aide de la console web

À partir des options présentées pour le déploiement d’une application, sélectionnez *À partir de Git*. Vous êtes redirigé vers la page *Importer à partir de Git*. Utilisez `https://github.com/sclorg/django-ex.git` comme **URL du référentiel Git**. L’exemple d’application web est implémenté à l’aide du langage de programmation Python.

![Projet Azure Red Hat OpenShift provenant de Git](media/serverless/serverless-from-git.png)

> [!NOTE]
> OpenShift détecte qu’il s’agit d’un projet Python et sélectionne l’image de générateur appropriée.

Faites défiler jusqu’à *Ressources* et vérifiez que **Service Knative** est sélectionné comme type de ressource à générer. Cette action crée un service Knative, un type de déploiement qui permet la mise à l’échelle OpenShift serverless à zéro lorsqu’il est inactif.

![Topologie de projet Azure Red Hat OpenShift - Knative](media/serverless/serverless-knative.png)


Lorsque vous êtes prêt, en bas de la page, cliquez sur **Créer**. Cela vous permet de créer des ressources pour gérer la création et le déploiement de l’application. Vous êtes ensuite redirigé vers la vue d’ensemble de la topologie pour le projet.

La vue d’ensemble de la topologie fournit une représentation visuelle de l’application que vous avez déployée. Cette vue vous permet de consulter la structure globale de l’application.

Attendez la fin de la génération. Cette opération peut prendre quelques minutes. Une fois la génération terminée, une coche verte s’affiche dans l’angle inférieur gauche du service.

![Topologie de projet Azure Red Hat OpenShift - Génération réussie](media/serverless/serverless-ready.png)

## <a name="see-your-application-scale"></a>Voir la mise à l’échelle de votre application

Dans la liste *Options d’affichage* en haut de la vue Topologie, cliquez sur *Nombre de pods*. Attendez que le nombre de pods soit mis à l’échelle sur zéro pod. Cette mise à l’échelle peut prendre quelques minutes.

![Topologie de projet Azure Red Hat OpenShift - Mise à l’échelle à zéro](media/serverless/serverless-scaledtozero.png)

Cliquez sur l’icône *Ouvrir l’URL* dans l’angle supérieur droit du panneau Service Knative. L’application s’ouvre dans un nouvel onglet. Fermez le nouvel onglet de navigateur et revenez à la vue Topologie. Dans la vue Topologie, vous pouvez voir que votre application est mise à l’échelle jusqu’à un pod pour répondre à votre demande. Après quelques minutes, votre application est redimensionnée à zéro pod.

![Topologie de projet Azure Red Hat OpenShift - Mise à l’échelle à un](media/serverless/serverless-scaledtoone.png)

## <a name="forcing-a-new-revision-and-setting-traffic-distribution"></a>Forcer une nouvelle révision et définir la distribution du trafic

À chaque mise à jour de la configuration d’un service, une nouvelle révision du service est créée. L’itinéraire du service pointe par défaut l’ensemble du trafic vers la dernière révision prête. Vous pouvez modifier ce comportement en définissant la révision qui obtient une partie du trafic. Les services Knative autorisent le mappage du trafic, ce qui signifie que les révisions d’un service peuvent être mappées à une partie allouée du trafic. Le mappage du trafic fournit également une option permettant de créer des URL uniques pour des révisions particulières.

Dans la *Topologie*, cliquez sur la révision à l’intérieur de votre service pour en afficher les détails. Les badges sous l’anneau du pod et en haut du panneau de détails doivent être `(REV)`. Dans le volet latéral, sous l’onglet *Ressources*, faites défiler l’écran et cliquez sur la configuration associée à votre service.

![Topologie de projet Azure Red Hat OpenShift - Révision](media/serverless/serverless-revdetails.png)

Pour forcer une mise à jour de la configuration, basculez vers l’onglet *YAML* et faites défiler pour remplacer la valeur de `timeoutSeconds` par `301`. Cliquez sur **Enregistrer** pour enregistrer la configuration mise à jour. Dans un scénario réel, une telle mise à jour de configuration peut également être déclenchée par la mise à jour de la balise d’image conteneur.

![Forcer une nouvelle révision en mettant la configuration à jour](media/serverless/serverless-confupdate.png)

Revenez à la vue *Topologie*. Vous verrez qu’une nouvelle révision a été déployée. Cliquez sur le service se terminant par le badge `(KSVC)`, puis cliquez sur le bouton **Définir la distribution du trafic**. Vous devez maintenant être en mesure de répartir le trafic entre les différentes révisions du service.

![Définir la distribution du trafic](media/serverless/serverless-trafficdist.png)

La vue *Topologie* vous affiche maintenant la distribution du trafic entre les deux révisions.

![Passer en revue la distribution du trafic](media/serverless/serverless-trafficdist2.png)

## <a name="using-the-knative-command-line-tool-kn"></a>Utilisation de l’outil en ligne de commande Knative (kn)

Dans les étapes précédentes, vous avez utilisé la console web OpenShift pour créer et déployer une application sur OpenShift serverless. OpenShift serverless exécutant Knative de façon sous-jacente, vous pouvez également utiliser l’outil en ligne de commande Knative (kn) pour créer des services Knative.

> [!NOTE]
> Si vous n’avez pas encore installé l’interface CLI `kn`, suivez les étapes de la section des conditions préalables de cet article. Assurez-vous également que vous êtes connecté à l’aide de l’outil en ligne de commande OpenShift `oc`.

Nous allons utiliser une image de conteneur déjà générée à l’adresse `quay.io/rhdevelopers/knative-tutorial-greeter`.

### <a name="deploy-a-service"></a>Déployer un service

Pour déployer le service, exécutez la commande suivante :

```azurecli-interactive
kn service create greeter \
--image quay.io/rhdevelopers/knative-tutorial-greeter:quarkus \
--namespace demoserverless \
--revision-name greeter-v1
```

Le résultat ressemble à ce qui suit.

```output
Creating service 'greeter' in namespace 'demoserverless':

  0.044s The Route is still working to reflect the latest desired specification.
  0.083s ...
  0.114s Configuration "greeter" is waiting for a Revision to become ready.
 10.420s ...
 10.489s Ingress has not yet been reconciled.
 10.582s Waiting for load balancer to be ready
 10.763s Ready to serve.

Service 'greeter' created to latest revision 'greeter-v1' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Vous pouvez récupérer la liste des itinéraires dans le projet en exécutant :

```azurecli-interactive
kn route list
```

Vous obtenez la liste des itinéraires dans l’espace de noms. Ouvrez l’URL dans un navigateur pour afficher le service déployé.

```output
NAME      URL                                                            READY
greeter   http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io   True
```

### <a name="deploy-a-new-version-of-the-service"></a>Déployer une nouvelle version du service

Déployez une nouvelle version de l’application en exécutant la commande ci-dessous, en transmettant la balise d’image `:latest` et une variable d’environnement `MESSAGE_PREFIX` :

```azurecli-interactive
kn service update greeter \
 --image quay.io/rhdevelopers/knative-tutorial-greeter:latest \
 --namespace demoserverless \
 --env MESSAGE_PREFIX=GreeterV2 \
 --revision-name greeter-v2
```

Vous recevez une confirmation qu’une nouvelle révision `greeter-v2` a été déployée.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  5.029s Traffic is not yet migrated to the latest revision.
  5.086s Ingress has not yet been reconciled.
  5.190s Waiting for load balancer to be ready
  5.332s Ready to serve.

Service 'greeter' updated to latest revision 'greeter-v2' is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Pour afficher la liste de toutes les révisions et de leur distribution du trafic, exécutez la commande suivante :

```azurecli-interactive
kn revision list
```

Vous obtenez une liste similaire à celle ci-dessous. Notez que la nouvelle révision obtient 100 % du trafic.

```output
NAME            SERVICE   TRAFFIC   TAGS   GENERATION   AGE     CONDITIONS   READY   REASON
greeter-v2      greeter   100%             2            90s     3 OK / 4     True
greeter-v1      greeter                    1            5m32s   3 OK / 4     True
```

### <a name="bluegreen-and-canary-deployments"></a>Déploiements bleus/verts et Canary

Par défaut, lorsqu’une nouvelle révision est déployée, elle obtient 100 % du trafic. Supposons que vous souhaitiez implémenter une stratégie de déploiement bleu/vert dans laquelle vous pouvez rapidement revenir à l’ancienne version de l’application. Knative facilite cette tâche.

Mettez à jour le service pour créer trois balises de trafic tout en envoyant 100 % du trafic à la balise

- **actuelle** : pointe vers la version déployée actuelle
- **préc** : pointe vers la version précédente
- **la plus récente** : pointe toujours vers la dernière version

```azurecli-interactive
kn service update greeter \
   --tag greeter-v2=current \
   --tag greeter-v1=prev \
   --tag @latest=latest
```

Vous recevez une confirmation similaire à celle ci-dessous.

```output
Updating Service 'greeter' in namespace 'demoserverless':

  0.037s Ingress has not yet been reconciled.
  0.121s Waiting for load balancer to be ready
  0.287s Ready to serve.

Service 'greeter' with latest revision 'greeter-v2' (unchanged) is available at URL:
http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
```

Répertoriez les itinéraires à l’aide de la commande ci-dessous :

```azurecli-interactive
kn route describe greeter
```

Vous obtenez une sortie qui indique les URL de chacune des balises, ainsi que la distribution du trafic.

```output
Name:       greeter
Namespace:  demoserverless
Age:        10m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
  100%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Supposons que vous souhaitiez revenir rapidement à la version précédente, vous pouvez mettre à jour la distribution du trafic pour envoyer 100 % du trafic à la balise précédente :

```azurecli-interactive
kn service update greeter --traffic current=0 --traffic prev=100
```

Revérifiez en répertoriant les itinéraires à l’aide de la commande ci-dessous :

```azurecli-interactive
kn route describe greeter
```

Vous obtenez une sortie montrant que 100 % de la distribution du trafic sont envoyés vers la version précédente.

```output
Name:       greeter
Namespace:  demoserverless
Age:        19m
URL:        http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
Service:    greeter

Traffic Targets:
    0%  @latest (greeter-v2) #latest
        URL:  http://latest-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
  100%  greeter-v1 #prev
        URL:  http://prev-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io
    0%  greeter-v2 #current
        URL:  http://current-greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io

[..]
```

Jouez avec la distribution du trafic tout en actualisant l’itinéraire principal (`http://greeter-demoserverless.apps.wzy5hg7x.eastus.aroapp.io` dans le cas présent) dans votre navigateur.

## <a name="clean-up-resources"></a>Nettoyer les ressources

Lorsque vous avez terminé avec l’application, vous pouvez exécuter la commande suivante pour supprimer le projet :

```azurecli-interactive
oc delete project demoserverless
```

Vous pouvez également supprimer le cluster en suivant les instructions figurant dans [Tutoriel : Supprimer un cluster Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md).

## <a name="next-steps"></a>Étapes suivantes

Dans ce guide, vous avez appris à :
> [!div class="checklist"]
>
> * Installer l’opérateur OpenShift serverless et Knative Serving
> * Déployer un projet serverless à l’aide de la console web
> * Déployer un projet serverless à l’aide de l’interface CLI Knative (kn)
> * Configurer des déploiements bleus/verts et des déploiements Canary à l’aide de l’interface CLI Knative (kn)

En savoir plus sur les procédures de création et de déploiement d’applications serverless basées sur les événements sur Azure Red Hat OpenShift à l’aide d’[OpenShift serverless](https://www.openshift.com/learn/topics/serverless), suivez la documentation [Prise en main d’OpenShift Serverless](https://docs.openshift.com/container-platform/4.6/serverless/serverless-getting-started.html) et la documentation sur la [création et la gestion d’applications serverless](https://docs.openshift.com/container-platform/4.6/serverless/serving-creating-managing-apps.html).
