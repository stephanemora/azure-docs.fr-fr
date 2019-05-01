---
title: Routage intelligent et déploiement Canary avec Istio dans Azure Kubernetes Service (AKS)
description: Découvrez comment utiliser Istio pour fournir un routage intelligent et déployer des versions Canary sur un cluster Azure Kubernetes Service (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: bd660a2b6ffb96478c3170cc7013ff22518b758f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64702205"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Utiliser le routage intelligent et le déploiement Canary avec Istio dans Azure Kubernetes Service (AKS)

[Istio][istio-github] est un maillage de services (service mesh) open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts] (Présentation d’Istio).

Cet article vous montre comment utiliser la fonctionnalité de gestion de trafic d’Istio. Un exemple d’application de vote AKS est utilisé pour explorer le routage intelligent et le déploiement de versions Canary.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Déployer l’application
> * Mettre à jour l’application
> * Déployer une version Canary de l’application
> * Finaliser le déploiement

## <a name="before-you-begin"></a>Avant de commencer

> [!NOTE]
> Ce scénario a été testé avec la version d’Istio `1.1.3`.

Les étapes décrites dans cet article supposent que vous avez créé un cluster ACS (Kubernetes `1.11` et versions ultérieures, avec RBAC activé) et établi un `kubectl` connexion avec le cluster. Vous aurez également besoin d’Istio installé dans votre cluster.

Si vous avez besoin d’aide avec un de ces éléments, consultez le [démarrage rapide d’ACS] [ aks-quickstart] et [installer d’Istio dans AKS] [ istio-install] des conseils.

## <a name="about-this-application-scenario"></a>À propos de ce scénario d’application

L’exemple d’application vote AKS fournit deux options de votantes (**chats** ou **chiens**) aux utilisateurs. Un composant de stockage conserve le nombre de votes pour chaque option. En outre, un composant d’analytique fournit des informations sur les votes en faveur de chaque option.

Dans ce scénario d’application, vous démarrez en déployant version `1.0` de l’application de vote et de la version `1.0` du composant analytique. Le composant d’analytique fournit les décomptes des nombres de votes. L’application de vote et le composant d’analytique interagissent avec la version `1.0` du composant de stockage, qui est stockée par Redis.

Vous mettez à niveau le composant analytique vers la version `1.1`, qui fournit les nombres et maintenant les totaux et les pourcentages.

Un sous-ensemble d’utilisateurs test version `2.0` de l’application par le biais de cette version. Cette nouvelle version utilise un composant de stockage qui est associé à une base de données MySQL.

Une fois que vous soyez certain que cette version `2.0` fonctionne comme prévu sur votre sous-ensemble d’utilisateurs, vous déployer la version `2.0` à tous vos utilisateurs.

## <a name="deploy-the-application"></a>Déployer l’application

Nous allons commencer par déployer l’application sur votre cluster Azure Kubernetes Service (AKS). Le diagramme suivant montre ce qui est exécuté à la fin de cette section - version `1.0` de tous les composants avec des demandes entrantes prises en charge par le biais de la passerelle d’entrée Istio :

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-01.png)

Les artefacts que vous devez suivre avec cet article sont disponibles dans le dépôt GitHub [Azure-Samples/aks-voting-app][github-azure-sample]. Vous pouvez télécharger les artefacts ou cloner le dépôt comme suit :

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Accédez au dossier suivant dans le dépôt téléchargé/cloné, puis exécutez toutes les étapes suivantes à partir de ce dossier :

```console
cd scenarios/intelligent-routing-with-istio
```

Commencez par créer un espace de noms dans votre cluster AKS pour l’application de vote AKS exemple nommée `voting` comme suit :

```azurecli
kubectl create namespace voting
```

Attribuez à l’espace de noms l’étiquette `istio-injection=enabled`. Cette étiquette indique à Istio d’injecter automatiquement les proxys istio en tant que side-cars dans tous vos pods dans cet espace de noms.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

Maintenant, nous allons créer les composants de l’application de vote AKS. Créer ces composants dans le `voting` espace de noms créé à l’étape précédente.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

