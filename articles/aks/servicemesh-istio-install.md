---
title: Installer Istio dans AKS (Azure Kubernetes Service)
description: Découvrez comment installer et utiliser Istio pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 02/19/2020
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: f0fe4ab46bfe5c0c0c2ea67aa2e2694321628be5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136361"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installer et utiliser Istio dans AKS (Azure Kubernetes Service)

[Istio][istio-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts].

Cet article vous montre comment installer Istio. Le binaire client Istio, `istioctl`, est installé sur votre machine cliente et les composants Istio sont installés dans un cluster Kubernetes sur AKS.

> [!NOTE]
> Les instructions suivantes référencent la version d’Istio `1.4.0`.
>
> Les versions Istio `1.4.x` ont été testées par l’équipe Istio par rapport aux versions Kubernetes `1.13`, `1.14` et `1.15`. Vous trouverez d’autres versions d’Istio dans la [page GitHub dédiée aux versions d’Istio][istio-github-releases], des informations sur chaque version sur le [site des actualités Istio][istio-release-notes] ainsi que les versions de Kubernetes prises en charge dans le [FAQ général sur Istio][istio-faq].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Télécharger et installer le binaire client istioctl d’Istio
> * Installer Istio sur AKS
> * Valider l’installation d’Istio
> * Accéder aux modules complémentaires
> * Désinstaller Istio d’AKS

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes version `1.13` ou supérieure, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin d’aide avec l’un quelconque de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Assurez-vous de lire la documentation relative aux [niveaux de performance et à l’extensibilité d’Istio](https://istio.io/docs/concepts/performance-and-scalability/) pour comprendre quelles sont les ressources supplémentaires nécessaires à l’exécution d’Istio dans votre cluster AKS. Les exigences de base et de mémoire varient en fonction de votre charge de travail précise. Choisissez un nombre de nœuds et une taille de machine virtuelle appropriés pour votre configuration.

Cet article répartit les instructions d’installation d’Istio en plusieurs étapes discrètes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][istio-install-istioctl] officiel d’installation d’Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="install-the-istio-components-on-aks"></a>Installer les composants Istio sur AKS

Nous allons installer [Grafana][grafana] et [Kiali][kiali] dans le cadre de notre installation Istio. Grafana fournit des tableaux de bord d’analyse et de surveillance, et Kiali fournit un tableau de bord d’observation du maillage de services. Dans notre configuration, chacun de ces composants nécessite des informations d’identification qui doivent être fournies en tant que [secret][kubernetes-secrets].

Avant d’installer les composants Istio, nous devons créer les secrets pour Grafana et Kiali. Ces secrets doivent être installés dans l’espace de noms `istio-system` qui sera utilisé par Istio. Nous devrons donc créer également l’espace de noms. Nous devons utiliser l’option `--save-config` lors de la création de l’espace de noms via `kubectl create` afin que le programme d’installation Istio puisse exécuter `kubectl apply` sur cet objet à l’avenir.

```console
kubectl create namespace istio-system --save-config
```

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - create secrets for Grafana and Kiali](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash check for CRDs](includes/servicemesh/istio/install-create-secrets-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell check for CRDs](includes/servicemesh/istio/install-create-secrets-powershell.md)]

::: zone-end

### <a name="install-istio-components"></a>Installer les composants Istio

Maintenant que nous avons correctement créé les secrets Grafana et Kiali dans notre cluster AKS, il est temps d’installer les composants Istio. 

L’approche d’installation de [Helm][helm] pour Istio sera déconseillée prochainement. La nouvelle approche d’installation d’Istio s’appuie sur le binaire du client `istioctl`, les [profils de configuration Istio][istio-configuration-profiles] et les nouvelles [spécifications et API du plan de contrôle Istio][istio-control-plane]. Cette nouvelle approche est celle que nous allons utiliser pour installer Istio.

