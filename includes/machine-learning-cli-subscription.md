---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296869"
---
Après vous être connecté, vous voyez une liste des abonnements associés à votre compte Azure. Les informations d’abonnement avec `isDefault: true` correspond à l’abonnement actuellement activé pour les commandes Azure CLI. Cet abonnement doit être le même que celui qui contient votre espace de travail Azure Machine Learning. Vous pouvez trouver l’ID d’abonnement à partir du [portail Azure](https://portal.azure.com) en visitant la page Vue d’ensemble de votre espace de travail. Vous pouvez également utiliser le kit de développement logiciel (SDK) pour récupérer l’ID d’abonnement de l’objet d’espace de travail. Par exemple : `Workspace.from_config().subscription_id`.
    
Pour sélectionner un autre abonnement, utilisez la commande [az account set](https://docs.microsoft.com/cli/azure/account#az-account-set) avec l’ID d’abonnement auquel vous voulez passer. Pour plus d’informations sur la sélection d’abonnements, consultez [Utiliser plusieurs abonnements Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).