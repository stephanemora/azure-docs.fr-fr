---
title: Comment les espaces de développement Azure fonctionne et est configuré
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: zr-msft
ms.author: zarhoads
ms.date: 03/04/2019
ms.topic: conceptual
description: Décrit les processus qu’espaces de développement Azure power et comment ils sont configurés dans le fichier de configuration azds.yaml
keywords: azds.yaml, espaces de développement Azure, les espaces de développement, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, conteneurs
ms.openlocfilehash: 8d17be31acc6868399e8f785255d28fa2314d48c
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/21/2019
ms.locfileid: "58316114"
---
# <a name="how-azure-dev-spaces-works-and-is-configured"></a>Comment les espaces de développement Azure fonctionne et est configuré

Développement d’une application Kubernetes peut s’avérer difficile. Vous avez besoin des fichiers de configuration Docker et Kubernetes. Vous devez déterminer comment tester votre application localement et d’interagir avec d’autres services dépendants. Vous devrez peut-être gérer le développement et le test sur plusieurs services simultanément et avec une équipe de développeurs.

Les espaces de développement Azure vous permettent de développer, déployer et déboguer des applications Kubernetes directement dans Azure Kubernetes Service (ACS). Les espaces de développement Azure permet également une équipe de partager un espace de développement. Partager un espace de développement dans une équipe permet à chacun de ses membres développer de manière isolée sans avoir à répliquer ou simuler des dépendances ou d’autres applications dans le cluster.

Les espaces de développement Azure crée et utilise un fichier de configuration pour le déploiement, en cours d’exécution et débogage de vos applications Kubernetes dans ACS. Ce fichier de configuration sont hébergés par le code de votre application et peut être ajouté à votre système de contrôle de version.

Cet article décrit les processus qu’espaces de développement power Azure et comment ces processus sont configurées dans le fichier de configuration des espaces de développement Azure. Pour obtenir des espaces de développement Azure en cours d’exécution rapide et pratique, effectuez une des Démarrages rapides :

* [Java avec l’interface CLI et Visual Studio Code](quickstart-java.md)
* [.NET core avec Visual Studio Code et CLI](quickstart-netcore.md)
* [.NET core avec Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js avec CLI et Visual Studio Code](quickstart-nodejs.md)

## <a name="how-azure-dev-spaces-works"></a>Fonctionnement des espaces de développement Azure

Les espaces de développement Azure comprend deux composants distincts que vous interagissez avec : le contrôleur et les outils côté client.

![Composants d’espaces de développement Azure](media/how-dev-spaces-works/components.svg)

Le contrôleur effectue les actions suivantes :

* Gère la sélection et création d’espace de développement.
* Installe le graphique de Helm de votre application et crée les objets Kubernetes.
* Génère l’image de conteneur de votre application.
* Déploie votre application sur AKS.
* Effectue des builds incrémentielles et redémarre lorsque votre code source est modifié.
* Gère les journaux et traces HTTP.
* Transfère stdout et stderr pour les outils côté client.
* Permet aux membres d’équipe créer des espaces de développement enfant dérivés d’un espace de développement parent.
* Configure le routage pour les applications au sein d’un espace, ainsi que sur les espaces de parents et enfants.

Le contrôleur se trouve en dehors de AKS. Ils influencent le comportement et la communication entre les outils côté client et le cluster AKS. Le contrôleur est activé à l’aide de l’interface CLI Azure lorsque vous préparez votre cluster pour utiliser les espaces de développement Azure. Une fois qu’elle est activée, vous pouvez interagir avec lui à l’aide d’outils côté client.

Les outils côté client permet à l’utilisateur :
* Générer un fichier Dockerfile, le graphique Helm et fichier de configuration des espaces de développement Azure pour l’application.
* Créer des espaces de développement parents et enfants.
* Indiquer le contrôleur pour générer et lancer votre application.

Pendant que votre application est en cours d’exécution, la côté client des outils également :
* Reçoit et affiche les stdout et stderr à partir de votre application en cours d’exécution dans ACS.
* Utilise [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) pour autoriser l’accès web à votre application à l’aide de http :\//localhost.
* Joint un débogueur à votre application en cours d’exécution dans ACS.
* Les synchronisations du code source pour votre espace de développement lorsqu’une modification est détectée pour les builds incrémentielles, ce qui permet une itération rapide.

Vous pouvez utiliser la côté client des outils à partir de la ligne de commande dans le cadre de la `azds` commande. Vous pouvez également utiliser du côté client avec des outils :

