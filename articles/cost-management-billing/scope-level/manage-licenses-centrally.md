---
title: Application par Azure des licences SQL attribuées à une utilisation horaire
description: Cet article explique en détail la façon dont Azure applique les licences SQL attribuées à une utilisation horaire avec Azure Hybrid Benefit.
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/07/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: ahb
ms.reviewer: chrisrin
ms.openlocfilehash: 69517a84fa28a6c1d9cde0cb51df82a296bd653a
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710157"
---
# <a name="how-azure-applies-assigned-sql-licenses-to-hourly-usage"></a>Application par Azure des licences SQL attribuées à une utilisation horaire

Cet article fournit une explication détaillée du fonctionnement d’Azure Hybrid Benefit au niveau de l’étendue pour vous aider à gérer vos licences SQL Server. Le processus commence par un administrateur qui attribue des licences à l’étendue d’un abonnement ou d’un compte de facturation.

Chaque ressource signale son utilisation une fois par heure à l’aide des compteurs appropriés de plein tarif ou de paiement à l’utilisation. En interne dans Azure, le moteur Application de l’utilisation évalue les licences de base normalisées disponibles et les applique pour cette heure. Pour une heure donnée de consommation de ressources vCore, les compteurs de paiement à l’utilisation sont remplacés par le compteur Azure Hybrid Benefit correspondant avec un prix nul (0 USD) s’il y a suffisamment de licences de base normalisées inutilisés dans l’étendue sélectionnée.

## <a name="license-discount"></a>Remise de licence 

Le diagramme suivant illustre le processus de remise lorsqu’il y a suffisamment de licences de base normalisées inutilisées pour réduire la consommation totale de vCore par toutes les ressources SQL pour l’heure.

Les prix indiqués dans l’image suivante sont donnés à titre d’exemple uniquement.

:::image type="content" source="./media/manage-licenses-centrally/fully-discounted-consumption.svg" alt-text="Diagramme montrant la consommation de vCore entièrement escomptée." border="false" lightbox="./media/manage-licenses-centrally/fully-discounted-consumption.svg":::


Lorsque la consommation de vCore par les ressources SQL dans l’étendue dépasse le nombre de licences de base normalisées inutilisées, la consommation excédentaire de vCore est facturée à l’aide du compteur de paiement à l’utilisation approprié. Le diagramme suivant illustre le processus de remise lorsque la consommation de vCore dépasse le nombre de licences de base normalisées non utilisées.

Les prix indiqués dans l’image suivante sont donnés à titre d’exemple uniquement.

:::image type="content" source="./media/manage-licenses-centrally/partially-discounted-consumption.svg" alt-text="Diagramme montrant une consommation partiellement escomptée." border="false" lightbox="./media/manage-licenses-centrally/partially-discounted-consumption.svg":::

Les ressources Azure SQL couvertes par les licences de base attribuées peuvent varier d’heure en heure. La variance dépend des ressources exécutées et de l’ordre dans lequel le système automatisé traite leur utilisation. Toutefois, le système garantit l’utilisation maximale des licences SQL attribuées dans l’étendue sélectionnée. Vous pouvez surveiller l’utilisation à l’aide de Cost Management. Pour plus d’informations, consultez [Suivi de l’utilisation des licences attribuées](create-sql-license-assignments.md#track-assigned-license-use).

Le diagramme suivant montre la manière dont s’appliquent les licences SQL Server attribuées au fil du temps pour bénéficier de la remise maximale d’Azure Hybrid Benefit.

:::image type="content" source="./media/manage-licenses-centrally/ncl-utilization-over-time.png" alt-text="Diagramme montrant l’utilisation des licences de base normalisées dans le temps." border="false" lightbox="./media/manage-licenses-centrally/ncl-utilization-over-time.png":::

## <a name="next-steps"></a>Étapes suivantes

- Consultez [Questions fréquentes sur la gestion de niveau étendue d’Azure Hybrid Benefit](faq-azure-hybrid-benefit-scope.yml).
- Découvrez comment [effectuer la transition à partir d’une expérience Azure Hybrid Benefit existante](transition-existing.md).