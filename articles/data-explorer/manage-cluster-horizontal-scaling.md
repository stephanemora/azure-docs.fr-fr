---
title: Effectuer un scale-out d’un cluster Azure Data Explorer
description: Cet article décrit les étapes à suivre pour augmenter et diminuer la taille d’un cluster Azure Data Explorer en fonction des fluctuations de la demande.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571263"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>Gérer la mise à l’échelle horizontale du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais la demande sur un cluster n’est pas prévisible avec une précision absolue. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun cas.

La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité en fonction des fluctuations de la demande. Il existe deux workflows de mise à l’échelle : 
* Mise à l’échelle horizontale, également appelée scale-out et scale-in.
* Mise à l’échelle verticale, également appelée scale-up et scale-down.

Cet article explique le workflow de mise à l’échelle horizontale.

La mise à l’échelle horizontale vous permet de mettre à l’échelle le nombre d’instances automatiquement en fonction de planifications et de règles prédéfinies. Spécifiez les paramètres de mise à l’échelle automatique de votre cluster dans le portail Azure, comme décrit dans cet article.

## <a name="steps-to-configure-horizontal-scaling"></a>Étapes pour configurer la mise à l’échelle horizontale

Dans le portail Azure, accédez à votre ressource de cluster Data Explorer. Sous l’en-tête **Paramètres**, sélectionnez **Monter en charge**. 

Sélectionnez la méthode de mise à l’échelle automatique que vous souhaitez : **Mise à l’échelle manuelle**, **Mise à l’échelle automatique optimisée** ou **Mise à l’échelle personnalisée**.

### <a name="manual-scale"></a>Mise à l’échelle manuelle

La mise à l’échelle manuelle est le paramétrage par défaut avec la création du cluster. Cela signifie que le cluster dispose d’une capacité de cluster statique qui ne change pas automatiquement. Vous pouvez choisir la capacité statique à l’aide de la barre ; elle ne changera pas tant que vous ne changerez pas le paramétrage de scale-out du cluster.

   ![Méthode de mise à l’échelle manuelle](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>Mise à l’échelle automatique optimisée

La mise à l’échelle automatique optimisée est la méthode de mise à l’échelle automatique recommandée. Étapes pour configurer la mise à l’échelle automatique optimisée :

1. Sélectionnez l’option Mise à l’échelle automatique optimisée et choisissez une limite inférieure et une limite supérieure pour la quantité d’instances du cluster ; la mise à l’échelle automatique sera effectuée entre ces limites.
2. Cliquez sur Enregistrer.

   ![Méthode de mise à l’échelle automatique optimisée](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

Une fois que vous avez cliqué sur Enregistrer, le mécanisme de mise à l’échelle automatique optimisée commence à fonctionner et ses actions sont visibles dans le journal d’activité du cluster. Cette méthode de mise à l’échelle automatique optimise le niveau de performance et les coûts du cluster : dès que ce dernier atteint un état de sous-exploitation, il subit un scale-in, qui permet de réduire les coûts tout en conservant le même niveau de performance, et dès qu’il atteint un état de surexploitation, il subit un scale-out afin de fonctionner correctement.

### <a name="custom-autoscale"></a>Mise à l’échelle automatique personnalisée

La méthode de mise à l’échelle automatique personnalisée vous permet de mettre à l’échelle votre cluster dynamiquement en fonction de métriques que vous spécifiez. Le graphique suivant illustre le flux et les étapes de la configuration d’une mise à l’échelle automatique personnalisée. Voir les détails sous le graphique.

1. Dans la zone **Nom du paramètre de mise à l’échelle automatique**, indiquez un nom, par exemple *Scale-out : utilisation de cache*. 

   ![Règle de mise à l’échelle](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. Pour **Mode de mise à l’échelle**, sélectionnez **Mettre à l’échelle selon une mesure**. Ce mode fournit une mise à l’échelle dynamique. Vous pouvez également sélectionner **Mettre à l’échelle à un nombre d’instances spécifique**.

3. Sélectionnez **+ Ajouter une règle**.

4. Dans la section **Règle de mise à l’échelle** à droite, entrez des valeurs pour chaque paramètre.

    **Critères**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Agrégation du temps** | Sélectionnez un critère d’agrégation, comme **Moyenne**. |
    | **Nom de la métrique** | Sélectionnez la métrique sur laquelle vous voulez baser l’opération de mise à l’échelle, comme **Utilisation du cache**. |
    | **Statistiques de fragment de temps** | Choisissez entre **Moyenne**, **Minimum**, **Maximum** et **Somme**. |
    | **Opérateur** | Choisissez l’option appropriée, par exemple **Supérieur ou égal à**. |
    | **Seuil** | Choisissez une valeur appropriée. Par exemple, pour l’utilisation du cache, 80 pour cent est un bon point de départ. |
    | **Durée (en minutes)** | Choisissez une durée appropriée pendant laquelle le système effectue des recherches lors du calcul de métriques. Commencez avec la valeur par défaut, 10 minutes. |
    |  |  |

    **Action**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **opération** | Choisissez l’option appropriée pour diminuer ou augmenter la taille. |
    | **Nombre d’instances** | Choisissez le nombre de nœuds ou d’instances que vous voulez ajouter ou supprimer quand une condition de métrique est remplie. |
    | **Refroidissement (minutes)** | Choisissez un intervalle de temps d’attente approprié entre les opérations de mise à l’échelle. Commencez avec la valeur par défaut, cinq minutes. |
    |  |  |

5. Sélectionnez **Ajouter**.

6. Dans la section **Limites d’instance** à gauche, entrez des valeurs pour chaque paramètre.

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Minimum** | Nombre d’instances en dessous duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Maximum** | Nombre d’instances au-dessus duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Par défaut** | Nombre d’instances par défaut. Ce paramètre est utilisé s’il existe des problèmes de lecture des métriques de ressource. |
    |  |  |

7. Sélectionnez **Enregistrer**.

Vous venez de configurer une opération de montée en charge pour votre cluster de l’Explorateur de données Azure. Ajoutez une autre règle pour une opération de diminution de la taille. Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, [ouvrez une demande de support dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Étapes suivantes

* [Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques](using-metrics.md)
* [Gérer la mise à l’échelle verticale du cluster](manage-cluster-vertical-scaling.md) pour dimensionner ce dernier de manière appropriée.
