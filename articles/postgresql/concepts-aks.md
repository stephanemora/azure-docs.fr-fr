---
title: Connecter Azure Kubernetes Service (AKS) à Azure Database pour PostgreSQL
description: Découvrez comment connecter Azure Kubernetes Service à Azure Database pour PostgreSQL
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 86474fe612fb93f3a5853f9fea98eb9ab2dd00e5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336507"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Connexion entre Azure Kubernetes Service et Azure Database pour PostgreSQL

Azure Kubernetes Service (AKS) fournit un cluster Kubernetes managé que vous pouvez utiliser dans Azure. Voici quelques options à prendre en compte lors de l’utilisation d’AKS et d’Azure Database pour PostgreSQL pour créer une application.


## <a name="accelerated-networking"></a>Mise en réseau accélérée
Utilisez des machines virtuelles sous-jacentes avec mise en réseau accélérée dans votre cluster AKS. Lorsque la mise en réseau accélérée est activée sur une machine virtuelle, il y a une latence plus faible, une instabilité moindre et une utilisation du processeur limitée sur la machine virtuelle. En savoir plus sur le fonctionnement de la mise en réseau accélérée, les versions de système d’exploitation prises en charge et les instances de machine virtuelle prises en charge pour [Linux](../virtual-network/create-vm-accelerated-networking-cli.md).

À partir de novembre 2018, AKS prend en charge la mise en réseau accélérée sur ces instances de machine virtuelle prises en charge. La mise en réseau accélérée est activée par défaut sur les nouveaux clusters AKS qui utilisent ces machines virtuelles.

Vous pouvez vérifier si votre cluster AKS a la mise en réseau accélérée :
1. Accédez au portail Azure et sélectionnez votre cluster AKS.
2. Sélectionnez l’onglet Propriétés.
3. Copiez le nom du **Groupe de ressources d’infrastructure**.
4. Utilisez la barre de recherche du portail pour localiser et ouvrir le groupe de ressources d’infrastructure.
5. Sélectionnez une machine virtuelle dans ce groupe de ressources.
6. Accédez à l’onglet **Mise en réseau** de la machine virtuelle.
7. Confirmez si la **Mise en réseau accélérée** est « Activée ».


## <a name="open-service-broker-for-azure"></a>Open Service Broker pour Azure 
[Open Service Broker pour Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) vous permet de provisionner des services Azure directement à partir de Kubernetes ou de Cloud Foundry. C’est une implémentation de [l’API Open Service Broker](https://www.openservicebrokerapi.org/) pour Azure.

Avec OSBA, vous pouvez créer un serveur Azure Database pour PostgreSQL et liez-le à votre cluster AKS à l’aide du langage natif Kubernetes. En savoir plus sur l’utilisation d’OSBA et d’Azure Database pour PostgreSQL sur la page [Github OSBA](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Regroupement de connexions
Un dispositif de regroupement de connexions réduit le coût et le temps associés à la création et la fermeture de nouvelles connexions à la base de données. Le pool est une collection de connexions qui peuvent être réutilisées. 

Il existe plusieurs dispositifs de regroupement de connexions que vous pouvez utiliser avec PostgreSQL. L’un d’eux est [PgBouncer](https://pgbouncer.github.io/). Dans le Registre de conteneurs Microsoft, nous fournissons un PgBouncer en conteneur léger qui peut être utilisé dans un side-car pour regrouper les connexions à partir d’AKS vers Azure Database pour PostgreSQL. Visitez la [page du hub docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/) pour savoir comment accéder à cette image et l’utiliser. 


## <a name="next-steps"></a>Étapes suivantes
-  [Créer un cluster Azure Kubernetes Service](../aks/kubernetes-walkthrough.md)
