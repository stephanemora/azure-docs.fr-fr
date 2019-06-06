---
title: Personnaliser CoreDNS pour Azure Kubernetes Service (AKS)
description: Découvrez comment personnaliser CoreDNS pour ajouter des sous-domaines ou étendre des points de terminaison DNS personnalisés à l’aide d’Azure Kubernetes Service (AKS)
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 03/15/2019
ms.author: jnoller
ms.openlocfilehash: 9f3a62c5782724f14f10b5875fc8db31dbffe67c
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693390"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Personnaliser CoreDNS avec Azure Kubernetes Service

Azure Kubernetes Service (AKS) utilise le [CoreDNS] [ coredns] projet pour la gestion du cluster de DNS et de résolution avec tous les *1.12.x* et clusters plus élevées. Auparavant, le projet kube-dns a été utilisé. Ce projet kube-dns est désormais déconseillé. Pour plus d’informations sur la personnalisation de CoreDNS et Kubernetes, consultez le [documentation officielle d’en amont][corednsk8s].

Comme ACS est un service géré, vous ne pouvez pas modifier la configuration principale pour CoreDNS (un *CoreFile*). Au lieu de cela, vous utilisez un Kubernetes *élément ConfigMap* pour remplacer les paramètres par défaut. Pour afficher la valeur par défaut AKS CoreDNS ConfigMaps, utilisez le `kubectl get configmaps coredns -o yaml` commande.

Cet article vous montre comment utiliser ConfigMaps pour les options de personnalisation de base de CoreDNS dans ACS.

> [!NOTE]
> `kube-dns` proposé différentes [options de personnalisation] [ kubednsblog] via une carte de configuration Kubernetes. CoreDNS est **pas** une compatibilité descendante avec kube-dns. Les personnalisations que vous avez utilisé précédemment doivent être mis à jour pour une utilisation avec CoreDNS.

## <a name="before-you-begin"></a>Avant de commencer

Cet article suppose que vous avez un cluster AKS existant. Si vous avez besoin d’un cluster AKS, consultez le guide de démarrage rapide d’AKS [avec Azure CLI][aks-quickstart-cli] ou [avec le portail Azure][aks-quickstart-portal].

## <a name="what-is-supportedunsupported"></a>Ce qui est pris en charge/non pris en charge

Tous les plug-ins CoreDNS intégrés sont pris en charge. Aucun plug-ins complémentaires/la tierce partie ne sont pris en charge.

## <a name="rewrite-dns"></a>Réécriture de DNS

Un scénario que vous avez consiste à effectuer les réécritures de nom DNS à la volée. Dans l’exemple suivant, remplacez `<domain to be written>` avec votre propre nom de domaine complet. Créez un fichier nommé `corednsms.json` et collez l’exemple de configuration suivantes :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        proxy .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10
    }
```

Créer l’élément ConfigMap en utilisant le [kubectl appliquer l’élément configmap] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML :

```console
kubectl apply -f corednsms.json
```

Pour vérifier les personnalisations ont été appliquées, utilisez le [kubectl get configmaps] [ kubectl-get] et spécifiez votre *coredns-custom* élément ConfigMap :

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Maintenant forcer CoreDNS pour recharger l’élément ConfigMap. Le [kubectl supprimer pod] [ kubectl delete] commande n’est pas de destructeur et n’entraîne aucun temps d’arrêt. Le `kube-dns` pods sont supprimés, et le planificateur Kubernetes recrée les. Ces nouveaux pods contiennent le changement de valeur de durée de vie.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> La commande ci-dessus est correcte. Bien que nous modifions `coredns`, le déploiement est sous le **kube-dns** nom.

## <a name="custom-proxy-server"></a>Serveur proxy personnalisé

Si vous devez spécifier un serveur proxy pour votre trafic réseau, vous pouvez créer un élément ConfigMap pour personnaliser le DNS. Dans l’exemple suivant, mettez à jour le `proxy` nom et l’adresse avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.json` et collez l’exemple de configuration suivantes :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        proxy foo.com 1.1.1.1
    }
```

Comme dans les exemples précédents, créer l’élément ConfigMap à l’aide de la [kubectl appliquer l’élément configmap] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML. Ensuite, forcer CoreDNS pour recharger l’élément ConfigMap à l’aide de la [kubectl supprimer pod] [ kubectl delete] pour le Planificateur de Kubernetes à le recréer :

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Utiliser des domaines personnalisés

Vous pouvez choisir de configurer des domaines personnalisés peuvent uniquement être résolus en interne. Par exemple, vous souhaitez résoudre le domaine personnalisé *puglife.local*, qui n’est pas un domaine de niveau supérieur valide. Sans un élément ConfigMap de domaine personnalisé, le cluster AKS ne peut pas résoudre l’adresse.

Dans l’exemple suivant, mettez à jour l’adresse IP et un domaine personnalisé pour diriger le trafic vers avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.json` et collez l’exemple de configuration suivantes :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: |
    puglife.local:53 {
        errors
        cache 30
        proxy . 192.11.0.1  # this is my test/dev DNS server
    }
```

Comme dans les exemples précédents, créer l’élément ConfigMap à l’aide de la [kubectl appliquer l’élément configmap] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML. Ensuite, forcer CoreDNS pour recharger l’élément ConfigMap à l’aide de la [kubectl supprimer pod] [ kubectl delete] pour le Planificateur de Kubernetes à le recréer :

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="stub-domains"></a>Domaines de stub

CoreDNS peut également être utilisé pour configurer les domaines de stub. Dans l’exemple suivant, mettre à jour les adresses IP et les domaines personnalisés avec les valeurs de votre propre environnement. Créez un fichier nommé `corednsms.json` et collez l’exemple de configuration suivantes :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: |
    abc.com:53 {
        errors
        cache 30
        proxy . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        proxy . 2.3.4.5
    }

```

Comme dans les exemples précédents, créer l’élément ConfigMap à l’aide de la [kubectl appliquer l’élément configmap] [ kubectl-apply] commande et spécifiez le nom de votre manifeste YAML. Ensuite, forcer CoreDNS pour recharger l’élément ConfigMap à l’aide de la [kubectl supprimer pod] [ kubectl delete] pour le Planificateur de Kubernetes à le recréer :

```console
kubectl apply -f corednsms.json
kubectl delete pod --namespace kube-system --label k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Plug-in d’hôtes

Comme tous les plug-ins intégrés sont pris en charge. cela signifie que le CoreDNS [hôtes] [ coredns hosts] plug-in est disponible pour personnaliser également :

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: |
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="next-steps"></a>Étapes suivantes

Cet article vous a montré des exemples de scénarios pour la personnalisation de CoreDNS. Pour plus d’informations sur le projet CoreDNS, consultez [la page de projet en amont CoreDNS][coredns].

Pour en savoir plus sur les concepts de réseau, consultez [réseau concepts pour les applications dans ACS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
