---
title: Ajouter des détails techniques pour une offre Azure Application
description: Ajouter des détails techniques pour une offre Azure Application dans l’Espace partenaires (Place de marché Azure).
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 373dd2c3f0b107b0a910af138570c9cf1782e68c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542469"
---
# <a name="add-technical-details-for-an-azure-application-offer"></a>Ajouter des détails techniques pour une offre Azure Application

Cet article explique comment entrer des détails techniques qui permettent à la place de marché commerciale de Microsoft de se connecter à votre solution. Cette connexion permet d’approvisionner votre offre pour le client, s’il choisit de l’acquérir et de la gérer.

Suivez cette section uniquement si votre offre comprend une application managée qui émet des événements de mesure utilisant les [API de facturation à la consommation de la Place de marché](marketplace-metering-service-apis.md) et dispose d’un service qui s’authentifie avec un jeton de sécurité Azure AD. Pour plus d’informations, consultez les différentes options d’authentification dans [Stratégies d’authentification du service de mesure de la Place de marché](marketplace-metering-service-authentication.md).

## <a name="technical-configuration-offer-level"></a>Configuration technique (niveau de l’offre)

L’onglet **Configuration technique** ne vous concerne que si vous créez une application managée qui émet des événements de mesure utilisant les [API de facturation à la consommation de la Place de marché](marketplace-metering-service-apis.md). Dans ce cas, suivez les étapes décrites. Dans le cas contraire, passez aux [étapes suivantes](#next-steps). 

Pour plus d’informations sur ces champs, consultez [Planifier une offre Azure Application pour la Place de marché commerciale](plan-azure-application-offer.md#technical-configuration).

1. Sous l’onglet **Configuration technique**, entrez l’**ID de locataire Azure Active Directory** et l’**ID d’application Azure Active Directory** utilisés pour vérifier que la connexion entre nos deux services se trouve derrière une communication authentifiée.

1. Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon** : Vue d’ensemble du plan.

## <a name="next-steps"></a>Étapes suivantes

- [Créer des plans pour cette offre](azure-app-plans.md)
