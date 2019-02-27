---
title: Mise à l’échelle de l’Explorateur de données Azure pour prendre en compte les fluctuations de la demande
description: Cet article décrit les étapes à suivre pour augmenter et diminuer la taille d’un cluster de l’Explorateur de données Azure en fonction des fluctuations de la demande.
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 15ef5282e0a073e870f2ac12b5fc442407535770
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408440"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gérer la montée en charge du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais la demande sur un cluster n’est pas prévisible avec une précision de 100 %. Une taille de cluster statique peut conduire à une sous-utilisation ou à une surutilisation, ce qui n’est idéal dans aucun des cas. La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité en fonction des fluctuations de la demande. Il existe deux flux de travail de mise à l’échelle, scale-up et scale-out. Cet article explique le flux de travail scale-out.

Cet article vous montre comment gérer la montée en charge d’un cluster, également appelée mise à l'échelle automatique. La mise à l’échelle automatique vous permet de mettre à l’échelle le nombre d’instances automatiquement en fonction des planifications et des règles prédéfinies. Déterminez les paramètres de mise à l’échelle automatique de votre cluster dans le portail Azure, comme décrit ci-dessous.

Accédez à votre cluster, puis sous **Paramètres**, sélectionnez **Montée en charge**. Sous **Configurer**, sélectionnez **Activer la mise à l’échelle automatique**.

![Activer la mise à l’échelle automatique](media/manage-cluster-scaling/enable-autoscale.png)

Le graphisme suivant illustre le flux des prochaines étapes. Nous fournissons des informations détaillées supplémentaires sous le graphisme.

![Règle de mise à l’échelle](media/manage-cluster-scaling/scale-rule.png)

1. Sous **Nom du paramètre de mise à l’échelle automatique**, indiquez un nom, par exemple *Montée en charge : utilisation de cache*.

1. Sous **Mode de mise à l’échelle**, sélectionnez **Mettre à l’échelle selon une métrique**. Ce mode fournit une mise à l’échelle dynamique. Vous pouvez également sélectionner **Mettre à l’échelle à un nombre d’instances spécifique**.

1. Sélectionnez **Ajouter une règle**.

1. Dans la section **Règle de mise à l’échelle** à droite, entrez des valeurs pour chaque paramètre.

    **Critères**

    | Paramètre | Description et valeur |
    | --- | --- | --- |
    | **Agrégation du temps** | Sélectionnez un critère d’agrégation, comme **Moyenne**. |
    | **Nom de la métrique** | Sélectionnez la métrique sur laquelle vous voulez baser l’opération de mise à l’échelle, comme **Utilisation du cache**. |
    | **Statistiques de fragment de temps** | Choisissez entre **Moyenne**, **Minimum**, **Maximum** et **Somme**. |
    | **Opérateur** | Choisissez l’option appropriée, par exemple **Supérieur ou égal à**. |
    | **Seuil** | Choisissez une valeur appropriée. Par exemple, pour l’utilisation du cache, 80 % est un bon point de départ. |
    | **Durée (en minutes)** | Choisissez une durée appropriée pendant laquelle le système effectue des recherches lors du calcul de métriques. Commencez avec la valeur par défaut, 10 minutes. |
    |  |  |

    **Action**

    | Paramètre | Description et valeur |
    | --- | --- | --- |
    | **opération** | Sélectionnez l’option appropriée pour effectuer une mise à l'échelle. |
    | **Nombre d’instances** | Choisissez le nombre de nœuds ou d’instances que vous voulez ajouter ou supprimer quand une condition de métrique est remplie. |
    | **Refroidissement (minutes)** | Choisissez un intervalle de temps d’attente approprié entre les opérations de mise à l’échelle. Commencez avec la valeur par défaut, cinq minutes. |
    |  |  |

1. Sélectionnez **Ajouter**.

1. Dans la section **Limites d’instance** à gauche, entrez des valeurs pour chaque paramètre.

    | Paramètre | Description et valeur |
    | --- | --- | --- |
    | *Minimum* | Nombre d’instances en dessous duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | *Maximum* | Nombre d’instances au-dessus duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | *Par défaut* | Nombre d’instances par défaut, utilisé en cas de problème de lecture de métriques de ressources. |
    |  |  |

1. Sélectionnez **Enregistrer**.

Vous venez de configurer une opération de montée en charge pour votre cluster de l’Explorateur de données Azure. Ajoutez une autre règle pour une opération de diminution de la taille. Cela permet à votre cluster d’être mis à l’échelle dynamiquement en fonction des métriques que vous spécifiez.

Vous pouvez également effectuer une [montée en puissance](manage-cluster-scale-up.md) pour le bon dimensionnement d’un cluster.

Si vous avez besoin d’aide pour résoudre des problèmes de mise à l’échelle de clusters, ouvrez une demande de support dans le [portail Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).