> [!NOTE]
> Istio doit être actuellement planifié pour s’exécuter sur des nœuds Linux. Si vous avez des nœuds Windows Server dans votre cluster, vous devez veiller à ce que l’exécution des pods Istio soit planifiée uniquement sur des nœuds Linux. Nous allons utiliser des [sélecteurs de nœud][kubernetes-node-selectors] pour vérifier que les pods sont planifiés sur les nœuds appropriés.

> [!CAUTION]
> Les fonctionnalités [SDS (secret Discovery Service)][istio-feature-sds] et [Istio CNI][istio-feature-cni] d’Istio étant actuellement en [version alpha][istio-feature-stages], réfléchissez avant de les activer. Par ailleurs, la fonctionnalité Kubernetes [Service Account Token Volume Projection][kubernetes-feature-sa-projected-volume] (exigée pour SDS) n’est pas activée dans les versions AKS actuelles.
Créez un fichier appelé `istio.aks.yaml` en utilisant le contenu suivant. Ce fichier contiendra les [détails de la spécification du plan de contrôle Istio][istio-control-plane] pour la configuration d’Istio.

```yaml
apiVersion: install.istio.io/v1alpha2
kind: IstioControlPlane
spec:
  # Use the default profile as the base
  # More details at: https://istio.io/docs/setup/additional-setup/config-profiles/
  profile: default
  values:
    global:
      # Ensure that the Istio pods are only scheduled to run on Linux nodes
      defaultNodeSelector:
        beta.kubernetes.io/os: linux
      # Enable mutual TLS for the control plane
      controlPlaneSecurityEnabled: true
      mtls:
        # Require all service to service communication to have mtls
        enabled: false
    grafana:
      # Enable Grafana deployment for analytics and monitoring dashboards
      enabled: true
      security:
        # Enable authentication for Grafana
        enabled: true
    kiali:
      # Enable the Kiali deployment for a service mesh observability dashboard
      enabled: true
    tracing:
      # Enable the Jaeger deployment for tracing
      enabled: true
```

Installez istio à l’aide de la commande `istioctl apply` et du fichier de spécifications du plan de contrôle Istio (`istio.aks.yaml`) ci-dessus, comme suit :

```console
istioctl manifest apply -f istio.aks.yaml --logtostderr --set installPackagePath=./install/kubernetes/operator/charts
```

Le programme d’installation déploiera un certain nombre de [CRD][kubernetes-crd] puis gérera les dépendances pour installer tous les objets pertinents définis pour cette configuration d’Istio. Un extrait de code similaire à celui-ci doit s’afficher.