L’exemple de sortie suivant montre les ressources en cours de création :

```console
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio a certaines exigences spécifiques concernant les pods et les services. Pour plus d’informations, consultez la [documentation sur les exigences d’Istio relatives aux pods et services][istio-requirements-pods-and-services].

Pour voir les pods qui ont été créés, utilisez la commande [kubectl get pods][kubectl-get] comme suit :

```azurecli
kubectl get pods -n voting
```

L’exemple suivant illustre trois instances de la `voting-app` pod et une seule instance d’à la fois le `voting-analytics` et `voting-storage` pods. Chacun des pods a deux conteneurs. Un de ces conteneurs est le composant et l’autre est le `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Pour voir des informations sur le pod, utilisez la commande [kubectl describe pod][kubectl-describe]. Remplacez le nom du pod par le nom d’un pod dans votre propre cluster AKS à partir de la sortie précédente :

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

Le `istio-proxy` conteneur a automatiquement été injecté par Istio pour gérer le trafic réseau vers et à partir de vos composants, comme indiqué dans l’exemple de sortie suivant :

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

Vous ne pouvez pas vous connecter à l’application de vote tant que vous n’avez pas créé la [passerelle][istio-reference-gateway] Istio et le [service virtuel][istio-reference-virtualservice]. Ces ressources Istio routent le trafic à partir de la passerelle d’entrée Istio par défaut vers notre application.

> [!NOTE]
> Une **passerelle** est un composant à la périphérie du maillage de services qui reçoit le trafic HTTP et TCP entrant ou sortant.
> 
> Un **service virtuel** définit un ensemble de règles de routage pour un ou plusieurs services de destination.

Utilisez le `kubectl apply` commande pour déployer le Service de passerelle et virtuel yaml. N’oubliez pas de spécifier l’espace de noms ces ressources sont déployées dans.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

L’exemple de sortie suivant montre la nouvelle passerelle et Service virtuel en cours de création :

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Obtenez l’adresse IP de la passerelle d’entrée Istio à l’aide de la commande suivante :

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

L’exemple de sortie suivant montre l’adresse IP de la passerelle d’entrée :

```
20.188.211.19
```

Ouvrez un navigateur et collez l’adresse IP. L’exemple d’application de vote AKS s’affiche.

![Application de vote AKS en cours d’exécution dans notre cluster AKS compatible Istio](media/istio/deploy-app-01.png)

Les informations au bas de l’écran indiquent que l’application utilise la version `1.0` de `voting-app` et version `1.0` de `voting-storage` (Redis).

## <a name="update-the-application"></a>Mettre à jour l’application

Nous allons déployer une nouvelle version du composant d’analytique. Cette nouvelle version `1.1` affiche des totaux et les pourcentages en plus le nombre pour chaque catégorie.

Le diagramme suivant illustre ce qui s’exécute à la fin de cette section, seule la version `1.1` de notre `voting-analytics` composant a du trafic routé à partir de la `voting-app` composant. Bien que version `1.0` de notre `voting-analytics` composant continue à s’exécuter et est référencé par le `voting-analytics` service, les proxys Istio interdisent le trafic vers et à partir de celui-ci.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-02.png)

Nous allons déployer la version `1.1` de la `voting-analytics` composant. Créez ce composant dans le `voting` espace de noms :

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

L’exemple de sortie suivant montre les ressources en cours de création :

```console
deployment.apps/voting-analytics-1-1 created
```

Rouvrez l’exemple d’application de vote AKS dans un navigateur, en utilisant l’adresse IP de la passerelle d’entrée Istio obtenue à l’étape précédente.

Votre navigateur alterne entre les deux vues indiquées ci-dessous. Étant donné que vous utilisez un Kubernetes [Service] [ kubernetes-service] pour le `voting-analytics` composant avec uniquement un sélecteur d’étiquette unique (`app: voting-analytics`), Kubernetes utilise le comportement par défaut de tourniquet entre le PODS qui correspondent à ce sélecteur. Dans ce cas, il s’agit à la fois version `1.0` et `1.1` de votre `voting-analytics` pods.

