---
title: Utiliser vos propres certificats TLS pour l’entrée avec un cluster Azure Kubernetes Service (AKS)
description: Découvrez comment installer et configurer un contrôleur d’entrée NGINX qui utilise vos propres certificats dans un cluster Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 03/27/2019
ms.author: iainfou
ms.openlocfilehash: e20f881d740c5d5b73c23c933ceb3d6f19e78ef9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60465969"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Créer un contrôleur d’entrée HTTPS et utiliser vos propres certificats TLS sur Azure Kubernetes Service (AKS)

Un contrôleur d’entrée est un logiciel qui fournit un proxy inversé, un routage du trafic configurable et un arrêt TLS pour les services Kubernetes. Des ressources d’entrée Kubernetes sont utilisées pour configurer les règles d’entrée et les itinéraires des services Kubernetes individuels. En utilisant un contrôleur d’entrée et des règles d’entrée, une seule adresse IP peut être utilisée pour router le trafic vers plusieurs services dans un cluster Kubernetes.

Cet article montre comment déployer le [contrôleur d’entrée NGINX][nginx-ingress] dans un cluster Azure Kubernetes Service (AKS). Vous générez vos propres certificats et créez un secret Kubernetes à utiliser avec l’itinéraire d’entrée. Enfin, plusieurs applications sont exécutées dans le cluster AKS, chacune étant accessible via une seule adresse IP.

Vous pouvez également :

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Avant de commencer

Cet article utilise Helm pour installer le contrôleur d’entrée NGINX et un exemple d’application web. Helm doit être initialisé dans votre cluster AKS et utiliser un compte de service pour Tiller. Vérifiez que vous utilisez la dernière version de Helm. Pour obtenir des instructions de mise à niveau, consultez la [documentation d’installation de Helm][helm-install]. Pour plus d’informations sur la configuration et l’utilisation de Helm, consultez [Installer des applications avec Helm dans Azure Kubernetes Service (AKS)][use-helm].

Cet article nécessite également que vous exécutiez Azure CLI version 2.0.61 ou version ultérieure. Exécutez `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Créer un contrôleur d’entrée

Pour créer le contrôleur d’entrée, utilisez `Helm` pour installer *nginx-ingress*. Pour renforcer la redondance, deux réplicas des contrôleurs d’entrée NGINX sont déployés avec le paramètre `--set controller.replicaCount`. Pour tirer pleinement parti de l’exécution de réplicas des contrôleurs d’entrée, vérifiez que votre cluster AKS comprend plusieurs nœuds.

> [!TIP]
> L’exemple suivant crée un espace de noms Kubernetes pour les ressources d’entrée nommé *entrée-basic*. Spécifiez un espace de noms pour votre propre environnement en fonction des besoins. Si votre cluster AKS n’est pas activée de RBAC, ajoutez `--set rbac.create=false` aux commandes Helm.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress --namespace ingress-basic --set controller.replicaCount=2
```

Pendant l’installation, une adresse IP publique Azure est créée pour le contrôleur d’entrée. Cette adresse IP publique est statique pour la durée de vie du contrôleur d’entrée. Si vous supprimez le contrôleur d’entrée, l’attribution d’adresse IP publique est perdue. Si vous créez ensuite un contrôleur d’entrée supplémentaires, une nouvelle adresse IP publique est attribuée. Si vous souhaitez conserver l’utilisation de l’adresse IP publique, vous pouvez au lieu de cela [créer un contrôleur d’entrée avec une adresse IP publique statique][aks-ingress-static-tls].

Pour obtenir l’adresse IP publique, utilisez la commande `kubectl get service`. L’affectation de l’adresse IP au service peut prendre quelques minutes.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Notez cette adresse IP publique, car elle sera utilisée à la dernière étape pour tester le déploiement.

Aucune règle d’entrée n’a encore été créée. Si vous accédez à l’adresse IP publique, la page 404 par défaut du contrôleur d’entrée NGINX s’affiche.

## <a name="generate-tls-certificates"></a>Générer des certificats TLS

Dans le cadre de cet article, nous allons générer un certificat auto-signé avec `openssl`. Pour une utilisation en production, vous devez demander un certificat signé approuvé auprès d’un fournisseur ou de votre propre autorité de certification (CA). Dans l’étape suivante, vous allez générer un *secret* Kubernetes en utilisant le certificat TLS et la clé privée générée par OpenSSL.

L’exemple suivant génère un certificat RSA X509 à 2 048 bits, valide pendant 365 jours, nommé *aks-ingress-tls.crt*. Le fichier de clé privée est nommé *aks-ingress-tls.key*. Un secret TLS Kubernetes nécessite ces deux fichiers.

Cet article fonctionne avec le nom commun sujet *demo.azure.com* et n’a pas besoin d’être modifié. Pour une utilisation en production, spécifiez les propres valeurs de votre organisation pour le paramètre `-subj` :

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Créer le secret Kubernetes pour le certificat TLS

Pour autoriser Kubernetes à utiliser le certificat TLS et la clé privée pour le contrôleur d’entrée, vous devez créer et utiliser un secret. Le secret est défini une seule fois et utilise le certificat et le fichier de clé créés à l’étape précédente. Vous référencez ensuite ce secret lorsque vous définissez les routes d’entrée.

L’exemple suivant crée un nom de secret *aks-ingress-tls* :

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Exécuter des applications de démonstration

Un contrôleur d’entrée et un secret avec votre certificat ont été configurés. Maintenant, nous allons exécuter deux applications de démonstration dans le cluster AKS. Pour cet exemple, Helm est utilisé pour déployer deux instances d’une simple application « Hello World ».

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

