---
title: Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)
description: Découvrez les composants de charge de travail et de cluster de base de Kubernetes et leur lien avec les fonctionnalités d’AKS (Azure Kubernetes Service)
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105933"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Concepts de base de Kubernetes pour AKS (Azure Kubernetes Service)

Le développement d’applications continue à évoluer vers une approche basée sur les conteneurs, ce qui augmente le besoin d’orchestrer et de gérer les ressources. En tant que plateforme de pointe, Kubernetes offre une planification fiable des charges de travail d’application tolérantes aux pannes. AKS (Azure Kubernetes Service) est une offre Kubernetes managés qui simplifie la gestion et le déploiement des applications basées sur les conteneurs.

Cet article présente :
* Composants principaux de l’infrastructure Kubernetes :
    * *Plan de contrôle*
    * *Nœuds*
    * *Pools de nœuds*
* Ressources de la charge de travail : 
    * *Pods*
    * *deployments*
    * *Jeux* 
* Comment regrouper des ressources dans des *espaces de noms*.

## <a name="what-is-kubernetes"></a>Présentation de Kubernetes

Kubernetes est une plateforme évoluant rapidement qui gère les applications basées sur les conteneurs, ainsi que leurs composants de mise en réseau et de stockage. Kubernetes se concentre sur les charges de travail d’application, pas sur les composants de l’infrastructure sous-jacente. Kubernetes fournit une approche déclarative des déploiements, assortie d’un ensemble robuste d’API pour les opérations de gestion.

Vous pouvez générer et exécuter des applications modernes, portables et basées sur des microservices avec l’orchestration et la gestion par Kubernetes de la disponibilité des composants d’application. Kubernetes prend en charge les applications sans état et avec état à mesure que les équipes adoptent les applications basées sur des microservices.

En tant que plateforme ouverte, Kubernetes vous permet de créer des applications avec vos langages de programmation, système d’exploitation, bibliothèques ou bus de messagerie préférés. Les outils d’intégration et de livraison continues (CI/CD) existants peuvent s’intégrer à Kubernetes dans le cadre de la planification et du déploiement de versions.

