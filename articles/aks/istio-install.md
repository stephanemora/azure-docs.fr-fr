---
title: Installer Istio dans AKS (Azure Kubernetes Service)
description: Découvrez comment installer et utiliser Istio pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 032a907e45e007cb51357300e4bbf3c7afb40dde
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542888"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installer et utiliser Istio dans AKS (Azure Kubernetes Service)

[Istio][istio-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts].

Cet article vous montre comment installer Istio. Le binaire client Istio, `istioctl`, est installé sur votre machine cliente et les composants Istio sont installés dans un cluster Kubernetes sur AKS.

> [!NOTE]
> Ces instructions référencent la version d’Istio `1.1.3`.
>
> Les versions Istio `1.1.x` ont été testées par l’équipe Istio par rapport aux versions Kubernetes `1.11`, `1.12` et `1.13`. Vous trouverez d’autres versions d’Istio sur la [page GitHub dédiée aux versions d’Istio][istio-github-releases] et des informations sur chaque version sur le site [Istio - Release Notes][istio-release-notes].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Télécharger Istio
> * Installer le binaire client Istio istioctl
> * Installer les définitions de ressources personnalisées (CRD) Istio sur AKS
> * Installer les composants Istio sur AKS
> * Valider l’installation d’Istio
> * Accès aux modules complémentaires
> * Désinstaller Istio d’AKS

## <a name="before-you-begin"></a>Avant de commencer

Les étapes détaillées dans cet article supposent que vous avez créé un cluster AKS (Kubernetes version `1.11` ou supérieure, avec RBAC activé) et que vous avez établi une connexion `kubectl` avec le cluster. Si vous avez besoin d’aide avec l’un quelconque de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Vous avez besoin de [Helm][helm] pour suivre ces instructions et installer Istio. Il est recommandé d’avoir correctement installé et configuré la version `2.12.2` ou ultérieure dans votre cluster. Si vous avez besoin d’aide pour installer Helm, consultez les [consignes d’installation de Helm dans AKS][helm-install]. Tous les pods Istio doivent également être planifiés pour s’exécuter sur des nœuds Linux.

Assurez-vous de lire la documentation relative aux [niveau performance et à l’extensibilité Istio](https://istio.io/docs/concepts/performance-and-scalability/) pour comprendre quelles sont les ressources supplémentaires nécessaires à l'exécution d'Istio dans votre cluster AKS. Les exigences de base et de mémoire varient en fonction de votre charge de travail précise. Choisissez un nombre de nœuds et une taille de machine virtuelle appropriés pour votre configuration.

Cet article répartit les instructions d’installation d’Istio en plusieurs étapes discrètes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][istio-install-helm] officiel d’installation d’Istio.

## <a name="download-istio"></a>Télécharger Istio

Tout d’abord, téléchargez et extrayez la dernière version d’Istio. Les étapes diffèrent légèrement selon que vous utilisez un interpréteur de commandes PowerShell ou un interpréteur de commandes bash sur MacOS, sur Linux ou sur le sous-système Windows pour Linux. Choisissez l’une des étapes d’installation suivantes qui correspondent à votre environnement favori :

