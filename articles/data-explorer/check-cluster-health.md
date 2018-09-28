---
title: Vérifier l’intégrité d’un cluster de l’Explorateur de données Azure
description: Cet article décrit la procédure permettant de déterminer si votre cluster de l’Explorateur de données Azure est sain.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0746247d2c912ba66e81b95f45b168e32b522130
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46988425"
---
# <a name="check-the-health-of-an-azure-data-explorer-cluster"></a>Vérifier l’intégrité d’un cluster de l’Explorateur de données Azure

Plusieurs facteurs affectent l’intégrité d’un cluster de l’Explorateur de données Azure, notamment le processeur, la mémoire et le sous-système de disque. Cet article illustre certaines étapes de base que vous pouvez suivre pour évaluer l’intégrité d’un cluster.

1. Connectez-vous à [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. Dans le volet gauche, sélectionnez votre cluster et exécutez la commande suivante.

    ```Kusto
    .show diagnostics
    | project IsHealthy
    ```
    Le résultat 1 indique un cluster sain ; le résultat 0 indique un cluster défectueux.

1. Connectez-vous au [portail Azure](https://portal.azure.com) et accédez à votre cluster.

1. Sous **Surveillance**, sélectionnez **Métriques**, puis **Keep Alive**, comme illustré dans l’image suivante. Un résultat proche de 1 signifie qu’un cluster est sain.

    ![Métriques Keep Alive de cluster](media/check-cluster-health/portal-metrics.png)

1. Ajoutez d’autres métriques comme le processeur et la mise en cache mémoire pour évaluer l’utilisation des ressources pour le cluster.

1. Si vous avez besoin d’aide pour diagnostiquer les problèmes d’intégrité d’un cluster, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com).