```console
Applying manifests for these components:
- Tracing
- EgressGateway
- NodeAgent
- Grafana
- Policy
- Citadel
- CertManager
- IngressGateway
- Injector
- Prometheus
- PrometheusOperator
- Kiali
- Telemetry
- Galley
- Cni
- Pilot
- Base
- CoreDNS
NodeAgent is waiting on a prerequisite...
Telemetry is waiting on a prerequisite...
Galley is waiting on a prerequisite...
Cni is waiting on a prerequisite...
Grafana is waiting on a prerequisite...
Policy is waiting on a prerequisite...
Citadel is waiting on a prerequisite...
EgressGateway is waiting on a prerequisite...
Tracing is waiting on a prerequisite...
Kiali is waiting on a prerequisite...
PrometheusOperator is waiting on a prerequisite...
IngressGateway is waiting on a prerequisite...
Prometheus is waiting on a prerequisite...
CertManager is waiting on a prerequisite...
Injector is waiting on a prerequisite...
Pilot is waiting on a prerequisite...
Applying manifest for component Base
Waiting for CRDs to be applied.
CRDs applied.
Finished applying manifest for component Base
Prerequisite for Tracing has completed, proceeding with install.
Prerequisite for Injector has completed, proceeding with install.
Prerequisite for Telemetry has completed, proceeding with install.
Prerequisite for Policy has completed, proceeding with install.
Prerequisite for PrometheusOperator has completed, proceeding with install.
Prerequisite for NodeAgent has completed, proceeding with install.
Prerequisite for IngressGateway has completed, proceeding with install.
Prerequisite for Kiali has completed, proceeding with install.
Prerequisite for EgressGateway has completed, proceeding with install.
Prerequisite for Galley has completed, proceeding with install.
Prerequisite for Grafana has completed, proceeding with install.
Prerequisite for Cni has completed, proceeding with install.
Prerequisite for Citadel has completed, proceeding with install.
Applying manifest for component Tracing
Prerequisite for Prometheus has completed, proceeding with install.
Prerequisite for Pilot has completed, proceeding with install.
Prerequisite for CertManager has completed, proceeding with install.
Applying manifest for component Kiali
Applying manifest for component Prometheus
Applying manifest for component IngressGateway
Applying manifest for component Policy
Applying manifest for component Telemetry
Applying manifest for component Citadel
Applying manifest for component Galley
Applying manifest for component Pilot
Applying manifest for component Injector
Applying manifest for component Grafana
Finished applying manifest for component Kiali
Finished applying manifest for component Tracing
Finished applying manifest for component Prometheus
Finished applying manifest for component Citadel
Finished applying manifest for component Policy
Finished applying manifest for component IngressGateway
Finished applying manifest for component Injector
Finished applying manifest for component Galley
Finished applying manifest for component Pilot
Finished applying manifest for component Grafana
Finished applying manifest for component Telemetry

Component IngressGateway installed successfully:
================================================

serviceaccount/istio-ingressgateway-service-account created
deployment.apps/istio-ingressgateway created
gateway.networking.istio.io/ingressgateway created
sidecar.networking.istio.io/default created
poddisruptionbudget.policy/ingressgateway created
horizontalpodautoscaler.autoscaling/istio-ingressgateway created
service/istio-ingressgateway created

...
```

