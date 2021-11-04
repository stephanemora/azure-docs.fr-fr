---
title: Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans un contrôleur de données Azure Arc
description: Listez les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans un contrôleur de données Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 95bda32a3ffc305a4523e952f0cc975917996292
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131555302"
---
# <a name="list-the-azure-arc-enabled-postgresql-hyperscale-server-groups-created-in-an-azure-arc-data-controller"></a>Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans un contrôleur de données Azure Arc

Cet article explique comment récupérer la liste des groupes de serveurs créés dans votre contrôleur de données Arc.

Pour récupérer cette liste, utilisez l’une des méthodes suivantes une fois que vous êtes connecté au contrôleur de données Arc :

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-cli-with-azure-cli-extension-az"></a>À partir de l’interface CLI avec l’extension Azure CLI (az)

Le format général de la commande est le suivant :
```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```

Elle renverra une sortie comme suit :
```console
[
  {
    "name": "postgres01",
    "replicas": 1,
    "state": "Ready",
    "workers": 4
  }
]
```
Pour plus d’informations sur les paramètres disponibles pour cette commande, exécutez :
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>À partir de l’interface CLI avec kubectl
Exécutez l’une des commandes suivantes.

**Pour répertorier les groupes de serveurs, quelle que soit la version de Postgres, exécutez :**
```console
kubectl get postgresqls -n <namespace>
```
Elle renverra une sortie comme suit :
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          12.345.67.890:5432   12d
```

## <a name="next-steps"></a>Étapes suivantes :

* [Lisez l’article sur la façon d’obtenir des points de terminaison de connexion et formez les chaînes de connexion pour vous connecter à votre groupe de serveurs](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Lisez l’article expliquant comment montrer la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](show-configuration-postgresql-hyperscale-server-group.md)