![Version 1.0 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/deploy-app-01.png)

![Version 1.1 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/update-app-01.png)

Vous pouvez visualiser la commutation entre les deux versions de la `voting-analytics` composant comme suit. Pensez à utiliser l’adresse IP de votre propre passerelle d’entrée Istio.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L’exemple de sortie suivant montre la partie pertinente du site web retourné quand le site passe d’une version à l’autre :

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Verrouiller le trafic vers la version 1.1 de l’application

Maintenant nous allons verrouiller le trafic vers la version uniquement `1.1` de la `voting-analytics` composant et à la version `1.0` de la `voting-storage` composant. Vous définissez ensuite des règles de routage pour tous les autres composants.

> * Un **service virtuel** définit un ensemble de règles de routage pour un ou plusieurs services de destination.
> * Une **règle de destination** définit des stratégies de trafic et des stratégies propres aux versions.
> * Une **stratégie** définit les méthodes d’authentification pouvant être acceptées sur les charges de travail.

Utilisez le `kubectl apply` commande pour remplacer la définition de Service virtuel sur votre `voting-app` et ajoutez [Destination règles] [ istio-reference-destinationrule] et [Services virtuels] [ istio-reference-virtualservice] pour les autres composants. Vous ajouterez un [stratégie] [ istio-reference-policy] à la `voting` espace de noms pour vous assurer que tous les communiquent entre les services est sécurisé à l’aide de TLS mutuelle et des certificats clients.

* La stratégie a `peers.mtls.mode` définie sur `STRICT` pour vous assurer que TLS mutuelle est appliquée entre vos services au sein de la `voting` espace de noms.
* Nous définissons également le `trafficPolicy.tls.mode` à `ISTIO_MUTUAL` dans toutes nos règles de Destination. Istio fournit des services avec des identités fortes et sécurise les communications entre les services à l’aide de certificats clients et Mutual TLS qu’il gère en toute transparence.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

L’exemple de sortie suivant montre la nouvelle stratégie, les règles de Destination et les Services virtuels en cours de mises à jour/créées :

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Si vous ouvrez l’application de vote AKS dans un navigateur à nouveau, seule la nouvelle version `1.1` de la `voting-analytics` composant est utilisé par le `voting-app` composant.

![Version 1.1 du composant d’analytique en cours d’exécution dans notre application de vote AKS](media/istio/update-app-01.png)

Vous pouvez visualiser que vous êtes redirigé désormais uniquement à la version `1.1` de votre `voting-analytics` composant comme suit. Pensez à utiliser l’adresse IP de votre propre passerelle d’entrée Istio :

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

L’exemple de sortie suivant montre la partie pertinente du site web retourné :

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Nous allons maintenant vérifier que Istio utilise TLS mutuelle pour sécuriser les communications entre chacun de nos services. Pour cela, nous utiliserons le [authn tls-vérification] [ istioctl-authn-tls-check] commande sur le `istioctl` client binaire, qui prend la forme suivante.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Cet ensemble de commandes fournissent des informations sur l’accès aux services spécifiés, à partir de tous les pods qui sont dans un espace de noms et correspondent à un jeu d’étiquettes :

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

Cet exemple de sortie suivant montre que TLS mutuelle est appliquée pour chacun de nos requêtes ci-dessus. La sortie indique également la stratégie et les règles de Destination qui applique le TLS mutuels :

