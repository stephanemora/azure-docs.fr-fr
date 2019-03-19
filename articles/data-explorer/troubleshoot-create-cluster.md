---
title: Échec de création d’un cluster dans l’Explorateur de données Azure
description: Cet article décrit la procédure de résolution des problèmes pour la création d’un cluster dans l’Explorateur de données Azure.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9b8bfe2a4b9b7a8432f14fb53b3e7a4cae49a3b4
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58189969"
---
# <a name="troubleshoot-failure-to-create-a-cluster-in-azure-data-explorer"></a>Résolution de problèmes : Échec de création d’un cluster dans l’Explorateur de données Azure

Dans le cas peu probable où la création de cluster échouerait dans l’Explorateur de données Azure, effectuez les étapes suivantes.

1. Vérifiez que vous disposez des autorisations appropriées. Pour créer un cluster, vous devez être un membre du rôle *Collaborateur* ou *Propriétaire* pour l’abonnement Azure. Si nécessaire, demandez à l’administrateur de votre abonnement de vous ajouter au rôle approprié.

1. Vérifiez l’absence d’erreurs de validation liées au nom de cluster que vous avez entré sous **Créer un cluster** dans le portail Azure.

1. Consultez le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Recherchez l’état de l’Explorateur de données Azure dans la région où vous tentez de créer le cluster.

    Si l’état n’est pas **correct** (coche verte), essayez de créer le cluster une fois que l’état s’est amélioré.

1. Si vous avez toujours besoin d’aide pour résoudre votre problème, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).