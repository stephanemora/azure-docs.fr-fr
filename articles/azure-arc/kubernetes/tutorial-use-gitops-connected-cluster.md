---
title: 'Tutoriel : Déployer des configurations à l’aide de GitOps sur un cluster Kubernetes avec Azure Arc'
description: Ce tutoriel montre comment appliquer des configurations sur un cluster Kubernetes avec Azure Arc. Pour une interprétation conceptuelle de ce processus, consultez l’article Configurations et GitOps - Kubernetes avec Azure Arc.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial , devx-track-azurecli
ms.openlocfilehash: 1c639c323fcd0dd0ed9f417070d679defeb4cf1e
ms.sourcegitcommit: 20abee54e48f9b40b83d39c5b970bd0193812cb6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/12/2021
ms.locfileid: "113632427"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Tutoriel : Déployer des configurations à l’aide de GitOps sur un cluster Kubernetes avec Azure Arc 

Dans ce tutoriel, vous allez appliquer des configurations à l’aide de GitOps sur un cluster Kubernetes avec Azure Arc. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer une configuration sur un cluster Kubernetes avec Azure Arc à l’aide d’un exemple de dépôt Git.
> * Vérifier que la configuration a été correctement créée.
> * Appliquer une configuration à partir d'un référentiel Git privé.
> * Valider la configuration de Kubernetes.

## <a name="prerequisites"></a>Prérequis

