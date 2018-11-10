---
title: Créer un contrôleur d’entrée de base dans Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX de base dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: fdce7c1f5876442a10fbf77b315e082a0a817dfd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093383"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée dans Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Plusieurs applications sont ensuite exécutées dans le cluster AKS, chacune étant accessible via l’adresse IP unique.

Vous pouvez également :

- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS[ avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article utilise Helm pour installer le contrôleur d’entrée NGINX, cert-manager et un exemple d’application web. Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Cet article nécessite également que vous exécutiez Azure CLI version 2.0.41 ou ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Pour créer le contrôleur d’entrée, utilisez `Helm` pour installer *nginx-ingress*. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

> [!TIP]
> L’exemple suivant installe le contrôleur d’entrée dans l’espace de noms `kube-system`. Si vous le souhaitez, vous pouvez spécifier un espace de noms différent pour votre propre environnement. Si le contrôle d’accès en fonction du rôle (RBAC) n’est pas activé sur votre cluster AKS, ajoutez `--set rbac.create=false` aux commandes.

```console
helm install stable/nginx-ingress --namespace kube-system --set controller.replicaCount=2
```

Lorsque le service équilibreur de charge Kubernetes est créé pour le contrôleur d’entrée NGINX, votre adresse IP publique dynamique est affectée, comme indiqué dans l’exemple de sortie suivant :

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                         TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
masked-otter-nginx-ingress-controller        LoadBalancer   10.0.92.99    40.117.74.8   80:31077/TCP,443:32592/TCP   7m
masked-otter-nginx-ingress-default-backend   ClusterIP      10.0.46.106   <none>        80/TCP                       7m
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
helm install azure-samples/aks-helloworld
```

Installez maintenant une deuxième instance de l’application de démonstration. Pour la deuxième instance, spécifiez un nouveau titre de manière à pouvoir distinguer visuellement les deux applications. Vous devez également spécifier un nom de service unique :

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
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
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Créez la ressource d’entrée avec la commande `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Tester le contrôleur d’entrée

Pour tester les itinéraires du contrôleur d’entrée, accédez aux deux applications. Ouvrez un navigateur web à l’adresse IP de votre contrôleur d’entrée NGINX, par exemple, *http://40.117.74.8*. La première application de démonstration s’affiche dans le navigateur web, comme illustré dans l’exemple suivant :

![Première application en cours d’exécution derrière le contrôleur d’entrée](media/ingress-basic/app-one.png)

Maintenant, ajoutez le chemin */hello-world-two* à l’adresse IP, par exemple, *http://40.117.74.8/hello-world-two*. La deuxième application de démonstration portant le titre personnalisé s’affiche :

![Deuxième application en cours d’exécution derrière le contrôleur d’entrée](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Supprimer des ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, répertoriez tout d’abord les versions Helm au moyen de la commande `helm list`. Recherchez les graphiques nommés *nginx-ingress* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
gilded-duck         1           Tue Oct 16 16:52:25 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
righteous-numbat    1           Tue Oct 16 16:53:53 2018    DEPLOYED    aks-helloworld-0.1.0                default
looming-moth        1           Tue Oct 16 16:53:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Supprimez les versions avec la commande `helm delete`. L’exemple suivant supprime le déploiement d’entrée NGINX et les deux exemples d’applications AKS « hello world ».

```
$ helm delete gilded-duck righteous-numbat looming-moth

release "gilded-duck" deleted
release "righteous-numbat" deleted
release "looming-moth" deleted
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

Enfin, supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]

Vous pouvez également :

- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- [Créer un contrôleur d’entrée qui utilise vos propres certificats TLS][aks-ingress-own-tls]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS[ avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
