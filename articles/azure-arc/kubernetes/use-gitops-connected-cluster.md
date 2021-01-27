---
title: Déployer des configurations à l’aide de GitOps sur un cluster Kubernetes compatible avec Azure Arc (préversion)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utiliser GitOps pour configurer un cluster Kubernetes à extension Azure Arc (préversion)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, conteneurs
ms.openlocfilehash: a068ed90ea53b3b25a1f41cebd9a5b8e607afa54
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737182"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Déployer des configurations à l’aide de GitOps sur un cluster Kubernetes compatible avec Azure Arc (préversion)

GitOps, par rapport à Kubernetes, est la pratique qui consiste à déclarer l’état souhaité de la configuration de Kubernetes (déploiements, espaces de noms, etc.) dans un référentiel Git, suivi d’un déploiement par interrogation et par extraction de ces configurations vers le cluster à l’aide d’un opérateur. Ce document traite de la configuration de ces workflows sur des clusters Kubernetes compatibles avec Azure Arc.

La connexion entre votre cluster et un dépôt Git créé dans Azure Resource Manager en tant que ressource d’extension `Microsoft.KubernetesConfiguration/sourceControlConfigurations`. Les propriétés de ressource `sourceControlConfiguration` indiquent où et comment les ressources Kubernetes doivent circuler de Git vers votre cluster. Les données `sourceControlConfiguration` sont stockées et chiffrées au repos dans une base de données Azure Cosmos DB pour garantir leur confidentialité.

Le `config-agent` s’exécutant dans votre cluster est responsable de la surveillance des ressources d’extension `sourceControlConfiguration` nouvelles ou mises à jour sur la ressource Kubernetes compatible avec Azure Arc, du déploiement d’un opérateur de flux pour surveiller le référentiel Git pour chaque `sourceControlConfiguration` et appliquer toute mise à jour nécessaire à `sourceControlConfiguration`. Il est possible de créer plusieurs ressources `sourceControlConfiguration` sur le même cluster Kubernetes compatible avec Azure Arc afin d’obtenir une architecture mutualisée. Vous pouvez créer chaque `sourceControlConfiguration` avec une autre étendue `namespace` pour limiter les déploiements dans les espaces de noms respectifs.

Le dépôt Git peut contenir des manifestes au format YAML qui décrivent toutes sortes de ressources Kubernetes valides, telles que Namespaces, ConfigMaps, Deployments, DaemonSets, etc.  Il peut également contenir des graphiques Helm pour le déploiement d’applications. Un ensemble commun de scénarios inclut la définition d’une configuration de base pour votre organisation, qui peut inclure des rôles et liaisons Azure courants, des agents de supervision ou de journalisation, ou des services à l’échelle du cluster.

Le même modèle peut être utilisé pour gérer une plus grande collection de clusters, qui peut être déployée dans des environnements hétérogènes. Par exemple, vous pouvez avoir un dépôt qui définit la configuration de base pour votre organisation et l’appliquer à des dizaines de clusters Kubernetes à la fois. [Azure Policy peut automatiser](use-azure-policy.md) la création d’un `sourceControlConfiguration` avec un ensemble spécifique de paramètres sur toutes les ressources Kubernetes compatibles avec Azure Arc dans une étendue (abonnement ou groupe de ressources).

Ce guide de prise en main explique comment appliquer un ensemble de configurations avec une étendue d’administration de cluster.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous disposez d’un cluster connecté Kubernetes avec Azure Arc. Si vous avez besoin d’un cluster connecté, reportez-vous au [guide de démarrage rapide Connecter un cluster](./connect-cluster.md).

## <a name="create-a-configuration"></a>Créer une configuration