À ce stade, vous avez déployé Istio dans votre cluster AKS. Pour nous assurer que nous avons un déploiement réussi d’Istio, nous allons passer à la section suivante pour [valider l’installation d’Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Valider l’installation d’Istio

Tout d’abord, vérifiez que les services attendus ont été créés. Utilisez la commande [kubectl get svc][kubectl-get] pour afficher les services en cours d’exécution. Interrogez l’espace de noms `istio-system`, où les composants Istio et de module complémentaire ont été installés par le chart Helm `istio` :

```console
kubectl get svc --namespace istio-system --output wide
```

L’exemple de sortie suivant montre les services qui doivent maintenant être en cours d’exécution :

- Services `istio-*`
- Services de suivi de module complémentaire `jaeger-*`, `tracing` et `zipkin`
- Service d’indicateurs de performance de module complémentaire `prometheus`
- Service de tableau de bord d’analyse et de surveillance de module complémentaire `grafana`
- Service de tableau de bord de maillage de services de module complémentaire `kiali`

Si `istio-ingressgateway` indique `<pending>` en guise d’adresse IP externe, patientez quelques minutes, le temps qu’une adresse IP soit attribuée par le réseau Azure.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.116.147   <none>           3000/TCP                                                                                                                     92s   app=grafana
istio-citadel            ClusterIP      10.0.248.152   <none>           8060/TCP,15014/TCP                                                                                                           94s   app=citadel
istio-galley             ClusterIP      10.0.50.100    <none>           443/TCP,15014/TCP,9901/TCP,15019/TCP                                                                                         93s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.36.213    20.188.221.111   15020:30369/TCP,80:31368/TCP,443:30045/TCP,15029:32011/TCP,15030:31212/TCP,15031:32411/TCP,15032:30009/TCP,15443:30010/TCP   93s   app=istio-ingressgateway
istio-pilot              ClusterIP      10.0.23.222    <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                       93s   istio=pilot
istio-policy             ClusterIP      10.0.59.250    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                 93s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.123.219   <none>           443/TCP                                                                                                                      93s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.216.9     <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                       89s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                   96s   app=jaeger
jaeger-collector         ClusterIP      10.0.221.24    <none>           14267/TCP,14268/TCP,14250/TCP                                                                                                95s   app=jaeger
jaeger-query             ClusterIP      10.0.46.154    <none>           16686/TCP                                                                                                                    95s   app=jaeger
kiali                    ClusterIP      10.0.174.97    <none>           20001/TCP                                                                                                                    94s   app=kiali
prometheus               ClusterIP      10.0.245.226   <none>           9090/TCP                                                                                                                     94s   app=prometheus
tracing                  ClusterIP      10.0.249.95    <none>           9411/TCP                                                                                                                     95s   app=jaeger
zipkin                   ClusterIP      10.0.154.89    <none>           9411/TCP                                                                                                                     94s   app=jaeger
```

Ensuite, vérifiez que les pods requis ont été créés. Utilisez la commande [kubectl get pods][kubectl-get] et réinterrogez l’espace de noms `istio-system` :

```console
kubectl get pods --namespace istio-system
```

L’exemple de sortie suivant montre les pods en cours d’exécution :

- Pods `istio-*`
- Pod d’indicateurs de performance de module complémentaire `prometheus-*`
- Pod de tableau de bord d’analyse et de surveillance de module complémentaire `grafana-*`
- Pod de tableau de bord de maillage de services de module complémentaire `kiali`

```console
NAME                                          READY   STATUS    RESTARTS   AGE
grafana-6bc97ff99-k9sk4                       1/1     Running   0          92s
istio-citadel-6b5c754454-tb8nf                1/1     Running   0          94s
istio-galley-7d6d78d7c5-zshsd                 2/2     Running   0          94s
istio-ingressgateway-85869c5cc7-x5d76         1/1     Running   0          95s
istio-pilot-787d6995b5-n5vrj                  2/2     Running   0          94s
istio-policy-6cf4fbc8dc-sdsg5                 2/2     Running   2          94s
istio-sidecar-injector-5d5b978668-wrz2s       1/1     Running   0          94s
istio-telemetry-5498db684-6kdnw               2/2     Running   1          94s
istio-tracing-78548677bc-74tx6                1/1     Running   0          96s
kiali-59b7fd7f68-92zrh                        1/1     Running   0          95s
prometheus-7c7cf9dbd6-rjxcv                   1/1     Running   0          94s
```

Tous les pods doivent indiquer l’état `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leur sortie et leur état.

## <a name="accessing-the-add-ons"></a>Accès aux modules complémentaires

Une série de modules complémentaires fournissant des fonctionnalités supplémentaires a été installée par Istio au cours de l’installation ci-dessus. Les applications web des modules complémentaires **ne sont pas** exposées publiquement par le biais d’une adresse IP externe. 

Pour accéder aux interfaces utilisateur des modules complémentaires, utilisez la commande `istioctl dashboard`. Cette commande exploite [kubectl port-forward][kubectl-port-forward] et un port aléatoire pour créer une connexion sécurisée entre votre machine cliente et le pod approprié dans votre cluster AKS. L’application web du module complémentaire s’ouvre ensuite automatiquement dans votre navigateur par défaut.

Nous avons ajouté une couche supplémentaire de sécurité pour Grafana et Kiali en spécifiant pour eux des informations d’identification, plus haut dans cet article.

### <a name="grafana"></a>Grafana

Les tableaux de bord d’analyse et de surveillance pour Istio sont fournis par [Grafana][grafana]. Pensez à utiliser les informations d’identification que vous avez créées via le secret Grafana précédemment lorsque vous y êtes invité. Ouvrez le tableau de bord Grafana de façon sécurisée, comme ceci :

```console
istioctl dashboard grafana
```

### <a name="prometheus"></a>Prometheus

Les indicateurs de performance pour Istio sont fournis par [Prometheus][prometheus]. Ouvrez le tableau de bord Prometheus de façon sécurisée, comme ceci :

```console
istioctl dashboard prometheus
```

### <a name="jaeger"></a>Jaeger

Le suivi au sein d’Istio est assuré par [Jaeger][jaeger]. Ouvrez le tableau de bord Jaeger de façon sécurisée, comme ceci :

```console
istioctl dashboard jaeger
```

### <a name="kiali"></a>Kiali

Un tableau de bord d’observation du maillage de services est fourni par [Kiali][kiali]. Pensez à utiliser les informations d’identification que vous avez créées via le secret Kiali précédemment lorsque vous y êtes invité. Ouvrez le tableau de bord Kiali de façon sécurisée, comme ceci :

```console
istioctl dashboard kiali
```

### <a name="envoy"></a>Envoy

Une interface simple est disponible pour les proxys [Envoy][envoy]. Elle fournit des informations de configuration et des métriques pour un proxy Envoy s’exécutant dans un pod spécifié. Ouvrez l’interface Envoy de façon sécurisée, comme ceci :

```console
istioctl dashboard envoy <pod-name>.<namespace>
```

## <a name="uninstall-istio-from-aks"></a>Désinstaller Istio d’AKS

> [!WARNING]
> La suppression d’Istio à partir d’un système en cours d’exécution peut entraîner des problèmes de trafic entre vos services. Vérifiez que vous avez mis en place des dispositions pour que votre système continue de fonctionner correctement sans Istio avant de continuer.

### <a name="remove-istio-components-and-namespace"></a>Supprimer les composants et l’espace de noms Istio

Pour supprimer Istio de votre cluster AKS, utilisez la commande `istioctl manifest generate` avec le fichier de spécifications du plan de contrôle d’Istio `istio.aks.yaml`. Cela génère le manifeste déployé, que nous allons diriger vers `kubectl delete` afin de supprimer tous les composants installés et l’espace de noms `istio-system`.

```console
istioctl manifest generate -f istio.aks.yaml -o istio-components-aks --logtostderr --set installPackagePath=./install/kubernetes/operator/charts 

kubectl delete -f istio-components-aks -R
```

### <a name="remove-istio-crds-and-secrets"></a>Supprimer les définitions CRD et les secrets Istio

Les commandes ci-dessus suppriment l’espace de noms et tous les composants Istio, mais il nous reste les secrets Istio générés. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

La documentation suivante décrit comment utiliser Istio pour fournir un routage intelligent afin de déployer un contrôle de validité de mise en production :

> [!div class="nextstepaction"]
> [Scénario de routage intelligent Istio AKS][istio-scenario-routing]

Pour explorer davantage d’options de configuration et d’installation pour Istio, consultez la documentation Istio officielle suivante :

- [Istio - guides d’installation][istio-installation-guides]

Pour suivre des scénarios supplémentaires, consultez :

- [Exemple d’application Istio BookInfo][istio-bookinfo-example]

Pour découvrir comment surveiller votre application AKS à l’aide d’Application Insights et d’Istio, consultez la documentation Azure Monitor suivante :

- [Surveillance d’applications sans instrumentation pour les applications hébergées Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-faq]: https://istio.io/faq/general/
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-installation-guides]: https://istio.io/docs/setup/install/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-istioctl]: https://istio.io/docs/setup/install/istioctl/
[istio-configuration-profiles]: https://istio.io/docs/setup/additional-setup/config-profiles/
[istio-control-plane]: https://istio.io/docs/reference/config/istio.operator.v1alpha1/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/traffic-management/ingress/secure-ingress-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/azure/aks/concepts-clusters-workloads#node-selectors
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/
[envoy]: https://www.envoyproxy.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./servicemesh-istio-scenario-routing.md
