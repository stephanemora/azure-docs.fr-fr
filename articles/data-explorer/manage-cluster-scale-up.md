---
title: Effectuer le scale-up d’un cluster Azure Data Explorer pour prendre en compte les fluctuations de la demande
description: Cet article décrit les étapes à suivre pour effectuer un scale-up et un scale-down de la taille d’un cluster Azure Data Explorer en fonction des fluctuations de la demande.
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 1f130f79b6b6924559e1693e1eef8ced2972b3d5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60758695"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gérer le scale-up du cluster pour prendre en compte les fluctuations de la demande

Il existe deux flux de travail pour la mise à l’échelle d’un cluster Azure Data Explorer : scale-up et [scale-out](manage-cluster-scale-out.md). Cet article explique comment gérer le scale-up d’un cluster.

Dimensionner un cluster de manière appropriée est essentiel pour les performances d’Azure Data Explorer. Mais la demande sur un cluster n’est pas prévisible avec une précision absolue. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun cas. La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité et des ressources d’UC en fonction des fluctuations de la demande. 

## <a name="steps-to-scale-up"></a>Étapes pour effectuer un scale-up
1. Accédez à votre cluster. Sous **Paramètres**, sélectionnez **Scale-up**.

    La liste des références SKU disponibles s’affiche. Par exemple, dans la figure suivante, seuls quatre références SKU sont disponibles.

    ![Scale-up](media/manage-cluster-scale-up/scale-up.png)

    Les références SKU sont désactivées soit car il s’agit de la référence actuelle, soit car elles ne sont pas disponibles dans la région où se trouve le cluster.

1. Pour changer de référence SKU, sélectionnez-la et choisissez le bouton **Sélectionner**.

> [!NOTE]
> La processus de scale-up peut prendre quelques minutes, et pendant ce temps l’exécution de votre cluster est suspendue. Notez que la baisse de puissance peut endommager les performances de votre cluster.

Vous venez d’effectuer une opération de scale-up ou de scale-down pour votre cluster Azure Data Explorer.

## <a name="next-steps"></a>Étapes suivantes
Vous pouvez également [gérer le scale-out de cluster](manage-cluster-scale-out.md) afin d’adapter de manière dynamique le nombre d’instances en fonction des métriques que vous spécifiez.

Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, [ouvrez une demande de support dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

Supervisez l’utilisation de vos ressources en suivant les instructions de cet article : [Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques](using-metrics.md).
