---
title: Résoudre les problèmes courants liés à Azure Kubernetes Service
description: Découvrir comment résoudre les problèmes courants liés à l’utilisation d’AKS (Azure Kubernetes Service)
services: container-service
author: sauryadas
ms.topic: troubleshooting
ms.date: 12/13/2019
ms.author: saudas
ms.openlocfilehash: b7aa90bd19e52059319570f1e7f6e64b90dee6e4
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593345"
---
# <a name="aks-troubleshooting"></a>Résolution des problèmes liés à AKS

Lorsque vous créez ou gérez des clusters Azure Kubernetes Service (AKS), vous pouvez occasionnellement rencontrer des problèmes. Cet article décrit en détail certains problèmes courants, et indique comment les résoudre.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>En règle générale, où trouver des informations sur le débogage de problèmes liés à Kubernetes ?

Essayez de vous référer au [guide officiel de résolution des problèmes de clusters Kubernetes](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Il existe également un [guide de résolution des problèmes](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) publié par un ingénieur Microsoft, portant sur le dépannage de pods, nœuds, clusters et autres fonctionnalités.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>J’obtiens une erreur de dépassement de quota pendant une opération de création ou de mise à niveau. Que dois-je faire ? 

Vous devez [demander des cœurs](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Quel est le nombre maximal de pods par nœud pour AKS ?

Le nombre maximal de pods par nœud est défini 30 par défaut si vous déployez un cluster AKS dans le portail Microsoft Azure.
Le nombre maximal de pods par nœud est 110 par défaut si vous déployez un cluster AKS dans Azure CLI. (assurez-vous que vous disposez de la version la plus récente d’Azure CLI). Ce paramètre par défaut peut être modifié à l’aide de l’indicateur `–-max-pods` dans la commande `az aks create`.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>J’obtiens un erreur insufficientSubnetSize quand je déploie un cluster AKS avec des fonctionnalités réseau avancées. Que dois-je faire ?

En cas d’utilisation d’Azure CNI (mise en réseau avancée), AKS alloue les adresses IP en fonction des « max-pods » par nœud configuré. Sur la base du nombre maximal de pods configurés, la taille de sous-réseau doit être supérieure au produit du nombre de nœuds et au paramètre du nombre maximal de pods par nœud. L’équation suivante en donne un aperçu :

Taille du sous-réseau > nombre de nœuds dans le cluster (en tenant compte des besoins futurs de mise à l’échelle) * nombre maximum de pods par ensemble de nœuds.

Pour plus d’informations, consultez [Planifier l’adressage IP pour votre cluster](configure-azure-cni.md#plan-ip-addressing-for-your-cluster).

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mon pod est bloqué en mode CrashLoopBackOff. Que dois-je faire ?

Il peut y avoir diverses raisons pour que le pod soit bloqué dans ce mode. Vous pourriez examiner :

* Le pod lui-même, en utilisant `kubectl describe pod <pod-name>`.
* Les journaux d’activité en utilisant `kubectl logs <pod-name>`.

Pour plus d’informations sur la façon de résoudre les problèmes de pod, voir [Déboguer des applications](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods).

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>J’essaie d’activer le contrôle d’accès en fonction du rôle (RBAC) sur un cluster existant. Comment procéder ?

Malheureusement, l’activation du contrôle d’accès en fonction du rôle (RBAC) sur des clusters existants n’est pas prise en charge actuellement. Vous devez créer explicitement de nouveaux clusters. Si vous utilisez l’interface CLI, le contrôle d’accès en fonction du rôle (RBAC) est activé par défaut. Si vous utilisez le portail AKS, un bouton bascule pour activer le contrôle d’accès en fonction du rôle (RBAC) est disponible dans le flux de travail de création.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>J’ai créé un cluster avec le contrôle d’accès en fonction du rôle (RBAC) activé à l’aide d’Azure CLI avec les valeurs par défaut ou du portail Microsoft Azure, et je vois maintenant de nombreux avertissements sur le tableau de bord Kubernetes. Le tableau de bord n’affichait généralement aucun avertissement. Que dois-je faire ?

Les avertissements sur le tableau de bord s’expliquent par le fait que le cluster est désormais activé avec le contrôle d’accès en fonction du rôle (RBAC) et que son accès a été désactivé par défaut. En règle générale, cette approche est une bonne pratique parce que l’exposition par défaut du tableau de bord à tous les utilisateurs du cluster peut entraîner des menaces de sécurité. Si vous souhaitez quand même activer le tableau de bord, procédez de la manière décrite dans ce [billet de blog](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Je ne parviens pas à me connecter au tableau de bord. Que dois-je faire ?

La façon la plus simple d’accéder à votre service à l’extérieur du cluster consiste à exécuter `kubectl proxy`, qui redirige les demandes adressées via proxy à votre port localhost 8001 vers le serveur d’API Kubernetes. À partir de là, le serveur d’API peut réacheminer par proxy vers votre service : `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Si vous ne voyez pas le tableau de bord Kubernetes, vérifiez si le pod `kube-proxy` est en cours d’exécution dans l’espace de noms `kube-system`. S’il n’est pas en cours d’exécution, supprimez-le afin qu’il redémarre.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Je ne parviens pas à obtenir les journaux d’activité à l’aide des journaux d’activité de kubectl, ou à me connecter au serveur API. J’obtiens le message « Error from server: error dialing backend: dial tcp… ». Que dois-je faire ?

Vérifiez que le groupe de sécurité réseau par défaut n’est pas modifié, et que les ports 22 et 9000 sont ouverts pour la connexion au serveur d’API. Vérifiez que le pod `tunnelfront` s’exécute dans l’espace de noms *kube-system* à l’aide de la commande `kubectl get pods --namespace kube-system`. Si ce n’est pas le cas, forcez la suppression du pod pour qu’il redémarre.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>J’essaie d’effectuer une mise à niveau ou une mise à l’échelle et j’obtiens l’erreur suivante : « Changing property 'imageReference' is not allowed » (La modification de la propriété 'imageReference' n’est pas autorisée). Comment résoudre ce problème ?

Cette erreur s’affiche peut-être parce que vous avez modifié les balises dans les nœuds d’agent à l’intérieur du cluster AKS. La modification et la suppression de balises et d’autres propriétés de ressources dans le groupe de ressources MC_ peuvent entraîner des résultats inattendus. La modification des ressources du groupe MC_ * dans le cluster AKS empêche d’atteindre l’objectif de niveau de service (SLO).

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Je reçois des erreurs qui indiquent que mon cluster est en état d’échec et que la mise à niveau ou la mise à l’échelle n’aboutira pas tant que ce problème n’aura pas été résolu

*Cette assistance de dépannage est redirigée depuis https://aka.ms/aks-cluster-failed*

Cette erreur se produit quand des clusters basculent dans un état d’échec pour plusieurs raisons. Suivez les étapes ci-dessous pour résoudre l’état d’échec de votre cluster avant de tenter à nouveau l’opération qui a échoué précédemment :

1. Tant que le cluster n’aura pas quitté l’état `failed`, les opérations `upgrade` et `scale` n’aboutiront pas. Voici quelques-unes des causes courantes à l’origine du problème ainsi que des solutions :
    * Mise à l’échelle avec un **quota de calcul (CRP) insuffisant**. Pour résoudre ce problème, remettez votre cluster dans un état stable dans les limites du quota. Suivez ensuite ces [étapes pour demander une augmentation du quota de calcul](../azure-portal/supportability/resource-manager-core-quotas-request.md) avant de tenter à nouveau un scale-up au-delà des limites du quota initial.
    * Mise à jour d’un cluster avec des fonctionnalités réseau avancées et des **ressources (réseau) de sous-réseau insuffisantes**. Pour résoudre ce problème, remettez votre cluster dans un état stable dans les limites du quota. Suivez ensuite ces [étapes pour demander une augmentation du quota de ressources](../azure-resource-manager/templates/error-resource-quota.md#solution) avant de tenter à nouveau un scale-up au-delà des limites du quota initial.
2. Une fois que la cause sous-jacente de l’échec de mise à niveau est résolue, votre cluster doit être dans un état de réussite. Une fois que l’état de réussite a été vérifié, tentez à nouveau l’opération d’origine.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Je reçois des erreurs lors de la mise à niveau ou de la mise à l’échelle qui indiquent que mon cluster fait actuellement l’objet d’une mise à niveau ou que l’opération a échoué

*Cette assistance de dépannage est redirigée depuis https://aka.ms/aks-pending-upgrade*

Les opérations de mise à niveau et de mise à l’échelle sur un cluster doté d’un seul pool de nœuds ou un cluster doté de [plusieurs pools de nœuds](use-multiple-node-pools.md) s’excluent mutuellement. Il ne peut pas y avoir de mise à niveau et de mise à l’échelle simultanées d’un cluster ou d’un pool de nœuds. En effet, chaque opération doit être terminée sur la ressource cible avant l’exécution de la demande suivante sur cette même ressource. De ce fait, les opérations sont limitées quand des opérations de mise à niveau ou de mise à l’échelle actives se produisent ou sont entreprises et qu’elles échouent par la suite. 

Pour faciliter le diagnostic du problème, exécutez `az aks show -g myResourceGroup -n myAKSCluster -o table` pour récupérer l’état détaillé de votre cluster. Selon le résultat :

* Si le cluster est en cours de mise à niveau, patientez le temps que l’opération se termine. Si elle a abouti, tentez à nouveau l’opération qui a échoué auparavant.
* Si la mise la mise à niveau du cluster a échoué, suivez les étapes décrites dans la section précédente.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Puis-je déplacer mon cluster vers un autre abonnement ou mon abonnement et mon cluster vers un nouveau locataire ?

Si vous avez déplacé votre cluster AKS vers un autre abonnement ou l’abonnement propriétaire du cluster vers un nouveau locataire, le cluster perdra sa fonctionnalité en raison de la perte des attributions de rôles et des droits de principaux du service. **AKS ne prend pas en charge le déplacement de clusters entre abonnements ou locataires** du fait de cette contrainte.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Je reçois des erreurs quand j’essaie d’utiliser des fonctionnalités qui nécessitent des groupes de machines virtuelles identiques

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-vmss-enablement*

Vous pouvez recevoir des erreurs qui indiquent que votre cluster AKS n’appartient pas à un groupe de machines virtuelles identiques, comme dans l’exemple suivant :

**AgentPool 'agentpool' has set auto scaling as enabled but is not on Virtual Machine Scale Sets**

Pour utiliser des fonctionnalités telles que le composant Cluster Autoscaler ou plusieurs pools de nœuds, il est nécessaire de créer des clusters AKS qui utilisent des groupes de machines virtuelles identiques. Des erreurs sont retournées si vous tentez d’utiliser des fonctionnalités qui dépendent de groupes de machines virtuelles identiques et que vous ciblez un cluster AKS normal composé d’un groupe de machines identiques non virtuelles.

Suivez les étapes *Avant de commencer* dans le document approprié pour créer correctement un cluster AKS :

* [Utiliser le composant Cluster Autoscaler](cluster-autoscaler.md)
* [Créer et utiliser plusieurs pools de nœuds](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Quelles restrictions d’affectation de noms sont appliquées pour les paramètres et les ressources AKS ?

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-naming-rules*

Les restrictions d’affectation de noms sont implémentées par la plateforme Azure et AKS. Si un nom ou paramètre de ressource enfreint une de ces restrictions, une erreur est retournée qui vous invite à fournir une entrée différente. Voici quelques-unes des recommandations qui s’appliquent en matière d’affectation de noms :

* Les noms de cluster doivent comporter entre 1 et 63 caractères. Les seuls caractères autorisés sont les lettres, les chiffres, les tirets et les traits de soulignement. Le premier et le dernier caractères doivent être une lettre ou un chiffre.
* Le nom de groupe de ressources AKS *MC_* combine le nom de groupe de ressources et le nom de la ressource. La syntaxe générée automatiquement de `MC_resourceGroupName_resourceName_AzureRegion` ne doit pas dépasser 80 caractères. Si nécessaire, réduisez la longueur du nom de groupe de ressources ou du nom de cluster AKS.
* L'élément *dnsPrefix* doit commencer et se terminer par des valeurs alphanumériques et doit comporter 1 à 54 caractères. Parmi les caractères autorisés figurent les valeurs alphanumériques et les traits d’union (-). L’élément *dnsPrefix* ne peut pas inclure de caractères spéciaux comme un point (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Je reçois une erreur quand j’essaie de créer, mettre à jour, mettre à l’échelle, supprimer ou mettre à niveau un cluster ; ces opérations ne sont pas autorisées quand une autre opération est en cours.

*Cette assistance de dépannage est redirigée depuis aka.ms/aks-pending-operation*

Les opérations de cluster sont limitées quand une opération précédente est toujours en cours. Pour récupérer un état détaillé de votre cluster, utilisez la commande `az aks show -g myResourceGroup -n myAKSCluster -o table`. Utilisez le nom de votre propre groupe de ressources et de votre propre cluster AKS, selon les besoins.

Selon la sortie de l’état du cluster :

* Si le cluster est dans un autre état de provisionnement que *Opération réussie* ou *En échec*, attendez que l’opération (*mise à niveau/mise à jour/création/mise à l’échelle/suppression/migration*) se termine. Une fois que l’opération précédente a abouti, tentez à nouveau votre dernière opération de cluster.

* Si la mise à niveau du cluster a échoué, suivez les étapes décrites dans [Je reçois des erreurs qui indiquent que mon cluster est en état d’échec et que la mise à niveau ou la mise à l’échelle n’aboutira pas tant que ce problème n’aura pas été résolu](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Je reçois des erreurs indiquant que mon principal de service est introuvable quand j’essaie de créer un cluster sans passer un principal de service existant.

Lors de la création d’un cluster AKS, un principal de service doit créer des ressources en votre nom. AKS offre la possibilité de créer un nouveau principal de service au moment de la création du cluster, mais cela nécessite qu’Azure Active Directory propage entièrement le nouveau principal de service dans un délai raisonnable afin que le cluster puisse être créé. Si ce processus de propagation prend trop de temps, la validation de la création du cluster échoue, car il ne trouve pas de principal de service disponible. 

Essayez les solutions de contournement suivantes :
1. Utilisez un principal de service existant qui a déjà été propagé dans les régions et qui puisse être passé dans AKS au moment de la création du cluster.
2. Si vous utilisez des scripts d’automatisation, augmentez les délais entre la création du principal de service et la création du cluster AKS.
3. Si vous utilisez le portail Azure, revenez aux paramètres du cluster au moment de la création, puis recommencez la page de validation après quelques minutes.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Je reçois des erreurs après avoir restreint mon trafic sortant

Lors de la restriction du trafic sortant d’un cluster AKS, il existe des règles de ports de sortie/réseau ainsi que des règles de nom de domaine complet (FQDN)/application [obligatoires et facultatives recommandées](limit-egress-traffic.md) pour AKS. Si vos paramètres sont en conflit avec l’une de ces règles, vous risquez de ne pas pouvoir exécuter certaines commandes `kubectl`. Vous pouvez également voir des erreurs lors de la création d’un cluster AKS.

Vérifiez que vos paramètres ne sont pas en conflit avec l’une règles de ports de sortie/réseau ainsi que des règles de nom de domaine complet (FQDN)/application obligatoires ou facultatives recommandées.

## <a name="azure-storage-and-aks-troubleshooting"></a>Résolution des problèmes de stockage Azure et AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-disk"></a>Quelles sont les versions stables recommandées de Kubernetes pour le disque Azure ? 

| Version de Kubernetes | Version recommandée |
| -- | :--: |
| 1.12 | 1.12.9 ou version ultérieure |
| 1.13 | 1.13.6 ou version ultérieure |
| 1.14 | 1.14.2 ou version ultérieure |


### <a name="what-versions-of-kubernetes-have-azure-disk-support-on-the-sovereign-cloud"></a>Quelles sont les versions de Kubernetes disposant d’une prise en charge des disques Azure sur le cloud souverain ?

| Version de Kubernetes | Version recommandée |
| -- | :--: |
| 1.12 | 1.12.0 ou version ultérieure |
| 1.13 | 1.13.0 ou version ultérieure |
| 1.14 | 1.14.0 ou version ultérieure |


### <a name="waitforattach-failed-for-azure-disk-parsing-devdiskazurescsi1lun1-invalid-syntax"></a>Échec de WaitForAttach pour le disque Azure : analyse de « /dev/disk/azure/scsi1/lun1 » : syntaxe non valide

Dans Kubernetes version 1.10, MountVolume.WaitForAttach peut échouer avec un remontage de disque Azure.

Sur Linux, vous pouvez voir une erreur de format DevicePath incorrecte. Par exemple :

```console
MountVolume.WaitForAttach failed for volume "pvc-f1562ecb-3e5f-11e8-ab6b-000d3af9f967" : azureDisk - Wait for attach expect device path as a lun number, instead got: /dev/disk/azure/scsi1/lun1 (strconv.Atoi: parsing "/dev/disk/azure/scsi1/lun1": invalid syntax)
  Warning  FailedMount             1m (x10 over 21m)   kubelet, k8s-agentpool-66825246-0  Unable to mount volumes for pod
```

Sur Windows, il se peut que vous rencontriez une erreur de numéro d’unité logique (LUN) DevicePath incorrect. Par exemple :

```console
Warning  FailedMount             1m    kubelet, 15282k8s9010    MountVolume.WaitForAttach failed for volume "disk01" : azureDisk - WaitForAttach failed within timeout node (15282k8s9010) diskId:(andy-mghyb
1102-dynamic-pvc-6c526c51-4a18-11e8-ab5c-000d3af7b38e) lun:(4)
```

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1,10 | 1.10.2 ou version ultérieure |
| 1.11 | 1.11.0 ou version ultérieure |
| 1.12 et ultérieure | N/A |

### <a name="failure-when-setting-uid-and-gid-in-mountoptions-for-azure-disk"></a>Échec lors de la définition de l’UID et du GID dans mountOptions pour le disque Azure

Le disque Azure utilise par défaut le filesytem ext4,xfs, et des mountOptions telles que uid=x,gid=x ne peuvent pas être définies au moment du montage. Par exemple, si vous avez essayé de définir mountOptions uid=999,gid=999, une erreur semblable à la suivante s’affiche :

```console
Warning  FailedMount             63s                  kubelet, aks-nodepool1-29460110-0  MountVolume.MountDevice failed for volume "pvc-d783d0e4-85a1-11e9-8a90-369885447933" : azureDisk - mountDevice:FormatAndMount failed with mount failed: exit status 32
Mounting command: systemd-run
Mounting arguments: --description=Kubernetes transient mount for /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985 --scope -- mount -t xfs -o dir_mode=0777,file_mode=0777,uid=1000,gid=1000,defaults /dev/disk/azure/scsi1/lun2 /var/lib/kubelet/plugins/kubernetes.io/azure-disk/mounts/m436970985
Output: Running scope as unit run-rb21966413ab449b3a242ae9b0fbc9398.scope.
mount: wrong fs type, bad option, bad superblock on /dev/sde,
       missing codepage or helper program, or other error
```

Vous pouvez atténuer le problème en procédant de l’une des manières suivantes :

* [Configurez le contexte de sécurité pour un pod](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/) en définissant uid dans runAsUser et gid dans fsGroup. Par exemple, le paramètre suivant définit l’exécution de pod en tant que root et le rend accessible à n’importe quel fichier :

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: security-context-demo
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
```

  >[!NOTE]
  > Comme gid et uid sont montés en tant que root ou 0 par défaut. Si gid ou uid sont définis comme non root, par exemple 1000, Kubernetes utilise `chown` pour modifier tous les répertoires et fichiers sous ce disque. Cette opération peut prendre du temps et peut ralentir considérablement le montage du disque.

* Utilisez `chown` dans initContainers pour définir gid et uid. Par exemple :

```yaml
initContainers:
- name: volume-mount
  image: busybox
  command: ["sh", "-c", "chown -R 100:100 /data"]
  volumeMounts:
  - name: <your data volume>
    mountPath: /data
```

### <a name="error-when-deleting-azure-disk-persistentvolumeclaim-in-use-by-a-pod"></a>Erreur lors de la suppression du disque Azure PersistentVolumeClaim utilisé par un pod

Si vous essayez de supprimer un disque Azure PersistentVolumeClaim utilisé par un pod, une erreur peut s’afficher. Par exemple :

```console
$ kubectl describe pv pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06
...
Message:         disk.DisksClient#Delete: Failure responding to request: StatusCode=409 -- Original Error: autorest/azure: Service returned an error. Status=409 Code="OperationNotAllowed" Message="Disk kubernetes-dynamic-pvc-d8eebc1d-74d3-11e8-902b-e22b71bb1c06 is attached to VM /subscriptions/{subs-id}/resourceGroups/MC_markito-aks-pvc_markito-aks-pvc_westus/providers/Microsoft.Compute/virtualMachines/aks-agentpool-25259074-0."
```

Dans Kubernetes version 1.10 et versions ultérieures, une caractéristique de protection de PersistentVolumeClaim est activée par défaut pour éviter cette erreur. Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème, vous pouvez atténuer ce problème en supprimant le pod à l’aide du PersistentVolumeClaim avant de supprimer le PersistentVolumeClaim.


### <a name="error-cannot-find-lun-for-disk-when-attaching-a-disk-to-a-node"></a>Erreur « Impossible de trouver le numéro d’unité logique pour le disque » lors de l’attachement d’un disque à un nœud

Lorsque vous attachez un disque à un nœud, vous pouvez voir l’erreur suivante :

```console
MountVolume.WaitForAttach failed for volume "pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6" : Cannot find Lun for disk kubernetes-dynamic-pvc-12b458f4-c23f-11e8-8d27-46799c22b7c6
```

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1,10 | 1.10.10 ou version ultérieure |
| 1.11 | 1.11.5 ou version ultérieure |
| 1.12 | 1.12.3 ou version ultérieure |
| 1.13 | 1.13.0 ou version ultérieure |
| 1.14 et versions ultérieures | N/A |

Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème, vous pouvez atténuer le problème en attendant quelques minutes et en réessayant.

### <a name="azure-disk-attachdetach-failure-mount-issues-or-io-errors-during-multiple-attachdetach-operations"></a>Échec d’attachement/détachement de disque Azure, problèmes de montage ou erreurs d’E/S pendant plusieurs opérations d’attachement/détachement

À compter de Kubernetes version 1.9.2, lors de l’exécution simultanée de plusieurs opérations d’attachement/détachement, vous pouvez voir les problèmes de disque suivants en raison d’un cache de machines virtuelles erroné :

* Échecs d’attachement/détachement de disque
* Erreurs d’E/S disque
* Détachement inattendu du disque de la machine virtuelle
* La machine virtuelle s’exécute en état d’échec en raison de l’attachement d’un disque inexistant

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1,10 | 1.10.12 ou version ultérieure |
| 1.11 | 1.11.6 ou version ultérieure |
| 1.12 | 1.12.4 ou version ultérieure |
| 1.13 | 1.13.0 ou version ultérieure |
| 1.14 et versions ultérieures | N/A |

Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème, vous pouvez atténuer le problème en essayant ce qui suit :

* Si un disque attend de se détacher pendant une longue période de temps, essayez de détacher le disque manuellement

### <a name="azure-disk-waiting-to-detach-indefinitely"></a>Disque Azure en attente de détachement indéfiniment

Dans certains cas, si une opération de détachement de disque Azure échoue à la première tentative, elle ne retente pas l’opération de détachement et reste attachée à la machine virtuelle du nœud d’origine. Cette erreur peut se produire lors du déplacement d’un disque d’un nœud à un autre. Par exemple :

```console
[Warning] AttachVolume.Attach failed for volume “pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9” : Attach volume “kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9" to instance “/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-0” failed with compute.VirtualMachinesClient#CreateOrUpdate: Failure sending request: StatusCode=0 -- Original Error: autorest/azure: Service returned an error. Status= Code=“ConflictingUserInput” Message=“Disk ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/disks/kubernetes-dynamic-pvc-7b7976d7-3a46-11e9-93d5-dee1946e6ce9’ cannot be attached as the disk is already owned by VM ‘/subscriptions/XXX/resourceGroups/XXX/providers/Microsoft.Compute/virtualMachines/aks-agentpool-57634498-1’.”
```

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1.11 | 1.11.9 ou version ultérieure |
| 1.12 | 1.12.7 ou version ultérieure |
| 1.13 | 1.13.4 ou version ultérieure |
| 1.14 et versions ultérieures | N/A |

Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème, vous pouvez atténuer le problème en détachant manuellement le disque.

### <a name="azure-disk-detach-failure-leading-to-potential-race-condition-issue-and-invalid-data-disk-list"></a>Échec du détachement de disque Azure menant à un problème potentiel de condition de concurrence et à une liste de disques de données non valides

En cas d’échec de détachement d’un disque Azure, une nouvelle tentative de détachement de disque est effectuée jusqu’à six fois à l’aide de l’interruption exponentielle. Un verrou est également maintenu au niveau du nœud sur la liste des disques de données pendant environ 3 minutes. Si la liste des disques est mise à jour manuellement pendant cette période, par exemple lors d’une opération manuelle d’attachement ou de détachement, la liste des disques détenues par le verrou au niveau du nœud est obsolète et entraîne une instabilité sur la machine virtuelle du nœud.

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1.12 | 1.12.9 ou version ultérieure |
| 1.13 | 1.13.6 ou version ultérieure |
| 1.14 | 1.14.2 ou version ultérieure |
| 1.15 et versions ultérieures | N/A |

Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème et que votre machine virtuelle de nœud présente une liste de disques obsolète, vous pouvez atténuer le problème en détachant tous les disques non existants de la machine virtuelle en tant qu’opération en bloc unique. **Le détachement individuel de disques non existants peut échouer.**


### <a name="large-number-of-azure-disks-causes-slow-attachdetach"></a>Un nombre élevé de disques Azure ralentit la procédure d’attachement/détachement

Lorsque le nombre de disques Azure attachés à une machine virtuelle de nœuds est supérieur à 10, les opérations d’attachement et de détachement peuvent être lentes. Ce problème est connu et il n’existe aucune solution de contournement pour l’instant.

### <a name="azure-disk-detach-failure-leading-to-potential-node-vm-in-failed-state"></a>Échec du détachement de disque Azure menant à un état d’échec potentiel pour la machine virtuelle de nœud

Dans certains cas, le détachement d’un disque Azure peut échouer partiellement et laisser la machine virtuelle du nœud dans un état d’échec.

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1.12 | 1.12.10 ou version ultérieure |
| 1.13 | 1.13.8 ou version ultérieure |
| 1.14 | 1.14.4 ou version ultérieure |
| 1.15 et versions ultérieures | N/A |

Si vous utilisez une version de Kubernetes qui ne présente pas le correctif pour ce problème et si votre machine virtuelle de nœud est dans un état d’échec, vous pouvez atténuer le problème en mettant à jour manuellement l’état de la machine virtuelle à l’aide de l’une des opérations ci-dessous :

* Pour un cluster à base de groupes à haute disponibilité :
    ```console
    az vm update -n <VM_NAME> -g <RESOURCE_GROUP_NAME>
    ```

* Pour un cluster basé sur VMSS :
    ```console
    az vmss update-instances -g <RESOURCE_GROUP_NAME> --name <VMSS_NAME> --instance-id <ID>
    ```

## <a name="azure-files-and-aks-troubleshooting"></a>Résolution des problèmes Azure Files et AKS

### <a name="what-are-the-recommended-stable-versions-of-kubernetes-for-azure-files"></a>Quelles sont les versions stables recommandées de Kubernetes pour les fichiers Azure ?
 
| Version de Kubernetes | Version recommandée |
| -- | :--: |
| 1.12 | 1.12.6 ou version ultérieure |
| 1.13 | 1.13.4 ou version ultérieure |
| 1.14 | 1.14.0 ou version ultérieure |

### <a name="what-versions-of-kubernetes-have-azure-files-support-on-the-sovereign-cloud"></a>Quelles sont les versions de Kubernetes disposant d’une prise en charge d’Azure Files sur le cloud souverain ?

| Version de Kubernetes | Version recommandée |
| -- | :--: |
| 1.12 | 1.12.0 ou version ultérieure |
| 1.13 | 1.13.0 ou version ultérieure |
| 1.14 | 1.14.0 ou version ultérieure |

### <a name="what-are-the-default-mountoptions-when-using-azure-files"></a>Quelles sont les mountOptions par défaut lors de l’utilisation d’Azure Files ?

Paramètres recommandés :

| Version de Kubernetes | Valeur de fileMode et dirMode|
| -- | :--: |
| 1.12.0 - 1.12.1 | 0755 |
| 1.12.2 et versions ultérieures | 0777 |

Si vous utilisez un cluster avec Kuberetes 1.8.5 ou ultérieur et que vous créez le volume persistant de manière dynamique avec une classe de stockage, les options de montage peuvent être spécifiées sur l’objet de classe de stockage. L’exemple suivant définit *0777* :

```yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azurefile
provisioner: kubernetes.io/azure-file
mountOptions:
  - dir_mode=0777
  - file_mode=0777
  - uid=1000
  - gid=1000
  - mfsymlinks
  - nobrl
  - cache=none
parameters:
  skuName: Standard_LRS
```

Certains paramètres *mountOptions* supplémentaires utiles :

* *mfsymlinks* fait en sorte que le montage Azure Files (cifs) prenne en charge les liens symboliques
* *nobrl*  empêche l’envoi des demandes de verrous de plage d’octets au serveur. Ce paramètre est nécessaire pour certaines applications qui s’arrêtent avec des verrous de plage d’octets obligatoires de type cifs. La plupart des serveurs cifs ne prennent pas encore en charge la demande de verrous de plage d’octets. Si vous n’utilisez pas *nobrl*, les applications qui s’arrêtent avec des verrous de plage d’octets obligatoires de type cifs peuvent entraîner des messages d’erreur comme suit :
    ```console
    Error: SQLITE_BUSY: database is locked
    ```

### <a name="error-could-not-change-permissions-when-using-azure-files"></a>Erreur « Impossible de modifier les autorisations » lors de l’utilisation d’Azure Files

Quand vous exécutez PostgreSQL sur le plug-in Azure Files, vous pouvez obtenir une erreur semblable à la suivante :

```console
initdb: could not change permissions of directory "/var/lib/postgresql/data": Operation not permitted
fixing permissions on existing directory /var/lib/postgresql/data
```

Cette erreur est provoquée par le plug-in Azure Files utilisant le protocole cifs/SMB. Lors de l’utilisation du protocole cifs/SMB, les autorisations de fichiers et de répertoires n’ont pas pu être modifiées après le montage.

Pour résoudre ce problème, utilisez *subPath*  avec le plug-in de disque Azure. 

> [!NOTE] 
> Pour le type de disque ext3/4, il y a un répertoire des « objets trouvés » une fois le disque formaté.

### <a name="azure-files-has-high-latency-compared-to-azure-disk-when-handling-many-small-files"></a>Azure Files présente une latence élevée par rapport au disque Azure lors du traitement de nombreux petits fichiers

Dans certains cas, tels que la gestion de nombreux petits fichiers, vous pouvez rencontrer une latence élevée lors de l’utilisation d’Azure Files par rapport au disque Azure.

### <a name="error-when-enabling-allow-access-allow-access-from-selected-network-setting-on-storage-account"></a>Erreur lors de l’activation du paramètre « Autoriser l’accès à partir du réseau sélectionné » sur le compte de stockage

Si vous activez *Autoriser l’accès à partir du réseau sélectionné* sur un compte de stockage utilisé pour l’approvisionnement dynamique dans AKS, vous obtiendrez une erreur lorsqu’AKS crée un partage de fichiers :

```console
persistentvolume-controller (combined from similar events): Failed to provision volume with StorageClass "azurefile": failed to create share kubernetes-dynamic-pvc-xxx in account xxx: failed to create file share, err: storage: service returned error: StatusCode=403, ErrorCode=AuthorizationFailure, ErrorMessage=This request is not authorized to perform this operation.
```

Cette erreur est due au fait que le *persistentvolume-controller*  Kubernetes ne figure pas sur le réseau choisi lors de la configuration de l’option *Autoriser l’accès à partir du réseau sélectionné*.

Vous pouvez atténuer le problème à l’aide de [l’approvisionnement statique avec Azure Files](azure-files-volume.md).

### <a name="azure-files-fails-to-remount-in-windows-pod"></a>Le remontage d’Azure Files échoue dans le pod Windows

Si un pod Windows avec un montage Azure Files est supprimé, puis planifié pour être recréé sur le même nœud, le montage échoue. Cet échec est dû à l’échec de la commande `New-SmbGlobalMapping`, car le montage d’Azure Files est déjà effectué sur le nœud.

Par exemple, vous pouvez recevoir une erreur comme suit :

```console
E0118 08:15:52.041014    2112 nestedpendingoperations.go:267] Operation for "\"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (\"42c0ea39-1af9-11e9-8941-000d3af95268\")" failed. No retries permitted until 2019-01-18 08:15:53.0410149 +0000 GMT m=+732.446642701 (durationBeforeRetry 1s). Error: "MountVolume.SetUp failed for volume \"pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\" (UniqueName: \"kubernetes.io/azure-file/42c0ea39-1af9-11e9-8941-000d3af95268-pvc-d7e1b5f9-1af3-11e9-8941-000d3af95268\") pod \"deployment-azurefile-697f98d559-6zrlf\" (UID: \"42c0ea39-1af9-11e9-8941-000d3af95268\") : azureMount: SmbGlobalMapping failed: exit status 1, only SMB mount is supported now, output: \"New-SmbGlobalMapping : Generic failure \\r\\nAt line:1 char:190\\r\\n+ ... ser, $PWord;New-SmbGlobalMapping -RemotePath $Env:smbremotepath -Cred ...\\r\\n+                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~\\r\\n    + CategoryInfo          : NotSpecified: (MSFT_SmbGlobalMapping:ROOT/Microsoft/...mbGlobalMapping) [New-SmbGlobalMa \\r\\n   pping], CimException\\r\\n    + FullyQualifiedErrorId : HRESULT 0x80041001,New-SmbGlobalMapping\\r\\n \\r\\n\""
```

Ce problème a été résolu dans les versions suivantes de Kubernetes :

| Version de Kubernetes | Version corrigée |
| -- | :--: |
| 1.12 | 1.12.6 ou version ultérieure |
| 1.13 | 1.13.4 ou version ultérieure |
| 1.14 et versions ultérieures | N/A |

### <a name="azure-files-mount-fails-due-to-storage-account-key-changed"></a>Échec du montage d’Azure Files en raison de la modification de la clé du compte de stockage

Si votre clé de compte de stockage a changé, vous pouvez voir des échecs de montage d’Azure Files.

Vous pouvez atténuer le problème en mettant manuellement à jour le champ *azurestorageaccountkey*  dans le secret du fichier Azure avec votre clé de compte de stockage codée en base64.

Pour encoder votre clé de compte de stockage en base64, vous pouvez utiliser `base64`. Par exemple :

```console
echo X+ALAAUgMhWHL7QmQ87E1kSfIqLKfgC03Guy7/xk9MyIg2w4Jzqeu60CVw2r/dm6v6E0DWHTnJUEJGVQAoPaBc== | base64
```

Pour mettre à jour votre fichier de secret Azure, utilisez `kubectl edit secret`. Par exemple :

```console
kubectl edit secret azure-storage-account-{storage-account-name}-secret
```

Après quelques minutes, le nœud de l’agent réessaie le montage du fichier Azure avec la clé de stockage mise à jour.

### <a name="cluster-autoscaler-fails-to-scale-with-error-failed-to-fix-node-group-sizes"></a>Échec de la mise à l’échelle automatique du cluster en cas d’échec de la résolution des tailles des groupes de nœuds

Si votre dispositif de mise à l’échelle automatique ne monte pas/ne descend pas en puissance et que vous voyez une erreur comme celle ci-dessous sur les [journaux d'activité du dispositif de mise à l’échelle automatique du cluster][view-master-logs].

```console
E1114 09:58:55.367731 1 static_autoscaler.go:239] Failed to fix node group sizes: failed to decrease aks-default-35246781-vmss: attempt to delete existing nodes
```

Cette erreur est due à une condition de concurrence du dispositif de mise à l’échelle automatique du cluster en amont, où le dispositif de mise à l’échelle automatique du cluster se termine par une valeur différente de la valeur effective du cluster. Pour sortir de cet état, il vous suffit de désactiver et de réactiver le [dispositif de mise à l’échelle automatique du cluster][cluster-autoscaler].

<!-- LINKS - internal -->
[view-master-logs]: view-master-logs.md
[cluster-autoscaler]: cluster-autoscaler.md
