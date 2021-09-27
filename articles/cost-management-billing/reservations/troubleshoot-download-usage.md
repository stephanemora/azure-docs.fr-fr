---
title: Résoudre les problèmes de téléchargement des détails d’utilisation des réservations Azure
description: Cet article vous aide à comprendre pourquoi le téléchargement des détails d’utilisation des instances réservées n’est pas disponible dans le portail Azure et à résoudre le problème.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: reservations
ms.author: banders
ms.reviewer: yashar
ms.topic: troubleshooting
ms.date: 09/15/2021
ms.openlocfilehash: 6b899f634c5fbaa1acd0493da2d7b97b54588157
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656927"
---
# <a name="troubleshoot-azure-reservation-download-usage-details"></a>Résoudre les problèmes de téléchargement des détails d’utilisation des réservations Azure

Cet article vous aide à comprendre pourquoi le téléchargement des détails d’utilisation des instances réservées n’est pas disponible dans le portail Azure et à résoudre le problème.

## <a name="symptoms"></a>Symptômes

1. Connectez-vous au [portail Azure](https://portal.azure.com/) et accédez à **Réservations**.
1. Sélectionnez une réservation.
1. Dans la page de vue d’ensemble de la réservation, l’affichage par défaut montre l’utilisation des sept derniers jours. Vous pouvez sélectionner **Télécharger au format CSV** pour télécharger les détails d’utilisation de la réservation.
1. Quand vous changez l’intervalle de temps, l’option **Télécharger au format CSV** devient indisponible.
    :::image type="content" source="./media/troubleshoot-download-usage/download-csv-unavailable.png" alt-text="Exemple d’indisponibilité de l’option Télécharger au format CSV" lightbox="./media/troubleshoot-download-usage/download-csv-unavailable.png" :::

## <a name="cause"></a>Cause

En raison de limitations techniques, Azure ne prend pas en charge le téléchargement de données pour une période supérieure à sept jours. Les périodes longues pour les clients avec de grandes quantités de réservations génèrent de grandes quantités de données. La transmission des données via les API entraîne une surcharge sur les ressources Azure.

## <a name="solution"></a>Solution

Nous comprenons que les clients souhaitent télécharger des données pour des périodes plus longues. Toutefois, il n’existe aucun moyen de télécharger les données d’utilisation des réservations pour de longues périodes.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les réservations, consultez [Qu’est-ce qu’une réservation Azure ?](save-compute-costs-reservations.md).