L’[exemple de dépôt](https://github.com/Azure/arc-k8s-demo) utilisé dans ce document est structuré autour du personnage d’un opérateur de cluster qui souhaite provisionner quelques espaces de noms, déployer une charge de travail commune et fournir une configuration propre à une équipe. L’utilisation de ce dépôt a pour effet de créer les ressources suivantes sur votre cluster :

**Namespaces:** `cluster-config`, `team-a`, `team-b`
**Deployment:** `cluster-config/azure-vote`
**ConfigMap:** `team-a/endpoints`

Le `config-agent` interroge Azure à la recherche d’un `sourceControlConfiguration` nouveau ou mis à jour toutes les 30 secondes, ce qui correspond à la durée maximale prise par `config-agent` pour récupérer une configuration nouvelle ou mise à jour.
Si vous associez un dépôt privé à `sourceControlConfiguration`, assurez-vous d’accomplir également les étapes décrites dans [Appliquer une configuration à partir d’un dépôt Git privé](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Utilisation de l’interface de ligne de commande Azure

Utilisez l’extension Azure CLI pour `k8sconfiguration` pour lier notre cluster connecté à un [exemple de référentiel Git](https://github.com/Azure/arc-k8s-demo). Nous allons donner à cette configuration un nom `cluster-config`, demander à l’agent de déployer l’opérateur dans l’espace de noms `cluster-config` et accorder à l’opérateur des autorisations `cluster-admin`.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repo"></a>Utilisez un référentiel Git public

| Paramètre | Format |
| ------------- | ------------- |
| --repository-url | http[s]://server/repo[.git] or git://server/repo[.git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Utilisez un référentiel Git privé avec des clés SSH et créées par flux

| Paramètre | Format | Notes
| ------------- | ------------- | ------------- |
| --repository-url | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@`

> [!NOTE]
> La clé publique générée par Flux doit être ajoutée au compte d’utilisateur dans votre fournisseur de services Git. Si la clé est ajoutée au référentiel au lieu du compte d’utilisateur, utilisez `git@` à la place de `user@` dans l’URL. [Afficher plus de détails](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Utilisez un référentiel Git privé avec des clés SSH et fournies par l’utilisateur

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@` |
| --ssh-private-key | clé encodée en base64 au [format PEM](https://aka.ms/PEMformat) | Fournissez la clé directement |
| --ssh-private-key-file | chemin d’accès complet au fichier local | Indiquez le chemin d’accès complet au fichier local qui contient la clé au format PEM

> [!NOTE]
> Fournissez votre propre clé privée directement ou dans un fichier. La clé doit être au format [PEM](https://aka.ms/PEMformat) et se terminer par un saut de ligne (\n).  La clé publique associée doit être ajoutée au compte d’utilisateur dans votre fournisseur de services Git. Si la clé est ajoutée au référentiel au lieu du compte d’utilisateur, utilisez `git@` à la place de `user@`. [Afficher plus de détails](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Utilisez un hôte Git privé avec des hôtes SSH et connus par l’utilisateur

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url  | ssh://user@server/repo[.git] ou user@server:repo[.git] | `git@` peut remplacer `user@` |
| --ssh-known-hosts | encodé en base64 | contenu des hôtes connus fourni directement |
| --ssh-hosts-known-file | chemin d’accès complet au fichier local | contenu des hôtes connus fourni dans un fichier local

> [!NOTE]
> L’opérateur de flux gère une liste d’hôtes Git courants dans son fichier d’hôtes connus afin d’authentifier le référentiel Git avant d’établir la connexion SSH. Si vous utilisez un référentiel Git non courant ou votre propre hôte Git, vous devrez peut-être fournir la clé hôte pour vous assurer que le flux peut identifier votre référentiel. Vous pouvez fournir le contenu de vos hôtes connus directement ou dans un fichier. [Afficher la spécification du format de contenu des hôtes connus](https://aka.ms/KnownHostsFormat).
> Vous pouvez l’utiliser conjointement avec l’un des scénarios de clé SSH décrits ci-dessus.

#### <a name="use-a-private-git-repo-with-https"></a>Utilisez un référentiel Git privé avec HTTPS

| Paramètre | Format | Notes |
| ------------- | ------------- | ------------- |
| --repository-url | https://server/repo [.git] | HTTPS avec authentification de base |
| --https-user | brut ou codé en base64 | Nom d’utilisateur HTTPS |
| --https-key | brut ou codé en base64 | Mot de passe ou jeton d’accès personnel HTTPS

> [!NOTE]
> L’authentification privée HTTPS Helm est prise en charge uniquement avec la version du graphique d’opérateur Helm > = 1.2.0.  La version 1.2.0 est utilisée par défaut.
> L’authentification privée Helm HTTPS n’est pas prise en charge actuellement pour les clusters gérés par Azure Kubernetes.
> Si vous avez besoin d’un flux pour accéder au référentiel Git par le biais de votre proxy, vous devez mettre à jour les agents Azure Arc avec les paramètres de proxy. [Plus d’informations](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Paramètres supplémentaires

Pour personnaliser la configuration, voici d’autres paramètres que vous pouvez utiliser :

`--enable-helm-operator` : commutateur *facultatif* pour activer la prise en charge des déploiements de graphiques Helm.

`--helm-operator-params` : valeurs de graphique *facultatives* pour l’opérateur Helm (si activé).  Par exemple, « --set helm.versions=v3 ».

`--helm-operator-chart-version` : version de graphique *facultative* pour l’opérateur Helm (si activé). Valeur par défaut : '1.2.0'.

`--operator-namespace` : nom *facultatif* pour l’espace de noms de l’opérateur. Valeur par défaut : « default ». 23 caractères maximum.

`--operator-params` : paramètres *facultatifs* pour l’opérateur. Doit être entouré d’apostrophes. Par exemple : ```--operator-params='--git-readonly --git-path=releases --sync-garbage-collection' ```

Options prises en charge dans --operator-params

| Option | Description |
| ------------- | ------------- |
| --git-branch  | Branche de référentiel Git à utiliser pour les manifestes Kubernetes. Par défaut, « master ». |
| --git-path  | Chemin d’accès relatif dans le dépôt Git pour permettre à Flux de localiser les manifestes Kubernetes. |
| --git-readonly | Le dépôt Git est considéré comme étant en lecture seule. Flux ne tente pas d’y écrire. |
| --manifest-generation  | Si l’option est activée, Flux recherche .flux.yaml et exécute Kustomize ou d’autres générateurs de manifeste. |
| --git-poll-interval  | Intervalle de sondage du dépôt Git pour voir s’il contient de nouvelles validations. La valeur par défaut est « 5m » (5 minutes). |
| --sync-garbage-collection  | Si l’option est activée, Flux supprime les ressources qu’il a créées mais qui ne sont plus présentes dans Git. |
| --git-label  | Étiquette permettant de suivre la progression de la synchronisation, utilisée pour baliser la branche Git.  La valeur par défaut est « flux-sync ». |
| --git-user  | Nom d’utilisateur pour la validation Git. |
| --git-email  | Email à utiliser pour la validation Git. |

* Si « --git-user » ou « --git-email » ne sont pas activés (ce qui signifie que vous ne souhaitez pas que Flux écrive dans le dépôt), l’option --git-readonly est automatiquement définie (si vous ne l’avez pas encore définie).

Pour plus d’informations, consultez la [documentation Flux](https://aka.ms/FluxcdReadme).

> [!TIP]
> Il est possible de créer une instance sourceControlConfiguration également sur le portail Azure, sous l’onglet **GitOps** du panneau des ressources Kubernetes compatibles avec Azure Arc.

## <a name="validate-the-sourcecontrolconfiguration"></a>Valider le configuration sourceControlConfiguration

Utilisez Azure CLI pour valider le fait que la configuration `sourceControlConfiguration` a été correctement créée.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Notez que la ressource `sourceControlConfiguration` est mise à jour avec un état de conformité, des messages et des informations de débogage.

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Lorsque de la création de la configuration `sourceControlConfiguration`, quelques événements se produisent en coulisses :

1. Le `config-agent` d’Azure Arc surveille la présence sur Azure Resource Manager de configurations nouvelles ou mises à jour (`Microsoft.KubernetesConfiguration/sourceControlConfigurations`).
1. `config-agent` remarque la nouvelle configuration `Pending`.
1. `config-agent` lit les propriétés de la configuration et prépare le déploiement d’une instance gérée de `flux`.
    * `config-agent` crée l’espace de noms de destination.
    * `config-agent` prépare un compte de service Kubernetes avec l’autorisation appropriée (étendue `cluster` ou `namespace`).
    * `config-agent` déploie une instance de `flux`.
    * `flux` génère une clé SSH et enregistre la clé publique (si vous utilisez l’option SSH avec les clés générées par le flux)
1. `config-agent` signale l’état à la ressource `sourceControlConfiguration` dans Azure

Pendant le processus d’approvisionnement, la configuration `sourceControlConfiguration` passe par quelques changements d’état. Surveillez la progression avec la commande `az k8sconfiguration show ...` ci-dessus :

1. `complianceStatus` -> `Pending` : représente les états initial et en cours.
1. `complianceStatus` -> `Installed` : `config-agent` a pu configurer correctement le cluster et déployer `flux` sans erreur.
1. `complianceStatus` -> `Failed`: `config-agent` a rencontré une erreur lors du déploiement de `flux`. Des détails doivent être disponibles dans le corps de la réponse `complianceStatus.message`

## <a name="apply-configuration-from-a-private-git-repository"></a>Appliquez une configuration à partir d’un référentiel Git privé

Si vous utilisez un référentiel Git privé, vous devez configurer la clé publique SSH dans votre référentiel. Vous pouvez configurer la clé publique soit sur le référentiel Git spécifique, soit sur l’utilisateur Git qui a accès au référentiel. La clé publique SSH correspond à celle que vous fournissez ou à celle générée par le flux.

**Obtenir vos propres adresses IP publiques**

Si vous avez généré vos propres clés SSH, vous disposez déjà des clés privées et publiques.

**Récupérez la clé publique à l’aide d’Azure CLI (utile si le flux génère les clés)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Récupérez la clé publique à partir du portail Azure (utile si le flux génère les clés)**

1. Dans le portail Azure, accédez à la ressource de cluster connectée.
2. Dans la page des ressources, sélectionnez « GitOps », puis consultez la liste des configurations pour ce cluster.
3. Sélectionnez la configuration qui utilise le dépôt Git privé.
4. Au bas de la fenêtre contextuelle qui s’ouvre, copiez la **Repository public key** (Clé publique du dépôt).

Si vous utilisez GitHub, utilisez l’une des deux options suivantes :

**Option n°1 : Ajoutez la clé publique à votre compte d’utilisateur (s’applique à tous les référentiels de votre compte)**

1. Ouvrez GitHub, puis cliquez sur l’icône de votre profil dans le coin supérieur droit de la page.
2. Cliquez sur **Paramètres**.
3. Cliquez sur **Clés SSH et GPG**
4. Cliquez sur **Nouvelle clé SSH**
5. Tapez un titre.
6. Collez la clé publique (sans les apostrophes qui l’entourent).
7. Cliquez sur **Ajouter une clé SSH**

**Option n°2 : Ajoutez la clé publique en tant que clé de déploiement au dépôt Git (s’applique seulement à ce référentiel)**

1. Ouvrez GitHub, accédez à votre référentiel, à **Paramètres**, puis à **Clés de déploiement**.
2. Cliquez sur **Ajouter une clé de déploiement**
3. Tapez un titre.
4. Activez l’option **Allow write access** (Autoriser l’accès en écriture).
5. Collez la clé publique (sans les apostrophes qui l’entourent).
6. Cliquez sur **Add key** (Ajouter une clé).

**Si vous utilisez un dépôt Azure DevOps, ajoutez la clé à vos clés SSH.**

1. Sous **User Settings** (Paramètres utilisateur) en haut à droite (en regard de l’image de profil), cliquez sur **SSH public keys** (Clés publiques SSH).
1. Sélectionnez **+ New Key** (+ Nouvelle clé).
1. Tapez un nom.
1. Collez la clé publique sans les apostrophes qui l’entourent.
1. Cliquez sur **Ajouter**

## <a name="validate-the-kubernetes-configuration"></a>Valider la configuration de Kubernetes

Après que `config-agent` a installé l’instance `flux`, les ressources se trouvant dans le référentiel Git doivent commencer à circuler vers le cluster. Vérifiez que les espaces de noms, les déploiements et les ressources ont été créés :

```console
kubectl get ns --show-labels
```

**Output:**

```console
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

L’opérateur de `flux` a été déployé vers l’espace de noms `cluster-config`, comme stipulé par notre `sourceControlConfig` :

```console
kubectl -n cluster-config get deploy  -o wide
```

**Output:**

```console
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Exploration approfondie

Vous pouvez explorer les autres ressources déployées dans le dépôt de configuration :

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Supprimer une configuration

Supprimez un `sourceControlConfiguration` à l’aide d’Azure CLI ou du portail Azure.  Une fois que vous avez lancé la commande de suppression, la ressource `sourceControlConfiguration` est supprimée immédiatement dans Azure, mais la suppression complète des objets associés du cluster prend normalement 10 minutes.  Si `sourceControlConfiguration` est en état d’échec lors de sa suppression, la suppression complète des objets associés peut prendre jusqu’à une heure.

> [!NOTE]
> Une fois qu’une étendue sourceControlConfiguration avec espace de noms est créée, il est possible pour les utilisateurs ayant une liaison de rôle `edit` sur l’espace de noms de déployer des charges de travail sur cet espace de noms. Lorsque cette étendue `sourceControlConfiguration` avec espace de noms est supprimée, l’espace de noms reste intact et ne peut pas être supprimé pour éviter d’arrêter ces autres charges de travail.  Si nécessaire, vous pouvez supprimer cet espace de noms manuellement avec kubectl.
> Les modifications apportées au cluster en raison des déploiements à partir du référentiel Git suivi ne sont pas supprimées lors de la suppression de `sourceControlConfiguration`.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Output:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Helm avec la configuration du contrôle de code source](./use-gitops-with-helm.md)
- [Utiliser Azure Policy pour gérer la configuration du cluster](./use-azure-policy.md)