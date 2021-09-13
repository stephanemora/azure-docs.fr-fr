---
title: Configurer une instance managée de SQL avec Azure Arc
description: Configurez une instance managée de SQL avec Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122532423"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configurer une instance managée de SQL avec Azure Arc

Cet article explique comment configurer une instance managée de SQL avec Azure Arc.


## <a name="configure-resources"></a>Configuration des ressources

### <a name="configure-using-cli"></a>Configurer via l’interface CLI

Vous pouvez modifier la configuration d’instances managées de SQL avec Azure Arc via l’interface CLI. Exécutez la commande suivante pour afficher les options de configuration. 

```azurecli
az sql mi-arc edit --help
```

L’exemple suivant définit les demandes et les limites de mémoire et de cœur de processeur.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

Pour voir les modifications apportées à l’instance gérée SQL, vous pouvez utiliser les commandes suivantes pour afficher le fichier yaml de configuration :

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Configurer les options de serveur

Vous pouvez définir les paramètres de configuration du serveur pour une instance managée de SQL avec Azure Arc après sa création. Cet article explique comment configurer des paramètres tels que l’activation ou la désactivation de l’agent mssql, et activer des indicateurs de trace spécifiques pour les scénarios de résolution des problèmes.

Pour modifier l’un de ces paramètres, procédez comme suit :

1. Créez un fichier `mssql-custom.conf` personnalisé qui comprend les paramètres ciblés. L’exemple suivant active SQL Agent et l’indicateur de trace 1204 :

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Copiez le fichier `mssql-custom.conf` dans `/var/opt/mssql` dans le conteneur `mssql-miaa` du pod `master-0`. Remplacez `<namespaceName>` par le nom de l’espace de noms Arc.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Redémarrez l’instance SQL Server.  Remplacez `<namespaceName>` par le nom de l’espace de noms Arc.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Limitations connues**
- Les étapes ci-dessus nécessitent des autorisations d’administrateur de cluster Kubernetes
