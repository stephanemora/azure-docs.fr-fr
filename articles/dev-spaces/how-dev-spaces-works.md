---
title: Fonctionnement et configuration d'Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/04/2019
ms.topic: conceptual
description: Décrit les processus qui alimentent Azure Dev Spaces et comment ils sont configurés dans le fichier de configuration azds.yaml.
keywords: azds.yaml, Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: e96541b0008dca9cbaeda92152f835c188036971
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771136"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Fonctionnement et configuration d'Azure Dev Spaces

Le développement d’une application Kubernetes peut s'avérer difficile. Vous avez besoin des fichiers de configuration Docker et Kubernetes. Vous devez trouver comment tester votre application localement et interagir avec d'autres services dépendants. Vous devrez éventuellement gérer le développement et le test sur plusieurs services à la fois et avec une équipe de développeurs.

Azure Dev Spaces vous aide à développer, déployer et déboguer les applications Kubernetes directement dans Azure Kubernetes Service (AKS). Azure Dev Spaces permet également à une équipe de partager un espace de développement. Le partage d'un espace de développement au sein d'une équipe permet à chacun des membres de cette équipe de se développer de manière isolée sans avoir à répliquer ou à simuler des dépendances ou d'autres applications dans le cluster.

Azure Dev Spaces crée et utilise un fichier de configuration pour déployer, exécuter et déboguer vos applications Kubernetes dans AKS. Ce fichier de configuration contient le code de votre application et peut être ajouté à votre système de contrôle de version.

Cet article décrit les processus qui alimentent Azure Dev Spaces et comment ces processus sont configurés dans le fichier de configuration Azure Dev Spaces. Pour configurer rapidement Azure Dev Spaces et le voir en pratique, complétez l'un des démarrages rapides suivants :

