---
title: Installer Istio dans AKS (Azure Kubernetes Service)
description: Découvrez comment installer et utiliser Istio pour créer un maillage de services (service mesh) dans un cluster AKS (Azure Kubernetes Service)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 12565d2b8004a5119add25473e5b088c9162035f
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/16/2019
ms.locfileid: "65780496"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Installer et utiliser Istio dans AKS (Azure Kubernetes Service)

[Istio][istio-github] est un maillage de services open source qui fournit un jeu de fonctionnalités de base dans les microservices d’un cluster Kubernetes. Ces fonctionnalités incluent la gestion du trafic, l’identité et la sécurité des services, l’application des stratégies et l’observabilité. Pour plus d’informations sur Istio, consultez la documentation officielle [What is Istio?][istio-docs-concepts].

Cet article vous montre comment installer Istio. Le Istio `istioctl` client binaire est installé sur votre ordinateur client et les composants Istio sont installés dans un cluster Kubernetes sur AKS.

> [!NOTE]
> Ces instructions font référence Istio version `1.1.3`.
>
> Le Istio `1.1.x` versions ont été testées par l’équipe Istio contre les versions Kubernetes `1.11`, `1.12`, `1.13`. Vous pouvez trouver des versions Istio supplémentaires sur le site [GitHub - libère Istio] [ istio-github-releases] et des informations sur chacune des versions à [Istio - Notes de publication] [ istio-release-notes].

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Télécharger Istio
> * Installer le client d’istioctl Istio binaire
> * Installer les CRDs Istio sur AKS
> * Installez les composants d’Istio sur AKS
> * Valider l’installation d’Istio
> * Accès aux modules complémentaires
> * Désinstaller Istio d’AKS

## <a name="before-you-begin"></a>Avant de commencer

Les étapes décrites dans cet article supposent que vous avez créé un cluster AKS (Kubernetes `1.11` et versions ultérieures, avec RBAC activé) et établi un `kubectl` connexion avec le cluster. Si vous avez besoin d’aide avec un de ces éléments, consultez le [guide de démarrage rapide d’AKS][aks-quickstart].

Vous devez [Helm] [ helm] suivez ces instructions et d’installer Istio. Il est recommandé d’avoir version `2.12.2` ou version ultérieure correctement installé et configuré dans votre cluster. Si vous avez besoin d’aide avec l’installation de Helm, consultez le [consignes d’installation AKS Helm][helm-install].

Cet article répartit les instructions d’installation d’Istio en plusieurs étapes discrètes. Le résultat final est, par sa structure, identique à celui obtenu avec le [guide][istio-install-helm] officiel d’installation d’Istio.

## <a name="download-istio"></a>Télécharger Istio

Tout d’abord, téléchargez et extrayez la dernière version d’Istio. Les étapes sont légèrement différentes pour un interpréteur de commandes bash sur MacOS, Linux ou sous-système Windows pour Linux et pour un interpréteur de commandes PowerShell. Choisissez une des étapes d’installation suivantes correspondant à votre environnement par défaut :

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

