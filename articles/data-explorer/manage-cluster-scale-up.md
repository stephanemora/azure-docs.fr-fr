---
title: Mise à l’échelle de l’Explorateur de données Azure pour prendre en compte les fluctuations de la demande
description: Cet article décrit les étapes à suivre pour augmenter et diminuer la taille d'un cluster Azure Data Explorer en fonction des fluctuations de la demande.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: bc3f97c798f5e040908e8103c00d3f015f8c824d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415332"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gérer la monter en puissance du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais la demande sur un cluster n’est pas prévisible avec une précision de 100 %. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun des cas. La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité et de l’UC en fonction des fluctuations de la demande. Il existe deux flux de travail pour la mise à l'échelle : la montée en puissance et le scale-out. Cet article explique comment gérer la montée en puissance d'un cluster.

1. Accédez à votre cluster, puis sous **Paramètres**, sélectionnez **Montée en puissance**.

    La liste des références SKU disponibles s'affiche. Par exemple, dans la figure ci-dessous, il n'y a qu'une seule référence SKU disponible : D14_V2.

    ![Monter en puissance](media/manage-cluster-scale-up/scale-up.png)

    D13_V2 est désactivé car il s'agit de la référence SKU actuelle du cluster. L8 et L16 sont désactivés car ils ne sont pas disponibles dans la région où se trouve le cluster.

1. Pour changer de référence SKU, sélectionnez-la et appuyez sur le bouton **Sélectionner**.

> [!NOTE]
> La processus de montée en puissance peut prendre quelques minutes, et pendant ce temps l’exécution de votre cluster est suspendue. Notez que la baisse de puissance peut endommager les performances de votre cluster.

Vous venez d'effectuer une opération de réduction ou de montée en puissance pour votre cluster Azure Data Explorer. Vous pouvez également effectuer un [scale-out du cluster](manage-cluster-scale-out.md), également appelé mise à l'échelle automatique, pour procéder à une mise à l'échelle dynamique basée sur les métriques que vous spécifiez.

Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
