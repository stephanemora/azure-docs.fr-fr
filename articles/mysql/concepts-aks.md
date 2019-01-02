---
title: Connecter Azure Kubernetes Service (AKS) à Azure Database pour MySQL
description: Découvrez comment connecter Azure Kubernetes Service à Azure Database pour MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/28/2018
ms.openlocfilehash: baba85aeb800dd8effe3be295b2149179604b41d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164838"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Connexion entre Azure Kubernetes Service et Azure Database pour MySQL

Azure Kubernetes Service (AKS) fournit un cluster Kubernetes managé que vous pouvez utiliser dans Azure. Voici quelques options à prendre en compte lors de l’utilisation conjointe d’AKS et d’Azure Database pour MySQL dans la création d’une application.


## <a name="accelerated-networking"></a>Mise en réseau accélérée
Utilisez des machines virtuelles sous-jacentes dotées de la mise en réseau accélérée dans votre cluster AKS. Lorsque la mise en réseau accélérée est activée sur une machine virtuelle, celle-ci bénéficie d’une latence plus faible, d’une diminution de l’instabilité et d’une utilisation moins importante du processeur. Apprenez-en davantage sur le fonctionnement de la mise en réseau accélérée, les versions de système d’exploitation prises en charge et les instances de machine virtuelle compatibles pour [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

À partir de novembre 2018, AKS prend en charge la mise en réseau accélérée sur ces instances de machine virtuelle prises en charge. La mise en réseau accélérée est activée par défaut sur les nouveaux clusters AKS qui utilisent ces machines virtuelles.

Vous pouvez vérifier si votre cluster AKS dispose de la mise en réseau accélérée de la façon suivante :
1. Accédez au portail Azure et sélectionnez votre cluster AKS.
2. Sélectionnez l’onglet Propriétés.
3. Copiez le nom du **Groupe de ressources d’infrastructure**.
4. Utilisez la barre de recherche du portail pour localiser et ouvrir le groupe de ressources d’infrastructure.
5. Sélectionnez une machine virtuelle dans ce groupe de ressources.
6. Accédez à l’onglet **Mise en réseau** de la machine virtuelle.
7. Vérifiez que la **Mise en réseau accélérée** est activée.


## <a name="open-service-broker-for-azure"></a>Open Service Broker pour Azure 
[Open Service Broker pour Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) vous permet de provisionner des services Azure directement à partir de Kubernetes ou de Cloud Foundry. Il s’agit d’une implémentation de l’[API Open Service Broker](https://www.openservicebrokerapi.org/) pour Azure.

Avec OSBA, vous pouvez créer un serveur Azure Database pour MySQL et le lier à votre cluster AKS au moyen du langage natif de Kubernetes. Apprenez-en davantage sur l’utilisation conjointe d’OSBA et d’Azure Database pour MySQL dans la [page OSBA de GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Étapes suivantes
- [Créer un cluster Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
- Découvrir comment [Installer WordPress à partir du graphique Helm à l’aide de OSBA et d’Azure Database pour MySQL](../aks/integrate-azure.md)
