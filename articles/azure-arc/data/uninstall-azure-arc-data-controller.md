---
title: Supprimer le contrôleur de données Azure Arc
description: Supprimer le contrôleur de données Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 26784222d49e6f48ed324ce345dcb1f2ba7d4cf1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524836"
---
# <a name="delete-azure-arc-data-controller"></a>Supprimer le contrôleur de données Azure Arc

L’article suivant explique comment supprimer un contrôleur de données Azure Arc.

Avant de continuer, vérifiez que tous les services de données qui ont été créés sur le contrôleur de données sont supprimés comme suit :

## <a name="list--delete-existing-data-services"></a>Répertorier et supprimer des services de données existants

Exécutez la commande suivante pour vérifier si des instances gérées SQL sont créées :

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Pour chaque instance gérée SQL figurant dans la liste ci-dessus, exécutez la commande delete comme suit :

```azurecli
az sql mi-arc delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az sql mi-arc delete -n sqlinstance1 --k8s-namespace <namespace> --use-k8s
```

De même, pour vérifier les instances PostgreSQL Hyperscale, exécutez la commande suivante :

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Et, pour chaque instance PostgreSQL Hyperscale, exécutez la commande delete comme suit :

```azurecli
az postgres arc-server delete -n <name> --k8s-namespace <namespace> --use-k8s
# for example: az postgres arc-server delete -n pg1 --k8s-namespace <namespace> --use-k8s
```

## <a name="delete-controller"></a>Supprimer le contrôleur

Une fois la totalité des instances gérées SQL et instances PostgreSQL Hyperscale supprimées, vous pouvez supprimer le contrôleur de données comme suit :

```azurecli
az arcdata dc delete -n <name> -ns <namespace>
# for example: az arcdata dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Supprimer des contraintes de contexte de sécurité (Red Hat OpenShift uniquement)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="delete-cluster-level-objects"></a>Supprimer les objets de niveau cluster

Outre les objets délimités par l’espace de noms, si vous souhaitez également supprimer les objets de niveau cluster tels que les CRD `clusterroles` et `clusterrolebindings`, exécutez les commandes suivantes :

```console
# Cleanup azure arc data service artifacts
#Delete CRDs
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
#Delete clusterrole
kubectl delete clusterrole <namespace>:cr-arc-metricsdc-reader
#For example: kubectl delete clusterrole arc:cr-arc-metricsdc-reader
#Delete rolebinding
kubectl delete clusterrolebinding <namespace>:crb-arc-metricsdc-reader
#For example: kubectl delete clusterrolebinding arc:crb-arc-metricsdc-reader
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Si vous le souhaitez, supprimez l’espace de noms du contrôleur de données Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example: kubectl delete ns arc
```

## <a name="next-steps"></a>Étapes suivantes

[Présentation des services de données avec Azure Arc](overview.md)
