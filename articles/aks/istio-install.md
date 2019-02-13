---
title: Installer Istio dans AKS (Azure Kubernetes Service)
description: Découvrez comment installer et utiliser Istio pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 12/3/2018
ms.author: pabouwer
ms.openlocfilehash: f34d8c547738921374eaf5edcfcec4911423d9dc
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55699209"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installer et utiliser Istio dans AKS (Azure Kubernetes Service)

[Istio][istio-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts].

Cet article vous montre comment installer Istio. Le binaire client `istioctl` Istio est installé sur votre machine cliente, puis les composants Istio sont installés dans un cluster Kubernetes sur AKS. Ces instructions référencent la version d’Istio *1.0.4*. Vous trouverez des versions Istio supplémentaires sur le site [GitHub - versions d’Istio][istio-github-releases].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Télécharger Istio
> * Installer le binaire client Istio
> * Installer les composants Kubernetes Istio
> * validation de l'installation

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées de cet article supposent que vous avez créé un cluster AKS (Kubernetes versions 1.10 et ultérieures, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin d’aide avec un de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Pour que vous puissiez installer Istio, il est nécessaire que [Helm][helm] version *2.10.0* ou ultérieure soit correctement installé et configuré dans votre cluster. Si vous avez besoin d’aide pour installer Helm, consultez les [consignes d’installation de Helm dans AKS][helm-install]. Si la version installée de Helm n’est pas au minimum la version *2.10.0*, effectuez une mise à niveau ou consultez [Istio - Installation with Helm guide][istio-install-helm] (guide Istio : installation avec Helm) pour découvrir d’autres options d’installation.

Cet article répartit les instructions d’installation d’Istio en plusieurs étapes discrètes. Chacune de ces étapes est décrite afin que vous appreniez à installer Istio et découvriez comment Istio fonctionne avec Kubernetes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][istio-install-k8s-quickstart] officiel d’installation d’Istio.

## <a name="download-istio"></a>Télécharger Istio

Tout d’abord, téléchargez et extrayez la dernière version d’Istio. Les étapes diffèrent légèrement selon que vous utilisez un interpréteur de commandes PowerShell ou un interpréteur de commandes bash sur MacOS, sur Linux ou sur le sous-système Windows pour Linux. Choisissez une des étapes d’installation suivantes en fonction de votre environnement :

