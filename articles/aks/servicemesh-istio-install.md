---
title: Installer Istio dans AKS (Azure Kubernetes Service)
description: Découvrez comment installer et utiliser Istio pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 9c9dcd567b8632626bf4b1f0bf2ef6b5e69b8a9d
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529845"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installer et utiliser Istio dans AKS (Azure Kubernetes Service)

[Istio][istio-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts].

Cet article vous montre comment installer Istio. Le binaire client Istio, `istioctl`, est installé sur votre machine cliente et les composants Istio sont installés dans un cluster Kubernetes sur AKS.

> [!NOTE]
> Ces instructions font référence à la version `1.3.2` d’Istio et utilisent la version `2.14.2` ou ultérieure de Helm.
>
> Les versions Istio `1.3.x` ont été testées par l’équipe Istio par rapport aux versions Kubernetes `1.13`, `1.14` et `1.15`. Vous trouverez d’autres versions d’Istio dans la [page GitHub dédiée aux versions d’Istio][istio-github-releases], des informations sur chaque version sur le [site des actualités Istio][istio-release-notes] ainsi que les versions de Kubernetes prises en charge dans le [FAQ général sur Istio][istio-faq].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Télécharger et installer le binaire client istioctl d’Istio
> * Installer Istio sur AKS
> * Valider l’installation d’Istio
> * Accéder aux modules complémentaires
> * Désinstaller Istio d’AKS

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes version `1.13` ou supérieure, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin d’aide avec l’un quelconque de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Vous avez besoin de [Helm][helm] pour suivre ces instructions et installer Istio. Nous vous recommandons d’installer et de configurer correctement la dernière version stable dans votre cluster. Si vous avez besoin d’aide pour installer Helm, consultez les [consignes d’installation de Helm dans AKS][helm-install]. Tous les pods Istio doivent également être planifiés pour s’exécuter sur des nœuds Linux.

Assurez-vous de lire la documentation relative aux [niveau performance et à l’extensibilité Istio](https://istio.io/docs/concepts/performance-and-scalability/) pour comprendre quelles sont les ressources supplémentaires nécessaires à l'exécution d'Istio dans votre cluster AKS. Les exigences de base et de mémoire varient en fonction de votre charge de travail précise. Choisissez un nombre de nœuds et une taille de machine virtuelle appropriés pour votre configuration.

Cet article répartit les instructions d’installation d’Istio en plusieurs étapes discrètes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][istio-install-helm] officiel d’installation d’Istio.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/istio/install-client-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [MacOS - download and install client binary](includes/servicemesh/istio/install-client-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/istio/install-client-binary-windows.md)]

::: zone-end

## <a name="add-the-istio-helm-chart-repository"></a>Ajouter le dépôt de charts Helm Istio

Ajoutez le dépôt de charts Helm Istio pour la version d’Istio. Veillez à exécuter `helm repo update` pour mettre à jour vos informations locales pour le dépôt de charts.

```azurecli
helm repo add istio.io https://storage.googleapis.com/istio-release/releases/$ISTIO_VERSION/charts/
helm repo update
```

## <a name="install-the-istio-crds-on-aks"></a>Installer les définitions de ressources personnalisées (CRD) Istio sur AKS

Istio utilise des [définitions de ressources personnalisées (CRD)][kubernetes-crd] pour gérer sa configuration de runtime. Nous devons commencer par installer les définitions CRD Istio, étant donné que les composants Istio dépendent d’elles. Utilisez Helm et le chart `istio-init` pour installer les définitions CRD Istio dans l’espace de noms `istio-system`, dans votre cluster AKS :

```azurecli
helm install istio.io/istio-init --name istio-init --namespace istio-system
```

Des [travaux][kubernetes-jobs] sont déployés dans le cadre du chart Helm `istio-init` pour installer les définitions CRD. L’exécution de ces travaux doit prendre moins de 20 s en fonction de votre environnement de cluster. Vous pouvez vérifier que les travaux se sont déroulés correctement, comme suit :

```azurecli
kubectl get jobs -n istio-system
```

L’exemple de sortie suivant montre que les travaux ont été correctement réalisés.

```console
NAME                      COMPLETIONS   DURATION   AGE
istio-init-crd-10-1.3.2   1/1           14s        14s
istio-init-crd-11-1.3.2   1/1           12s        14s
istio-init-crd-12-1.3.2   1/1           14s        14s
```