* Visual Studio Code en utilisant le [extension d’Azure Dev espaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio 2017 avec [Visual Studio Tools pour Kubernetes](https://aka.ms/get-vsk8stools).

Voici le flux de base pour la configuration et l’utilisation des espaces de développement Azure :
1. Préparer votre cluster AKS pour les espaces de développement Azure
1. Préparer votre code en cours d’exécution sur les espaces de développement Azure
1. Exécuter votre code sur un espace de développement
1. Déboguer votre code sur un espace de développement
1. Partager un espace de développement

Nous aborderons plus de détails du fonctionnement des espaces de développement Azure dans chacun des sections suivantes.

## <a name="prepare-your-aks-cluster"></a>Préparer votre cluster AKS

La préparation de votre cluster AKS implique :
* Vérification de votre ACS cluster se trouve dans une région [pris en charge par des espaces de développement Azure](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).
* Vérification de Kubernetes 1.10.3 sont en cours d’exécution ou une version ultérieure.
* L’activation des espaces de développement Azure sur votre cluster à l’aide `az aks use-dev-spaces`

Pour plus d’informations sur la façon de créer et configurer un cluster ACS pour les espaces de développement Azure, consultez un des guides de prise en main mise en route :
* [Prise en main sur les espaces de développement Azure avec Java](get-started-java.md)
* [Prise en main sur les espaces de développement Azure avec .NET Core et Visual Studio](get-started-netcore-visualstudio.md)
* [Prise en main sur les espaces de développement Azure avec .NET Core](get-started-netcore.md)
* [Prise en main sur les espaces de développement Azure avec Node.js](get-started-nodejs.md)

Lorsque Azure Dev espaces est activée sur votre cluster AKS, il installe le contrôleur de votre cluster. Le contrôleur est une ressource Azure distincte en dehors de votre cluster et effectue les opérations suivantes pour les ressources de votre cluster :

* Crée ou désigne un espace de noms Kubernetes à utiliser comme un espace de développement.
* Supprime tout espace de noms Kubernetes nommé *azds*, si elle existe et crée un nouveau.
* Déploie un objet de l’initialiseur Kubernetes.

! [Azure Dev espaces préparer cluster]] (media/how-dev-spaces-works/prepare-cluster.svg)

Pour pouvoir utiliser les espaces de développement Azure, il doit y avoir au moins un espace de développement. Les espaces de développement Azure utilise des espaces de noms Kubernetes dans votre cluster AKS pour les espaces de développement. Lorsqu’un contrôleur est en cours d’installation, il vous invite à créer un nouvel espace de noms Kubernetes ou choisissez un espace de noms existant à utiliser comme votre premier espace de développement. Lorsqu’un espace de noms est désigné comme un espace de développement, le contrôleur ajoute le *azds.io/space=true* étiquette à cet espace de noms pour l’identifier comme un espace de développement. L’espace de développement initial, vous créez ou désignez est sélectionnée par défaut après avoir préparé votre cluster. Lorsqu’un espace est sélectionné, il est utilisé par des espaces de développement Azure pour la création de nouvelles charges de travail.

Par défaut, le contrôleur crée un espace de développement nommé *par défaut* en mettant à niveau existantes *par défaut* espace de noms Kubernetes. Vous pouvez utiliser les outils côté client pour créer de nouveaux espaces de développement et supprimer les espaces de développement existant. En raison d’une limitation dans Kubernetes, le *par défaut* dev espace ne peut pas être supprimé. Le contrôleur supprime également les espaces de noms Kubernetes existant nommé *azds* pour éviter les conflits avec la `azds` commande utilisée par les outils côté client.

L’objet de l’initialiseur de Kubernetes est utilisé pour injecter des pods avec trois conteneurs au cours du déploiement pour l’instrumentation : un proxy devspaces conteneur, un conteneur devspaces-proxy-init et un conteneur devspaces-build. **Trois de ces conteneurs exécuter avec l’accès racine sur votre cluster AKS.**

![Azure Dev espaces Kubernetes initialiseur](media/how-dev-spaces-works/kubernetes-initializer.svg)

Le conteneur de devspaces-proxy est un conteneur side-car qui gère tout le trafic TCP vers et depuis le conteneur d’application et permet d’effectuer le routage. Le conteneur devspaces-proxy redirige les messages HTTP si certains espaces sont utilisées. Par exemple, il peut aider à acheminer les messages HTTP entre les applications dans des espaces parents et enfants. Tout le trafic non HTTP traverse devspaces-proxy tels quels. Le conteneur de proxy devspaces également journalise tous les messages HTTP entrants et sortants et les envoie au côté client des outils en tant que traces. Ces traces peuvent ensuite être affichés par le développeur pour inspecter le comportement de l’application.

Le conteneur devspaces-proxy-init est un [init conteneur](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) qui ajoute des règles de routage supplémentaires en fonction de la hiérarchie d’espace au conteneur de votre application. Il ajoute des règles de routage en mettant à jour le conteneur application */etc/resolv.conf* iptables de fichiers et de configuration avant de démarrer. Les mises à jour */etc/resolv.conf* autoriser pour la résolution DNS des services dans les espaces de parent. Les mises à jour de la configuration iptables Assurez-vous que tout le trafic TCP dans et en dehors du conteneur de l’application sont acheminés via devspaces-proxy. Toutes les mises à jour à partir de devspaces-proxy-init se produisent en plus des règles qui ajoute de Kubernetes.

Le conteneur de build de devspaces est un conteneur d’init et a le code source du projet et le socket Docker monté. Le code source du projet et l’accès à Docker permet au conteneur de l’application être généré directement par le pod.

> [!NOTE]
> Les espaces de développement Azure utilise le même nœud pour créer le conteneur de votre application et l’exécuter. Par conséquent, un Registre de conteneur externe des espaces de développement Azure n’a pas besoin pour générer et exécuter votre application.

L’objet de l’initialiseur de Kubernetes écoute pour n’importe quel nouveau pod qui est créé dans le cluster AKS. Si ce pod est déployé sur n’importe quel espace de noms avec la *azds.io/space=true* étiquette, il injecte ce pod avec les conteneurs supplémentaires. Le conteneur de build de devspaces est injecté uniquement si le conteneur de l’application est exécutée à l’aide d’outils côté client.

Une fois que vous avez préparé votre cluster AKS, vous pouvez utiliser les outils côté client pour préparer et exécuter votre code dans votre espace de développement.

## <a name="prepare-your-code"></a>Préparer votre code

Pour exécuter votre application dans un espace de développement, il doit être mis en conteneur, et vous devez définir comment il doit être déployé dans Kubernetes. Pour mettre en conteneur de votre application, vous avez besoin d’un fichier Dockerfile. Pour définir la façon dont votre application est déployée sur Kubernetes, vous devez un [graphique Helm](https://docs.helm.sh/). Pour vous aider à créer un graphique à la fois le fichier Dockerfile et Helm pour votre application, fournissent les outils côté client le `prep` commande :

```cmd
azds prep --public
```

Le `prep` commande examine les fichiers dans votre projet et essayez de créer le fichier Dockerfile et Helm le graphique pour l’exécution de votre application dans Kubernetes. Actuellement, le `prep` commande génère un fichier Dockerfile et Helm le graphique avec les langues suivantes :

* Java
* Node.js
* .NET Core

Vous *doit* exécuter la `prep` commande à partir d’un répertoire qui contient le code source. En cours d’exécution le `prep` commande à partir du répertoire correct permet les outils côté client identifier la langue et de créer un fichier Dockerfile approprié pour conteneuriser votre application. Vous pouvez également exécuter la `prep` commande à partir d’un répertoire qui contient un *pom.xml* fichier pour les projets Java.

Si vous exécutez le `prep` commande à partir du répertoire qui ne contient-elle pas de code source, les outils côté client ne générera pas d’un fichier Dockerfile. Il affiche également un message d’erreur indiquant : *Fichier Dockerfile n’a pas pu être généré en raison d’une langue non prise en charge*. Cette erreur se produit également si les outils côté client ne reconnaissent pas le type de projet.

Lorsque vous exécutez le `prep` commande, vous avez la possibilité de spécifier le `--public` indicateur. Cet indicateur signale le contrôleur pour créer un point de terminaison accessible via internet pour ce service. Si vous ne spécifiez pas cet indicateur, le service est uniquement accessible à partir du cluster ou à l’aide du tunnel localhost créés par les outils côté client. Vous pouvez activer ou désactiver ce comportement après l’exécution du `prep` commande en mettant à jour le graphique Helm généré.

Le `prep` commande ne remplacera pas les graphiques de fichiers Dockerfile ou Helm existants vous avez dans votre projet. Si un fichier Dockerfile ou Helm graphique utilise la même convention d’affectation de noms comme les fichiers générés par le `prep` commande, le `prep` commande ignorera la génération de ces fichiers. Sinon, le `prep` commande génère son propre fichier Dockerfile ou Helm graphique côté les fichiers existants.

Le `prep` commande génère également un `azds.yaml` fichier à la racine de votre projet. Les espaces de développement Azure utilise ce fichier pour générer, installer, configurer et exécuter votre application. Ce fichier de configuration répertorie l’emplacement de votre graphique de fichier Dockerfile et Helm et fournit également une configuration supplémentaire sur ces artefacts.

Voici un exemple de fichier azds.yaml créé à l’aide [exemple d’application .NET Core](https://github.com/Azure/dev-spaces/tree/master/samples/dotnetcore/getting-started/webfrontend):

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

Le `azds.yaml` fichier généré par le `prep` commande devrait fonctionner sans problème pour un scénario de développement de projet simple et unique. Si votre projet spécifique a augmenté la complexité, vous devrez peut-être mettre à jour ce fichier après l’exécution du `prep` commande. Par exemple, votre projet peut nécessitent quelques réglages à votre build ou lancer le processus en fonction de votre développement ou le débogage doit. Vous pouvez également avoir plusieurs applications dans votre projet, qui nécessitent plusieurs processus de génération ou d’un contenu de build différente.

## <a name="run-your-code"></a>Exécuter votre code

Pour exécuter votre code dans un espace de développement, vous devez émettre la `up` commande dans le même répertoire que votre `azds.yaml` fichier :

```cmd
azds up
```

Le `up` commande télécharge les fichiers de source de votre application et d’autres artefacts nécessaires pour générer et exécuter votre projet à l’espace de développement. À partir de là, le contrôleur dans votre espace de développement :

1. Crée les objets Kubernetes pour déployer votre application.
1. Crée le conteneur pour votre application.
1. Déploie votre application sur le domaine du développement.
1. Crée un nom DNS accessible publiquement pour le point de terminaison de votre application si configuré.
1. Utilise *port-forward* pour fournir l’accès à votre point de terminaison d’application en utilisant http://locahost.
1. Transfère stdout et stderr pour les outils côté client.


### <a name="starting-a-service"></a>Démarrage d’un service

Lorsque vous démarrez un service dans un espace de développement, les outils de côté client et le contrôleur fonctionnent conjointement pour synchroniser vos fichiers sources, créer votre conteneur et les objets Kubernetes et exécuter votre application.

À un niveau plus granulaire, voici ce qui se passe lorsque vous exécutez `azds up`:

1. Les fichiers sont synchronisés à partir de l’ordinateur de l’utilisateur vers un stockage de fichiers Azure qui est unique pour le cluster AKS de l’utilisateur. Le code source, graphique Helm et fichiers de configuration sont chargés. Plus d’informations sur le processus de synchronisation sont disponibles dans la section suivante.
1. Le contrôleur crée une demande pour démarrer une nouvelle session. Cette requête contient plusieurs propriétés, y compris un ID unique, nom de l’espace, chemin d’accès au code source et un indicateur de débogage.
1. Le contrôleur remplace le *$(tag)* espace réservé dans le graphique Helm avec l’ID de session unique, puis installe le Helm du graphique pour votre service. Ajout de qu'une référence à l’ID de session unique pour le graphique Helm permet au conteneur déployé sur le cluster AKS pour cette session spécifique pour être lié à la demande de session et des informations associées.
1. Pendant l’installation du graphique Helm, l’objet de l’initialiseur de Kubernetes ajoute des conteneurs supplémentaires à pod de votre application pour l’instrumentation et l’accès au code source de votre projet. Le proxy de devspaces et devspaces-proxy-init conteneurs sont ajoutés à fournissent le suivi de HTTP et le routage de l’espace. Le conteneur de build de devspaces est ajouté pour fournir le pod avec accès à l’instance de Docker et d’un code source du projet pour la création de conteneur de votre application.
1. Au démarrage de pod de l’application, les conteneurs de build de devspaces et devspaces-proxy-init sont utilisés pour créer le conteneur d’application. Le conteneur d’applications et les conteneurs de devspaces-proxy sont ensuite démarrés.
1. Une fois que le conteneur de l’application a démarré, les fonctionnalités côté client utilisant le Kubernetes *port-forward* fonctionnalités pour fournir l’accès HTTP à votre application sur http://localhost. Ce réacheminement de port connecte à votre ordinateur de développement pour le service dans votre espace de développement.
1. Tous les conteneurs dans le pod ont démarré, le service est en cours d’exécution. À ce stade, les fonctionnalités côté client commencent à diffuser les traces HTTP, stdout et stderr. Ces informations sont affichées par les fonctionnalités côté client pour les développeurs.

### <a name="updating-a-running-service"></a>La mise à jour d’un service en cours d’exécution

Pendant l’exécution d’un service, les espaces de développement Azure a la possibilité de mettre à jour ce service, si un des fichiers de code source du projet change. Espaces de développement gère également la mise à jour le service différemment selon le type de fichier qui a changé. Il existe trois façons Qu'espaces de développement peuvent mettre à jour un service en cours d’exécution :

* Mise à jour directe d’un fichier
* La reconstruction et le redémarrage du processus de l’application à l’intérieur du conteneur de l’application en cours d’exécution
* Régénérer et redéployer le conteneur de l’application

![Synchronisation de fichiers Azure Dev espaces](media/how-dev-spaces-works/file-sync.svg)

Certains fichiers projet qui sont des ressources statiques, tels que html, css et fichiers cshtml, peuvent être mis à jour directement dans le conteneur de l’application sans avoir à redémarrer quoi que ce soit. Si une ressource statique change, le nouveau fichier est synchronisé avec le domaine du développement et ensuite utilisé par le conteneur en cours d’exécution.

Modifications apportées aux fichiers de code source ou de fichiers de configuration d’application peuvent être appliquées en redémarrant le processus de l’application au sein du conteneur en cours d’exécution. Une fois que ces fichiers sont synchronisés, le processus d’application est redémarré dans le conteneur en cours d’exécution à l’aide du *devhostagent* processus. Lorsque vous créez initialement conteneur de l’application, le contrôleur remplace la commande de démarrage de l’application avec un autre processus appelé *devhostagent*. Processus réel de l’application est alors exécuté comme un processus enfant sous *devhostagent*, et ses sorties sont extraites à l’aide de *devhostagent*de sortie. Le *devhostagent* processus fait également partie des espaces de développement et peuvent exécuter des commandes dans le conteneur en cours d’exécution pour le compte des espaces de développement. Lorsque vous effectuez un redémarrage, *devhostagent*:

* Arrête le processus en cours ou processus associés à l’application
* Reconstruit l’application
* Redémarre l’ou les processus associés à l’application

La façon *devhostagent* exécute précédentes étapes est configuré dans le `azds.yaml` fichier de configuration. Cette configuration est détaillée dans une section ultérieure.

Mises à jour des fichiers de projet tels que les fichiers Dockerfile, les fichiers csproj ou n’importe quelle partie du graphique Helm nécessitent le conteneur de l’application soit régénérée et redéployé. Lorsqu’un de ces fichiers est synchronisé avec le domaine du développement, le contrôleur s’exécute le [mise à niveau helm](https://helm.sh/docs/helm/#helm-upgrade) commande et un conteneur de l’application est reconstruit et redéployé.

### <a name="file-synchronization"></a>Synchronisation de fichiers

La première fois qu’une application est démarrée dans un espace de développement, tous les fichiers de l’application source sont chargés. Pendant l’exécution de l’application et sur les redémarrages ultérieures, uniquement les fichiers modifiés sont téléchargés. Deux fichiers sont utilisés pour coordonner ce processus : un fichier côté client et un côté contrôleur.

Le fichier côté client est stocké dans un répertoire temporaire et est nommé en fonction du hachage du répertoire du projet en cours d’exécution dans les espaces de développement. Par exemple, sur Windows ont un fichier comme *Users\USERNAME\AppData\Local\Temp\1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef.synclog* pour votre projet. Sur Linux, le fichier côté client est stocké dans le */tmp* directory. Vous pouvez trouver le répertoire sur macOS en exécutant la `echo $TMPDIR` commande.

Ce fichier est au format JSON et contienne :

* Une entrée pour chaque fichier de projet qui est synchronisé avec le domaine du développement
* Un ID de synchronisation
* L’horodatage de la dernière opération de synchronisation

Chaque entrée du fichier projet contient un chemin d’accès au fichier et son horodatage.

Le fichier côté contrôleur est stocké sur le cluster AKS. Il contient l’ID de la synchronisation et l’horodatage de la dernière synchronisation.

Une synchronisation se produit lorsque les horodateurs de synchronisation ne correspondent pas entre la côté client et les fichiers côté contrôleur. Pendant une synchronisation, les outils côté client effectue une itération sur les entrées de fichier dans le fichier côté client. Si l’horodatage de ce fichier est après l’horodatage de synchronisation, ce fichier est synchronisé avec le domaine du développement. Une fois la synchronisation terminée, les horodateurs de synchronisation sont mis à jour sur les fichiers côté client et côté contrôleur.

Tous les fichiers projet sont synchronisées si le fichier côté client n’est pas présent. Ce comportement vous permet de forcer une synchronisation complète en supprimant le fichier côté client.

### <a name="how-routing-works"></a>Le fonctionnement du routage

Un espace de développement s’appuie sur AKS et utilise le même [mise en réseau des concepts](../aks/concepts-network.md). Les espaces de développement Azure a également un centralisé *ingressmanager* de service et déploie sa propre contrôleur d’entrée au cluster AKS. Le *ingressmanager* moniteurs AKS clusters avec des espaces de développement et augmente le contrôleur d’entrée Azure Dev espaces dans le cluster avec les objets d’entrée pour le routage avec les pods d’application de service. Le conteneur devspaces-proxy dans chaque pod ajoute un `azds-route-as` en-tête HTTP pour le trafic HTTP vers un espace de développement basé sur l’URL. Par exemple, une demande à l’URL *http://azureuser.s.default.serviceA.fedcba09...azds.io* obtiendriez un en-tête HTTP avec `azds-route-as: azureuser`. Le conteneur devspaces-proxy n’ajoutera pas un `azds-route-as` en-tête si une est déjà présente.

Lorsqu’une requête HTTP est effectuée pour un service à l’extérieur du cluster, la demande est transmise au contrôleur d’entrée. Le contrôleur d’entrée achemine la demande directement vers le pod approprié en fonction de ses règles et les objets d’entrée. Le conteneur devspaces-proxy dans le pod reçoit la demande, ajoute le `azds-route-as` en-tête basé sur l’URL, puis achemine la demande vers le conteneur d’application.

Lorsqu’une requête HTTP est effectuée à un service à partir d’un autre service au sein du cluster, la demande est d’abord envoyé via le conteneur de devspaces-proxy du service de l’appelant. Le conteneur devspaces-proxy examine la demande HTTP et les vérifications de la `azds-route-as` en-tête. En fonction de l’en-tête, le conteneur de proxy devspaces recherchera l’adresse IP du service associé à la valeur d’en-tête. Si une adresse IP est trouvée, le conteneur devspaces-proxy redirige la demande à cette adresse IP. Si une adresse IP n’est pas trouvée, le conteneur de proxy devspaces achemine la demande vers le conteneur d’application parent.

Par exemple, les applications *serviceA* et *serviceB* sont déployés sur un espace de développement parent appelé *par défaut*. *serviceA* s’appuie sur *serviceB* et effectue des appels HTTP à celle-ci. Utilisateur Azure crée un espace de développement enfant selon le *par défaut* espace appelé *azureuser*. Utilisateur Azure déploie également leur propre version de *serviceA* à leur espace enfant. Quand une demande est faite pour *http://azureuser.s.default.serviceA.fedcba09...azds.io*:

![Azure Dev espaces routage](media/how-dev-spaces-works/routing.svg)

1. Recherche l’adresse IP pour le pod associé à l’URL, ce qui est le contrôleur d’entrée *serviceA.azureuser*.
1. Le contrôleur d’entrée recherche l’adresse IP pour le pod dans l’espace de développement de l’utilisateur d’Azure et achemine la demande à la *serviceA.azureuser* pod.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod reçoit la demande et ajoute `azds-route-as: azureuser` comme en-tête HTTP.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod achemine la demande à la *serviceA* conteneur d’application dans le *serviceA.azureuser* pod.
1. Le *serviceA* application dans le *serviceA.azureuser* pod effectue un appel à *serviceB*. Le *serviceA* application contient également du code pour conserver l’existant `azds-route-as` en-tête, qui dans ce cas est `azds-route-as: azureuser`.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod reçoit la demande et recherche l’adresse IP de *serviceB* selon la valeur de la `azds-route-as` en-tête.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod ne trouve pas une adresse IP pour *serviceB.azureuser*.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod recherche l’adresse IP de *serviceB* dans l’espace de parent, qui est *serviceB.default*.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod recherche l’adresse IP de *serviceB.default* et achemine la demande à la *serviceB.default* pod.
1. Le conteneur devspaces-proxy dans le *serviceB.default* pod reçoit la demande et achemine la demande à la *serviceB* conteneur d’application dans le *serviceB.default*pod.
1. Le *serviceB* application dans le *serviceB.default* pod renvoie une réponse à la *serviceA.azureuser* pod.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod reçoit la réponse et achemine la réponse à la *serviceA* conteneur d’application dans le *serviceA.azureuser* pod.
1. Le *serviceA* application reçoit la réponse, puis retourne sa propre réponse.
1. Le conteneur devspaces-proxy dans le *serviceA.azureuser* pod reçoit la réponse à partir de la *serviceA* conteneur d’application et l’achemine la réponse à l’appelant d’origine en dehors du cluster.

Tout autre trafic TCP qui n’est pas HTTP passe par le contrôleur d’entrée et les conteneurs devspaces-proxy tels quels.

### <a name="how-running-your-code-is-configured"></a>Comment votre code en cours d’exécution est configuré

Les espaces de développement Azure utilise le `azds.yaml` fichier pour installer et configurer votre service. Le contrôleur emploie le `install` propriété dans le `azds.yaml` fichier pour installer le graphique Helm et créer les objets Kubernetes :

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
      - $(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)
...
```

Par défaut, le `prep` commande génère le graphique Helm. Il définit également la *install.chart* propriété dans le répertoire du graphique Helm. Si vous souhaitez utiliser un graphique Helm dans un autre emplacement, vous pouvez mettre à jour cette propriété pour utiliser cet emplacement.

Lorsque vous installez les graphiques Helm, espaces de développement Azure fournit un moyen pour remplacer des valeurs dans le graphique Helm. Les valeurs par défaut pour le graphique Helm sont dans `charts/APP_NAME/values.yaml`.

À l’aide de la *install.values* propriété, vous pouvez répertorier un ou plusieurs fichiers qui définissent les valeurs souhaitées remplacé dans le graphique Helm. Par exemple, si vous souhaitiez une configuration de nom d’hôte ou de la base de données spécifiquement lorsque vous exécutez votre application dans un espace de développement, vous pouvez utiliser cette fonctionnalité de remplacement. Vous pouvez également ajouter un *?* à la fin d’un des noms de fichier pour le définir comme étant facultatifs.

Le *install.set* propriété vous permet de configurer une ou plusieurs valeurs souhaitées remplacé dans le graphique Helm. Toutes les valeurs configurées dans *install.set* remplacera les valeurs configurées dans les fichiers répertoriés dans *install.values*. Les propriétés sous *install.set* sont dépendantes sur les valeurs dans le graphique Helm et peut être différent selon le graphique Helm généré.

Dans l’exemple ci-dessus, le *install.set.replicaCount* propriété indique le contrôleur de combien d’instances de votre application s’exécute dans votre espace de développement. Selon votre scénario, vous pouvez augmenter cette valeur, mais il aura un impact sur l’attachement d’un débogueur sur les pod de votre application. Pour plus d’informations, consultez le [article sur le dépannage](troubleshooting.md).

Dans le graphique Helm généré, l’image de conteneur est définie sur *{{. Values.image.Repository}}  :{{. Values.image.Tag}}*. Le `azds.yaml` fichier définit *install.set.image.tag* propriété en tant que *$(tag)* par défaut, qui est utilisé comme valeur pour *{{. Values.image.Tag}}*. En définissant le *install.set.image.tag* propriété de cette façon, il permet l’image de conteneur pour votre application doit être référencé de manière distincte lors de l’exécution des espaces de développement Azure. Dans ce cas précis, l’image est marqué comme  *<value from image.repository>: $(tag)*. Vous devez utiliser le *$(tag)* variable comme valeur de *install.set.image.tag* dans l’ordre pour les espaces de développement reconnaître et de trouver le conteneur dans le cluster AKS.

Dans l’exemple ci-dessus, `azds.yaml` définit *install.set.ingress.hosts*. Le *install.set.ingress.hosts* propriété définit un format de nom d’hôte pour les points de terminaison publics. Cette propriété utilise également *$(spacePrefix)*, *$(rootSpacePrefix)*, et *$(hostSuffix)*, qui sont des valeurs fournies par le contrôleur. 

Le *$(spacePrefix)* est le nom de l’espace de développement enfant, qui prend la forme de *SPACENAME.s*. Le *$(rootSpacePrefix)* est le nom de l’espace de parent. Par exemple, si *azureuser* est un espace enfant de *par défaut*, la valeur de *$(rootSpacePrefix)* est *par défaut* et la valeur de *$(spacePrefix)* est *azureuser.s*. Si l’espace n’est pas un espace enfant, *$(spacePrefix)* est vide. Par exemple, si le *par défaut* espace n’a parent d’espace, la valeur de *$(rootSpacePrefix)* est *par défaut* et la valeur de *$(spacePrefix)* est vide. Le *$(hostSuffix)* est un suffixe DNS qui pointe vers le contrôleur d’entrée Azure Dev espaces qui s’exécute dans votre cluster AKS. Ce suffixe DNS correspond à une entrée DNS générique, par exemple  *\*. RANDOM_VALUE.eus.azds.IO*, qui a été créé lorsque le contrôleur d’espaces de développement Azure a été ajouté à votre cluster AKS.

Dans l’exemple ci-dessus `azds.yaml` fichier, vous pouvez également mettre à jour *install.set.ingress.hosts* pour modifier le nom d’hôte de votre application. Par exemple, si vous souhaitez simplifier le nom d’hôte de votre application à partir de *$(spacePrefix)$(rootSpacePrefix)webfrontend$(hostSuffix)* à *$(spacePrefix)$(rootSpacePrefix)web$(hostSuffix)*.

Pour créer le conteneur de votre application, le contrôleur utilise le dessous des sections de la `azds.yaml` fichier de configuration :

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

Le contrôleur utilise un fichier Dockerfile pour générer et exécuter votre application.

Le *build.context* propriété répertorie le répertoire où les fichiers Dockerfile existe. Le *build.dockerfile* propriété définit le nom du fichier Dockerfile pour la création de la version de production de l’application. Le *configurations.develop.build.dockerfile* propriété configure le nom du fichier Dockerfile pour la version de développement de l’application.

Différents fichiers Dockerfile pour le développement et de production permet activer certaines choses pendant le développement et la désactivation de ces éléments pour les déploiements de production. Par exemple, vous pouvez activer une journalisation plus détaillée pendant le développement et les désactiver dans un environnement de production ou débogage. Vous pouvez également mettre à jour ces propriétés si vos fichiers Dockerfile est nommés différemment ou est dans un autre emplacement.

Pour vous aider à rapidement effectuer une itération au cours du développement, les espaces de développement Azure sera synchroniser les modifications à partir de votre projet local et mise à jour incrémentielle de votre application. La section dans ci-dessous le `azds.yaml` fichier de configuration est utilisé pour configurer la synchronisation et de mettre à jour :

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

Les fichiers et répertoires qui seront synchronise les modifications sont répertoriées dans le *configurations.develop.container.sync* propriété. Ces répertoires ont été synchronisés au départ lorsque vous exécutez le `up` commande, ainsi que lorsque les modifications sont détectées. S’il existe des répertoires supplémentaires ou différents que vous aimeriez synchronisés dans votre espace de développement, vous pouvez modifier cette propriété.

Le *configurations.develop.container.iterate.buildCommands* propriété spécifie la manière de générer l’application dans un scénario de développement. Le *configurations.develop.container.command* propriété fournit la commande pour l’exécution de l’application dans un scénario de développement. Vous pouvez mettre à jour une de ces propriétés s’il existe des indicateurs supplémentaires de génération ou d’exécution ou les paramètres que vous souhaitez utiliser au cours du développement.

Le *configurations.develop.container.iterate.processesToKill* répertorie les processus à arrêter pour arrêter l’application. Vous pouvez mettre à jour cette propriété si vous souhaitez modifier le comportement de redémarrage de votre application pendant le développement. Par exemple, si vous mis à jour le *configurations.develop.container.iterate.buildCommands* ou *configurations.develop.container.command* propriétés pour modifier la façon dont l’application est générée ou démarré, vous devrez peut-être modifier les processus sont arrêtés.

Lors de la préparation de votre code en utilisant le `azds prep` commande, vous avez la possibilité d’ajouter le `--public` indicateur. Ajout de la `--public` indicateur crée une URL accessible publiquement pour votre application. Si vous omettez cet indicateur, l’application est uniquement accessible dans le cluster ou à l’aide du tunnel de localhost. Après avoir exécuté le `azds prep` commande, vous pouvez modifier ce paramètre modifiant le *ingress.enabled* propriété dans `charts/APPNAME/values.yaml`:

```yaml
ingress:
  enabled: true
```

## <a name="debug-your-code"></a>Déboguer votre code

Pour les applications Java, .NET et Node.js, vous pouvez déboguer votre application s’exécutant directement dans votre espace de développement à l’aide de Visual Studio Code ou Visual Studio 2017. Visual Studio Code et Visual Studio 2017 fournissent des outils pour vous connecter à votre espace de développement, lancez votre application et attacher un débogueur. Après l’exécution `azds prep`, vous pouvez ouvrir votre projet dans Visual Studio Code ou Visual Studio 2017. Visual Studio Code ou Visual Studio 2017 génère leurs propres fichiers de configuration pour la connexion en cours d’exécution indépendant `azds prep`. À partir de dans Visual Studio Code ou Visual Studio 2017, vous pouvez définir des points d’arrêt et lancer votre application dans votre espace de développement.

![Débogage de votre code](media/get-started-node/debug-configuration-nodejs2.png)

Lorsque vous lancez votre application à l’aide de Visual Studio Code ou Visual Studio 2017 pour le débogage, ils gèrent de lancement et la connexion à votre espace de développement de la même façon que l’exécution `azds up`. Les outils côté client dans le Code de Visual Studio et Visual Studio 2017 fournissent également un paramètre supplémentaire avec des informations spécifiques pour le débogage. Le paramètre contient le nom de l’image de débogueur, l’emplacement du débogueur dans l’image du débogueur et l’emplacement de destination au sein du conteneur de l’application pour monter le dossier de débogueur. 

L’image de débogueur est automatiquement déterminée par les outils côté client. Il utilise une méthode similaire à celui utilisé pendant le fichier Dockerfile et générer un graphique Helm lors de l’exécution `azds prep`. Une fois le débogueur est monté dans l’image de l’application, il est exécuté à l’aide de `azds exec`.

## <a name="sharing-a-dev-space"></a>Partager un espace de développement

Lorsque vous travaillez avec une équipe, vous pouvez [partager un espace de développement entre une équipe entière](how-to/share-dev-spaces.md) et créer des espaces de développement dérivée. Un espace de développement peut être utilisé par toute personne ayant accès collaborateur au groupe de ressources de l’espace de développement.

Vous pouvez également créer un nouvel espace de développement qui est dérivé d’un autre espace de développement. Lorsque vous créez un espace de développement dérivée la *azds.io/parent-espace = nom de l’espace PARENT* étiquette est ajoutée à l’espace de noms de l’espace de développement dérivée. En outre, toutes les applications à partir de l’espace de développement parent sont partagées avec le domaine du développement dérivée. Si vous déployez une version mise à jour d’une application à l’espace de développement dérivée, il existe uniquement dans le domaine du développement dérivée et le domaine du développement parent n’est pas affecté. Vous pouvez avoir un maximum de trois niveaux d’espaces de développement dérivée ou *grand-parent* espaces.

L’espace de développement dérivée sera également intelligemment acheminer les demandes entre ses propres applications et les applications partagées à partir de son parent. Le fonctionnement du routage par une tentative de demande d’itinéraire à une application dans le domaine du développement dérivée et revenir à l’application partagée à partir de l’espace de développement parent. Le routage se tourne vers l’application partagée dans l’espace grand-parent si l’application n’est pas dans l’espace de parent.

Par exemple : 
* L’espace de développement *par défaut* possède des applications *serviceA* et *serviceB* .
* L’espace de développement *azureuser* est dérivée de *par défaut*.
* Une version mise à jour de *serviceA* est déployée sur *azureuser*.

Lorsque vous utilisez *azureuser*, toutes les demandes à *serviceA* sera routé vers la version mise à jour dans *azureuser*. Une demande de *serviceB* essaie d’abord être acheminés vers le *azureuser* version de *serviceB*. Dans la mesure où il n’existe pas, il sera routé vers le *par défaut* version de *serviceB*. Si le *azureuser* version de *serviceA* est supprimé, toutes les demandes à *serviceA* reviendra à l’aide du *par défaut* version de *serviceA*.

## <a name="next-steps"></a>Étapes suivantes

Pour commencer à l’aide d’espaces de développement Azure, consultez les Démarrages rapides suivants :

* [Java avec l’interface CLI et Visual Studio Code](quickstart-java.md)
* [.NET core avec Visual Studio Code et CLI](quickstart-netcore.md)
* [.NET core avec Visual Studio 2017](quickstart-netcore-visualstudio.md)
* [Node.js avec CLI et Visual Studio Code](quickstart-nodejs.md)

Pour vous familiariser avec le développement en équipe, consultez les articles de procédures suivants :

* [Développement en équipe - Java avec l’interface CLI et Visual Studio Code](team-development-java.md)
* [Développement en équipe - .NET Core avec Visual Studio Code et CLI](team-development-netcore.md)
* [Développement en équipe - .NET Core avec Visual Studio 2017](team-development-netcore-visualstudio.md)
* [Développement en équipe - Node.js avec CLI et Visual Studio Code](team-development-nodejs.md)
