---
title: 'Notes de publication : Azure Synapse Analytics (espaces de travail en préversion)'
description: Notes de publication d’Azure Synapse Analytics (espaces de travail en préversion)
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: f2050bf671af35df4faec8b0b1d53f4c10a9075e
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90031668"
---
# <a name="azure-synapse-analytics-workspaces-preview-release-notes"></a>Notes de publication d’Azure Synapse Analytics (espaces de travail en préversion)

Cet article décrit les limitations et les problèmes liés à Azure Synapse Analytics (espaces de travail). Pour obtenir des informations connexes, consultez [Qu’est-ce qu’Azure Synapse Analytics (espaces de travail) ?](overview-what-is.md).

[!INCLUDE [preview](includes/note-preview.md)]

## <a name="azure-cli"></a>Azure CLI

- Problème et impact sur le client : Les espaces de travail créés par le SDK ne peuvent pas lancer Synapse Studio

- Solution de contournement : Suivez les étapes ci-dessous : 
  1.    Créez un espace de travail en exécutant `az synapse workspace create`.
  2.    Extrayez l’ID de l’identité managée en exécutant `$identity=$(az synapse workspace show --name {workspace name}  --resource-group {resource group name} --query "identity.principalId")`.
  3.    Ajoutez l’espace de travail en tant que rôle au compte de stockage en exécutant ` az role assignment create --role "Storage Blob Data Contributor" --assignee-object-id {identity } --scope {storage account resource id}`.
  4.    Ajoutez une règle de pare-feu en exécutant ` az synapse firewall-rule create --name allowAll --start-ip-address 0.0.0.0 --end-ip-address 255.255.255.255 `.

## <a name="next-steps"></a>Étapes suivantes

* [Qu’est-ce qu’Azure Synapse ?](overview-what-is.md)
* [Bien démarrer](get-started.md)
* [FORUM AUX QUESTIONS](overview-faq.md)
