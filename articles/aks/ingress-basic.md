---
title: Créer un contrôleur d’entrée de base dans Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX de base dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 7b1fb26adc49067c35745011414ada7b33d7e55e
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913578"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Plusieurs applications sont ensuite exécutées dans le cluster AKS, chacune étant accessible via l’adresse IP unique.

Vous pouvez également :

- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article utilise Helm pour installer le contrôleur d’entrée NGINX, cert-manager et un exemple d’application web. Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Pour les besoins de cet article, vous devez également exécuter Azure CLI version 2.0.64 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, voir [Installer Azure CLI][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Pour créer le contrôleur d’entrée, utilisez `Helm` pour installer *nginx-ingress*. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

Le contrôleur d’entrée doit également être planifié sur un nœud Linux. Les nœuds Windows Server (actuellement en préversion dans AKS) ne doivent pas exécuter le contrôleur d’entrée. Un sélecteur de nœud est spécifié en utilisant le paramètre `--set nodeSelector` pour que le planificateur Kubernetes exécute le contrôleur d’entrée NGINX sur un nœud Linux.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée *ingress-basic*. Spécifiez un espace de noms de votre propre environnement, si besoin. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes Helm.

> [!TIP]
> Si vous souhaitez activer la [préservation de l’adresse IP source du client][client-source-ip] pour les requêtes aux conteneurs de votre cluster, ajoutez `--set controller.service.externalTrafficPolicy=Local` à la commande d’installation Helm. L’IP source du client est stockée dans l’en-tête de la requête sous *X-Forwarded-For*. Lors de l’utilisation d’un contrôleur d’entrée pour lequel la conservation de l’adresse IP source du client est activée, le protocole SSL direct ne fonctionnera pas.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Lorsque le service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX, votre adresse IP publique dynamique est affectée, comme indiqué dans l’exemple de sortie suivant :

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                                 TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
aspiring-labradoodle-nginx-ingress-controller        LoadBalancer   10.0.61.144    40.117.74.8   80:30386/TCP,443:32276/TCP   6m2s
aspiring-labradoodle-nginx-ingress-default-backend   ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Aucune règle d’entrée n’a encore été créée. Par conséquent, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche si vous accédez à l’adresse IP interne. Les étapes suivantes permettent de configurer les règles d’entrée.

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Pour voir le contrôleur d’entrée en action, nous allons exécuter deux applications de démonstration dans votre cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application « Hello World ».

Avant d’installer les exemples de graphiques Helm, ajoutez le référentiel d’exemples Azure à votre environnement Helm :

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Créez la première application de démonstration à partir d’un graphique Helm avec la commande suivante :

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Installez maintenant une deuxième instance de l’application de démonstration. Pour la deuxième instance, spécifiez un nouveau titre de manière à pouvoir distinguer visuellement les deux applications. Vous devez également spécifier un nom de service unique :

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Créer un itinéraire d’entrée

Les deux applications sont maintenant en cours d’exécution sur votre cluster Kubernetes. Pour acheminer le trafic vers chaque application, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Dans l’exemple suivant, le trafic vers l’adresse `http://40.117.74.8/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `http://40.117.74.8/hello-world-two` est acheminé vers le service `ingress-demo`.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y l’exemple de code YAML suivant.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Tester le contrôleur d’entrée

Pour tester les itinéraires du contrôleur d’entrée, accédez aux deux applications. Ouvrez un navigateur web à l’adresse IP de votre contrôleur d’entrée NGINX, par exemple, *http://40.117.74.8* . La première application de démonstration s’affiche dans le navigateur web, comme illustré dans l’exemple suivant :

![Première application en cours d’exécution derrière le contrôleur d’entrée](media/ingress-basic/app-one.png)

Maintenant, ajoutez le chemin */hello-world-two* à l’adresse IP, par exemple, *http://40.117.74.8/hello-world-two* . La deuxième application de démonstration portant le titre personnalisé s’affiche :

![Deuxième application en cours d’exécution derrière le contrôleur d’entrée](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms exemple en entier ou des ressources individuelles.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Supprimer l’espace de noms exemple et toutes les ressources

Pour supprimer l’espace de noms exemple en entier, utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms. Toutes les ressources de l’espace de noms sont supprimées.

```console
kubectl delete namespace ingress-basic
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Supprimer les ressources individuellement

Sinon, une approche plus précise consiste à supprimer les ressources individuelles créées. Listez les versions de Helm avec la commande `helm list`. Recherchez les graphiques nommés *nginx-ingress* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
aspiring-labradoodle    1           Wed Mar 27 19:55:37 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      ingress-basic
esteemed-koala          1           Wed Mar 27 19:59:18 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
wonderful-puma          1           Wed Mar 27 19:59:07 2019    DEPLOYED    aks-helloworld-0.1.0                ingress-basic
```

Supprimez les versions avec la commande `helm delete`. L’exemple suivant supprime le déploiement d’entrée NGINX et les deux exemples d’applications AKS « hello world ».

```
$ helm delete aspiring-labradoodle esteemed-koala wonderful-puma

release "aspiring-labradoodle" deleted
release "esteemed-koala" deleted
release "wonderful-puma" deleted
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

Supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml
```

Pour finir, vous pouvez supprimer l’espace de noms lui-même. Utilisez la commande `kubectl delete` et spécifiez le nom de votre espace de noms :

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]

Vous pouvez également :

- [Routage d’applications HTTP][aks-http-app-routing]
- [Create an ingress controller to an internal virtual network in Azure Kubernetes Service (AKS)][aks-ingress-internal] (Créer un contrôleur d’entrée pour un réseau virtuel interne dans Azure Kubernetes Service (AKS))
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
