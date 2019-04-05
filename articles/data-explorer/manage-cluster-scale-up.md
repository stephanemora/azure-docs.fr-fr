---
title: Mettre à l’échelle d’un cluster de l’Explorateur de données Azure pour s’adapter à la demande de modification
description: Cet article décrit les étapes pour monter et Descendre un cluster de l’Explorateur de données Azure en fonction des modifications à la demande.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 565953c8e0c6f9765d5eeb16a9fa18c3e79b8370
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/05/2019
ms.locfileid: "59044927"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gérer la monter en puissance du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais, à la demande sur un cluster ne peut pas être prédite avec précision absolue. Une taille de cluster statiques pouvant conduire à une sous-utilisation ou de surexploitation, qui ne convient.

Une meilleure approche consiste à *mise à l’échelle* un cluster, ajout et suppression de capacité et des ressources de processeur avec à la demande de modification. Il existe deux flux de travail pour la mise à l’échelle : monter en puissance et montée en puissance. Cet article explique comment gérer la montée en charge de cluster.

1. Accédez à votre cluster. Sous **paramètres**, sélectionnez **monter**.

    Affiche une liste de références (SKU) disponibles. Par exemple, dans la figure suivante, seulement quatre références SKU est disponibles.

    ![Monter en puissance](media/manage-cluster-scale-up/scale-up.png)

    Références (SKU) est désactivés, car il s’agit de la référence actuelle, ou ils ne sont pas disponibles dans la région où se trouve le cluster.

1. Pour modifier votre référence (SKU), sélectionnez la référence (SKU) puis choisissez le **sélectionnez** bouton.

> [!NOTE]
> Le processus de mise à l’échelle peut prendre quelques minutes, et pendant ce temps votre cluster sera interrompu. Notez que la baisse de puissance peut endommager les performances de votre cluster.

Vous avez maintenant terminé une opération de montée ou descente en puissance pour votre cluster de l’Explorateur de données Azure. Vous pouvez également [gérer montée cluster](manage-cluster-scale-out.md) permettent d’adapter dynamiquement le nombre d’instances en fonction des métriques que vous spécifiez la sortie.

Si vous avez besoin d’aide sur les problèmes de mise à l’échelle le cluster, [ouvrir une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) dans le portail Azure.
