---
title: Résoudre les problèmes d’échec de la création du cluster Explorateur de données Azure
description: Cet article décrit la procédure de résolution des problèmes pour la création d’un cluster dans l’Explorateur de données Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 9e6b3f53f07ac86d6b648a8562be4ef45879c37e
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044108"
---
# <a name="troubleshoot-failed-cluster-creation-of-azure-data-explorer"></a>Résolution de problèmes : Création du cluster ayant échoué de l’Explorateur de données Azure

Dans le cas peu probable où la création de cluster échouerait dans l’Explorateur de données Azure, effectuez les étapes suivantes.

1. Vérifiez que vous disposez des autorisations appropriées. Pour créer un cluster, vous devez être un membre du rôle *Collaborateur* ou *Propriétaire* pour l’abonnement Azure. Si nécessaire, demandez à l’administrateur de votre abonnement de vous ajouter au rôle approprié.

1. Vérifiez l’absence d’erreurs de validation liées au nom de cluster que vous avez entré sous **Créer un cluster** dans le portail Azure.

1. Consultez le [tableau de bord d’état du service Azure](https://azure.microsoft.com/status/). Recherchez l’état de l’Explorateur de données Azure dans la région où vous tentez de créer le cluster.

    Si l’état n’est pas **correct** (coche verte), essayez de créer le cluster une fois que l’état s’est amélioré.

1. Si vous avez toujours besoin d’aide pour résoudre votre problème, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