* [Java avec l’interface CLI et Visual Studio Code](quickstart-java.md)
* [.NET Core avec l’interface CLI et Visual Studio Code](quickstart-netcore.md)
* [.NET Core avec Visual Studio](quickstart-netcore-visualstudio.md)
* [Node.js avec l’interface CLI et Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Fonctionnement d’Azure Dev Spaces

Azure Dev Spaces comporte deux composants distincts avec lesquels vous interagissez : le contrôleur et les outils côté client.

![Composants Azure Dev Spaces](media/how-dev-spaces-works/components.svg)

Le contrôleur effectue les actions suivantes :

* Gère la création et la sélection de l'espace de développement.
* Installe le graphique Helm de votre application et crée des objets Kubernetes.
* Génère l'image du conteneur de votre application.
* Déploie votre application sur AKS.
* Crée des builds incrémentales et redémarre lorsque votre code source change.
* Gère les journaux et les traces HTTP.
* Transfère stdout et stderr vers les outils côté client.
* Permet aux membres de l'équipe de créer des espaces de développement enfants dérivés d'un espace de développement parent.
* Configure le routage pour les applications à l'intérieur d'un espace et entre les espaces parents et enfants.

Le contrôleur réside à l'extérieur d’AKS. Il gère le comportement et la communication entre les outils côté client et le cluster AKS. Le contrôleur est activé à l'aide de l’interface de ligne de commande CLI Azure lorsque vous préparez votre cluster pour utiliser Azure Dev Spaces. Une fois ce contrôleur activé, vous pouvez interagir avec lui à l'aide des outils côté client.

Les outils côté client permettent à l’utilisateur d’effectuer les tâches suivantes :
* Générer un Dockerfile, un graphique Helm et un fichier de configuration Azure Dev Spaces pour l'application.
* Créer des espaces de développement parents et enfants.
* Demander au contrôleur de générer et de démarrer votre application.

Pendant que votre application est en cours d'exécution, les outils côté client effectuent aussi les tâches suivantes :
* Reçoivent et affichent stdout et stderr depuis votre application en cours d’exécution dans AKS.
* Utilisent [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) pour autoriser l'accès web à votre application via http:\//localhost.
* Associe un débogueur à votre application en cours d'exécution dans AKS.
* Synchronisent le code source sur votre espace de développement lorsqu'un changement est détecté pour les builds incrémentielles, permettant ainsi une itération rapide.

Vous pouvez utiliser les outils côté client depuis la ligne de commande dans le cadre de la commande `azds`. Vous pouvez également utiliser les outils côté client avec :

* Visual Studio Code avec l'[extension Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio avec [Visual Studio Tools pour Kubernetes](https://aka.ms/get-vsk8stools).

Voici le flux de base pour la configuration et l'utilisation d’Azure Dev Spaces :
1. Préparer votre cluster AKS pour Azure Dev Spaces
1. Préparer votre code pour une exécution sur Azure Dev Spaces
1. Exécuter votre code sur un espace de développement
1. Déboguer votre code sur un espace de développement
1. Partager un espace de développement

Nous reviendrons plus en détail sur le fonctionnement d'Azure Dev Spaces dans chacune des sections ci-dessous.

## <a name="prepare-your-aks-cluster"></a>Préparer votre cluster AKS

La préparation de votre cluster AKS implique les étapes suivantes :
* Vérification que votre cluster Azure Kubernetes Service se trouve dans une région [prise en charge par Azure Dev Spaces][supported-regions].
* Vérification que vous exécutez Kubernetes 1.10.3 ou version ultérieure.
* Activer Azure Dev Spaces sur votre cluster avec `az aks use-dev-spaces`

Pour plus d'informations sur la création et la configuration d'un cluster AKS pour Azure Dev Spaces, consultez l'un des guides de démarrage suivants :
* [Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à Java](get-started-java.md)
* [Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à .NET Core et Visual Studio](get-started-netcore-visualstudio.md)
* [Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à .NET Core](get-started-netcore.md)
* [Bien démarrer avec l’utilisation d’Azure Dev Spaces conjointement à Node.js](get-started-nodejs.md)

Quand Azure Dev Spaces est activé sur votre cluster AKS, il installe le contrôleur pour votre cluster. Le contrôleur est une ressource Azure distincte à l'extérieur de votre cluster et qui effectue les actions suivantes sur les ressources de votre cluster :

* Crée ou désigne un espace de noms Kubernetes à utiliser comme espace de développement.
* Supprime tout espace de noms Kubernetes nommé *azds*, s'il existe, et en crée un nouveau.
* Déploie une configuration webhook Web Kubernetes.
* Déploie un serveur d’admission webhook.
    

Il utilise également le même principal de service que votre cluster AKS pour passer des appels de service vers d'autres composants Azure Dev Spaces.

![Azure Dev Spaces prépare le cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Pour utiliser Azure Dev Spaces, au moins un espace de développement est nécessaire. Azure Dev Spaces utilise les espaces de noms Kubernetes de votre cluster AKS comme espaces de développement. Lorsqu'un contrôleur est installé, il vous invite à créer un espace de noms Kubernetes ou à choisir un espace de noms existant à utiliser comme espace de développement initial. Lorsqu'un espace de noms est désigné comme espace de développement, le contrôleur ajoute l’étiquette *azds.io/space=true* à cet espace de noms pour l'identifier comme espace de développement. Une fois que vous avez préparé votre cluster, l'espace de développement initial que vous créez ou désignez est sélectionné par défaut. Lorsqu'un espace est sélectionné, il est utilisé par Azure Dev Spaces pour créer des charges de travail.

Par défaut, le contrôleur crée un espace de développement nommé *default* (par défaut) en mettant à niveau l'espace de développement existant Kubernetes intitulé *default* (par défaut). Vous pouvez utiliser les outils côté client pour créer des espaces de développement et supprimer les espaces de développement existants. En raison d'une limitation dans Kubernetes, l'espace de développement *default* (par défaut) ne peut pas être supprimé. Le contrôleur supprime également tous les espaces de noms Kubernetes existants nommés *azds* pour éviter les conflits avec la commande `azds` utilisée par les outils côté client.

Le serveur d'admission webhook de Kubernetes sert à injecter des pods avec trois conteneurs pendant le déploiement pour l'instrumentation : un conteneur devspaces-proxy, un conteneur devspaces-proxy-init et un conteneur devspaces-build. **Ces trois conteneurs s'exécutent avec un accès root sur votre cluster AKS.** Ils utilisent également le même principal de service que votre cluster AKS pour passer des appels de service vers d'autres composants Azure Dev Spaces.

![Serveur d'admission webhook Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Le conteneur devspaces-proxy est un conteneur sidecar qui gère tout le trafic TCP entrant et sortant du conteneur de l'application et en facilite le routage. Le conteneur devspaces-proxy redirige les messages HTTP si certains espaces sont utilisés. Par exemple, il peut faciliter le routage des messages HTTP entre les applications dans les espaces parents et enfants. Tout le trafic non-HTTP transite par devspaces-proxy, sans être modifié. Le conteneur devspaces-proxy enregistre également tous les messages HTTP entrants et sortants et les envoie aux outils côté client sous forme de traces. Ces traces peuvent ensuite être visualisées par le développeur pour étudier le comportement de l'application.

Le conteneur devspaces-proxy-init est un conteneur [init](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) qui ajoute des règles de routage supplémentaires basées sur la hiérarchie spatiale au conteneur de votre application. Il ajoute des règles de routage en mettant à jour le fichier */etc/resolv.conf* du conteneur de l’application et la configuration des tables d’adresses IP avant le démarrage. Les mises à jour appliquées à */etc/resolv.conf* permettent la résolution DNS des services dans les espaces parents. Les mises à jour de la configuration des tables d’adresses IP garantissent que tout le trafic TCP entrant et sortant du conteneur de l'application est acheminé via devspaces-proxy. Toutes les mises à jour provenant de devspaces-proxy-init viennent compléter les règles que Kubernetes ajoute.

Le conteneur devspaces-build est un conteneur init et contient le code source du projet ainsi que le socket Docker monté. Le code source du projet et l'accès à Docker permettent au conteneur de l’application d'être généré directement par le pod.

> [!NOTE]
> Azure Dev Spaces utilise le même nœud pour générer le conteneur de votre application et l'exécuter. Par conséquent, Azure Dev Spaces n'a pas besoin d'un registre de conteneurs externe pour générer et exécuter votre application.

Le serveur d'admission webhook Kubernetes écoute tout nouveau pod créé dans le cluster AKS. Si ce pod est déployé dans un espace de noms étiqueté *azds.io/space=true*, il injecte ce pod avec les conteneurs supplémentaires. Le conteneur devspaces-build n'est injecté que si le conteneur de l'application est exécuté avec les outils côté client.

Une fois que vous avez préparé votre cluster AKS, vous pouvez utiliser les outils côté client pour préparer et exécuter votre code dans votre espace de développement.

## <a name="prepare-your-code"></a>Préparer votre code

Pour exécuter votre application dans un espace de développement, elle doit être conteneurisée, et vous devez définir la façon dont elle doit être déployée sur Kubernetes. Pour conteneuriser votre application, vous avez besoin d'un Dockerfile. Pour définir la façon dont votre application est déployée sur Kubernetes, vous avez besoin d'un [graphique Helm](https://docs.helm.sh/). Afin vous aider à créer à la fois le Dockerfile et le graphique Helm pour votre application, les outils côté client fournissent la commande `prep` :

```cmd
azds prep --public
```

La commande `prep` examinera les fichiers de votre projet et tentera de créer le Dockerfile et le graphique Helm pour exécuter votre application dans Kubernetes. Actuellement, la commande `prep` génère un Dockerfile et un diagramme Helm avec les langages suivants :

* Java
* Node.js
* .NET Core

Vous *devez* exécuter la commande `prep` depuis un répertoire contenant le code source. Exécuter la commande `prep` depuis le répertoire adéquat permet aux outils côté client d'identifier le langage et de créer un Dockerfile approprié pour conteneuriser votre application. Vous pouvez également exécuter la commande `prep` à partir d'un répertoire contenant un fichier *pom.xml* pour les projets Java.

Si vous exécutez la commande `prep` à partir d'un répertoire qui ne contient aucun code source, les outils côté client ne généreront aucun Dockerfile. Ils afficheront également le message d'erreur suivant : *Impossible de générer Dockerfile en raison d’un langage non pris en charge*. Cette erreur se produit également si les outils côté client ne reconnaissent pas le type de projet.

Lorsque vous exécutez la commande `prep`, vous avez la possibilité de spécifier l'indicateur `--public`. Cet indicateur demande au contrôleur de créer un point d'accès Internet pour ce service. Si vous ne spécifiez pas cet indicateur, le service n'est accessible que depuis le cluster ou via le tunnel localhost créé par les outils côté client. Vous pouvez activer ou désactiver ce comportement après avoir exécuté la commande `prep` en mettant à jour le graphique Helm généré.

La commande `prep` ne remplacera pas les Dockerfiles ou les graphiques Helm existants que contient votre projet. Si un Dockerfile ou un graphique Helm existant utilise la même convention d'appellation que les fichiers générés par la commande `prep`, celle-ci `prep` ignore la génération de ces fichiers. Sinon, la commande `prep` générera son propre Dockerfile ou graphique Helm à côté des fichiers existants.

La commande `prep` générera également un fichier `azds.yaml` à la racine de votre projet. Azure Dev Spaces utilise ce fichier pour générer, installer, configurer et exécuter votre application. Ce fichier de configuration indique l'emplacement de votre Dockerfile et de votre graphique Helm et également une configuration supplémentaire en plus de ces artefacts.

Voici un exemple de fichier azds.yaml créé avec un [exemple d'application .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend) :

```yaml
kind: helm-release
apiVersion: 1.1
build:
  context: .
  dockerfile: Dockerfile
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
        # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
        # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
        # For more information see https://aka.ms/devspaces/routing
        - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
```

Le fichier `azds.yaml` généré par la commande `prep` devrait fonctionner correctement pour un scénario de développement de projet simple et unique. Si votre projet spécifique est plus complexe, vous devrez peut-être mettre à jour ce fichier après avoir exécuté la commande `prep`. Par exemple, votre projet peut nécessiter quelques ajustements à votre processus de génération ou de lancement selon vos besoins de développement ou de débogage. Votre projet contient peut-être plusieurs applications nécessitant plusieurs processus de génération ou un contenu de génération différent.

## <a name="run-your-code"></a>Exécuter votre code

Pour exécuter votre code dans un espace de développement, lancez la commande `up` dans le même répertoire que votre fichier `azds.yaml` :

```cmd
azds up
```

La commande `up` charge les fichiers source de votre application et autres artefacts nécessaires pour générer et exécuter votre projet dans l'espace de développement. De là, le contrôleur dans votre espace de développement :

1. Crée les objets Kubernetes pour déployer votre application.
1. Génère le conteneur pour votre application.
1. Déploie votre application dans l'espace de développement.
1. Crée un nom DNS accessible au public pour votre point de terminaison d'application, s'il est configuré.
1. Utilise *port-forward* pour donner accès à votre point de terminaison d'application grâce à http://localhost.
1. Transfère stdout et stderr vers les outils côté client.


### <a name="starting-a-service"></a>Démarrage d'un service

Lorsque vous démarrez un service dans un espace de développement, les outils côté client et le contrôleur se coordonnent pour synchroniser vos fichiers source, créer votre conteneur et vos objets Kubernetes, puis exécuter votre application.

À un niveau plus granulaire, voici ce qui se passe lorsque vous exécutez `azds up` :

1. Les fichiers sont synchronisés depuis la machine de l'utilisateur vers un stockage de fichiers Azure spécifique au cluster AKS de cet utilisateur. Le code source, le graphique Helm et les fichiers de configuration sont chargés. La section suivant fournit plus de détails sur le processus de synchronisation.
1. Le contrôleur crée une demande de démarrage d'une nouvelle session. Cette requête contient plusieurs propriétés, notamment un ID unique, un nom d'espace, le chemin vers le code source et un indicateur de débogage.
1. Le contrôleur remplace le caractère générique *$(tag)* dans le graphique Helm par l'ID de session unique et installe ce graphique pour votre service. L'ajout d'une référence à l'ID de session unique au graphique Helm permet de relier le conteneur déployé dans le cluster AKS pour cette session spécifique à la demande de session et aux informations associées.
1. Lors de l'installation du graphique Helm Chart, le serveur d'admission webhook Kubernetes ajoute des conteneurs supplémentaires au pod de votre application pour l'instrumentation et l'accès au code source de votre projet. Les conteneurs devspaces-proxy et devspaces-proxy-init sont ajoutés pour permettre le traçage HTTP et le routage des espaces. Le conteneur devspaces-build est ajouté pour permettre au pod d'accéder à l'instance Docker et au code source du projet afin de générer le conteneur de votre application.
1. Lorsque le pod de l'application est lancé, le conteneur devspaces-build et le conteneur devspaces-proxy-init sont utilisés pour générer le conteneur de l'application. Le conteneur d'application et les conteneurs devspaces-proxy sont alors démarrés.
1. Après le démarrage du conteneur d'application, la fonctionnalité côté client utilise la fonctionnalité Kubernetes *port-forward* pour fournir un accès HTTP à votre application sur http://localhost. Cette redirection de port connecte votre machine de développement au service dans votre espace de développement.
1. Lorsque tous les conteneurs du pod ont démarré, le service est actif. À ce stade, la fonctionnalité côté client commence à diffuser en continu les traces HTTP, stdout et stderr. Ces informations sont affichées par la fonctionnalité côté client pour le développeur.

### <a name="updating-a-running-service"></a>Mise à jour d’un service en cours d’exécution

Pendant l'exécution d'un service, Azure Dev Spaces peut mettre à jour ce service si l'un des fichiers source du projet change. Dev Spaces gère également la mise à jour du service différemment selon le type de fichier modifié. Il existe trois façons pour Dev Spaces de mettre à jour un service en cours d'exécution :

* Mise à jour directe d’un fichier
* Régénération et redémarrage du processus de l'application à l'intérieur du conteneur de l'application en cours d'exécution
* Régénération et redéploiement du conteneur de l'application

![Synchronisation des fichiers Azure Dev Spaces](media/how-dev-spaces-works/file-sync.svg)

Certains fichiers du projet représentant des ressources statiques, notamment les fichiers html, css et cshtml, peuvent être mis à jour directement dans le conteneur de l'application, sans aucun redémarrage. Si une ressource statique change, le nouveau fichier est synchronisé avec l'espace de développement puis utilisé par le conteneur courant.

Les modifications apportées aux fichiers tels que le code source ou les fichiers de configuration de l'application peuvent être appliquées en redémarrant le processus de l'application dans le conteneur actif. Une fois ces fichiers synchronisés, le processus de l'application est relancé dans le conteneur en cours d'exécution à l’aide du processus *devhostagent*. Lors de la création initiale du conteneur de l'application, le contrôleur remplace la commande de démarrage de l'application par un processus différent appelé *devhostagent*. Le processus réel de l'application est alors exécuté en tant que processus enfant sous *devhostagent*, et sa sortie est extraite à l’aide de *devhostagent*. Le processus *devhostagent* fait également partie de Dev Spaces et peut exécuter des commandes dans le conteneur courant pour le compte de Dev Spaces. Lors d'un redémarrage, *devhostagent* :

* Arrête le ou les processus actuels associés à l’application
* Régénère l'application
* Redémarre le ou les processus associés à l'application

La façon dont *devhostagent* exécute les étapes précédentes est définie dans le fichier de configuration `azds.yaml`. Cette configuration est détaillée dans une section ultérieure.

Les mises à jour des fichiers du projet tels que les fichiers Dockerfile, les fichiers csproj ou toute partie du graphique Helm nécessitent la régénération et le redéploiement du conteneur de l’application. Si un de ces fichiers est synchronisé avec l’espace de développement, le contrôleur exécute la commande [helm upgrade](https://helm.sh/docs/intro/using_helm/#helm-upgrade-and-helm-rollback-upgrading-a-release-and-recovering-on-failure), puis un conteneur de l’application est régénéré et redéployé.

### <a name="file-synchronization"></a>Synchronisation de fichiers

La première fois qu’une application démarre dans un espace de développement, tous les fichiers de l’application source sont chargés. Pendant l’exécution de l’application et lors des redémarrages ultérieurs, seuls les fichiers modifiés sont chargés. Deux fichiers sont utilisés pour coordonner ce processus : un fichier côté client et un autre côté contrôleur.

Stocké dans un répertoire temporaire, le fichier côté client est nommé en fonction du hachage du répertoire du projet en cours d’exécution dans les espaces de développement. Par exemple, sur Windows, le fichier peut être *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* pour votre projet. Sur Linux, le fichier côté client est stocké dans le répertoire */tmp*. Vous pouvez trouver le répertoire sur macOS en exécutant la commande `echo $TMPDIR`.

Ce fichier au format JSON contient :

* Une entrée pour chaque fichier de projet synchronisé avec l’espace de développement
* Un ID de synchronisation
* L’horodatage de la dernière opération de synchronisation

Chaque entrée du fichier du projet contient un chemin d’accès au fichier et son horodatage.

Le fichier côté contrôleur est stocké sur le cluster AKS. Il contient l’ID de synchronisation et l’horodatage de la dernière synchronisation.

Une synchronisation se produit lorsque les horodatages de synchronisation ne correspondent pas entre les fichiers côté client et côté contrôleur. Au cours d'une synchronisation, les outils côté client effectuent une itération sur les entrées du fichier côté client. Si l'horodatage du fichier est postérieur à l'horodatage de synchronisation, ce fichier est synchronisé avec l'espace de développement. Une fois la synchronisation terminée, les horodatages de synchronisation sont mis à jour sur les fichiers côté client et côté contrôleur.

Tous les fichiers du projet sont synchronisés si le fichier côté client n'est pas présent. Ce comportement vous permet de forcer une synchronisation complète en supprimant le fichier côté client.

### <a name="how-routing-works"></a>Fonctionnement du routage

Un espace de développement est créé par dessus AKS et utilise les mêmes [concepts de mise en réseau](../aks/concepts-network.md). Azure Dev Spaces dispose également d'un service *ingressmanager* centralisé et déploie son propre contrôleur d'entrée sur le cluster AKS. Le service *ingressmanager* surveille les clusters AKS avec des espaces de développement et augmente le contrôleur d'entrée Azure Dev Spaces dans le cluster avec des objets d’entrée pour le routage vers les pods d’application. Le conteneur devspaces-proxy de chaque pod ajoute un en-tête HTTP `azds-route-as` pour le trafic HTTP vers un espace de développement basé sur l'URL. Par exemple, une requête à l'URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obtiendrait un en-tête HTTP avec `azds-route-as: azureuser`. Le conteneur devspaces-proxy n'ajoutera pas d'en-tête `azds-route-as` s'il y en a déjà un.

Lorsqu'une requête HTTP est envoyée à un service extérieur au cluster, la requête est transmise au contrôleur d’entrée. Le contrôleur d’entrée achemine la requête directement au pod approprié en fonction de ses objets et règles d’entrée. Le conteneur devspaces-proxy du pod reçoit la requête, ajoute l'en-tête `azds-route-as` basé sur l'URL, puis achemine la requête vers le conteneur d’application.

Lorsqu'une requête HTTP est envoyée à un service depuis un autre service au sein du cluster, la requête transite d'abord par le conteneur devspaces-proxy du service appelant. Le conteneur devspaces-proxy examine la requête HTTP et vérifie l'en-tête `azds-route-as`. Selon l'en-tête, le conteneur devspaces-proxy cherchera l'adresse IP du service associé à la valeur de cet en-tête. Si une adresse IP est trouvée, le conteneur devspaces-proxy redirige la requête vers cette adresse IP. Si aucune adresse IP n'est trouvée, le conteneur devspaces-proxy achemine la requête vers le conteneur de l’application parente.

Par exemple, les applications *serviceA* et *serviceB* sont déployées sur un espace de développement parent appelé *default* (par défaut). *serviceA* s'appuie sur *serviceB* et lui passe des appels HTTP. Azure User crée un espace de développement enfant basé sur l'espace *default* (par défaut), appelé *azureuser*. Azure User déploie également sa propre version de *serviceA* sur son espace enfant. Lorsqu'une requête est envoyée à *http://azureuser.s.default.serviceA.fedcba09...azds.io* :

![Routage Azure Dev Spaces](media/how-dev-spaces-works/routing.svg)

1. Le contrôleur d’entrée recherche l'adresse IP du pod associé à l'URL, soit *serviceA.azureuser*.
1. Le contrôleur d'entrée trouve l'adresse IP du pod dans l'espace de développement de l'utilisateur Azure et achemine la requête vers le pod *serviceA.azureuser*.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* reçoit la requête et ajoute `azds-route-as: azureuser` comme en-tête HTTP.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* achemine la requête vers le conteneur *serviceA* dans le pod *serviceA.azureuser*.
1. L'application *serviceA* du pod *serviceA.azureuser* appelle *serviceB*. L'application *serviceA* contient également un code pour conserver l'en-tête `azds-route-as` existant, soit `azds-route-as: azureuser` dans ce cas.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* reçoit la requête et recherche l'IP de *serviceB* selon la valeur de l'en-tête `azds-route-as`.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* ne trouve pas d'IP pour *serviceB.azureuser*.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* cherche l'IP pour *serviceB* dans l'espace parent, soit *serviceB.default*.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* trouve l'IP pour *serviceB.default* et achemine la requête vers le pod *serviceB.default*.
1. Le conteneur devspaces-proxy du pod *serviceB.default* reçoit la requête et l'achemine vers le conteneur d’application *serviceB* dans le pod *serviceB.default*.
1. L'application *serviceB* du pod *serviceB.default* renvoie une réponse au pod *serviceA.azureuser*.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* reçoit la réponse et l’achemine vers le conteneur d’application *serviceA* dans le pod *serviceA.azureuser*.
1. L'application *serviceA* reçoit la réponse et renvoie ensuite sa propre réponse.
1. Le conteneur devspaces-proxy du pod *serviceA.azureuser* reçoit la réponse du conteneur d'application *serviceA* et achemine la réponse vers l'appelant original en dehors du cluster.

Tout autre trafic TCP non HTTP transite par le contrôleur d’entrée et les conteneurs devspaces-proxy ne sont pas modifiés.

### <a name="how-running-your-code-is-configured"></a>Configuration de l'exécution de votre code

Azure Dev Spaces utilise le fichier `azds.yaml` pour installer et configurer votre service. Le contrôleur utilise la propriété `install` du fichier `azds.yaml` pour installer le graphique Helm et créer les objets Kubernetes :

```yaml
...
install:
  chart: charts/webfrontend
  values:
  - values.dev.yaml?
  - secrets.dev.yaml?
  set:
    replicaCount: 1
    image:
      repository: webfrontend
      tag: $(tag)
      pullPolicy: Never
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik-azds
      hosts:
      # This expands to [space.s.][rootSpace.]webfrontend.<random suffix>.<region>.azds.io
      # Customize the public URL by changing the 'webfrontend' text between the $(rootSpacePrefix) and $(hostSuffix) tokens
      # For more information see https://aka.ms/devspaces/routing
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Par défaut, la commande `prep` génère le graphique Helm. Elle définit également la propriété *install.chart* sur le répertoire du graphique Helm. Si vous voulez utiliser un graphique Helm à un autre emplacement, vous pouvez mettre à jour cette propriété en fonction de cet emplacement.

Lors de l'installation des graphiques Helm, Azure Dev Spaces fournit un moyen de remplacer les valeurs du graphique Helm. Les valeurs par défaut du graphique Helm sont affichées dans `charts/APP_NAME/values.yaml`.

En utilisant la propriété *install.values*, vous pouvez répertorier un ou plusieurs fichiers qui définissent les valeurs que vous voulez remplacer dans le graphique Helm. Par exemple, vous pouvez utiliser cette fonctionnalité de remplacement pour obtenir une configuration de nom d'hôte ou de base de données spécifique lors de l'exécution de votre application dans un espace de développement. Vous pouvez également ajouter un *?* . à la fin de n'importe quel nom de fichier pour le définir comme facultatif.

La propriété *install.set* vous permet de configurer une ou plusieurs valeurs à remplacer dans le graphique Helm. Toutes les valeurs configurées dans *install.set* remplaceront les valeurs configurées dans les fichiers répertoriés dans *install.values*. Les propriétés sous *install.set* dépendent des valeurs du graphique Helm et peuvent être différentes selon le graphique Helm généré.

Dans l'exemple ci-dessus, la propriété *install.set.replicaCount* indique au contrôleur combien d'instances de votre application doivent être exécutées dans votre espace de développement. Selon votre scénario, vous pouvez augmenter cette valeur, mais cela aura un impact sur la connexion d'un débogueur au pod de votre application. Pour plus d’informations, consultez l’[article de dépannage](troubleshooting.md).

Dans le graphique Helm généré, l'image du conteneur est définie sur *{{ .Values.image.repository }}:{{ .Values.image.tag }}* . Le ficher `azds.yaml` définit la propriété *install.set.image.tag* sur *$(tag)* par défaut, utilisée comme valeur pour *{{ .Values.image.tag }}* . En définissant la propriété *install.set.image.tag* de cette façon, l’image du conteneur de votre application sera étiquetée d'une manière distincte lors de l'exécution des espaces de développement Azure. Dans ce cas spécifique, l'image est étiquetée ainsi : *\<valeur de image.repository>:$(tag)* . Vous devez utiliser la variable *$(tag)* comme valeur de *install.set.image.tag* pour que Dev Spaces reconnaisse et situe le conteneur dans le cluster AKS.

Dans l'exemple ci-dessus, `azds.yaml` définit *install.set.ingress.hosts*. La propriété *install.set.ingress.hosts* définit un format de nom d'hôte pour les points de terminaison publics. Cette propriété utilise également *$$(spacePrefix)* , *$(rootSpacePrefix)* , et *$(hostSuffix)* , valeurs fournies par le contrôleur. 

*$$(spacePrefix)* est le nom de l'espace de développement enfant, qui prend la forme *SPACENAME.s*. *$$(rootSpacePrefix)* est le nom de l'espace parent. Par exemple, si *azureuser* est un espace enfant de *default*, la valeur pour *$(rootSpacePrefix)* est *default*, et la valeur de *$(spacePrefix)* est *azureuser.s*. Si l'espace n'est pas un espace enfant, *$(spacePrefix)* est vide. Par exemple, si l'espace *default* n'a pas d'espace parent, la valeur pour *$(rootSpacePrefix)* est *default* et la valeur de *$(spacePrefix)* est vide. *$$(hostSuffix)* est un suffixe DNS qui pointe vers le contrôleur d'entrée Azure Dev Spaces exécuté dans votre cluster AKS. Ce suffixe DNS correspond à une entrée DNS générique, par exemple *\*.RANDOM_VALUE.eus.azds.io*, créée lorsque le contrôleur Azure Dev Spaces a été ajouté à votre cluster AKS.

Dans le fichier `azds.yaml` ci-dessus, vous pouvez également mettre à jour *install.set.ingress.hosts* pour changer le nom d'hôte de votre application. Par exemple, si vous voulez simplifier le nom d'hôte de votre application de *$$(spacePrefix)$(rootSpacePrefix)$(hostSuffix)* à *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)* .

Afin de générer le conteneur pour votre application, le contrôleur utilise les sections ci-dessous du fichier de configuration `azds.yaml` :

```yaml
build:
  context: .
  dockerfile: Dockerfile
...
configurations:
  develop:
    build:
      dockerfile: Dockerfile.develop
      useGitIgnore: true
      args:
        BUILD_CONFIGURATION: ${BUILD_CONFIGURATION:-Debug}
...
```

Le contrôleur utilise un Dockerfile pour générer et exécuter votre application.

La propriété *build.context* indique le répertoire où figurent les Dockerfiles. La propriété *build.dockerfile* définit le nom du Dockerfile pour générer la version de production de l'application. La propriété *configurations.develop.build.build.dockerfile* configure le nom du Dockerfile pour la version de développement de l'application.

Avoir différents Dockerfiles pour le développement et la production vous permet d'activer certains éléments pendant le développement et de désactiver ces éléments pour les déploiements en production. Par exemple, vous pouvez activer le débogage ou la journalisation détaillée pendant le développement et le désactiver dans un environnement de production. Vous pouvez également mettre à jour ces propriétés si vos Dockerfiles sont nommés différemment ou se trouvent dans un autre emplacement.

Pour accélérer l’itération pendant le développement, Azure Dev Spaces synchronisera les changements de votre projet local et mettra à jour progressivement votre application. La section ci-dessous du fichier de configuration `azds.yaml` sert à configurer la synchronisation et la mise à jour :

```yaml
...
configurations:
  develop:
    ...
    container:
      sync:
      - "**/Pages/**"
      - "**/Views/**"
      - "**/wwwroot/**"
      - "!**/*.{sln,csproj}"
      command: [dotnet, run, --no-restore, --no-build, --no-launch-profile, -c, "${BUILD_CONFIGURATION:-Debug}"]
      iterate:
        processesToKill: [dotnet, vsdbg]
        buildCommands:
        - [dotnet, build, --no-restore, -c, "${BUILD_CONFIGURATION:-Debug}"]
...
```

Les fichiers et répertoires qui synchroniseront les changements sont répertoriés dans la propriété *configurations.develop.container.sync*. Ces répertoires sont synchronisés initialement lorsque vous exécutez la commande `up` et lorsque des changements sont détectés. Si vous souhaitez synchroniser des répertoires supplémentaires ou différents avec votre espace de développement, vous pouvez modifier cette propriété.

La propriété *configurations.develop.container.iterate.buildCommands* indique comment générer l'application dans un scénario de développement. La propriété *configurations.develop.container.command* fournit la commande pour exécuter l'application dans un scénario de développement. Vous pouvez mettre à jour l'une ou l'autre de ces propriétés si vous souhaitez utiliser d'autres indicateurs ou paramètres de génération ou d'exécution pendant le développement.

La propriété *configurations.develop.container.iterate.processesToKill* répertorie les processus à tuer pour arrêter l'application. Vous pouvez mettre à jour cette propriété pour changer le comportement de redémarrage de votre application pendant le développement. Par exemple, si vous avez mis à jour les propriétés *configurations.develop.container.iterate.buildCommands* ou *configurations.develop.container.command* pour modifier la génération ou le lancement de l'application, vous devrez peut-être changer quels processus sont arrêtés.

Lorsque vous préparez votre code à l'aide de la commande `azds prep`, vous avez la possibilité d'ajouter l'indicateur `--public`. L'ajout de l'indicateur `--public` crée une URL accessible au public pour votre application. Si vous omettez cet indicateur, l'application n'est accessible que dans le cluster ou en utilisant le tunnel localhost. Après avoir exécuté la commande `azds prep`, vous pouvez modifier ce paramètre en changeant la propriété *ingress.enabled* dans `charts/APPNAME/values.yaml` :

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Déboguer votre code

Pour les applications Java,.NET et Node.js, vous pouvez déboguer votre application exécutée directement dans votre espace de développement en utilisant Visual Studio Code ou Visual Studio. Visual Studio Code et Visual Studio fournissent des outils pour se connecter à votre espace de développement, lancer votre application et joindre un débogueur. Après avoir lancé `azds prep`, vous pouvez ouvrir votre projet dans Visual Studio Code ou Visual Studio. Visual Studio Code ou Visual Studio généreront leurs propres fichiers de configuration pour la connexion, une opération distincte de l’exécution de `azds prep`. Depuis Visual Studio Code ou Visual Studio, vous pouvez définir des points d'arrêt et lancer votre application dans votre espace de développement.

![Débogage de votre code](media/get-started-node/debug-configuration-nodejs2.png)

Lorsque vous lancez votre application en utilisant Visual Studio Code ou Visual Studio pour le débogage, ces programmes gèrent le lancement et la connexion à votre espace de développement de la même manière qu'en exécutant `azds up`. Les outils côté client de Visual Studio Code et Visual Studio fournissent également un paramètre supplémentaire avec des informations spécifiques pour le débogage. Ce paramètre contient le nom de l'image du débogueur, l'emplacement du débogueur dans cette image, et l'emplacement de destination dans le conteneur de l'application pour monter le dossier du débogueur.

L'image du débogueur est automatiquement déterminée par les outils côté client. Ces outils adoptent une méthode similaire à celle utilisée lors de l'exécution du Dockerfile et du graphique Helm Chart générés lors de l'exécution de `azds prep`. Une fois le débogueur monté dans l'image de l'application, il est lancé en utilisant `azds exec`.

## <a name="sharing-a-dev-space"></a>Partage d’un espace de développement

Lorsque vous travaillez en équipe, vous pouvez [partager un espace de développement avec tous les membres de cette équipe](how-to/share-dev-spaces.md) et créer des espaces de développement dérivés. Un espace de développement peut être utilisé par toute personne ayant un accès contributeur au groupe de ressources de cet espace de développement.

Vous pouvez également créer un espace de développement dérivé d'un autre espace de développement. Lorsque vous créez un espace de développement dérivé, l’étiquette *azds.io/parent-space=PARENT-SPACE-NAME* est ajouté à l'espace de noms de l'espace de développement dérivé. De plus, toutes les applications de l'espace de développement parent sont partagées avec l'espace de développement dérivé. Si vous déployez une version mise à jour d'une application dans l'espace de développement dérivé, elle n'existera que dans cet espace de développement dérivé, et l'espace de développement parent ne sera pas affecté. Vous pouvez avoir un maximum de trois niveaux d'espaces de développement dérivés ou espaces *grands-parents*.

L'espace de développement dérivé acheminera également de façon intelligente les requêtes entre ses propres applications et les applications partagées par son parent. Le routage fonctionne en tentant d'acheminer la requête vers une application dans l'espace de développement dérivé, puis en revenant à l'application partagée depuis l'espace de développement parent. Le routage reviendra à l'application partagée dans l'espace grand-parent si l'application ne figure pas dans l'espace parent.

Par exemple :
* L’espace de développement *default* possède des applications *serviceA* et *serviceB* .
* L’espace de développement *azureuser* est dérivé de *default*.
* Une version mise à jour de *serviceA* est déployée sur *azureuser*.

Lorsque vous utilisez *azureuser*, toutes les requêtes envoyées à *serviceA* seront acheminées vers la version mise à jour dans *azureuser*. Une requête envoyée à *serviceB* tente d’abord d’être acheminée vers la version *azureuser* de *serviceB*. Comme elle n’existe pas, elle sera acheminée vers la version *default* de *serviceB*. Si la version *azureuser* de *serviceA* est supprimée, toutes les requêtes envoyées à *serviceA* seront retournées à l’aide de la version *default* de *serviceA*.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à utiliser Azure Dev Spaces, consultez les guides de démarrage rapide suivants :

* [Java avec l’interface CLI et Visual Studio Code](quickstart-java.md)
* [.NET Core avec l’interface CLI et Visual Studio Code](quickstart-netcore.md)
* [.NET Core avec Visual Studio](quickstart-netcore-visualstudio.md)
* [Node.js avec l’interface CLI et Visual Studio Code](quickstart-nodejs.md)

Pour bien démarrer avec le développement en équipe, consultez les articles sur les procédures ci-dessous :

* [Développement en équipe - Java avec l’interface CLI et Visual Studio Code](team-development-java.md)
* [Développement en équipe - .NET Core avec l’interface CLI et Visual Studio Code](team-development-netcore.md)
* [Développement en équipe - .NET Core avec Visual Studio](team-development-netcore-visualstudio.md)
* [Développement en équipe - Node.js avec l’interface CLI et Visual Studio Code](team-development-nodejs.md)



[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
