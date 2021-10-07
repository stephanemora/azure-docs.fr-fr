---
title: Créer et gérer Azure Cosmos DB avec Bicep
description: Utilisez Bicep pour créer et configurer l’API Azure Cosmos DB pour Core (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 09/13/2021
ms.author: mjbrown
ms.openlocfilehash: b5a3fcdffe3c93a396bb607595b1ee02775a26f2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602673"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-bicep"></a>Gérez les ressources de l’API Azure Cosmos DB Core (SQL) avec Bicep

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Dans cet article, vous apprenez à utiliser Bicep pour déployer et gérer vos comptes, bases de données et conteneurs Azure Cosmos DB.

Cet article présente des exemples Bicep pour des comptes d’API Core (SQL). Vous pouvez également trouver des exemples Bicep pour les API [Cassandra](../cassandra/manage-with-bicep.md), [Gremlin](../graph/manage-with-bicep.md), [MongoDB](../mongodb/manage-with-bicep.md)et [Table](../table/manage-with-bicep.md).

> [!IMPORTANT]
>
> * Les noms de compte sont limités à 44 caractères, tout en minuscules.
> * Pour changer les valeurs de débit, redéployez le fichier Bicep avec les valeurs de RU/s mises à jour.
> * Quand vous ajoutez ou supprimez des emplacements dans un compte Azure Cosmos, vous ne pouvez pas simultanément modifier d’autres propriétés. Ces opérations doivent être effectuées séparément.
> * Les ressources Azure Cosmos DB ne peuvent pas être renommées, car cela enfreint les règles de traitement des URI de ressources par Azure Resource Manager.
> * Pour approvisionner le débit au niveau de la base de données et le partager sur tous les conteneurs, appliquez les valeurs de débit à la propriété des options de base de données.

Pour créer l’une des ressources Azure Cosmos DB ci-dessous, copiez l’exemple suivant dans un nouveau fichier Bicep. Vous pouvez éventuellement créer un fichier de paramètres à utiliser lors du déploiement de plusieurs instances de la même ressource avec des valeurs et noms différents. Il existe de nombreuses façons de déployer des modèles du Gestionnaire de ressource Azure, notamment : l’[interface CLI Azure](../../azure-resource-manager/bicep/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/bicep/deploy-powershell.md) et [Cloud Shell](../../azure-resource-manager/bicep/deploy-cloud-shell.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Compte Azure Cosmos avec débit approvisionné en mode de mise à l’échelle automatique

Ce modèle crée un compte Azure Cosmos dans deux régions avec des options de cohérence et de basculement, ainsi qu’une base de données et un conteneur configurés pour le débit approvisionné en mode de mise à l’échelle automatique et dont la plupart des options de stratégie sont activées.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/main.bicep":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Compte Azure Cosmos avec le magasin analytique

Créer un compte Azure Cosmos dans une région avec un conteneur ayant une durée de vie analytique activée et des options de débit manuel ou de mise à l’échelle automatique.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/main.bicep":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Compte Azure Cosmos avec débit approvisionné en mode standard

Créer un compte Azure Cosmos dans deux régions avec des options de cohérence et de basculement, ainsi qu’une base de données et un conteneur configurés pour le débit approvisionné en mode standard et dont la plupart des options de stratégie sont activées.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/main.bicep":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Conteneur Azure Cosmos DB avec des fonctionnalités côté serveur

Créer uun compte, une base de données et un conteneur Azure Cosmos avec une procédure stockée, un déclencheur et une fonction définie par l’utilisateur.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/main.bicep":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Compte Azure Cosmos DB avec Azure AD et RBAC

Créer un compte Azure Cosmos, une définition de rôle managée en mode natif et une attribution de rôle managée en mode natif pour une identité AAD.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/main.bicep":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Compte Azure Cosmos DB de niveau Gratuit

Ce modèle crée un compte Azure Cosmos de niveau Gratuit et une base de données avec un débit partagé qui peut être partagé avec un maximum de 25 conteneurs.

:::code language="bicep" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/main.bicep":::

## <a name="next-steps"></a>Étapes suivantes

Voici quelques ressources supplémentaires :

* [Documentation Bicep](../../azure-resource-manager/bicep/index.yml)
* [Installer les outils Bicep](../../azure-resource-manager/bicep/install.md)