* [Bash sur MacOS, Linux ou le sous-système Windows pour Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

Sur MacOS, utilisez `curl` pour télécharger la dernière version d’Istio, puis extrayez-la avec `tar` comme suit :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Sur Linux ou le sous-système Windows pour Linux, utilisez `curl` pour télécharger la dernière version d’Istio, puis extrayez-la avec `tar` comme suit :

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Maintenant, passez à la section [Installer le binaire client Istio istioctl](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

Dans PowerShell, utilisez `Invoke-WebRequest` pour télécharger la dernière version d’Istio, puis extrayez-la avec `Expand-Archive`, comme suit :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Maintenant, passez à la section [Installer le binaire client Istio istioctl](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installer le binaire client Istio istioctl

> [!IMPORTANT]
> Veillez à exécuter les étapes de cette section à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargée et extraite.

Le binaire client `istioctl` s’exécute sur votre machine cliente et vous permet d’interagir avec le maillage de services Istio. Les étapes d’installation diffèrent légèrement d’un système d’exploitation client à l’autre. Choisissez l’une des étapes d’installation suivantes qui correspondent à votre environnement favori :

* [MacOS](#macos)
* [Linux ou sous-système Windows pour Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Pour installer le binaire client `istioctl` Istio dans un interpréteur de commandes bash sur MacOS, utilisez les commandes suivantes. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

À présent, passez à la section suivante pour [installer les définitions de ressources personnalisées (CRD) Istio sur AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou sous-système Windows pour Linux

Utilisez les commandes suivantes pour installer le binaire client Istio `istioctl` dans un interpréteur de commandes bash sur Linux ou le [sous-système Windows pour Linux][install-wsl]. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
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

À présent, passez à la section suivante pour [installer les définitions de ressources personnalisées (CRD) Istio sur AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Pour installer le binaire client Istio `istioctl` dans un interpréteur de commandes **Powershell** sur Windows, utilisez les commandes suivantes. Ces commandes copient le binaire client `istioctl` vers un dossier Istio et le rendent accessible immédiatement (dans l’interpréteur de commandes actuel) et en permanence (entre les redémarrages de l’interpréteur de commandes) via votre `PATH`. Vous n’avez pas besoin de privilèges élevés (Admin) pour exécuter ces commandes et vous ne devez pas redémarrer votre interpréteur de commandes.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

À présent, passez à la section suivante pour [installer les définitions de ressources personnalisées (CRD) Istio sur AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installer les définitions de ressources personnalisées (CRD) Istio sur AKS

> [!IMPORTANT]
> Veillez à exécuter les étapes de cette section à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargée et extraite.

Istio utilise des [définitions de ressources personnalisées (CRD)][kubernetes-crd] pour gérer sa configuration de runtime. Nous devons commencer par installer les définitions CRD Istio, étant donné que les composants Istio dépendent d’elles. Utilisez Helm et le chart `istio-init` pour installer les définitions CRD Istio dans l’espace de noms `istio-system`, dans votre cluster AKS :

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

Des [travaux][kubernetes-jobs] sont déployés dans le cadre du chart Helm `istio-init` pour installer les définitions CRD. L’exécution de ces travaux doit prendre entre 1 et 2 minutes en fonction de votre environnement de cluster. Vous pouvez vérifier que les travaux se sont déroulés correctement, comme suit :

```azurecli
kubectl get jobs -n istio-system
```

L’exemple de sortie suivant montre que les travaux ont été correctement réalisés.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Maintenant que nous avons confirmé la réussite des travaux, nous allons vérifier que nous avons le nombre correct de définitions CRD Istio installées. Vous pouvez vérifier que les 53 définitions CRD Istio ont été installées en exécutant la commande appropriée pour votre environnement. La commande doit renvoyer le nombre `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Si vous en êtes arrivé à ce stade, cela signifie que vous avez correctement installé les définitions CRD Istio. À présent, passez à la section suivante pour [installer les composants Istio sur AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installer les composants Istio sur AKS

> [!IMPORTANT]
> Veillez à exécuter les étapes de cette section à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargée et extraite.

Nous allons installer [Grafana][grafana] et [Kiali][kiali] dans le cadre de notre installation Istio. Grafana fournit des tableaux de bord d’analyse et de surveillance, et Kiali fournit un tableau de bord d’observation du maillage de services. Dans notre configuration, chacun de ces composants nécessite des informations d’identification qui doivent être fournies en tant que [secret][kubernetes-secrets].

Avant d’installer les composants Istio, nous devons créer les secrets pour Grafana et Kiali. Créez ces secrets en exécutant les commandes appropriées pour votre environnement.

### <a name="add-grafana-secret"></a>Ajouter un secret Grafana

Remplacez le jeton `REPLACE_WITH_YOUR_SECURE_PASSWORD` par votre mot de passe et exécutez les commandes suivantes :

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Ajouter un secret Kiali

Remplacez le jeton `REPLACE_WITH_YOUR_SECURE_PASSWORD` par votre mot de passe et exécutez les commandes suivantes :

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Installer les composants Istio

Maintenant que nous avons correctement créé les secrets Grafana et Kiali dans notre cluster AKS, il est temps d’installer les composants Istio. Utilisez Helm et le chart `istio` pour installer les composants Istio dans l’espace de noms `istio-system`, dans votre cluster AKS. Utilisez les commandes appropriées pour votre environnement.

> [!NOTE]
> Nous utilisons les options suivantes dans le cadre de notre installation :
> - `global.controlPlaneSecurityEnabled=true` - Mutual TLS est activé pour le plan de contrôle
> - `mixer.adapters.useAdapterCRDs=false` - supprimer les observations sur les définitions CRD des adaptateurs Mixer étant donné qu’elles sont vouées à être déconseillées et que cela améliorera les performances
> - `grafana.enabled=true` - activer le déploiement de Grafana pour bénéficier de tableaux de bord d’analyse et de surveillance
> - `grafana.security.enabled=true` - activer l’authentification pour Grafana
> - `tracing.enabled=true` - activer le déploiement de Jaeger pour le suivi
> - `kiali.enabled=true` - activer le déploiement de Kiali pour bénéficier d’un tableau de bord d’observation du maillage de services

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Le chart Helm `istio` déploie un grand nombre d’objets. Vous pouvez voir la liste à partir de la sortie de votre commande `helm install` ci-dessus. Le déploiement des composants Istio peut prendre 4 à 5 minutes, selon votre environnement de cluster.

> [!NOTE]
> Tous les pods Istio doivent être planifiés pour s’exécuter sur des nœuds Linux. Si vous avez des pools de nœuds Windows Server en plus des pools de nœuds Linux sur votre cluster, vérifiez que tous les pods Istio ont été planifiés pour s’exécuter sur les nœuds Linux.

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
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
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
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Il doit y avoir deux pods `istio-init-crd-*` avec un état `Completed`. Ces pods étaient chargés d’exécuter les travaux qui ont créé les définitions CRD dans une étape précédente. Tous les autres pods doivent indiquer le statut `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leur sortie et leur état.

## <a name="accessing-the-add-ons"></a>Accès aux modules complémentaires

Une série de modules complémentaires fournissant des fonctionnalités supplémentaires a été installée pour Istio au cours de l’installation ci-dessus. Les interfaces utilisateur des modules complémentaires ne sont pas exposées publiquement par le biais d’une adresse IP externe. Pour accéder aux interfaces utilisateur des modules complémentaires, utilisez la commande [kubectl port-forward][kubectl-port-forward]. Cette commande crée une connexion sécurisée entre votre machine cliente et le pod approprié dans votre cluster AKS.

Nous avons ajouté une couche supplémentaire de sécurité pour Grafana et Kiali en spécifiant pour eux des informations d’identification, plus haut dans cet article.

### <a name="grafana"></a>Grafana

Les tableaux de bord d’analyse et de surveillance pour Istio sont fournis par [Grafana][grafana]. Transférez le port local `3000` de votre machine cliente au port `3000` du pod qui exécute Grafana dans votre cluster AKS :

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Grafana :

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Vous pouvez maintenant atteindre Grafana à l’URL suivante sur votre machine cliente : [http://localhost:3000](http://localhost:3000). Pensez à utiliser les informations d’identification que vous avez créées via le secret Grafana précédemment lorsque vous y êtes invité.

### <a name="prometheus"></a>Prometheus

Les indicateurs de performance pour Istio sont fournis par [Prometheus][prometheus]. Transférez le port local `9090` de votre machine cliente au port `9090` du pod qui exécute Prometheus dans votre cluster AKS :

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

Le suivi au sein d’Istio est assuré par [Jaeger][jaeger]. Transférez le port local `16686` de votre machine cliente au port `16686` du pod qui exécute Jaeger dans votre cluster AKS :

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

Un tableau de bord d’observation du maillage de services est fourni par [Kiali][kiali]. Transférez le port local `20001` de votre machine cliente au port `20001` du pod qui exécute Kiali dans votre cluster AKS :

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Kiali :

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Vous pouvez maintenant atteindre le tableau de bord d’observabilité du maillage de services Kiali à l’URL suivante sur votre machine cliente : [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Pensez à utiliser les informations d’identification que vous avez créées via le secret Kiali précédemment lorsque vous y êtes invité.

## <a name="uninstall-istio-from-aks"></a>Désinstaller Istio d’AKS

> [!WARNING]
> La suppression d’Istio à partir d’un système en cours d’exécution peut entraîner des problèmes de trafic entre vos services. Vérifiez que vous avez mis en place des dispositions pour que votre système continue de fonctionner correctement sans Istio avant de continuer.

### <a name="remove-istio-components-and-namespace"></a>Supprimer les composants et l’espace de noms Istio

Pour supprimer Istio de votre cluster AKS, utilisez les commandes suivantes. Les commandes `helm delete` suppriment les charts `istio` et `istio-init`, et la commande `kubectl delete ns` supprime l’espace de noms `istio-system`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Supprimer les définitions CRD Istio

Les commandes ci-dessus suppriment l’espace de noms et tous les composants Istio, mais il nous reste les définitions CRD Istio. Pour supprimer les définitions CRD, vous pouvez utiliser l’une des approches suivantes.

Approche n° 1 - Cette commande suppose que vous exécutez cette étape à partir du dossier de niveau supérieur de la version téléchargée et extraite d’Istio que vous avez utilisée pour installer Istio.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Approche n° 2 - Utilisez l’une de ces commandes si vous n’avez plus accès à la version téléchargée et extraite d’Istio que vous avez utilisée pour installer Istio. Cette commande prend un peu plus de temps et dure généralement quelques minutes.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Étapes suivantes

La documentation suivante décrit comment utiliser Istio pour fournir un routage intelligent afin de déployer un contrôle de validité de mise en production :

> [!div class="nextstepaction"]
> [Scénario de routage intelligent Istio AKS][istio-scenario-routing]

Pour explorer davantage d’options de configuration et d’installation pour Istio, consultez les articles Istio officiels suivants :

- [Istio - Helm installation guide][istio-install-helm]
- [Istio - Helm installation options][istio-install-helm-options]

Vous pouvez également suivre des scénarios supplémentaires à l’aide de l’[exemple d’application Bookinfo d’Istio][istio-bookinfo-example].

Pour découvrir comment surveiller votre application AKS à l’aide d’Application Insights et d’Istio, consultez la documentation Azure Monitor suivante :
- [Surveillance d’applications sans instrumentation pour les applications hébergées Kubernetes][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/about/notes/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md
