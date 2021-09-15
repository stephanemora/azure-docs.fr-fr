---
title: Créer une instance gérée Azure SQL sur Azure Arc
description: Créer une instance gérée Azure SQL sur Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: a5aac5704463b28ea9bb124f9fb3120d7a1da52b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123102073"
---
# <a name="create-an-azure-sql-managed-instance-on-azure-arc"></a>Créer une instance gérée Azure SQL sur Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]


## <a name="create-an-azure-sql-managed-instance"></a>Créer une instance gérée SQL Azure

Pour afficher les options disponibles de la commande create pour SQL Managed Instance, utilisez la commande suivante :
```azurecli
az sql mi-arc create --help
```

Pour créer une instance SQL Managed Instance, utilisez la commande suivante :

```azurecli
az sql mi-arc create -n <instanceName> --k8s-namespace <namespace> --use-k8s
```

Exemple :

```azurecli
az sql mi-arc create -n sqldemo --k8s-namespace my-namespace --use-k8s
```
> [!NOTE]
>  La longueur des noms doit être inférieure à 13 caractères et conforme aux [conventions d’affectation des noms DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-label-names)
>
>  Lorsque vous spécifiez l’allocation de mémoire et l’allocation vCore, utilisez cette formule pour garantir des performances acceptables : pour chaque vCore, vous devez disposer d’au moins 4 Go de RAM de capacité disponibles sur le nœud Kubernetes où le pod d’instance gérée SQL s’exécutera.
>
>  Si vous souhaitez automatiser la création d’instances SQL et éviter l’invite interactive pour le mot de passe d’administrateur, vous pouvez définir les variables d’environnement `AZDATA_USERNAME` et `AZDATA_PASSWORD` sur le nom d’utilisateur et le mot de passe souhaités avant d’exécuter la commande `az sql mi-arc create`.
> 
>  Si vous avez créé le contrôleur de données à l’aide d’AZDATA_USERNAME et d’AZDATA_PASSWORD dans la même session de terminal, les valeurs d’AZDATA_USERNAME et d’AZDATA_PASSWORD seront également utilisées pour créer l’instance gérée SQL.

> [!NOTE]
> Si vous utilisez le mode de connectivité indirect, la création d’une instance Azure SQL Managed Instance dans Kubernetes n’inscrit pas automatiquement les ressources dans Azure. Les étapes d’inscription de la ressource sont décrites dans les articles suivants : 
> - [Charger des données de facturation dans Azure et les afficher dans le portail Azure](view-billing-data-in-azure.md) 


## <a name="view-instance-on-azure-arc"></a>Afficher l’instance sur Azure Arc

Pour afficher l’instance, utilisez la commande suivante :

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

Vous pouvez copier l’adresse IP externe et le numéro de port à partir d’ici et vous y connecter à l’aide de votre outil favori de connexion à une instance SQL Server/Azure SQL, tel qu’Azure Data Studio ou SQL Server Management Studio.

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="next-steps"></a>Étapes suivantes
- [Se connecter à SQL Managed Instance avec Azure Arc](connect-managed-instance.md)
- [Inscrire votre instance auprès d’Azure et charger les métriques et les journaux relatifs à votre instance](upload-metrics-and-logs-to-azure-monitor.md)
- [Déployer Azure SQL Managed Instance en utilisant Azure Data Studio](create-sql-managed-instance-azure-data-studio.md)