```console
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Déployer une version Canary de l’application

Maintenant nous allons déployer une nouvelle version `2.0` de la `voting-app`, `voting-analytics`, et `voting-storage` composants. La nouvelle `voting-storage` composant utiliser MySQL au lieu de Redis et le `voting-app` et `voting-analytics` composants sont mis à jour pour leur permettre d’utiliser les nouveaux `voting-storage` composant.

Le `voting-app` composant prend désormais en charge les fonctionnalités d’indicateur de fonctionnalité. Cet indicateur de fonctionnalité vous permet de tester les fonctionnalités du déploiement de versions Canary d’Istio pour un sous-ensemble d’utilisateurs.

Le diagramme suivant illustre ce que vous devrez en cours d’exécution à la fin de cette section.

* Version `1.0` de la `voting-app` composant, version `1.1` de la `voting-analytics` composant et version `1.0` de la `voting-storage` composant sont en mesure de communiquer entre eux.
* Version `2.0` de la `voting-app` composant, version `2.0` de la `voting-analytics` composant et version `2.0` de la `voting-storage` composant sont en mesure de communiquer entre eux.
* Version `2.0` de la `voting-app` composant sont uniquement accessibles aux utilisateurs qui possèdent un ensemble d’indicateur de fonctionnalité spécifique. Cette modification est gérée à l’aide d’un indicateur de fonctionnalité par le biais d’un cookie.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-03.png)

Tout d’abord, mettez à jour les règles de destination et les services virtuels Istio pour prendre en charge ces nouveaux composants. Ces mises à jour évitent de router le trafic de manière incorrecte vers les nouveaux composants et de donner un accès inapproprié aux utilisateurs :

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

L’exemple de sortie suivant montre les règles de Destination et les Services virtuel mis à jour :

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Ensuite, nous allons ajouter les objets Kubernetes pour la nouvelle version `2.0` composants. Vous également de mettre à jour le `voting-storage` service pour inclure le `3306` port pour MySQL :

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

L’exemple de sortie suivant montre que les objets Kubernetes ont été correctement mis à jour ou créés :

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Attendez que toutes les versions de la `2.0` pods sont en cours d’exécution. Utilisez le [kubectl get pods] [ kubectl-get] commande pour afficher tous les pods dans le `voting` espace de noms :

```azurecli
kubectl get pods --namespace voting
```

Vous devez maintenant être en mesure de basculer entre la version `1.0` et la version `2.0` (contrôle de validité) de l’application de vote. Le commutateur d’indicateur de fonctionnalité en bas de l’écran définit un cookie. Ce cookie est utilisé par le `voting-app` Service virtuel pour diriger les utilisateurs vers la nouvelle version `2.0`.

![Version 1.0 de l’application de vote AKS : indicateur de fonctionnalité non défini](media/istio/canary-release-01.png)

![Version 2.0 de l’application de vote AKS : indicateur de fonctionnalité défini](media/istio/canary-release-02.png)

Les nombres de vote sont différents d’une version à l’autre de l’application. Cette différence reflète le fait que vous utilisez deux back-ends de stockage différents.

## <a name="finalize-the-rollout"></a>Finaliser le déploiement

Une fois que vous avez testé avec succès la version de contrôle de validité, mettre à jour le `voting-app` Service virtuel pour acheminer tout le trafic vers la version `2.0` de la `voting-app` composant. Tous les utilisateurs voient ensuite la version `2.0` de l’application, même si l’indicateur de fonctionnalité est défini ou non :

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-04.png)

Mettez à jour toutes les règles de destination pour supprimer les versions des composants que vous souhaitez désactiver. Ensuite, mettez à jour tous les services virtuels pour arrêter le référencement de ces versions.

Dans la mesure où il n’y a plus de trafic vers les versions antérieures des composants, vous pouvez supprimer tous les déploiements pour ces composants de manière sécurisée.

![Composants de l’application de vote AKS et routage](media/istio/components-and-routing-05.png)

Vous avez maintenant correctement déployé une nouvelle version de l’application de vote AKS.

## <a name="clean-up"></a>Nettoyer 

Vous pouvez supprimer l’application de vote AKS que nous avons utilisé dans ce scénario à partir de votre cluster AKS en supprimant le `voting` espace de noms comme suit :

```azurecli
kubectl delete namespace voting
```

L’exemple de sortie suivant montre que tous les composants de l’application de vote AKS ont été supprimés à partir de votre cluster AKS.

```console
namespace "voting" deleted
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez explorer des scénarios supplémentaires à l’aide de l’[exemple d’application Bookinfo d’Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./istio-install.md