* [Bash sur MacOS, Linux ou le sous-système Windows pour Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

Sur MacOS, utilisez `curl` pour télécharger la dernière version d’Istio, puis extrayez-la avec `tar` comme suit :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Sur Linux ou le sous-système Windows pour Linux, utilisez `curl` pour télécharger la dernière version d’Istio, puis extrayez-la avec `tar` comme suit :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.0.4

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

### <a name="powershell"></a>PowerShell

Dans PowerShell, utilisez [Invoke-WebRequest][Invoke-WebRequest] pour télécharger la dernière version d’Istio, puis extrayez-la avec [Expand-Archive][Expand-Archive] comme suit :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.0.4"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

## <a name="install-the-istio-client-binary"></a>Installer le binaire client Istio

Le binaire client `istioctl` s’exécute sur votre machine cliente et vous permet de gérer les stratégies et les règles de routage Istio. Là encore, les étapes d’installation diffèrent légèrement d’un système d’exploitation client à l’autre. Choisissez une des étapes d’installation suivantes en fonction de votre environnement.

> [!IMPORTANT]
> Exécutez toutes les étapes restantes à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargée et extraite à la section précédente.

### <a name="macos"></a>MacOS

Pour installer le binaire client `istioctl` Istio dans un interpréteur de commandes bash sur MacOS, utilisez les commandes suivantes. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Si vous souhaitez utiliser la complétion de ligne de commande pour le binaire client `istioctl` Istio, configurez-la comme suit :

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Maintenant, passez à la section [Installer les composants Kubernetes Istio](#install-the-istio-kubernetes-components).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou sous-système Windows pour Linux

Utilisez les commandes suivantes pour installer le binaire lient `istioctl` Istio dans un interpréteur de commandes bash sur Linux ou le [sous-système Windows pour Linux][install-wsl]. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
cd istio-$ISTIO_VERSION
chmod +x ./bin/istioctl
sudo mv ./bin/istioctl /usr/local/bin/istioctl
```

Si vous souhaitez utiliser la complétion de ligne de commande pour le binaire client `istioctl` Istio, configurez-la comme suit :

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Maintenant, passez à la section [Installer les composants Kubernetes Istio](#install-the-istio-kubernetes-components).

### <a name="windows"></a> Windows

Pour installer le binaire client `istioctl` Istio dans un interpréteur de commandes Powershell sur Windows, utilisez les commandes suivantes. Ces commandes copient le binaire client `istioctl` à un nouvel emplacement de programme utilisateur et le rendent accessible via votre `PATH`.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:/Program Files/Istio"
mv ./bin/istioctl.exe "C:/Program Files/Istio/"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Program Files\Istio\", "User")
```

## <a name="install-the-istio-kubernetes-components"></a>Installer les composants Kubernetes Istio

Pour installer les composants Istio dans votre cluster AKS, utilisez Helm. Installez les ressources Istio dans l’espace de noms `istio-system` et activez des options supplémentaires pour la sécurité et la supervision comme suit :

```azurecli
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set grafana.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

Le chart Helm déploie un grand nombre d’objets. Le déploiement peut prendre 2 à 3 minutes, selon l’environnement du cluster.

## <a name="validate-the-installation"></a>validation de l'installation

Pour vérifier le déploiement d’Istio, nous allons valider l’installation.

Tout d’abord, vérifiez que les services attendus ont été créés. Utilisez la commande [kubectl get svc][kubectl-get] pour afficher les services en cours d’exécution. Interrogez l’espace de noms *istio-system*, où les composants Istio et de module complémentaire ont été installés par le chart Helm :

```console
kubectl get svc --namespace istio-system --output wide
```

L’exemple de sortie suivant montre les services qui doivent maintenant être en cours d’exécution :

- Services *istio-**
- Services de suivi de module complémentaire *jaeger-**, *tracing* et *zipkin*
- Service de métriques de module complémentaire *Prometheus*
- Service de tableau de bord de supervision et d’analytique de module complémentaire *grafana*
- Service de tableau de bord de maillage de services de module complémentaire *kiali*

Si `istio-ingressgateway` indique `<pending>` en guise d’adresse IP externe, patientez quelques minutes, le temps qu’une adresse IP soit attribuée par le réseau Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                   AGE       SELECTOR
grafana                  ClusterIP      10.0.26.60     <none>           3000/TCP                                                                                                                  3m        app=grafana
istio-citadel            ClusterIP      10.0.88.87     <none>           8060/TCP,9093/TCP                                                                                                         3m        istio=citadel
istio-egressgateway      ClusterIP      10.0.115.115   <none>           80/TCP,443/TCP                                                                                                            3m        app=istio-egressgateway,istio=egressgateway
istio-galley             ClusterIP      10.0.104.183   <none>           443/TCP,9093/TCP                                                                                                          3m        istio=galley
istio-ingressgateway     LoadBalancer   10.0.12.216    52.187.250.239   80:31380/TCP,443:31390/TCP,31400:31400/TCP,15011:30469/TCP,8060:31999/TCP,853:31235/TCP,15030:32000/TCP,15031:30293/TCP   3m        app=istio-ingressgateway,istio=ingressgateway
istio-pilot              ClusterIP      10.0.38.134    <none>           15010/TCP,15011/TCP,8080/TCP,9093/TCP                                                                                     3m        istio=pilot
istio-policy             ClusterIP      10.0.253.81    <none>           9091/TCP,15004/TCP,9093/TCP                                                                                               3m        istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.181.186   <none>           443/TCP                                                                                                                   3m        istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.177.113   <none>           9091/TCP,15004/TCP,9093/TCP,42422/TCP                                                                                     3m        istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                3m        app=jaeger
jaeger-collector         ClusterIP      10.0.112.81    <none>           14267/TCP,14268/TCP                                                                                                       3m        app=jaeger
jaeger-query             ClusterIP      10.0.179.193   <none>           16686/TCP                                                                                                                 3m        app=jaeger
kiali                    ClusterIP      10.0.211.63    <none>           20001/TCP                                                                                                                 3m        app=kiali
prometheus               ClusterIP      10.0.70.113    <none>           9090/TCP                                                                                                                  3m        app=prometheus
tracing                  ClusterIP      10.0.139.121   <none>           80/TCP                                                                                                                    3m        app=jaeger
zipkin                   ClusterIP      10.0.60.155    <none>           9411/TCP                                                                                                                  3m        app=jaeger
```

Ensuite, vérifiez que les pods requis ont été créés. Utilisez la commande [kubectl get pods][kubectl-get] pour réinterroger l’espace de noms *istio-system* :

```console
kubectl get pods --namespace istio-system
```

L’exemple de sortie suivant montre les pods en cours d’exécution :

- Pods *istio-**
- Pod de métriques de module complémentaire *prometheus-**
- Pod de tableau de bord de supervision et d’analytique de module complémentaire *grafana-**
- Pod de tableau de bord de maillage de services de module complémentaire *kiali*

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-59b787b9b-cr6d7                  1/1       Running     0          6m
istio-citadel-78df8c67d9-9lfpf           1/1       Running     0          6m
istio-egressgateway-6b96cd7f5-k848h      1/1       Running     0          6m
istio-galley-58f566cb66-8mhbv            1/1       Running     0          6m
istio-ingressgateway-6cbbf596f6-6jz8g    1/1       Running     0          6m
istio-pilot-8449d555fc-sl6kp             2/2       Running     0          6m
istio-policy-6b99d88bc5-55s52            2/2       Running     0          6m
istio-sidecar-injector-b88dfb954-8m86s   1/1       Running     0          6m
istio-telemetry-675cb4cb9d-8s7wd         2/2       Running     0          6m
istio-tracing-7596597bd7-sbnt9           1/1       Running     0          6m
kiali-5fbd6ffb-4qcxw                     1/1       Running     0          6m
prometheus-76db5fddd5-2tkxs              1/1       Running     0          6m
```

Tous les pods indiquent l’état `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leurs sortie et état.

## <a name="accessing-the-add-ons"></a>Accès aux modules complémentaires

Une série de modules complémentaires fournissant des fonctionnalités supplémentaires a été installée pour Istio au cours de l’installation ci-dessus. Les interfaces utilisateur des modules complémentaires ne sont pas exposées publiquement par le biais d’une adresse IP externe. Pour accéder aux interfaces utilisateur des modules complémentaires, utilisez la commande [kubectl port-forward][kubectl-port-forward]. Cette commande crée une connexion sécurisée entre un port local sur votre machine cliente et le pod approprié dans votre cluster AKS.

### <a name="grafana"></a>Grafana

Les tableaux de bord d’analytique et de supervision pour Istio sont fournis par [Grafana][grafana]. Transférez le port local *3000* de votre machine cliente au port *3000* du pod qui exécute Grafana dans votre cluster AKS :

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Grafana :

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Vous pouvez maintenant atteindre Grafana à l’URL suivante sur votre machine cliente : [http://localhost:3000](http://localhost:3000).

### <a name="prometheus"></a>Prometheus

Le navigateur d’expression pour les métriques est fourni par [Prometheus][prometheus]. Transférez le port local *9090* de votre machine cliente au port *9090* du pod qui exécute Prometheus dans votre cluster AKS :

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Prometheus :

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

Vous pouvez maintenant atteindre le navigateur d’expression Prometheus à l’URL suivante sur votre machine cliente : [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

L’interface utilisateur du suivi est fournie par [Jaeger][jaeger]. Transférez le port local *16686* de votre machine cliente au port *16686* du pod qui exécute Jaeger dans votre cluster AKS :

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Jaeger :

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

Vous pouvez maintenant atteindre l’interface utilisateur du suivi Jaeger à l’URL suivante sur votre machine cliente : [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Un tableau de bord d’observabilité du maillage de services est fourni par [Kiali][kiali]. Transférez le port local *20001* de votre machine cliente au port *20001* du pod qui exécute Kiali dans votre cluster AKS :

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Kiali :

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Vous pouvez maintenant atteindre le tableau de bord d’observabilité du maillage de services Kiali à l’URL suivante sur votre machine cliente : [http://localhost:20001](http://localhost:20001).

Les nom d’utilisateur et mot de passe par défaut pour le tableau de bord Kiali sont *username:admin/password:admin*. Ces informations d’identification peuvent être configurées via les valeurs Helm *kiali.dashboard.username* et *kiali.dashboard.passphrase*.

## <a name="next-steps"></a>Étapes suivantes

Pour voir comment utiliser Istio afin de fournir un routage intelligent entre plusieurs versions de votre application et de déployer une version de contrôle de validité, consultez la documentation suivante :

> [!div class="nextstepaction"]
> [Scénario de routage intelligent Istio AKS][istio-scenario-routing]

Pour explorer davantage d’options de configuration et d’installation pour Istio, consultez les articles Istio officiels suivants :

- [Istio - Kubernetes installation quickstart][istio-install-k8s-quickstart]
- [Istio - Helm installation guide][istio-install-helm]
- [Istio - Helm installation options][istio-install-helm-options]

Vous pouvez également suivre des scénarios supplémentaires à l’aide de l’[exemple d’application Bookinfo d’Istio][istio-bookinfo-example].

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-k8s-quickstart]: https://istio.io/docs/setup/kubernetes/quick-start/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/helm-install/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10
[kubernetes-crd]: https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
[Invoke-WebRequest]: /powershell/module/microsoft.powershell.utility/invoke-webrequest
[Expand-Archive]: /powershell/module/Microsoft.PowerShell.Archive/Expand-Archive