AKS fournit un service Kubernetes géré qui réduit la complexité des tâches de déploiement et de gestion de base, comme la coordination de la mise à niveau. La plateforme Azure gère le plan de contrôle AKS et vous ne payez que pour les nœuds AKS qui exécutent vos applications. AKS repose sur le moteur open source Azure Kubernetes Service : [aks-engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Architecture d’un cluster Kubernetes

Un cluster Kubernetes comprend deux composants :

- *Plan de contrôle* : fournit les services Kubernetes de base et l’orchestration des charges de travail d’applications.
- *Nœuds* : exécutent vos charges de travail d’application.

![Plan de contrôle et composants de nœud Kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Plan de contrôle

Quand vous créez un cluster AKS, un plan de contrôle est automatiquement créé et configuré. Ce plan de contrôle est fourni gratuitement en tant que ressource Azure managée tirée de l’utilisateur. Vous payez uniquement pour les nœuds attachés au cluster AKS. Le plan de contrôle et ses ressources se trouvent uniquement sur la région dans laquelle le cluster a été créé.

Le plan de contrôle inclut les composants Kubernetes principaux suivants :

| Composant | Description |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | Le serveur d’API détermine comment les API Kubernetes sous-jacentes sont exposées. Ce composant fournit l’interaction des outils de gestion, tels que `kubectl` ou le tableau de bord Kubernetes.                                                        |  
| *etcd* | Pour maintenir l’état de la configuration et du cluster Kubernetes, le composant *etcd* hautement disponible est un magasin de valeurs essentiel dans Kubernetes.                                      |  
| *kube-scheduler*                                                                            | Quand vous créez ou mettez à l’échelle des applications, le planificateur détermine les nœuds pouvant exécuter la charge de travail et les démarre.                                                                                    |  
| *kube-controller-manager*                                                                            | Le gestionnaire de contrôleurs surveille une série de contrôleurs plus petits qui effectuent des actions telles que la réplication des pods et la gestion des opérations sur les nœuds.                                                                  |  

AKS fournit un plan de contrôle monolocataire doté de dispositifs dédiés (serveur d’API, Scheduler, etc.). Vous définissez le nombre et la taille des nœuds, puis la plateforme Azure configure la communication sécurisée entre les nœuds et le plan de contrôle. L’interaction avec le plan de contrôle se produit par le biais d’API Kubernetes, telles que `kubectl` ou le tableau de bord Kubernetes.

Vous n’avez pas besoin de configurer des composants tels qu’un magasin *etcd* hautement disponible avec ce plan de contrôle managé, mais ce plan n’est pas accessible directement. Le plan de contrôle Kubernetes et les mises à niveau des nœuds sont orchestrés via Azure CLI ou le portail Azure. Pour résoudre les problèmes éventuels, vous pouvez consulter les journaux d’activité du plan de contrôle par le biais des journaux d’activité Azure Monitor.

Pour configurer un plan de contrôle ou y accéder directement, déployez votre propre cluster Kubernetes à l’aide de [aks-engine][aks-engine].

Pour connaître les meilleures pratiques associées, consultez [Meilleures pratiques relatives aux mises à jour et à la sécurité du cluster dans AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Nœuds et pools de nœuds

Pour exécuter vos applications et les services de prise en charge, vous avez besoin d’un *nœud* Kubernetes. Un cluster AKS comporte au moins un nœud, une machine virtuelle exécutant les composants des nœuds Kubernetes et le runtime de conteneur.

| Composant | Description |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | L’agent Kubernetes qui traite les requêtes d’orchestration du plan de contrôle et la planification de l’exécution des conteneurs demandés.                                                        |  
| *kube-proxy* | Gère la mise en réseau virtuelle sur chaque nœud. Le proxy route le trafic réseau et gère l’adressage IP pour les services et les pods.                                      |  
| *Runtime de conteneur*                                                                            | Permet aux applications en conteneur de s’exécuter et d’interagir avec d’autres ressources telles que le réseau virtuel et le stockage. Les clusters AKS utilisant des pools de nœuds Kubernetes version 1.19 utilisent `containerd` comme runtime de conteneur. Les clusters AKS utilisant une version de Kubernetes antérieure au pool de nœuds 1.19 pour les pools de nœuds utilisent [Moby](https://mobyproject.org/) (Docker en amont) comme runtime de conteneur.                                                                                    |  


![Ressources des machines virtuelles Azure et de prise en charge pour un nœud Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

La taille des machines virtuelles Azure pour vos nœuds détermine les UC, la mémoire, la taille et le type de stockage disponible (par exemple, SSD hautes performances ou HDD classique). Planifiez la taille du nœud pour déterminer si vos applications peuvent nécessiter une grande quantité d’UC et de mémoire ou un stockage hautes performances. Augmentez le nombre de nœuds dans votre cluster AKS afin de répondre à la demande.

Dans AKS, l’image de machine virtuelle pour les nœuds de votre cluster est basée sur Ubuntu Linux ou sur Windows Server 2019. Quand vous créez un cluster AKS ou augmentez le nombre de nœuds, la plateforme Azure crée et configure automatiquement le nombre demandé de machines virtuelles. Les nœuds agent étant facturés en tant que machines virtuelles standard, les remises dont vous bénéficiez sur la taille de machine virtuelle que vous utilisez (y compris les [réservations Azure][reservation-discounts]) sont automatiquement appliquées.

Déployez votre propre cluster Kubernetes avec [aks-engine][aks-engine] si vous utilisez un autre système d’exploitation hôte, un autre runtime de conteneur ou d’autres packages personnalisés. Les versions en amont des fonctionnalités `aks-engine` fournissent des options de configuration avant la prise en charge des clusters AKS. Si vous souhaitez utiliser un runtime de conteneur autre que `containerd` ou [Moby](https://mobyproject.org/), vous pouvez exécuter `aks-engine` pour configurer et déployer un cluster Kubernetes qui répond à vos besoins actuels.

### <a name="resource-reservations"></a>Réservations de ressources

AKS utilise des ressources de nœud pour aider la fonction de nœud dans le cadre de votre cluster. Cette utilisation peut créer un écart entre les ressources totales du nœud et les ressources allouables dans AKS. Souvenez-vous de cette information au moment de définir les demandes et les limites des pods déployés par l’utilisateur.

Pour rechercher les ressources allouables d’un nœud, exécutez :
```kubectl
kubectl describe node [NODE_NAME]
```

Pour conserver les fonctionnalités et les performances des nœuds, AKS réserve des ressources sur chaque nœud. Lorsque le nœud gagne en taille dans les ressources, la réservation de ressources augmente en raison d’une plus grande quantité de pods déployés par l’utilisateur et nécessitant une gestion.

>[!NOTE]
> L’utilisation de modules complémentaires AKS tels que Container Insights (OMS) nécessite des ressources de nœud supplémentaires.

Deux types de ressources sont réservés :

- **UC**  
    L’UC réservé dépend du type de nœud et de la configuration du cluster, ce qui peut le rendre moins allouable en raison de l’exécution de fonctionnalités supplémentaires.

   | Cœurs de processeur sur l’hôte | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Réservés par Kube (millicores)|60|100|140|180|260|420|740|

- **Mémoire**  
    La mémoire utilisée par AKS comprend la somme de deux valeurs.

   1. **Démon `kubelet`**    
       Le démon `kubelet` est installé sur tous les nœuds de l’agent Kubernetes pour gérer la création et l’arrêt du conteneur. 
   
        Par défaut sur AKS, le démon `kubelet` a la règle d’éviction suivante : *memory.available<750Mi*, ce qui signifie qu’un nœud doit toujours avoir au moins 750 Mi allouable à tout moment. Quand un ordinateur hôte se trouve au-dessous du seuil de mémoire disponible, `kubelet` déclenche l’arrêt d’un des modules en cours d’exécution et libère de la mémoire sur l’ordinateur hôte.

   2. **Une vitesse régressive des réservations de la mémoire** pour que le démon kubelet fonctionne correctement (*kube-reserved*).
      - 25 % des 4 premiers Go de mémoire
      - 20 % des 4 Go suivants de mémoire (jusqu’à 8 Go)
      - 10 % des 8 Go suivants de mémoire (jusqu’à 16 Go)
      - 6 % des 112 Go suivants de mémoire (jusqu’à 128 Go)
      - 2 % de la mémoire au-dessus de 128 Go

Règles d’allocation de mémoire et d’UC :
* Maintenez l’intégrité des nœuds d’agent, avec quelques blocs de système d’hébergement critiques pour l’intégrité du cluster. 
* Ces règles font en sorte que le nœud signale moins de mémoire allouée et d’UC qu’il ne le ferait s’il faisait partie d’un cluster Kubernetes. 

Vous ne pouvez pas changer les réservations de ressources ci-dessus.

Par exemple, si un nœud offre 7 Go, il signalera 34 % de la mémoire non-allouable incluant le seuil d’éviction dur de 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

En plus des réservations pour Kubernetes lui-même, le système d’exploitation du nœud sous-jacent réserve également une quantité de ressources de processeur et de mémoire pour gérer les fonctions du système d’exploitation.

Pour connaître les meilleures pratiques associées, consultez la section [Meilleures pratiques relatives aux fonctionnalités de base du planificateur dans AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Pools de nœuds

Les nœuds d’une même configuration sont regroupés dans des *pools de nœuds*. Un cluster Kubernetes contient au moins un pool de nœuds. Le nombre et la taille initiaux des nœuds sont définis quand vous créez un cluster AKS, opération qui engendre la création d’un *nœud de pools par défaut*. Ce pool de nœuds par défaut dans AKS contient les machines virtuelles sous-jacentes qui exécutent vos nœuds d’agent.

> [!NOTE]
> Pour garantir un fonctionnement fiable de votre cluster, vous devez exécuter au moins 2 (deux) nœuds dans le pool de nœuds par défaut.

Vous mettez à l’échelle ou à niveau un cluster AKS sur le pool de nœuds par défaut. Vous pouvez choisir de mettre à l’échelle ou de mettre à niveau un pool de nœuds spécifique. Pour les opérations de mise à niveau, les conteneurs en cours d’exécution sont planifiés sur d’autres nœuds du pool de nœuds jusqu’à ce que tous les nœuds soient mis à niveau.

Pour en savoir plus sur l’utilisation de plusieurs pools de nœuds dans AKS, voir [Créer et gérer plusieurs pools de nœuds pour un cluster dans AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Sélecteurs de nœud

Dans un cluster AKS avec plusieurs pools de nœuds, vous devrez peut-être indiquer au planificateur Kubernetes Scheduler le pool de nœuds qui devra être utilisé pour une ressource donnée. Par exemple, les contrôleurs d’entrée ne doivent pas s’exécuter sur des nœuds Windows Server. 

Les sélecteurs de nœud vous permettent de définir différents paramètres, comme le système d’exploitation des nœuds, pour contrôler à quel endroit un pod doit être planifié.

L’exemple de base suivant planifie une instance NGINX sur un nœud Linux en utilisant le sélecteur de nœud *"beta.kubernetes.io/os": linux* :

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Pour plus d’informations sur la façon de contrôler l’endroit où sont planifiés les pods, consultez la section [Meilleures pratiques relatives aux fonctionnalités avancées du planificateur dans AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Pods

Kubernetes Utilise des *pods* pour exécuter une instance de votre application. Un pod représente une instance unique de votre application. 

Les pods utilisent généralement un mappage individuel avec un conteneur. Dans les scénarios avancés, un pod peut contenir plusieurs conteneurs. Ces pods multiconteneurs sont planifiés ensemble sur le même nœud et permettent aux conteneurs de partager des ressources connexes.

Quand vous créez un pod, vous pouvez définir des *demandes de ressources* afin de demander une certaine quantité de ressources de processeur ou de mémoire. Le planificateur de Kubernetes essaie de répondre à cette demande en planifiant les pods afin qu’ils s’exécutent sur un nœud avec des ressources disponibles. Vous pouvez également spécifier des limites de ressources maximales qui empêchent un pod de consommer trop de ressources de calcul à partir du nœud sous-jacent. La meilleure pratique consiste à inclure des limites de ressources pour tous les pods afin d’aider le planificateur de Kubernetes à identifier les ressources nécessaires et autorisées.

Pour plus d’informations, consultez [Kubernetes pods][kubernetes-pods] (Pods Kubernetes) et [Kubernetes pod lifecycle][kubernetes-pod-lifecycle] (cycle de vie des pods Kubernetes).

Un pod est une ressource logique, mais les charges de travail s’exécutent sur les conteneurs. Les pods sont généralement des ressources éphémères et jetables. Il manque aux pods planifiés individuellement une partie des caractéristiques de haute disponibilité et de redondance de Kubernetes. En effet, les pods sont déployés et gérés par des *contrôleurs* Kubernetes, comme le contrôleur de déploiement.

## <a name="deployments-and-yaml-manifests"></a>Déploiements et manifestes YAML

Un *déploiement* représente des pods identiques, gérés par le contrôleur de déploiement Kubernetes. Un déploiement définit le nombre de réplicas de *pods* à créer. Le planificateur Kubernetes veille à ce que des pods supplémentaires soient planifiés sur des nœuds sains si des pods ou des nœuds rencontrent des problèmes.

Vous pouvez mettre à jour les déploiements pour changer la configuration des pods, l’image de conteneur utilisée ou le stockage attaché. Le contrôleur de déploiement :
* Purge et termine un nombre donné de réplicas.
* Crée des réplicas à partir de la nouvelle définition de déploiement.
* Poursuit le processus jusqu’à ce que tous les réplicas du déploiement soient mis à jour.

La plupart des applications sans état dans AKS doivent utiliser le modèle de déploiement plutôt que la planification de pods individuels. Kubernetes peut superviser l’intégrité et l’état du déploiement pour s’assurer que le nombre requis de réplicas s’exécutent dans le cluster. Quand vous planifiez des pods individuels, ces derniers ne sont pas redémarrés s’ils rencontrent un problème et ne sont pas replanifiés sur des nœuds sains si leur nœud actuel rencontre un problème.

Vous ne souhaitez pas interrompre les décisions de gestion avec un processus de mise à jour si votre application nécessite un nombre minimal d’instances disponibles. Les *budgets d’interruption de pods* permettent de définir le nombre de réplicas dans un déploiement pouvant être retirés pendant une mise à niveau d’un nœud ou une mise à jour. Par exemple, si votre déploiement comprend *cinq (5)*  réplicas, vous pouvez définir une interruption de pods de *4 (quatre)* pour limiter la suppression ou la replanification autorisée à un seul réplica à la fois. Comme dans le cas des limites de ressources des pods, une bonne pratique consiste à définir des budgets d’interruption de pods sur les applications qui nécessitent la présence systématique d’un nombre minimal de réplicas.

Les déploiements sont généralement créés et gérés avec `kubectl create` ou `kubectl apply`. Créez un déploiement en définissant un fichier manifeste au format YAML. 

L’exemple suivant crée un déploiement de base du serveur web NGINX. Le déploiement spécifie la création de *trois (3)*  réplicas ; le port *80* doit être ouvert sur le conteneur. Des demandes et limites de ressources sont également définies pour l’UC et la mémoire.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Vous pouvez également créer des applications plus complexes en incluant des services tels que des équilibreurs de charge dans le manifeste YAML.

Pour plus d’informations, consultez la section [Déploiements Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestion des packages avec Helm

[Helm][helm] est couramment utilisé pour gérer les applications dans Kubernetes. Vous pouvez déployer des ressources en créant et en utilisant des *graphiques* Helm publics existants qui contiennent une version empaquetée d’un code d’application et de manifestes YAML Kubernetes. Ces graphiques Helm peuvent être stockés localement ou dans un référentiel distant comme un [référentiel de graphiques Helm Azure Container Registry][acr-helm].

Pour utiliser Helm, installez le client Helm sur votre ordinateur, ou utilisez le client Helm dans [Azure Cloud Shell][azure-cloud-shell]. Recherchez ou créez des graphiques Helm, puis installez-les sur votre cluster Kubernetes. Pour plus d’informations, consultez la section [Installer des applications existantes avec Helm dans AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Ressources StatefulSet et ressources DaemonSet

À l’aide du planificateur de Kubernetes, le contrôleur de déploiement exécute des réplicas sur n’importe quel nœud disponible ayant des ressources. Bien que cette approche soit suffisante pour les applications sans état, le contrôleur de déploiement n’est pas idéal pour les applications qui requièrent :
* Une convention d’affectation de noms ou un stockage persistant. 
* Un réplica sur certains nœuds au sein d’un cluster.

Cependant, il existe deux ressources Kubernetes qui vous permettent de gérer ces types d’applications :

- *StatefulSet* maintient l’état des applications au-delà du cycle de vie d’un pod individuel, tel que le stockage.
- *DaemonSet* garantit une instance en cours d’exécution sur chaque nœud, tôt dans le processus de démarrage de Kubernetes.

### <a name="statefulsets"></a>Ressources StatefulSet

Le développement d’applications modernes est souvent destiné à des applications sans état. Pour les applications avec état, comme celles qui incluent des composants de base de données, vous pouvez utiliser *StatefulSets*. Comme pour les déploiements, StatefulSet crée et gère au moins un pod identique. Les réplicas d’une ressource StatefulSet suivent une approche séquentielle et sans perte de données du déploiement, de la mise à l’échelle, de la mises à niveau et de l’arrêt. Avec une ressource StatefulSet, la convention de nommage, les noms de réseau et le stockage persistent quand les réplicas sont replanifiés.

Définissez l’application au format YAML à l’aide de `kind: StatefulSet`. À partir de là, le contrôleur StatefulSet gère le déploiement et la gestion des réplicas requis. Les données sont écrites dans un stockage persistant, fourni par Azure Disques managés ou Azure Files. Avec une ressource StatefulSet, le stockage permanent sous-jacent demeure, même quand la ressource est supprimée.

Pour plus d’informations, consultez la section [Kubernetes StatefulSets][kubernetes-statefulsets].

Les réplicas dans une ressource StatefulSet sont planifiés et exécutés sur n’importe quel nœud disponible dans un cluster AKS. Pour garantir qu’au moins un pod dans votre jeu s’exécute sur un nœud, vous utilisez un DaemonSet à la place.

### <a name="daemonsets"></a>Ressources DaemonSet

Dans le cadre d’une supervision ou d’une collecte de journaux spécifique, vous pouvez être amené à exécuter un pod sur la totalité ou une partie des nœuds. Vous pouvez utiliser *DaemonSet* pour déployer un ou plusieurs pods identiques, mais le contrôleur DaemonSet garantit que chaque nœud spécifié exécute une instance du pod.

Le contrôleur DaemonSet peut planifier des pods sur des nœuds tôt dans le processus de démarrage du cluster, avant que ne démarre le planificateur Kubernetes par défaut. Ainsi, les pods dans une ressource DaemonSet sont démarrés avant que ne soient planifiés les pods traditionnels dans un déploiement ou une ressource StatefulSet.

À l’image des ressources StatefulSet, une ressource DaemonSet est définie dans le cadre d’une définition YAML à l’aide de `kind: DaemonSet`.

Pour plus d’informations, consultez la section [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Si vous utilisez le [complément add-on Virtual Nodes](virtual-nodes-cli.md#enable-virtual-nodes-addon), les DaemonSets ne créeront pas de pods sur le nœud virtuel.

## <a name="namespaces"></a>Espaces de noms

Les ressources Kubernetes, telles que les pods et les déploiements, sont regroupées logiquement dans un *espace de noms* pour diviser un cluster AKS et limiter l’accès à la création, à l’affichage ou à la gestion des ressources. Par exemple, vous pouvez créer des espaces de noms pour séparer les groupes métier. Les utilisateurs ne peuvent interagir qu’avec les ressources appartenant aux espaces de noms qui leur sont attribués.

![Espaces de noms Kubernetes pour séparer logiquement les ressources et les applications](media/concepts-clusters-workloads/namespaces.png)

Quand vous créez un cluster AKS, les espaces de noms suivants sont disponibles :

| Espace de noms | Description |  
| ----------------- | ------------- |  
| *default*                                                                                 | Espace de noms dans lequel sont créés par défaut les pods et les déploiements quand aucun espace de noms n’est fourni. Dans les environnements plus petits, vous pouvez déployer les applications directement dans l’espace de noms par défaut sans provoquer la création de séparations logiques supplémentaires. Quand vous interagissez avec l’API Kubernetes, comme avec `kubectl get pods`, l’espace de noms par défaut est utilisé si aucun n’est spécifié.                                                        |  
| *kube-system* | Espace de noms où se trouvent les ressources principaless, telles que les fonctionnalités réseau, comme le DNS et le proxy, ou bien le tableau de bord Kubernetes. En règle générale, vous ne déployez pas vos propres applications dans cet espace de noms.                                      |  
| *kube-public*                                                                            | Cet espace de noms n’est généralement pas utilisé, mais vous pouvez y recourir pour rendre les ressources visibles dans l’ensemble du cluster et consultables par tous les utilisateurs.                                                                                    |  


Pour plus d’informations, consultez la section [Espace de noms Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Étapes suivantes

Cet article décrit certains des principaux composants Kubernetes et leur application aux clusters AKS. Pour plus d’informations sur les concepts fondamentaux de Kubernetes et d’AKS, consultez les articles suivants :

- [Accès et identité Kubernetes/AKS][aks-concepts-identity]
- [Sécurité Kubernetes/AKS][aks-concepts-security]
- [Réseaux virtuels Kubernetes/AKS][aks-concepts-network]
- [Stockage Kubernetes/AKS][aks-concepts-storage]
- [Mise à l’échelle Kubernetes/AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
