---
title: Configurer des ressources DNS personnalisées dans un cluster Azure Red Hat OpenShift (ARO)
description: Découvrez comment ajouter un serveur DNS personnalisé sur tous vos nœuds dans Azure Red Hat OpenShift (ARO).
author: bryanro92
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/02/2021
ms.openlocfilehash: 842e0a4d57254c4ec27bdadf2ff168fe2f4c0424
ms.sourcegitcommit: 070122ad3aba7c602bf004fbcf1c70419b48f29e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111442077"
---
# <a name="configure-custom-dns-for-your-azure-red-hat-openshift-aro-cluster"></a>Configurer un DNS personnalisé pour votre cluster Azure Red Hat OpenShift (ARO)

Cet article fournit les informations nécessaires pour vous permettre de configurer votre cluster Azure Red Hat OpenShift (ARO) afin d’utiliser un serveur DNS personnalisé. Il décrit la configuration requise du cluster pour un déploiement ARO de base.

## <a name="before-you-begin"></a>Avant de commencer

Cet article part de l’hypothèse que vous créez un cluster ou que vous disposez d’un cluster auquel les dernières mises à jour ont été appliquées. Si vous avez besoin d’un cluster ARO, consultez le [Démarrage rapide ARO](./tutorial-create-cluster.md) pour un cluster public, ou le [tutoriel sur les clusters privés](./howto-create-private-cluster-4x.md) pour un cluster privé. La procédure de configuration de votre cluster pour utiliser un serveur DNS personnalisé est la même pour les clusters publics et privés.

### <a name="confirm-cluster-compatibility-with-custom-dns"></a>Vérifier la compatibilité du cluster avec DNS personnalisé

Vérifiez que votre cluster est éligible pour la prise en charge de cette fonctionnalité en confirmant l’existence du `99-master-aro-dns` et du `99-worker-aro-dns` `machineconfigs`.

```
oc get machineconfig
```

Si les résultats de la commande ci-dessus incluent les machineconfigs suivants, votre cluster est éligible pour la prise en charge d’un DNS personnalisé.

```
NAME                 GENERATEDBYCONTROLLER                      IGNITIONVERSION   AGE
...
99-master-aro-dns                                               2.2.0             54d
99-worker-aro-dns                                               2.2.0             54d
...
```

## <a name="dns-architecture-overview"></a>Vue d’ensemble de l’architecture DNS

À mesure que chaque nœud du cluster Azure Red Hat OpenShift s’allume et rejoint le réseau, le protocole DHCP configure la machine virtuelle avec des informations telles que l’adresse IP et le serveur DNS à utiliser.

Vous trouverez ci-dessous une vue d’ensemble du flux de processus montrant comment obtenir la configuration :

![DNS](media/concepts-networking/custom-dns-pfd.jpg)

Un inconvénient important de l’utilisation de votre propre serveur DNS au lieu du serveur DNS par défaut dans le réseau virtuel est que vous perdez la configuration fournie par le serveur DNS. Les noms de machines virtuelles ne seront plus résolus via le DNS sur le réseau.

### <a name="update-process-overview"></a>Vue d’ensemble du processus de mise à jour

La configuration d’un serveur DNS personnalisé pour le cluster comprend deux étapes.

1. Modification du paramètre de configuration des serveurs DNS du réseau virtuel.
2. Redémarrage des nœuds dans le cluster pour répercuter les modifications.


## <a name="configure-a-custom-dns-server"></a>Configurer un serveur DNS personnalisé

Vous pouvez accomplir les étapes suivantes à l’aide de la ligne de commande, mais cette documentation va vous guider dans l’utilisation de l’interface web du portail.

### <a name="update-dns-configuration-in-virtual-network"></a>Mettre à jour une configuration DNS dans un réseau virtuel

Connectez-vous au portail Azure et accédez au réseau virtuel que vous souhaitez mettre à jour. Sélectionnez **Serveurs DNS** dans la liste des paramètres de réseaux virtuels.

![Sélectionner un DNS](media/concepts-networking/vnet-dns-config.png)

Une fois dans l’écran de configuration DNS, dans la configuration du bouton radial, sélectionnez **Personnalisé** . Entrez les adresses IP de vos serveurs DNS.

>[!IMPORTANT]
> Si vous choisissez de spécifier un serveur DNS personnalisé, vous ne pourrez plus résoudre les noms de nœuds dans le réseau virtuel via DNS. Les nœuds ne seront accessibles que via une adresse IP.

![Spécifier des serveurs DNS personnalisés](media/concepts-networking/vnet-custom-dns.png)

Sélectionnez **Enregistrer**.

>[!NOTE]
> Comme montré dans l’interface du portail, vous devez redémarrer toutes les machines virtuelles pour que les modifications prennent effet.

Vous devriez recevoir une notification indiquant que votre mise à jour a réussi.

![Confirmer les modifications de DNS](media/concepts-networking/vnet-dns-confirm-saved.png)

### <a name="gracefully-reboot-your-cluster"></a>Redémarrer votre cluster proprement

Ces étapes nécessitant de disposer d’un fichier kubeconfig valide pour votre cluster, consultez [ce tutoriel](./tutorial-connect-cluster.md) pour plus d’informations sur la façon d’obtenir un fichier kubeconfig.