Maintenant passer à la section à [installer le client d’istioctl Istio binaire](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

Dans PowerShell, utilisez `Invoke-WebRequest` pour télécharger la dernière version d’Istio, puis extrayez avec `Expand-Archive` comme suit :

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Maintenant passer à la section à [installer le client d’istioctl Istio binaire](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>Installer le client d’istioctl Istio binaire

> [!IMPORTANT]
> Assurez-vous que vous exécutez les étapes décrites dans cette section, à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargé et extrait.

Le `istioctl` binaire client s’exécute sur votre ordinateur client et vous permet d’interagir avec la maille de services Istio. Les étapes d’installation sont un peu différentes entre les systèmes d’exploitation clients. Choisissez une des étapes d’installation suivantes correspondant à votre environnement par défaut :

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

Maintenant passer à la section suivante pour [installez-y la CRDs Istio AKS](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux ou sous-système Windows pour Linux

Utilisez les commandes suivantes pour installer le binaire lient `istioctl` Istio dans un interpréteur de commandes bash sur Linux ou le [sous-système Windows pour Linux][install-wsl]. Ces commandes copient le binaire client `istioctl` à l’emplacement du programme utilisateur standard dans votre `PATH`.

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

Maintenant passer à la section suivante pour [installez-y la CRDs Istio AKS](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Pour installer le Istio `istioctl` client binaire dans un **Powershell**-shell en sur Windows, utilisez les commandes suivantes. Ces commandes copient le `istioctl` binaire dans un dossier Istio client et le rendre disponible définitivement via votre `PATH`. Vous n’avez pas besoin des privilèges élevés (Admin) pour exécuter ces commandes.

```powershell
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"
$PATH = [environment]::GetEnvironmentVariable("PATH", "User")
[environment]::SetEnvironmentVariable("PATH", $PATH + "; C:\Istio\", "User")
```

Maintenant passer à la section suivante pour [installez-y la CRDs Istio AKS](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installer les CRDs Istio sur AKS

> [!IMPORTANT]
> Assurez-vous que vous exécutez les étapes décrites dans cette section, à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargé et extrait.

Istio utilise [définitions de ressource personnalisé (CRDs)] [ kubernetes-crd] pour gérer la configuration de son exécution. Nous devons installer la CRDs Istio tout d’abord, étant donné que les composants Istio ont une dépendance sur eux. Utilisez Helm et `istio-init` graphique pour installer le CRDs Istio dans le `istio-system` espace de noms dans votre cluster AKS :

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Travaux] [ kubernetes-jobs] sont déployés dans le cadre de la `istio-init` graphique Helm pour installer les CRDs. Ces travaux doit prendre entre 1 à 2 minutes en fonction de votre environnement de cluster. Vous pouvez vérifier que les tâches terminées avec succès comme suit :

```azurecli
kubectl get jobs -n istio-system
```

L’exemple de sortie suivant montre les tâches terminées avec succès.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Maintenant que nous avons confirmé la réussite des travaux, nous allons vérifier que nous avons le nombre correct de CRDs Istio installé. Vous pouvez vérifier que tous les CRDs Istio 53 ont été installés en exécutant la commande appropriée pour votre environnement. La commande doit renvoyer le nombre `53`.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Si vous avez à ce stade, cela signifie que vous avez correctement installé le CRDs Istio. Maintenant passer à la section suivante pour [installer les composants d’Istio sur AKS](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>Installez les composants d’Istio sur AKS

> [!IMPORTANT]
> Assurez-vous que vous exécutez les étapes décrites dans cette section, à partir du dossier de niveau supérieur de la version d’Istio que vous avez téléchargé et extrait.

Nous allons installer [Grafana] [ grafana] et [Kiali] [ kiali] dans le cadre de notre installation Istio. Grafana fournit analytique et la surveillance des tableaux de bord et Kiali fournit un tableau de bord l’observabilité de maillage de service. Dans notre configuration, chacun de ces composants requiert des informations d’identification qui doivent être fournies en tant qu’un [Secret][kubernetes-secrets].

Avant que nous pouvons installer les composants Istio, nous devons créer les clés secrètes pour Grafana et Kiali. Créez ces secrets en exécutant les commandes appropriées pour votre environnement.

### <a name="add-grafana-secret"></a>Ajouter la clé secrète de Grafana

Remplacez le `REPLACE_WITH_YOUR_SECURE_PASSWORD` jeton avec votre mot de passe, puis exécutez les commandes suivantes :

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

### <a name="add-kiali-secret"></a>Ajouter Kiali Secret

Remplacez le `REPLACE_WITH_YOUR_SECURE_PASSWORD` jeton avec votre mot de passe, puis exécutez les commandes suivantes :

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

### <a name="install-istio-components"></a>Installer les composants d’Istio

Maintenant que nous avons correctement créé les secrets de Grafana et Kiali dans notre cluster AKS, il est temps pour installer les composants d’Istio. Utilisez Helm et `istio` graphique pour installer les composants Istio dans le `istio-system` espace de noms dans votre cluster AKS. Utilisez les commandes appropriées pour votre environnement.

> [!NOTE]
> Nous utilisons les options suivantes dans le cadre de notre installation :
> - `global.controlPlaneSecurityEnabled=true` -TLS mutuelle est activée pour le plan de contrôle
> - `mixer.adapters.useAdapterCRDs=false` -supprimer les observations sur la carte de mixer CRDs comme ils déconseillé et cela améliorera les performances
> - `grafana.enabled=true` -Activer le déploiement de Grafana pour l’analytique et la surveillance des tableaux de bord
> - `grafana.security.enabled=true` -Activer l’authentification pour Grafana
> - `tracing.enabled=true` -Activer le déploiement Jaeger pour le suivi
> - `kiali.enabled=true` -Activer le déploiement Kiali pour un tableau de bord l’observabilité de maillage de service

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

Le `istio` graphique Helm déploie un grand nombre d’objets. Vous pouvez voir la liste à partir de la sortie de votre `helm install` commande ci-dessus. Le déploiement des composants Istio peut prendre 4 à 5 minutes, selon votre environnement de cluster.

À ce stade, vous avez déployé Istio dans votre cluster AKS. Pour vous assurer que nous avons un déploiement réussi de Istio, nous allons passer à la section suivante pour [valider l’installation de Istio](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>Valider l’installation d’Istio

Tout d’abord, vérifiez que les services attendus ont été créés. Utilisez la commande [kubectl get svc][kubectl-get] pour afficher les services en cours d’exécution. Requête la `istio-system` espace de noms, où les composants Istio et module complémentaire ont été installés par le `istio` graphique Helm :

```console
kubectl get svc --namespace istio-system --output wide
```

L’exemple de sortie suivant montre les services qui doivent maintenant être en cours d’exécution :

- `istio-*` services
- `jaeger-*`, `tracing`, et `zipkin` services suivi du module complémentaire
- `prometheus` service de métriques de module complémentaire
- `grafana` analytique de module complémentaire et le service de tableau de bord de surveillance
- `kiali` service de tableau de bord de maillage de service complémentaire

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

Ensuite, vérifiez que les pods requis ont été créés. Utilisez le [kubectl get pods] [ kubectl-get] commande et l’interroger à nouveau la `istio-system` espace de noms :

```console
kubectl get pods --namespace istio-system
```

L’exemple de sortie suivant montre les pods en cours d’exécution :

- le `istio-*` pods
- le `prometheus-*` pod de métriques de module complémentaire
- le `grafana-*` analytique de module complémentaire et surveillance pod de tableau de bord
- le `kiali` pod de tableau de bord de maillage de service module complémentaire

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

Il doit y avoir deux `istio-init-crd-*` pods avec un `Completed` état. Ces POD n’était chargé d’exécuter les travaux qui a créé les CRDs à l’étape précédente. Tous les autres pods doivent indiquer le statut `Running`. Si vos pods n’ont pas cet état, patientez une minute ou deux. Si des pods signalent un problème, utilisez la commande [kubectl describe pod][kubectl-describe] pour examiner leurs sortie et état.

## <a name="accessing-the-add-ons"></a>Accès aux modules complémentaires

Une série de modules complémentaires fournissant des fonctionnalités supplémentaires a été installée pour Istio au cours de l’installation ci-dessus. Les interfaces utilisateur des modules complémentaires ne sont pas exposées publiquement par le biais d’une adresse IP externe. Pour accéder aux interfaces utilisateur des modules complémentaires, utilisez la commande [kubectl port-forward][kubectl-port-forward]. Cette commande crée une connexion sécurisée entre votre ordinateur client et le pod pertinentes dans votre cluster AKS.

Nous avons ajouté une couche supplémentaire de sécurité pour Grafana et Kiali en spécifiant les informations d’identification de leur plus haut dans cet article.

### <a name="grafana"></a>Grafana

L’analytique et la surveillance des tableaux de bord pour Istio sont fournis par [Grafana][grafana]. Transférez le port local `3000` sur votre ordinateur client au port `3000` sur le pod qui est en cours d’exécution Grafana dans votre cluster AKS :

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Grafana :

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

Vous pouvez maintenant atteindre Grafana à l’URL suivante sur votre machine cliente : [http://localhost:3000](http://localhost:3000). Pensez à utiliser les informations d’identification que vous avez créé via le Grafana secrète précédemment lorsque vous y êtes invité.

### <a name="prometheus"></a>Prometheus

Métriques pour Istio sont fournies par [Prometheus][prometheus]. Transférez le port local `9090` sur votre ordinateur client au port `9090` sur le pod qui est en cours d’exécution Prometheus dans votre cluster AKS :

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

Le suivi au sein d’Istio est fourni par [Jaeger][jaeger]. Transférez le port local `16686` sur votre ordinateur client au port `16686` sur le pod qui est en cours d’exécution Jaeger dans votre cluster AKS :

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

Un tableau de bord d’observabilité du maillage de services est fourni par [Kiali][kiali]. Transférez le port local `20001` sur votre ordinateur client au port `20001` sur le pod qui est en cours d’exécution Kiali dans votre cluster AKS :

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

L’exemple de sortie suivant illustre le transfert de port configuré pour Kiali :

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

Vous pouvez maintenant atteindre le tableau de bord d’observabilité du maillage de services Kiali à l’URL suivante sur votre machine cliente : [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Pensez à utiliser les informations d’identification que vous avez créé via le Kiali secrète précédemment lorsque vous y êtes invité.

## <a name="uninstall-istio-from-aks"></a>Désinstaller Istio d’AKS

> [!WARNING]
> Suppression d’Istio à partir d’un système en cours d’exécution peut entraîner le trafic des problèmes entre vos services. Vérifiez que vous avez apporté des dispositions pour que votre système continuent de fonctionner correctement sans Istio avant de continuer.

### <a name="remove-istio-components-and-namespace"></a>Supprimer des composants Istio et espace de noms

Pour supprimer Istio à partir de votre cluster AKS, utilisez les commandes suivantes. Le `helm delete` commandes suppriment la `istio` et `istio-init` graphiques et le `kubectl delete ns` commande supprimera le `istio-system` espace de noms.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Supprimer Istio CRDs

Les commandes ci-dessus suppriment tous les composants d’Istio et espace de noms, mais nous restons confrontés toujours à la CRDs Istio. Pour supprimer les CRDs, faites une des approches suivantes.

Approche #1 : cette commande suppose que vous exécutiez cette étape à partir du dossier de niveau supérieur de la version téléchargé et extrait de Istio que vous avez utilisé pour installer Istio avec.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Approche #2 : utilisez une de ces commandes si vous n’avez plus accès à la version téléchargée et extraite d’Istio que vous avez utilisé pour installer Istio avec. Cette commande peut prendre un peu Pluse - cette opération peut prendre quelques minutes pour terminer.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Étapes suivantes

La documentation suivante décrit comment vous pouvez utiliser Istio pour fournir un routage intelligent pour déployer cette version :

> [!div class="nextstepaction"]
> [Scénario de routage intelligent Istio AKS][istio-scenario-routing]

Pour explorer davantage d’options de configuration et d’installation pour Istio, consultez les articles Istio officiels suivants :

- [Istio - Helm installation guide][istio-install-helm]
- [Istio - Helm installation options][istio-install-helm-options]

Vous pouvez également suivre des scénarios supplémentaires à l’aide de l’[exemple d’application Bookinfo d’Istio][istio-bookinfo-example].

Pour savoir comment surveiller votre application AKS à l’aide d’Application Insights et Istio, consultez la documentation d’Azure Monitor suivante :
- [Applications hébergées par zéro analyse des applications instrumentation pour Kubernetes][app-insights]

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