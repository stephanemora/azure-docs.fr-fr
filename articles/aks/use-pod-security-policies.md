---
title: Utiliser des stratégies de sécurité pod dans Azure Kubernetes Service (ACS)
description: Apprenez à contrôler les admissions pod à l’aide de PodSecurityPolicy dans Azure Kubernetes Service (ACS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: iainfou
ms.openlocfilehash: 7ce311ab9c554481f64c6c9be40e2018893a0966
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013984"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Afficher un aperçu : sécuriser votre cluster à l’aide de stratégies de sécurité pod dans Azure Kubernetes Service (ACS)

Pour améliorer la sécurité de votre cluster AKS, vous pouvez limiter les pods pouvant être planifiée. PODS qui demandent des ressources que vous n’autorisez pas exécutés dans le cluster AKS. Vous définissez cet accès à l’aide de stratégies de sécurité pod. Cet article vous montre comment utiliser des stratégies de sécurité pod pour limiter le déploiement de pods dans ACS.

> [!IMPORTANT]
> Fonctionnalités de préversion AKS sont libre-service et participer. Les préversions sont fournies pour recueillir des commentaires et des bogues à partir de notre communauté. Toutefois, ils ne sont pas pris en charge par le support technique Azure. Si vous créez un cluster, ou ajoutez ces fonctionnalités à des clusters existants, ce cluster est non pris en charge jusqu'à ce que la fonctionnalité n’est plus disponible en version préliminaire et atteignent à la disposition générale (GA).
>
> Si vous rencontrez des problèmes avec les fonctionnalités en version préliminaire, [de signaler un problème sur le référentiel GitHub d’AKS][aks-github] par le nom de la fonctionnalité d’aperçu dans le titre du bogue.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

Vous avez besoin d’Azure CLI version 2.0.61 ou ultérieur installé et configuré. Exécutez  `az --version` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez  [Installation d’Azure CLI 2.0][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installer l’extension CLI de préversion d’aks

Les clusters AKS sont mis à jour pour activer les stratégies de sécurité pod à l’aide de la *aks-preview* extension CLI. Installer le *aks-preview* extension Azure CLI à l’aide du [az extension ajoutez] [ az-extension-add] commande, comme indiqué dans l’exemple suivant :

```azurecli-interactive
az extension add --name aks-preview
```

> [!NOTE]
> Si vous avez installé précédemment le *aks-preview* extension, installez toutes des mises à jour à l’aide de la `az extension update --name aks-preview` commande.

### <a name="register-pod-security-policy-feature-provider"></a>Inscrire le fournisseur de fonctionnalités de stratégie de sécurité pod

Pour créer ou mettre à jour un cluster AKS pour utiliser des stratégies de sécurité pod, tout d’abord activer un indicateur de fonctionnalité sur votre abonnement. Pour inscrire le *PodSecurityPolicyPreview* indicateur des fonctionnalités, utilisez le [register de fonctionnalité az] [ az-feature-register] commande comme indiqué dans l’exemple suivant :

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Quelques minutes sont nécessaires pour que l’état s’affiche *Registered* (Inscrit). Vous pouvez vérifier l'état de l'enregistrement à l'aide de la commande [az feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Lorsque vous êtes prêt, actualisez l’inscription du fournisseur de ressources *Microsoft.ContainerService* à l’aide de la commande [az provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Vue d’ensemble des stratégies de sécurité pod

Dans un cluster Kubernetes, un contrôleur d’admission est utilisé pour intercepter les demandes au serveur API lorsqu’une ressource doit être créé. Le contrôleur d’admission peut ensuite *valider* la demande de ressource par rapport à un ensemble de règles, ou *muter* la ressource à modifier les paramètres de déploiement.

*PodSecurityPolicy* est un contrôleur d’admission qui valide une spécification de pod répond à vos besoins définis. Ces exigences peuvent limiter l’utilisation de conteneurs privilégiés, l’accès à certains types de stockage, ou que l’utilisateur ou groupe que conteneur peut s’exécuter en tant que. Lorsque vous tentez de déployer une ressource où les spécifications de pod ne répondent pas aux exigences décrites dans la stratégie de sécurité pod, la demande est refusée. Cette possibilité de contrôler les pods pouvant être planifiée dans l’ACS cluster empêche certaines vulnérabilités de sécurité possibles ou les escalades de privilèges.

Lorsque vous activez la stratégie de sécurité pod dans un cluster AKS, certaines stratégies par défaut sont appliquées. Ces stratégies par défaut fournissent une expérience out-of-the-box pour définir les pods peut être planifiée. Toutefois, les utilisateurs de cluster peuvent rencontrer des problèmes de déploiement de pods jusqu'à ce que vous définissez vos propres stratégies. L’approche recommandée consiste à :

* Créer un cluster AKS
* Définir vos propres stratégies de sécurité pod
* Activer la fonctionnalité de stratégie de sécurité pod

Pour montrer comment les stratégies par défaut limitent les déploiements de pod, dans cet article nous tout d’abord activer la fonctionnalité de stratégies de sécurité pod, puis créer une stratégie personnalisée.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Activer la stratégie de sécurité pod sur un cluster AKS

Vous pouvez activer ou désactiver la stratégie de sécurité pod à l’aide du [az aks mettre à jour] [ az-aks-update] commande. La stratégie de sécurité de pod du permet de suivant exemple sur le nom du cluster *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*.

> [!NOTE]
> Pour une utilisation du monde réel, n’activez pas la stratégie de sécurité pod jusqu'à ce que vous avez défini vos propres stratégies personnalisées. Dans cet article, vous activez la stratégie de sécurité pod en tant que la première étape pour voir comment les stratégies par défaut limitent pod déploiements.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Stratégies AKS par défaut

Lorsque vous activez la stratégie de sécurité pod, AKS crée deux stratégies par défaut nommés *privilégié* et *restreint*. Ne pas modifier ou supprimer ces stratégies par défaut. Au lieu de cela, créez vos propres stratégies qui définissent les paramètres souhaités pour le contrôle. Nous allons examen quelles ces stratégies par défaut sont leur impact sur les déploiements de pod.

Pour afficher les stratégies disponibles, utilisez la [kubectl get psp] [ kubectl-get] commande, comme indiqué dans l’exemple suivant. Dans le cadre de la valeur par défaut *restreint* stratégie, l’utilisateur est refusé *PRIV* destiné à la réaffectation de pod privilégié et l’utilisateur *MustRunAsNonRoot*.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted   false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Le *restreint* stratégie de sécurité pod est appliquée à tout utilisateur authentifié dans le cluster AKS. Cette affectation est contrôlée par ClusterRoles et ClusterRoleBindings. Utilisez le [kubectl get clusterrolebindings] [ kubectl-get] commande et recherchez le *par défaut : restreint :* liaison :

```console
kubectl get clusterrolebindings default:restricted -o yaml
```

Comme indiqué dans la sortie condensée suivante, le *psp : restreint* ClusterRole est assigné à aucun *système : authentifié* les utilisateurs. Cette possibilité offre un niveau de base des restrictions sans vos propres stratégies en cours de définition.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:restricted
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:restricted
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Il est important de comprendre comment ces stratégies par défaut interagissent avec les requêtes utilisateur pour planifier les pods avant de commencer à créer des stratégies de sécurité de votre propre bloc. Dans les sections suivantes, nous allons planifier certains pods pour afficher ces stratégies par défaut en action.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Créer un utilisateur de test dans un cluster AKS

Par défaut, lorsque vous utilisez le [az aks get-credentials] [ az-aks-get-credentials] commande, le *administrateur* des informations d’identification pour le cluster AKS et ajouté à votre `kubectl` config. L’utilisateur administrateur ignore la mise en œuvre des stratégies de sécurité pod. Si vous utilisez l’intégration d’Azure Active Directory pour vos clusters AKS, vous pouvez se connecter avec les informations d’identification d’un utilisateur non administrateur pour afficher la mise en œuvre des stratégies en action. Dans cet article, nous allons créer un compte d’utilisateur de test dans le cluster AKS que vous pouvez utiliser.

Créer un espace de noms exemple nommé *psp-aks* pour les ressources de test à l’aide de la [kubectl créer l’espace de noms] [ kubectl-create] commande. Ensuite, créez un compte de service nommé *nonadmin-utilisateur* à l’aide de la [kubectl créer serviceaccount] [ kubectl-create] commande :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Ensuite, créez un RoleBinding pour la *nonadmin-utilisateur* pour effectuer des actions de base dans l’espace de noms à l’aide de la [kubectl créer rolebinding] [ kubectl-create] commande :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Créer des commandes alias d’utilisateur administrateur et non-administrateur

Pour mettre en évidence la différence entre l’utilisateur admin régulière lors de l’utilisation `kubectl` et l’utilisateur non administrateur créé dans les étapes précédentes, créez deux alias de ligne de commande :

* Le **kubectl-admin** alias est de l’utilisateur admin régulière et est limité à la *psp-aks* espace de noms.
* Le **kubectl-nonadminuser** alias concerne le *nonadmin-utilisateur* créé à l’étape précédente et est limité à la *psp-aks* espace de noms.

Créer ces deux alias comme indiqué dans les commandes suivantes :

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Tester la création d’un pod privilégié

Nous allons commencer par tester que se passe-t-il lorsque vous planifiez un pod avec le contexte de sécurité `privileged: true`. Ce contexte de sécurité fait remonter des privilèges du pod. Dans la section précédente qui montrait le pod d’ACS par défaut des stratégies de sécurité, le *restreint* stratégie doit refuser cette demande.

Créez un fichier nommé `nginx-privileged.yaml` et collez le manifeste YAML suivant :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Créez le pod à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Le pod ne parvient pas à être planifié, comme indiqué dans l’exemple de sortie suivant :

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Le pod n’atteint pas la phase de planification, il n’existe aucune ressource à supprimer avant de poursuivre.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testez la création d’un pod non privilégié

Dans l’exemple précédent, la spécification de pod a demandé une élévation des privilèges. Cette demande est refusée par la valeur par défaut *restreint* pod de stratégie de sécurité, donc le pod ne parvient pas à être planifiée. Nous allons essayer ce même bloc NGINX sans que la demande d’élévation de privilège en cours d’exécution.

Créez un fichier nommé `nginx-unprivileged.yaml` et collez le manifeste YAML suivant :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Créez le pod à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Le planificateur Kubernetes accepte la demande de pod. Toutefois, si vous examinez l’état du pod en utilisant `kubectl get pods`, il existe une erreur :

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Utilisez le [kubectl décrivent pod] [ kubectl-describe] commande pour examiner les événements pour le pod. L’exemple condensé suivant montre que le conteneur et l’image nécessitent des autorisations de la racine, même si nous n’avez pas demandé les :

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Même si nous n’avez pas demandé un accès privilégié, l’image de conteneur pour NGINX a besoin créer une liaison de port *80*. Pour lier les ports *1024* et versions antérieures, le *racine* utilisateur est requis. Lorsque le pod essaie de démarrer, le *restreint* pod la stratégie de sécurité refuse cette demande.

Cet exemple montre que les stratégies de sécurité pod par défaut créés par AKS sont en vigueur et limitent les actions qu’un utilisateur peut effectuer. Il est important de comprendre le comportement de ces stratégies par défaut, comme vous ne pouvez pas prévoir un pod NGINX de base doit être refusé.

Avant de passer à l’étape suivante, supprimez ce pod de test à l’aide de la [kubectl supprimer pod] [ kubectl-delete] commande :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testez la création d’un pod avec un contexte utilisateur spécifique

Dans l’exemple précédent, l’image de conteneur automatiquement tenté racine à lier NGINX au port 80. Cette demande a été refusée par la valeur par défaut *restreint* pod de stratégie de sécurité, donc le pod ne parvient pas à démarrer. Nous allons essayer maintenant en cours d’exécution ce même bloc NGINX avec un contexte utilisateur spécifique, tel que `runAsUser: 2000`.

Créez un fichier nommé `nginx-unprivileged-nonroot.yaml` et collez le manifeste YAML suivant :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Créez le pod à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Le planificateur Kubernetes accepte la demande de pod. Toutefois, si vous examinez l’état du pod en utilisant `kubectl get pods`, il existe une erreur autre que l’exemple précédent :

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Utilisez le [kubectl décrivent pod] [ kubectl-describe] commande pour examiner les événements pour le pod. L’exemple condensé suivant montre les événements de pod :

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

Les événements indiquent que le conteneur a été créé et démarré. Il n’y a rien de tout de suite évident quant à la raison pour laquelle le pod est en état d’échec. Nous allons examiner les journaux du pod à l’aide de la [kubectl journaux] [ kubectl-logs] commande :

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

L’exemple de sortie de journal suivante donne une indication qui au sein de la configuration de NGINX lui-même, il existe une erreur d’autorisations lorsque le service essaie de démarrer. Cette erreur est due à nouveau par devoir lier au port 80. Bien que la spécification de pod défini un compte d’utilisateur normal, ce compte d’utilisateur n’est pas suffisant dans le système d’exploitation pour le service NGINX pour démarrer et le lier au port restreint.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Là encore, il est important de comprendre le comportement des stratégies de sécurité pod par défaut. Cette erreur était un peu plus difficile à repérer et à nouveau, vous ne pouvez pas prévoir un pod NGINX de base doit être refusé.

Avant de passer à l’étape suivante, supprimez ce pod de test à l’aide de la [kubectl supprimer pod] [ kubectl-delete] commande :

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Créer une stratégie de sécurité pod personnalisé

Maintenant que vous avez vu le comportement des stratégies de sécurité par défaut pod, nous allons fournir un moyen pour le *nonadmin-utilisateur* à planifier correctement les pods.

Nous allons créer une stratégie pour rejeter les pods qui demandent l’accès privilégié. Autres options, telles que *runAsUser* ou autorisé *volumes*, ne sont pas explicitement restreint. Ce type de stratégie refuse une requête pour l’accès privilégié, mais sinon exécutons le cluster les pods demandées.

Créez un fichier nommé `psp-deny-privileged.yaml` et collez le manifeste YAML suivant :

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Créer la stratégie à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f psp-deny-privileged.yaml
```

Pour afficher les stratégies disponibles, utilisez la [kubectl get psp] [ kubectl-get] commande, comme indiqué dans l’exemple suivant. Comparer la *psp refuser privilégié* stratégie avec la valeur par défaut *restreint* stratégie était appliquée dans les exemples précédents pour créer un pod. Uniquement l’utilisation de *PRIV* escalade de verrous est refusé par votre stratégie. Il n’existe aucune restriction sur l’utilisateur ou le groupe pour le *psp refuser privilégié* stratégie.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
restricted            false          RunAsAny   MustRunAsNonRoot   MustRunAs   MustRunAs   false            configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Autoriser le compte d’utilisateur à utiliser la stratégie de sécurité pod personnalisé

L’étape précédente, vous avez créé une stratégie de sécurité pod pour rejeter les pods que l’accès privilégié de demande. Pour autoriser la stratégie à utiliser, vous créez un *rôle* ou un *ClusterRole*. Ensuite, vous associez un de ces rôles à l’aide un *RoleBinding* ou *ClusterRoleBinding*.

Pour cet exemple, créez un ClusterRole qui vous permet de *utiliser* le *psp refuser privilégié* stratégie créée à l’étape précédente. Créez un fichier nommé `psp-deny-privileged-clusterrole.yaml` et collez le manifeste YAML suivant :

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Créer le ClusterRole à l’aide de la [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Créez maintenant un ClusterRoleBinding pour utiliser le ClusterRole créé à l’étape précédente. Créez un fichier nommé `psp-deny-privileged-clusterrolebinding.yaml` et collez le manifeste YAML suivant :

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Créer un à l’aide de ClusterRoleBinding le [kubectl appliquer] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> Dans la première étape de cet article, la fonctionnalité de stratégie de sécurité pod a été activée sur le cluster AKS. Cette pratique recommandée consistait à activer uniquement la fonctionnalité de stratégie de sécurité pod une fois que vous avez défini vos propres stratégies. Il s’agit du stade où vous devez activer la fonctionnalité de stratégie de sécurité pod. Un ou plusieurs stratégies personnalisées ont été définies, et les comptes d’utilisateur ont été associées à ces stratégies. Désormais, vous pouvez en toute sécurité de la stratégie de sécurité pod de fonctionnalités et de réduire les problèmes causés par les stratégies par défaut.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Tester la création d’un pod non privilégié

Avec votre stratégie de sécurité pod personnalisé appliqué et une liaison pour le compte d’utilisateur à utiliser la stratégie, nous allons essayer à nouveau de créer un pod non privilégié. Utiliser le même `nginx-privileged.yaml` manifeste pour créer le pod à l’aide de la [kubectl appliquer] [ kubectl-apply] commande :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Le pod est planifié avec succès. Lorsque vous vérifiez l’état du pod en utilisant le [kubectl get pods] [ kubectl-get] commande, le pod est *en cours d’exécution*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Cet exemple montre comment vous pouvez créer des stratégies de sécurité pod personnalisé pour définir l’accès au cluster AKS pour différents utilisateurs ou groupes. Par conséquent, la valeur par défaut, les stratégies ACS fournissent un contrôle strict les pods permettre exécuter, créer vos propres stratégies personnalisées, puis définir correctement les restrictions que vous avez besoin.

Supprimer le pod NGINX non privilégié à l’aide de la [kubectl supprimer] [ kubectl-delete] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Supprimer des ressources

Pour désactiver la stratégie de sécurité pod, utilisez le [az aks mettre à jour] [ az-aks-update] réexécutez la commande. La stratégie de sécurité de pod du désactive de suivant exemple sur le nom du cluster *myAKSCluster* dans le groupe de ressources nommé *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Ensuite, supprimez le ClusterRole et ClusterRoleBinding :

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Supprimer la stratégie de réseau à l’aide [kubectl supprimer] [ kubectl-delete] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl delete -f psp-deny-privileged.yaml
```

Enfin, supprimez le *psp-aks* espace de noms :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré comment créer une stratégie de sécurité pod pour empêcher l’utilisation de l’accès privilégié. Il existe un grand nombre de fonctionnalités à une stratégie peut imposer, tels que le type de volume ou de l’utilisateur RunAs. Pour plus d’informations sur les options disponibles, consultez le [Kubernetes pod documents de référence de stratégie de sécurité][kubernetes-policy-reference].

Pour plus d’informations sur la limitation du trafic réseau de pod, consultez [sécuriser le trafic entre les pods dans ACS à l’aide de stratégies de réseau][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference
[aks-github]: https://github.com/azure/aks/issues

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