- Compte Azure avec un abonnement actif. [Créez un compte gratuitement](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Cluster Kubernetes avec Azure Arc connecté.
    - Si vous n’avez pas encore connecté de cluster, consultez notre [guide de démarrage rapide sur la connexion d’un cluster Kubernetes avec Azure Arc](quickstart-connect-cluster.md).
- Compréhension suffisante des avantages et de l’architecture de cette fonctionnalité. Pour plus d’informations, consultez l’article [Configurations et GitOps - Kubernetes avec Azure Arc](conceptual-configurations.md).
- Installez l'extension `k8s-configuration` d'Azure CLI, version >= 1.0.0 :
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

    >[!TIP]
    > Si l'extension `k8s-configuration` est déjà installée, vous pouvez la mettre à jour vers la version la plus récente à l'aide de la commande suivante - `az extension update --name k8s-configuration`

- Si votre référentiel git est situé en dehors du pare-feu et que le protocole git est utilisé avec le paramètre de dépôt de configuration, TCP sur le port 9418 (`git://:9418`) doit être activé pour l’accès sortant sur le pare-feu.

## <a name="create-a-configuration"></a>Créer une configuration

L’[exemple de dépôt](https://github.com/Azure/arc-k8s-demo) utilisé dans cet article est structuré autour d’un personnage, l’opérateur de cluster. Les manifestes de ce dépôt provisionnent quelques espaces de noms, déploient des charges de travail et fournissent une configuration spécifique à l’équipe. L’utilisation de ce dépôt avec GitOps permet de créer les ressources suivantes sur votre cluster :

* Espaces de noms : `cluster-config`, `team-a`, `team-b`
* Déploiement : `arc-k8s-demo`
* ConfigMap : `team-a/endpoints`

Le `config-agent` interroge Azure sur l’existence de configurations nouvelles ou mises à jour. Cette tâche peut prendre jusqu’à 5 minutes.

Si vous associez un dépôt privé à la configuration, effectuez les étapes ci-dessous dans [Appliquer une configuration à partir d’un dépôt Git privé](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Utiliser l’interface de ligne de commande Microsoft Azure
Utilisez l’extension Azure CLI pour `k8s-configuration` pour lier notre cluster connecté à un [exemple de référentiel Git](https://github.com/Azure/arc-k8s-demo). 
1. Nommez cette configuration `cluster-config`.
1. Demandez à l’agent de déployer l’opérateur dans l’espace de noms `cluster-config`.
1. Accordez les autorisations `cluster-admin` à l’opérateur.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Utiliser un dépôt Git public

| Paramètre | Format |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] or git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Utiliser un dépôt Git privé avec des clés créées par SSH et Flux

Ajoutez la clé publique générée par Flux au compte d’utilisateur dans votre fournisseur de services Git. Si la clé est ajoutée au dépôt au lieu du compte d’utilisateur, utilisez `git@` à la place de `user@` dans l’URL. 

Pour plus d’informations, accédez à la section [Appliquer une configuration à partir d’un dépôt Git privé](#apply-configuration-from-a-private-git-repository).


| Paramètre | Format | Notes
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Utiliser un dépôt Git privé avec SSH et des clés fournies par l’utilisateur

Fournissez votre propre clé privée directement ou dans un fichier. La clé doit être au format [PEM](https://aka.ms/PEMformat) et se terminer par un saut de ligne (\n). 

Ajoutez la clé publique associée au compte d’utilisateur dans votre fournisseur de services Git. Si la clé est ajoutée au dépôt au lieu du compte d’utilisateur, utilisez `git@` à la place de `user@`. 

Pour plus d’informations, accédez à la section [Appliquer une configuration à partir d’un dépôt Git privé](#apply-configuration-from-a-private-git-repository).  

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@` |
| `--ssh-private-key` | clé encodée en base64 au [format PEM](https://aka.ms/PEMformat) | Fournissez la clé directement |
| `--ssh-private-key-file` | chemin d’accès complet au fichier local | Indiquez le chemin d’accès complet au fichier local qui contient la clé au format PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Utilisez un hôte Git privé avec des hôtes SSH et connus par l’utilisateur

L’opérateur Flux gère une liste d’hôtes Git courants dans son fichier d’hôtes connus pour authentifier le dépôt Git avant d’établir la connexion SSH. Si vous utilisez un dépôt Git *peu courant* ou votre propre hôte Git, vous pouvez fournir la clé d’hôte pour permettre à Flux d’identifier votre dépôt. 

Tout comme les clés privées, vous pouvez fournir le contenu known_hosts directement ou dans un fichier. Quand vous fournissez votre propre contenu, utilisez les [spécifications de format de contenu known_hosts](https://aka.ms/KnownHostsFormat) ainsi que l’un des scénarios relatifs aux clés SSH ci-dessus.

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@` |
| `--ssh-known-hosts` | encodé en base64 | Fournissez directement le contenu des hôtes connus |
| `--ssh-known-hosts-file` | chemin d’accès complet au fichier local | Fournissez le contenu des hôtes connus dans un fichier local |

### <a name="use-a-private-git-repository-with-https"></a>Utiliser un dépôt Git privé avec HTTPS

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo [.git] | HTTPS avec authentification de base |
| `--https-user` | brut ou codé en base64 | Nom d’utilisateur HTTPS |
| `--https-key` | brut ou codé en base64 | Mot de passe ou jeton d’accès personnel HTTPS

>[!NOTE]
>* Le chart d’opérateur Helm version 1.2.0+ prend en charge l’authentification privée pour la version Helm HTTPS.
>* La version Helm HTTPS n’est pas prise en charge pour les clusters managés AKS.
>* Si vous avez besoin de Flux pour accéder au dépôt Git via votre proxy, vous devez mettre à jour les agents Azure Arc avec les paramètres du proxy. Pour plus d’informations, consultez [Se connecter à l’aide d’un serveur proxy sortant](./quickstart-connect-cluster.md#5-connect-using-an-outbound-proxy-server).


## <a name="additional-parameters"></a>Paramètres supplémentaires

Personnalisez la configuration avec les paramètres facultatifs suivants :

| Paramètre | Description |
| ------------- | ------------- |
| `--enable-helm-operator`| Commutateur pour activer la prise en charge des déploiements de charts Helm. |
| `--helm-operator-params` | Valeurs de chart pour l’opérateur Helm (si activé). Par exemple : `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Version de chart pour l’opérateur Helm (si activé). Utilisez la version 1.2.0 et ultérieure. Valeur par défaut : '1.2.0'. |
| `--operator-namespace` | Nom de l’espace de noms de l’opérateur. Valeur par défaut : « default ». 23 caractères au maximum. |
| `--operator-params` | Paramètres de l’opérateur. Doit être entouré d’apostrophes. Par exemple : ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Options prises en charge dans `--operator-params`

| Option | Description |
| ------------- | ------------- |
| `--git-branch`  | Branche du dépôt Git à utiliser pour les manifestes Kubernetes. Par défaut, « master ». Les référentiels plus récents ont une branche racine nommée `main`, auquel cas vous devez définir `--git-branch=main`. |
| `--git-path`  | Chemin relatif dans le dépôt Git pour permettre à Flux de localiser les manifestes Kubernetes. |
| `--git-readonly` | Le dépôt Git est considéré comme étant en lecture seule. Flux ne tentera pas d’y accéder en écriture. |
| `--manifest-generation`  | Si l’option est activée, Flux recherche .flux.yaml et exécute Kustomize ou d’autres générateurs de manifeste. |
| `--git-poll-interval`  | Période à laquelle interroger le dépôt Git sur les nouveaux commits. La valeur par défaut est `5m` (5 minutes). |
| `--sync-garbage-collection`  | Si l’option est activée, Flux supprime les ressources qu’il a créées mais qui ne sont plus présentes dans Git. |
| `--git-label`  | Étiquette permettant de suivre la progression de la synchronisation. Utilisée pour étiqueter la branche GIT.  La valeur par défaut est `flux-sync`. |
| `--git-user`  | Nom d’utilisateur pour la validation Git. |
| `--git-email`  | Email à utiliser pour la validation Git. 

Si vous ne souhaitez pas que Flux écrive dans le dépôt, et si `--git-user` ou `--git-email` ne sont pas définis, `--git-readonly` est automatiquement défini.

Pour plus d’informations, consultez la [documentation relative au flux](https://aka.ms/FluxcdReadme).

>[!NOTE]
> Valeurs Flux par défaut à synchroniser à partir de la branche `master` du dépôt git. Toutefois, les dépôts git plus récents ont la branche racine qui s’appelle `main`, donc vous devez définir `--git-branch=main` dans --operator-params. 

> [!TIP]
> Vous pouvez créer une configuration dans le portail Azure sous l’onglet **GitOps** de la ressource Kubernetes avec Azure Arc.

## <a name="validate-the-configuration"></a>Valider la configuration

Utilisez Azure CLI pour vérifier si la configuration a été correctement créée.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

La ressource de configuration va être mise à jour avec l’état de conformité, les messages et les informations de débogage.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quand une configuration est créée ou mise à jour, il se passe certaines choses :

1. Le `config-agent` d’Azure Arc surveille Azure Resource Manager pour les configurations nouvelles ou mises à jour (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`) et remarque la nouvelle configuration `Pending`.
1. Le `config-agent` lit les propriétés de configuration et crée l’espace de noms de destination.
1. Le `controller-manager` Azure Arc crée un compte de service Kubernetes et le mappe à [ClusterRoleBinding ou RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) pour disposer des autorisations appropriées (étendue `cluster` ou `namespace`). Il déploie ensuite une instance de `flux`.
1. Si vous utilisez l’option SSH avec les clés générées par le flux, `flux` génère une clé SSH et enregistre la clé publique.
1. Le `config-agent` signale l’état à la ressource de configuration dans Azure.

Pendant le processus de provisionnement, la ressource de configuration passe par quelques changements d’état. Surveillez la progression avec la commande `az k8s-configuration show ...` ci-dessus :

| Modification du stade | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Représente les états initial et en cours. |
| `complianceStatus` -> `Installed`  | `config-agent` a correctement configuré le cluster et a déployé `flux` sans erreur. |
| `complianceStatus` -> `Failed` | `config-agent` a rencontré une erreur durant le déploiement de `flux`. Les détails sont fournis dans le corps de la réponse de `complianceStatus.message`. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Appliquez une configuration à partir d’un référentiel Git privé

Si vous utilisez un dépôt Git privé, vous devez configurer la clé publique SSH dans votre dépôt. Soit vous la fournissez, soit Flux génère la clé publique SSH. Vous pouvez configurer la clé publique pour le dépôt Git spécifié ou pour l’utilisateur Git ayant accès au dépôt. 

### <a name="get-your-own-public-key"></a>Obtenir vos propres adresses IP publiques

Si vous avez généré vos propres clés SSH, vous disposez déjà des clés privées et publiques.

#### <a name="get-the-public-key-using-azure-cli"></a>Obtenir la clé publique à l’aide d’Azure CLI 

Utilisez ce qui suit dans Azure CLI si Flux génère les clés.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Obtenir la clé à partir du portail Azure

Effectuez les étapes suivantes dans le portail Azure si Flux génère les clés.

1. Dans le portail Azure, accédez à la ressource de cluster connectée.
2. Dans la page des ressources, sélectionnez « GitOps », puis consultez la liste des configurations pour ce cluster.
3. Sélectionnez la configuration qui utilise le dépôt Git privé.
4. Au bas de la fenêtre contextuelle qui s’ouvre, copiez la **Repository public key** (Clé publique du dépôt).

#### <a name="add-public-key-using-github"></a>Ajouter une clé publique à l’aide de GitHub

Utilisez l’une des options suivantes :

* Option 1 : Ajoutez la clé publique à votre compte d’utilisateur (s’applique à tous les dépôts de votre compte) :  
    1. Ouvrez GitHub et cliquez sur l’icône de votre profil dans le coin supérieur droit de la page.
    2. Cliquez sur **Paramètres**.
    3. Cliquez sur **Clés SSH et GPG**.
    4. Cliquez sur **Nouvelle clé SSH**.
    5. Tapez un titre.
    6. Collez la clé publique sans les guillemets qui l’entourent.
    7. Cliquez sur **Ajouter une clé SSH**.

* Option 2 : Ajoutez la clé publique en tant que clé de déploiement au dépôt Git (s’applique uniquement à ce dépôt) :  
    1. Ouvrez GitHub et accédez à votre dépôt.
    1. Cliquez sur **Paramètres**.
    1. Cliquez sur **Déployer les clés**.
    1. Cliquez sur **Ajouter une clé de déploiement**.
    1. Tapez un titre.
    1. Activez l’option **Autoriser l’accès en écriture**.
    1. Collez la clé publique sans les guillemets qui l’entourent.
    1. Cliquez sur **Ajouter une clé**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Ajouter une clé publique à l’aide d’un référentiel Azure DevOps

Pour ajouter la clé à vos clés SSH, procédez comme suit :

1. Sous **Paramètres utilisateur** en haut à droite (à côté de l’image de profil), cliquez sur **Clés publiques SSH**.
1. Sélectionnez **+ Nouvelle clé**.
1. Tapez un nom.
1. Collez la clé publique sans les guillemets qui l’entourent.
1. Cliquez sur **Add**.

## <a name="validate-the-kubernetes-configuration"></a>Valider la configuration de Kubernetes

Après que `config-agent` a installé l’instance `flux`, les ressources se trouvant dans le référentiel Git doivent commencer à circuler vers le cluster. Vérifiez que les espaces de noms, les déploiements et les ressources ont été créés à l’aide de la commande suivante :

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Nous pouvons voir que les espaces de noms `team-a`, `team-b`, `itops` et `cluster-config` ont été créés.

L’opérateur `flux` a été déployé sur l’espace de noms `cluster-config`, comme indiqué par la ressource de configuration :

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Exploration approfondie

Vous pouvez explorer les autres ressources déployées dans le référentiel de configuration en utilisant ce qui suit :

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Nettoyer les ressources

Supprimez une configuration à l’aide de l’interface Azure CLI ou du portail Azure. Une fois la commande de suppression exécutée, la ressource de configuration est supprimée immédiatement dans Azure. La suppression complète des objets associés du cluster doit se produire dans les 10 minutes. Si la configuration est à l’état d’échec quand elle est supprimée, la suppression complète des objets associés peut prendre jusqu’à une heure.

Quand une configuration ayant l’étendue `namespace` est supprimée, l’espace de noms n’est pas supprimé par Azure Arc pour éviter l’arrêt des charges de travail existantes. Si nécessaire, vous pouvez supprimer cet espace de noms manuellement à l’aide de `kubectl`.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Les changements apportés au cluster et qui résultent des déploiements effectués à partir du dépôt Git suivi ne sont pas supprimés quand la configuration est supprimée.

## <a name="next-steps"></a>Étapes suivantes

Passez au tutoriel suivant pour apprendre à implémenter CI/CD avec GitOps.
> [!div class="nextstepaction"]
> [Implémenter CI/CD avec GitOps](./tutorial-gitops-ci-cd.md)