Maintenant que nous avons confirmé la réussite des travaux, nous allons vérifier que nous avons le nombre correct de définitions CRD Istio installées. Vous pouvez vérifier que les 23 définitions CRD Istio ont été installées en exécutant la commande suivante. La commande doit renvoyer le nombre `23`.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - check CRD count](includes/servicemesh/istio/install-check-crd-count-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - check CRD count](includes/servicemesh/istio/install-check-crd-count-powershell.md)]

::: zone-end

Si vous en êtes arrivé à ce stade, cela signifie que vous avez correctement installé les définitions CRD Istio. À présent, passez à la section suivante pour [installer les composants Istio sur AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installer les composants Istio sur AKS

Nous allons installer [Grafana][grafana] et [Kiali][kiali] dans le cadre de notre installation Istio. Grafana fournit des tableaux de bord d’analyse et de surveillance, et Kiali fournit un tableau de bord d’observation du maillage de services. Dans notre configuration, chacun de ces composants nécessite des informations d’identification qui doivent être fournies en tant que [secret][kubernetes-secrets].

Avant d’installer les composants Istio, nous devons créer les secrets pour Grafana et Kiali. 

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

Maintenant que nous avons correctement créé les secrets Grafana et Kiali dans notre cluster AKS, il est temps d’installer les composants Istio. Utilisez Helm et le chart `istio` pour installer les composants Istio dans l’espace de noms `istio-system`, dans votre cluster AKS. 

> [!NOTE]
> **Options d’installation**
> 
> Nous utilisons les options suivantes dans le cadre de notre installation :
> - `global.controlPlaneSecurityEnabled=true` - Mutual TLS est activé pour le plan de contrôle
> - `global.mtls.enabled=true` - exiger mtls pour toutes les communications de service à service
> - `grafana.enabled=true` - activer le déploiement de Grafana pour bénéficier de tableaux de bord d’analyse et de surveillance
> - `grafana.security.enabled=true` - activer l’authentification pour Grafana
> - `tracing.enabled=true` - activer le déploiement de Jaeger pour le suivi
> - `kiali.enabled=true` - activer le déploiement de Kiali pour bénéficier d’un tableau de bord d’observation du maillage de services
>
> **Sélecteurs de nœud**
>
> Istio doit être actuellement planifié pour s’exécuter sur des nœuds Linux. Si vous avez des nœuds Windows Server dans votre cluster, vous devez veiller à ce que l’exécution des pods Istio soit planifiée uniquement sur des nœuds Linux. Nous allons utiliser des [sélecteurs de nœuds][kubernetes-node-selectors] pour vérifier que les pods sont planifiés sur les nœuds appropriés.

> [!CAUTION]
> Les fonctionnalités [SDS (secret Discovery Service)][istio-feature-sds] et [Istio CNI][istio-feature-cni] d’Istio étant actuellement en [version alpha][istio-feature-stages], réfléchissez avant de les activer. Par ailleurs, la fonctionnalité Kubernetes [Service Account Token Volume Projection][kubernetes-feature-sa-projected-volume] (exigée pour SDS) n’est pas activée dans les versions AKS actuelles.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - install Istio components](includes/servicemesh/istio/install-components-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - install Istio components](includes/servicemesh/istio/install-components-powershell.md)]

::: zone-end