Les extraits de code suivants créent des éléments `machineconfig` noop pour le nœud principal et le nœud Worker. Cela vous permet de lancer des redémarrages propagés pour le nœud principal ou le nœud Worker. Pour plus d’informations sur l’opérateur de configuration d’ordinateur (MCO), consultez [le code source](https://github.com/openshift/machine-config-operator) ou la [documentation OpenShift sur le MCO](https://docs.openshift.com/container-platform/4.6/architecture/control-plane.html).
#### <a name="machineconfig-definitions"></a>Définitions MachineConfig

Le nœud Worker redémarre :

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: worker
  name: 25-machineconfig-worker-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-noop-worker-restart.txt
```

Le nœud principal redémarre :

```
apiVersion: machineconfiguration.openshift.io/v1
kind: MachineConfig
metadata:
  labels:
    machineconfiguration.openshift.io/role: master
  name: 25-machineconfig-master-reboot
spec:
  config:
    ignition:
      version: 2.2.0
    storage:
      files:
      - contents:
          source: data:text/plain;charset=utf-8;base64,cmVzdGFydAo=
        filesystem: root
        mode: 0644
        path: /etc/mco-master-noop-restart.txt
```

#### <a name="reboot-worker-nodes"></a>Redémarrer des nœuds Worker

Créez le fichier de redémarrage de nœud Worker. Cet exemple appelle le fichier `worker-restarts.yml` et l’applique.

```
[user@bastion ~]$ vim worker-restarts.yml
[user@bastion ~]$ oc apply -f worker-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-worker-reboot created
```

Le MCO déplace les charges de travail, puis redémarre chaque nœud successivement. Une fois les nœuds Worker de nouveau en ligne, nous suivons la même procédure pour redémarrer les nœuds principaux. Vous pouvez vérifier l’état des nœuds Workers en les interrogeant pour vérifier qu’ils sont tous dans l’état `Ready`.

>[!NOTE]
> Selon la taille de la charge de travail du cluster, le redémarrage de chaque nœud peut prendre plusieurs minutes.


Exemples de nœuds Worker non complètement prêts :

```
NAME                                  STATUS                     ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready                      master   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready                      worker   5h35m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready,SchedulingDisabled   worker   5h34m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready                      worker   5h35m   v1.19.0+a5a0987
```

Au redémarrage du nœud, vous verrez celui-ci passer à l’état NotReady :

```
dns-docs-tm45t-worker-eastus2-ln2kq   NotReady,SchedulingDisabled   worker   5h38m   v1.19.0+a5a0987
```

Complètement prêt :

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE     VERSION
dns-docs-tm45t-master-0               Ready    master   5h45m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   5h46m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   5h41m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   5h40m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   5h41m   v1.19.0+a5a0987
```

#### <a name="reboot-master-nodes"></a>Redémarrer des nœuds principaux

À présent, répétez le même processus pour les nœuds principaux :

```
[user@bastion ~]$ vim master-restarts.yml
[user@bastion ~]$ oc apply -f master-restarts.yml
machineconfig.machineconfiguration.openshift.io/25-machineconfig-master-reboot created
```

Vérifiez que tous les nœuds sont revenus à l’état `Ready` :

```
[user@bastion ~]$ oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
dns-docs-tm45t-master-0               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-1               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-master-2               Ready    master   6h8m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus1-8t6q8   Ready    worker   6h3m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus2-ln2kq   Ready    worker   6h2m   v1.19.0+a5a0987
dns-docs-tm45t-worker-eastus3-gg75h   Ready    worker   6h3m   v1.19.0+a5a0987
```

#### <a name="confirm-changes-on-a-node-optional"></a>Vérifier les modifications sur un nœud (facultatif)

Pour vérifier le nouveau serveur DNS sur un nœud, nous allons utiliser le pod `oc debug`.

```
[user@bastion ~]$ oc debug node/dns-docs-tm45t-worker-eastus2-ln2kq
Starting pod/dns-docs-tm45t-worker-eastus2-ln2kq-debug ...
To use host binaries, run `chroot /host`
chroot Pod IP: 10.0.2.6
If you don't see a command prompt, try pressing enter.
sh-4.4# chroot /host
sh-4.4# uptime
 18:40:16 up 1 min,  0 users,  load average: 0.82, 0.32, 0.12
sh-4.4# cat /etc/resolv.conf.dnsmasq
# Generated by NetworkManager
search reddog.microsoft.com
nameserver 192.168.0.1
```
## <a name="modifying-the-custom-dns-server"></a>Modification du serveur DNS personnalisé

La modification du DNS personnalisé sur un cluster qui dispose déjà d’un DNS personnalisé suit le même [processus](#update-process-overview).

### <a name="modify-dns"></a>Modifier un DNS

Suivez la procédure décrite [ici](#update-dns-configuration-in-virtual-network) pour mettre à jour la configuration DNS sur le réseau virtuel.

### <a name="reboot-nodes"></a>Redémarrer des nœuds

Au lieu de créer le fichier `machineconfig`, nous allons supprimer les fichiers `machineconfig` créés la première fois. Nous allons commencer par les nœuds Worker.

```
oc delete machineconfig 25-machineconfig-worker-reboot
```

La sortie est la suivante :
```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-worker-reboot" deleted
```

Attendez que tous les nœuds Worker redémarrent. Le processus est similaire à celui décrit pour le [redémarrage de nœuds Worker](#reboot-worker-nodes) ci-dessus.

À présent, nous allons redémarrer les nœuds principaux.

```
oc delete machineconfig 25-machineconfig-master-reboot
```

La sortie est la suivante :

```
machineconfig.machineconfiguration.openshift.io "25-machineconfig-master-reboot" deleted
```

Attendez que tous les nœuds principaux aient redémarré et soient revenus à l’état Ready.