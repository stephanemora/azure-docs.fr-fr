---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616840"
---
> [!TIP]
> Après vous être connecté, vous voyez une liste des abonnements associés à votre compte Azure. Les informations d’abonnement avec `isDefault: true` correspond à l’abonnement actuellement activé pour les commandes Azure CLI. Cet abonnement doit être le même que celui qui contient votre espace de travail Azure Machine Learning. Vous pouvez trouver l’ID d’abonnement à partir du [portail Azure](https://portal.azure.com) en visitant la page Vue d’ensemble de votre espace de travail. Vous pouvez également utiliser le kit de développement logiciel (SDK) pour récupérer l’ID d’abonnement de l’objet d’espace de travail. Par exemple : `Workspace.from_config().subscription_id`.
> 
> Pour sélectionner un autre abonnement, utilisez la commande `az account set -s <subscription name or ID>` et spécifiez le nom ou l’ID de l’abonnement auquel vous voulez passer. Pour plus d’informations sur la sélection d’abonnements, consultez [Utiliser plusieurs abonnements Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).