Le chart Helm `istio` déploie un grand nombre d’objets. Vous pouvez voir la liste à partir de la sortie de votre commande `helm install` ci-dessus. Le déploiement des composants Istio doit prendre moins de 2 minutes selon votre environnement de cluster.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                                                                                                                                      AGE   SELECTOR
grafana                  ClusterIP      10.0.164.244   <none>           3000/TCP                                                                                                                                     53s   app=grafana
istio-citadel            ClusterIP      10.0.49.16     <none>           8060/TCP,15014/TCP                                                                                                                           53s   istio=citadel
istio-galley             ClusterIP      10.0.175.173   <none>           443/TCP,15014/TCP,9901/TCP                                                                                                                   53s   istio=galley
istio-ingressgateway     LoadBalancer   10.0.226.151   20.188.221.111   15020:31128/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:30817/TCP,15030:30436/TCP,15031:32485/TCP,15032:30980/TCP,15443:30124/TCP   53s   app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.102.158   <none>           15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       53s   istio=pilot
istio-policy             ClusterIP      10.0.234.53    <none>           9091/TCP,15004/TCP,15014/TCP                                                                                                                 53s   istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.216.8     <none>           443/TCP,15014/TCP                                                                                                                            53s   istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.154.215   <none>           9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       53s   istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>           5775/UDP,6831/UDP,6832/UDP                                                                                                                   52s   app=jaeger
jaeger-collector         ClusterIP      10.0.26.109    <none>           14267/TCP,14268/TCP                                                                                                                          52s   app=jaeger
jaeger-query             ClusterIP      10.0.70.55     <none>           16686/TCP                                                                                                                                    52s   app=jaeger
kiali                    ClusterIP      10.0.36.206    <none>           20001/TCP                                                                                                                                    53s   app=kiali
prometheus               ClusterIP      10.0.236.99    <none>           9090/TCP                                                                                                                                     53s   app=prometheus
tracing                  ClusterIP      10.0.83.152    <none>           80/TCP                                                                                                                                       52s   app=jaeger
zipkin                   ClusterIP      10.0.25.86     <none>           9411/TCP                                                                                                                                     52s   app=jaeger
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
NAME                                     READY   STATUS      RESTARTS   AGE
grafana-7c48555456-msl7b                 1/1     Running     0          88s
istio-citadel-566fc66db7-m8wgl           1/1     Running     0          87s
istio-galley-5746db8d56-pl5gg            1/1     Running     0          88s
istio-ingressgateway-6c94f7c9bf-f5lt5    1/1     Running     0          88s
istio-init-crd-10-1.3.2-xw9g2            0/1     Completed   0          92m
istio-init-crd-11-1.3.2-54rz8            0/1     Completed   0          92m
istio-init-crd-12-1.3.2-789qj            0/1     Completed   0          92m
istio-pilot-6748968b6d-rvdfx             2/2     Running     0          87s
istio-policy-7576bbbcf7-2stft            2/2     Running     0          87s
istio-sidecar-injector-76d79d494-7jk9n   1/1     Running     0          87s
istio-telemetry-74b7bf676d-tfrcl         2/2     Running     0          88s
istio-tracing-655d9588bc-d2htg           1/1     Running     0          86s
kiali-65d55bcfb8-tqrfk                   1/1     Running     0          88s
prometheus-846f9849bd-br8kp              1/1     Running     0          87s
```

Il doit y avoir trois pods `istio-init-crd-*` avec un état `Completed`. Ces pods étaient chargés d’exécuter les travaux qui ont créé les définitions CRD dans une étape précédente. Tous les autres pods doivent indiquer le statut `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leur sortie et leur état.

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

Pour supprimer Istio de votre cluster AKS, utilisez les commandes suivantes. Les commandes `helm delete` suppriment les charts `istio` et `istio-init`, et la commande `kubectl delete namespace` supprime l’espace de noms `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete namespace istio-system
```

### <a name="remove-istio-crds-and-secrets"></a>Supprimer les définitions CRD et les secrets Istio

Les commandes ci-dessus suppriment l’espace de noms et tous les composants Istio, mais il nous reste les définitions CRD et les secrets Istio. 

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - remove Istio CRDs and secrets](includes/servicemesh/istio/uninstall-powershell.md)]

::: zone-end

## <a name="next-steps"></a>Étapes suivantes

La documentation suivante décrit comment utiliser Istio pour fournir un routage intelligent afin de déployer un contrôle de validité de mise en production :

> [!div class="nextstepaction"]
> [Scénario de routage intelligent Istio AKS][istio-scenario-routing]

Pour explorer davantage d’options de configuration et d’installation pour Istio, consultez les articles Istio officiels suivants :

- [Istio - Helm installation guide][istio-install-helm]
- [Istio - Helm installation options][istio-install-helm-options]

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
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/

[istio-feature-stages]: https://istio.io/about/feature-stages/
[istio-feature-sds]: https://istio.io/docs/tasks/security/auth-sds/
[istio-feature-cni]: https://istio.io/docs/setup/additional-setup/cni/

[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-feature-sa-projected-volume]: https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#service-account-token-volume-projection
[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubernetes-node-selectors]: https://docs.microsoft.com/en-us/azure/aks/concepts-clusters-workloads#node-selectors
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
[helm-install]: ./kubernetes-helm.md
