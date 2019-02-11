---
title: Mise à l’échelle de l’Explorateur de données Azure pour prendre en compte les fluctuations de la demande
description: Cet article décrit les étapes à suivre pour augmenter et diminuer la taille d’un cluster de l’Explorateur de données Azure en fonction des fluctuations de la demande.
author: radennis
ms.author: radennis
ms.reviewer: v-orspod
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 213a49d87d5e9f801bb17604a322c231a4e3dee2
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735312"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>Gérer la monter en puissance du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais la demande sur un cluster n’est pas prévisible avec une précision de 100 %. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun des cas. La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité et de l’UC en fonction des fluctuations de la demande. Cet article vous montre comment gérer la montée en puissance d’un cluster.

Accédez à votre cluster, puis sous **Paramètres**, sélectionnez **Montée en puissance**.

Vous obtiendrez alors la liste des références SKU disponibles. Vous pouvez choisir dans la liste des cartes activées. Par exemple, dans la figure ci-dessous, la liste D14_vs comporte une seule référence SKU sélectionnable.

![Monter en puissance](media/manage-cluster-scale-up/scale-up.png)

D13_v2 est désactivé car il s’agit de la référence SKU actuelle. L8 et L16 sont désactivés car ils ne sont pas disponibles dans la région où réside le cluster.

Pour changer une référence SKU, il suffit de cliquer sur la référence SKU souhaitée et de cliquer sur le bouton **Sélectionner**.

[!NOTE] La processus de montée en puissance peut prendre quelques minutes, et pendant ce temps l’exécution de votre cluster est suspendue. Notez que la baisse de puissance peut endommager les performances de votre cluster.

Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).