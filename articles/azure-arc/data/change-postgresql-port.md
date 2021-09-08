---
title: Changer le port PostgreSQL
description: Changez le port sur lequel le groupe de serveurs PostgreSQL Hyperscaleavec Azure Arc est en écoute.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 47a64b9e1207536e951f61059d472a88e9f9d8c5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532900"
---
# <a name="change-the-port-on-which-the-server-group-is-listening"></a>Changer le port sur lequel le groupe de serveurs écoute 

Le changement de port est une opération de modification standard du groupe de serveurs. Pour changer de port, exécutez la commande suivante :
```azurecli
 az postgres arc-server edit -n <server group name> --port <desired port number> --k8s-namespace <namespace> --use-k8s
```

Par exemple, supposons que le nom de votre groupe de serveurs est _postgres01_ et que vous souhaitez qu’il écoute sur le port _866_. Exécutez ensuite la commande suivante :
```azurecli
 az postgres arc-server edit -n postgres01 --port 866 --k8s-namespace <namespace> --use-k8s
```

## <a name="verify-that-the-port-was-changed"></a>Vérifiez que le port a changé

Pour vérifier que le port a changé, exécutez la commande suivante pour afficher la configuration de votre groupe de serveurs :
```azurecli
az postgres arc-server show -n <server group name> --k8s-namespace <namespace> --use-k8s
```

Dans la sortie de cette commande, examinez le numéro de port affiché pour l’élément « port » dans la section « service » des spécifications de votre groupe de serveurs.
Vous pouvez également vérifier dans l’externalEndpoint de l’élément de la section État des spécifications de votre groupe de serveurs que l’adresse IP est suivie par le numéro de port que vous avez configuré.

À titre d’illustration, si nous continuons l’exemple ci-dessus, vous devez exécuter la commande :
```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace <namespace> --use-k8s
```

et vous verrez le port 866 désigné ici :

```console
"service": {
      "port": 866,
      "type": "LoadBalancer"
    },
```
et ici

```console
"status": {
    "externalEndpoint": "12.678.345.09:866",
    "logSearchDashboard": "https://12.345.678.90:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))",
    "metricsDashboard": "https://12.345.678.90:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01",
    "readyPods": "3/3",
    "state": "Ready"
  }
```
## <a name="next-steps"></a>Étapes suivantes
- En savoir plus sur la [procédure de connexion à votre groupe de serveurs](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md).
- En savoir plus sur la procédure de configuration d’autres aspects de votre groupe de serveurs dans la section Procédures\Gérer\Configurer et mettre à l'échelle de la documentation.
