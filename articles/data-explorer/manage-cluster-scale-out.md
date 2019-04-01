---
title: Mise à l’échelle de l’Explorateur de données Azure pour prendre en compte les fluctuations de la demande
description: Cet article décrit les étapes pour monter en charge et de mettre à l’échelle dans un cluster de l’Explorateur de données Azure basée sur la modification de la demande.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
services: data-explorer
ms.topic: conceptual
ms.date: 02/18/2019
ms.openlocfilehash: 9b54bf182f23eceb47c392059ff52c04bf0a8aed
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755066"
---
# <a name="manage-cluster-scale-out-to-accommodate-changing-demand"></a>Gérer la montée en charge du cluster pour prendre en compte les fluctuations de la demande

Dimensionner un cluster de manière appropriée est essentiel pour les performances de l’Explorateur de données Azure. Mais, à la demande sur un cluster ne peut pas être prédite avec précision absolue. Une taille de cluster statiques pouvant conduire à une sous-utilisation ou de surexploitation, qui ne convient.

La meilleure approche consiste à *mettre à l’échelle* un cluster, en ajoutant et en supprimant de la capacité en fonction des fluctuations de la demande. Il existe deux flux de travail pour la mise à l’échelle : monter en puissance et montée en puissance. Cet article explique le flux de travail scale-out.

Cet article explique comment gérer le cluster montée en puissance, également appelé mise à l’échelle. À l’échelle automatique vous permet de faire évoluer le nombre d’instances automatiquement en fonction des planifications et des règles prédéfinies. Spécifiez vos paramètres de mise à l’échelle de votre cluster dans le portail Azure, comme décrit dans cet article.

Accédez à votre cluster. Sous **paramètres**, sélectionnez **monter en charge**. Sous **Configurer**, sélectionnez **Activer la mise à l’échelle automatique**.

![Activer la mise à l’échelle automatique](media/manage-cluster-scaling/enable-autoscale.png)

Le graphisme suivant illustre le flux des prochaines étapes. Plus de détails se trouvent sous le graphique.

![Règle de mise à l’échelle](media/manage-cluster-scaling/scale-rule.png)

1. Dans le **nom du paramètre de mise à l’échelle** zone, fournissez un nom, tel que *Scale-out : utilisation de cache*.

1. Pour **en mode de mise à l’échelle**, sélectionnez **mise à l’échelle selon une mesure**. Ce mode fournit une évolutivité dynamique. Vous pouvez également sélectionner **mise à l’échelle à un nombre d’instances spécifique**.

1. Sélectionnez **+ ajouter une règle**.

1. Dans la section **Règle de mise à l’échelle** à droite, entrez des valeurs pour chaque paramètre.

    **Critères**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Agrégation du temps** | Sélectionnez un critère d’agrégation, comme **Moyenne**. |
    | **Nom de la métrique** | Sélectionnez la métrique sur laquelle vous voulez baser l’opération de mise à l’échelle, comme **Utilisation du cache**. |
    | **Statistiques de fragment de temps** | Choisissez entre **Moyenne**, **Minimum**, **Maximum** et **Somme**. |
    | **Opérateur** | Choisissez l’option appropriée, par exemple **Supérieur ou égal à**. |
    | **Seuil** | Choisissez une valeur appropriée. Par exemple, pour l’utilisation du cache, 80 pour cent est un bon point de départ. |
    | **Durée (en minutes)** | Choisissez une durée appropriée pendant laquelle le système effectue des recherches lors du calcul de métriques. Commencez avec la valeur par défaut, 10 minutes. |
    |  |  |

    **Action**

    | Paramètre | Description et valeur |
    | --- | --- |
    | **opération** | Choisissez l’option appropriée pour diminuer ou augmenter la taille. |
    | **Nombre d’instances** | Choisissez le nombre de nœuds ou d’instances que vous voulez ajouter ou supprimer quand une condition de métrique est remplie. |
    | **Refroidissement (minutes)** | Choisissez un intervalle de temps d’attente approprié entre les opérations de mise à l’échelle. Commencez avec la valeur par défaut, cinq minutes. |
    |  |  |

1. Sélectionnez **Ajouter**.

1. Dans la section **Limites d’instance** à gauche, entrez des valeurs pour chaque paramètre.

    | Paramètre | Description et valeur |
    | --- | --- |
    | **Minimum** | Nombre d’instances en dessous duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Maximum** | Nombre d’instances au-dessus duquel la mise à l’échelle de votre cluster ne se met pas en œuvre, quelle que soit l’utilisation. |
    | **Par défaut** | Nombre d’instances par défaut. Ce paramètre est utilisé s’il existe des problèmes de lecture des métriques de ressource. |
    |  |  |

1. Sélectionnez **Enregistrer**.

Vous venez de configurer une opération de montée en charge pour votre cluster de l’Explorateur de données Azure. Ajoutez une autre règle pour une opération de diminution de la taille. Cette configuration permet à votre cluster à l’échelle dynamiquement en fonction de mesures que vous spécifiez.

Vous pouvez également [gérer monter en puissance cluster](manage-cluster-scale-up.md) pour le dimensionnement approprié d’un cluster.

Si vous avez besoin d’aide sur les problèmes de mise à l’échelle le cluster, [ouvrir une demande de support](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) dans le portail Azure.