Les deux applications sont maintenant exécutées dans votre cluster Kubernetes. Toutefois, elles ont été configurées avec un service de type `ClusterIP`. Ainsi, ces applications ne sont pas accessibles à partir d’Internet. Pour les rendre disponibles publiquement, créez une ressource d’entrée Kubernetes. La ressource d’entrée configure les règles qui acheminent le trafic vers l’une des deux applications.

Dans l’exemple suivant, le trafic vers l’adresse `https://demo.azure.com/` est acheminé vers le service nommé `aks-helloworld`. Le trafic vers l’adresse `https://demo.azure.com/hello-world-two` est acheminé vers le service `ingress-demo`. Dans le cadre de cet article, vous n’avez pas besoin de changer les noms d’hôte de démonstration. Pour une utilisation en production, fournissez les noms spécifiés dans le cadre du processus de demande et de génération de certificat.

> [!TIP]
> Si le nom d’hôte spécifié pendant le processus de demande de certificat, le nom CN, ne correspond pas à l’hôte défini dans votre route d’entrée, votre contrôleur d’entrée affiche un *faux certificat de contrôleur d’entrée Kubernetes*. Assurez-vous que les noms d’hôte de votre certificat et de votre route d’entrée correspondent.

La section *tls* indique à la route d’entrée d’utiliser le secret nommé *aks-ingress-tls* pour l’hôte *demo.azure.com*. Là encore, pour une utilisation en production, spécifiez votre propre adresse d’hôte.

Créez un fichier nommé `hello-world-ingress.yaml` et copiez-y l’exemple de code YAML suivant.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

## <a name="test-the-ingress-configuration"></a>Tester la configuration d’entrée

Pour tester les certificats avec notre faux hôte *demo.azure.com*, utilisez `curl` et spécifiez le paramètre *--resolve*. Ce paramètre vous permet de mapper le nom *demo.azure.com* à l’adresse IP publique de votre contrôleur d’entrée. Spécifiez l’adresse IP publique de votre propre contrôleur d’entrée, telle qu’indiquée dans l’exemple suivant :

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Aucun chemin d’accès supplémentaire n’ayant été fourni avec l’adresse, l’itinéraire par défaut du contrôleur d’entrée est */*. La première application de démonstration est retournée, comme l’illustre l’exemple de sortie condensée suivant :

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Le paramètre *-v* figurant dans notre commande `curl` fournit en sortie des informations détaillées incluant le certificat TLS reçu. À mi-chemin vers la sortie de la commande curl, vous pouvez vérifier que votre propre certificat TLS a été utilisé. Le paramètre *-k* continue le chargement de la page même si nous utilisons un certificat auto-signé. L’exemple suivant montre que le certificat *issuer: CN=demo.azure.com; O=aks-ingress-tls* a été utilisé :

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Ajoutez maintenant le chemin d’accès */hello-world-two* à l’adresse, par exemple, `https://demo.azure.com/hello-world-two`. La deuxième application de démonstration avec le titre personnalisé est retournée, comme l’illustre l’exemple de sortie condensée suivant :

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Cet article vous a montré comment utiliser Helm pour installer les composants d’entrée et les exemples d’applications. Quand vous déployez un graphique Helm, une série de ressources Kubernetes est créée. Ces ressources incluent des pods, des déploiements et des services. Pour nettoyer ces ressources, vous pouvez supprimer l’espace de noms d’ensemble de l’exemple ou ressources individuelles.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Supprimer l’espace de noms d’exemple et toutes les ressources

Pour supprimer l’espace de noms d’ensemble de l’exemple, utilisez le `kubectl delete` commande et spécifiez le nom de votre espace de noms. Toutes les ressources dans l’espace de noms sont supprimés.

```console
kubectl delete namespace ingress-basic
```

Ensuite, supprimez le référentiel Helm pour l’application ACS hello world :

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Supprimer les ressources individuellement

Vous pouvez également une approche plus précise consiste à supprimer les ressources individuelles créées. Liste le Helm libère avec la `helm list` commande. Recherchez les graphiques nommés *nginx-ingress* et *aks-helloworld*, comme illustré dans l’exemple de sortie suivant :

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Supprimez les versions avec la commande `helm delete`. L’exemple suivant supprime le déploiement d’entrée NGINX et les deux exemples d’application AKS « hello world ».

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Ensuite, supprimez le référentiel Helm pour l’application AKS « hello world » :

```console
helm repo remove azure-samples
```

Supprimez la route d’entrée qui a dirigé le trafic vers les exemples d’applications :

```console
kubectl delete -f hello-world-ingress.yaml
```

Supprimer le certificat de clé secrète :

```console
kubectl delete secret aks-ingress-tls
```

Enfin, vous pouvez supprimer le lui-même espace de noms. Utilisez le `kubectl delete` commande et spécifiez le nom de votre espace de noms :

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Étapes suivantes

Cet article a mentionné certains composants qui n’appartiennent pas à AKS. Pour plus d’informations sur ces composants, consultez les pages projet suivantes :

- [Interface CLI Helm][helm-cli]
- [Contrôleur d’entrée NGINX][nginx-ingress]

Vous pouvez également :

- [Créer un contrôleur d’entrée de base avec une connectivité réseau externe][aks-ingress-basic]
- [Activer le module complémentaire de routage d’application HTTP][aks-http-app-routing]
- [Créer un contrôleur d’entrée qui utilise un réseau privé interne et une adresse IP][aks-ingress-internal]
- Créer un contrôleur d’entrée qui utilise Let’s Encrypt pour générer automatiquement des certificats TLS [avec une adresse IP publique dynamique][aks-ingress-tls] ou [avec une adresse IP publique statique][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
