---
title: 'Notes de publication : Azure Synapse Analytics (espaces de travail)'
description: Notes de publication d’Azure Synapse Analytics (espaces de travail)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: add5c89e83f33980803bf571239023859653c4f1
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059609"
---
# <a name="azure-synapse-analytics-preview-release-notes"></a>Notes de publication d’Azure Synapse Analytics (préversion)

Cet article décrit les limitations et les problèmes liés à Azure Synapse Analytics (espaces de travail). Pour obtenir des informations connexes, consultez [Qu’est-ce qu’Azure Synapse Analytics (espaces de travail) ?](overview-what-is.md).

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-synapse-workspaces"></a>Azure Synapse (espaces de travail) 

### <a name="azure-synapse-cli"></a>Interface CLI Azure Synapse

- Problème et impact sur le client : Les espaces de travail créés par le SDK ne peuvent pas lancer Synapse Studio

- Solution de contournement : Suivez les étapes ci-dessous : 
  1.    Créez un espace de travail en exécutant `az synapse workspace create`.
  2.    Extrayez l’ID d’identité managée en exécutant `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Ajoutez l’espace de travail en tant que rôle au compte de stockage en exécutant ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Ajoutez une règle de pare-feu en exécutant ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Étapes suivantes

* [Créer un espace de travail](quickstart-create-workspace.md)
* [Utiliser Synapse Studio](quickstart-synapse-studio.md)
* [Créer un pool SQL](quickstart-create-sql-pool-portal.md)
* [Utiliser SQL à la demande](quickstart-sql-on-demand.md)
* [Créer un pool Apache Spark](quickstart-create-apache-spark-pool-portal.md)