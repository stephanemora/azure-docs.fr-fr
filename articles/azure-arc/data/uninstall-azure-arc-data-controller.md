---
title: Supprimer le contrôleur de données Azure Arc
description: Supprimer le contrôleur de données Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 2bae661218989d49b74ed8ca3f694ccb912ef912
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90930181"
---
# <a name="delete-azure-arc-data-controller"></a>Supprimer le contrôleur de données Azure Arc

L’article suivant explique comment supprimer un contrôleur de données Azure Arc.

Avant de continuer, vérifiez que tous les services de données qui ont été créés sur le contrôleur de données sont supprimés comme suit :

## <a name="log-in-to-the-data-controller"></a>Se connecter au contrôleur de données

Connectez-vous au contrôleur de données que vous souhaitez supprimer :

```
azdata login
```

## <a name="list--delete-existing-data-services"></a>Répertorier et supprimer des services de données existants

Exécutez la commande suivante pour vérifier si des instances gérées SQL sont créées :

```
azdata arc sql mi list
```

Pour chaque instance gérée SQL figurant dans la liste ci-dessus, exécutez la commande delete comme suit :

```
azdata arc sql mi delete -n <name>
# for example: azdata arc sql mi delete -n sqlinstance1
```

De même, pour vérifier les instances PostgreSQL Hyperscale, exécutez la commande suivante :

```
azdata arc postgres server list
```

Et, pour chaque instance PostgreSQL Hyperscale, exécutez la commande delete comme suit :
```
azdata arc postgres server delete -n <name>
# for example: azdata arc postgres server delete -n pg1
```

## <a name="delete-controller"></a>Supprimer le contrôleur

Une fois la totalité des instances gérées SQL et instances PostgreSQL Hyperscale supprimées, vous pouvez supprimer le contrôleur de données comme suit :

```
azdata arc dc delete -n <name> -ns <namespace>
# for example: azdata arc dc delete -ns arc -n arcdc
```

### <a name="remove-sccs-red-hat-openshift-only"></a>Supprimer des contraintes de contexte de sécurité (Red Hat OpenShift uniquement)

```console
oc adm policy remove-scc-from-user privileged -z default -n arc
oc adm policy remove-scc-from-user anyuid     -z default -n arc
```

### <a name="optionally-delete-the-azure-arc-data-controller-namespace"></a>Si vous le souhaitez, supprimez l’espace de noms du contrôleur de données Azure Arc


```console
kubectl delete ns <nameSpecifiedDuringCreation>
# for example kubectl delete ns arc
```

## <a name="next-steps"></a>Étapes suivantes

[Que sont les services de données activés pour Azure Arc ?](overview.md)