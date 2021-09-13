---
title: Lister les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans un contrôleur de données Azure Arc
description: Listez les groupes de serveurs PostgreSQL Hyperscale avec Azure Arc créés dans un contrôleur de données Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 7254ed303e45c69f291aa5c7a06f63390aaed162
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122525256"
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
Name        State    Workers
----------  -------  ---------
postgres01  Ready    2
postgres02  Ready    2
```
Pour plus d’informations sur les paramètres disponibles pour cette commande, exécutez :
```azurecli
az postgres arc-server list --help
```

## <a name="from-cli-with-kubectl"></a>À partir de l’interface CLI avec kubectl
Exécutez l’une des commandes suivantes.

**Pour répertorier les groupes de serveurs, quelle que soit la version de Postgres, exécutez :**
```console
kubectl get postgresqls
```
Elle renverra une sortie comme suit :
```console
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      51s
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d
```

**Pour répertorier les groupes de serveurs d’une version spécifique de Postgres, exécutez :**
```console
kubectl get postgresql-12
```

Pour répertorier les groupes de serveurs qui exécutent la version 11 de postgres, remplacez _postgresql-12_ par _postgresql-11_.

## <a name="next-steps"></a>Étapes suivantes :

* [Lisez l’article sur la façon d’obtenir des points de terminaison de connexion et formez les chaînes de connexion pour vous connecter à votre groupe de serveurs](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
* [Lisez l’article expliquant comment montrer la configuration d’un groupe de serveurs PostgreSQL Hyperscale avec Azure Arc](show-configuration-postgresql-hyperscale-server-group.md)
