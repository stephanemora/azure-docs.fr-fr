---
title: Effectuer un scale-out d’un cluster Azure Data Explorer
description: Cet article décrit les étapes à suivre pour augmenter et diminuer la taille d’un cluster Azure Data Explorer en fonction des fluctuations de la demande.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 882f44683bbdc7f4eb49ff4912ca7a33187afbf8
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537891"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gérer la montée en charge du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais la demande sur un cluster n’est pas prévisible avec une précision absolue. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun cas.

La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité en fonction des fluctuations de la demande. Il existe deux workflows de mise à l’échelle : la montée en puissance (scale-up) et la montée en charge (scale-out). Cet article explique le flux de travail scale-out.

Il montre comment gérer le scale-out d’un cluster, également appelé mise à l’échelle automatique. La mise à l’échelle automatique vous permet de mettre à l’échelle le nombre d’instances automatiquement en fonction de planifications et de règles prédéfinies. Spécifiez les paramètres de mise à l’échelle automatique de votre cluster dans le portail Azure, comme décrit dans cet article.

## <a name="steps-to-configure-autoscale"></a>Étapes pour configurer la mise à l’échelle automatique

Dans le portail Azure, accédez à votre ressource de cluster Data Explorer. Sous l’en-tête **Paramètres**, sélectionnez **Monter en charge**. Sous l’onglet **Configurer**, sélectionnez **Activer la mise à l’échelle automatique**.

   ![Activer la mise à l’échelle automatique](media/manage-cluster-scaling/enable-autoscale.png)

Le graphisme suivant illustre le flux des prochaines étapes. Voir les détails sous le graphique.

1. Dans la zone **Nom du paramètre de mise à l’échelle automatique**, indiquez un nom, par exemple *Scale-out : utilisation de cache*. 

   ![Règle de mise à l’échelle](media/manage-cluster-scaling/scale-rule.png)

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

Vous venez de configurer une opération de montée en charge pour votre cluster de l’Explorateur de données Azure. Ajoutez une autre règle pour une opération de diminution de la taille. Cette configuration permet à votre cluster d’être mis à l’échelle dynamiquement en fonction des métriques que vous spécifiez.

Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, [ouvrez une demande de support dans le portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="next-steps"></a>Étapes suivantes

* [Superviser les performances, l’intégrité et l’utilisation d’Azure Data Explorer avec des métriques](using-metrics.md)

* [Gérer la montée en puissance du cluster](manage-cluster-scale-up.md) pour dimensionner ce dernier de manière appropriée.
