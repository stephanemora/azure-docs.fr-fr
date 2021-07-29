---
title: Comment ajouter un public de préversion pour une offre Azure Application
description: Ajouter un public de préversion pour une application Azure dans l’Espace partenaires (Place de marché Azure).
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 74f270922694dd0416638b8d429eca31cebf2609
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/06/2021
ms.locfileid: "111542493"
---
# <a name="add-a-preview-audience-for-an-azure-application-offer"></a>Comment ajouter un public de préversion pour une offre Azure Application

Cet article explique comment configurer un public de préversion pour une offre Azure Application dans la Place de marché commerciale. Vous devez définir un public de préversion pouvant consulter le référencement de votre offre avant sa mise en ligne.

## <a name="define-a-preview-audience"></a>Définir un public de préversion

Dans la page **Public de préversion**, vous pouvez définir un public limité pouvant consulter votre offre Azure Application avant sa publication en ligne pour le public plus large de la Place de marché. Vous définissez le public de la préversion à l’aide d’ID d’abonnement Azure et d’une description facultative pour chacun. Aucun de ces champs ne peut être vu par les clients. Vous trouverez votre ID d’abonnement Azure dans la page **Abonnements** du portail Azure.

Ajoutez de un à 10 ID d’abonnement Azure individuellement ou chargez un fichier CSV (jusqu’à 100 ID) pour définir qui peut accéder à la préversion votre offre avant sa publication en ligne. Si votre offre est déjà publiée, vous pouvez toujours définir un public pour la préversion en vue de test de changements ou de mises à jour futurs de l’offre.

> [!NOTE]
> Le public pour la préversion n’est pas le même que le public privé. Un public de préversion peut accéder à votre offre avant sa publication dans les magasins en ligne. Il peut voir et valider tous les plans, y compris les plans qui seront disponibles uniquement pour un public privé une fois que votre offre sera entièrement publiée sur la Place de marché. Vous pouvez rendre un plan disponible uniquement pour un public privé. Un public privé (défini sous l’onglet **Disponibilité** du plan) dispose d’un accès exclusif à un plan particulier.

### <a name="add-subscription-ids-manually"></a>Ajouter manuellement des ID d’abonnement

1. Sur la page **Public de préversion**, ajoutez un ID d’abonnement Azure et une description facultative dans les zones fournies.
1. Pour ajouter un autre ID, sélectionnez le lien **Ajouter ID (10 max)** .
1. Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon** : Configuration technique.
1. Passez aux [étapes suivantes](#next-steps).

### <a name="add-subscription-ids-with-a-csv-file"></a>Ajouter des ID d’abonnement avec un fichier CSV

1. Sur la page **Public de préversion**, sélectionnez le lien **Exporter le public (CSV)** .
1. Ouvrez le fichier .CSV dans une application adaptée, par exemple Microsoft Excel.
1. Dans le fichier .CSV, dans la colonne **ID**, entrez les ID d’abonnement Azure que vous souhaitez ajouter au public de préversion.
1. Dans la colonne **Description**, vous pouvez ajouter une description pour chaque adresse e-mail.
1. Pour chaque ID d’abonnement que vous entrez dans la colonne B, entrez un **type** dans la colonne A de « SubscriptionId ».
1. Enregistrez sous un fichier .CSV.
1. Sur la page **Public de préversion**, sélectionnez le lien **Importer le public (csv)** .
1. Dans la boîte de dialogue **Confirmer**, sélectionnez **Oui**.
1. Sélectionnez le fichier .CSV, puis **Ouvrir**.
1. Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon** : Configuration technique.

## <a name="next-steps"></a>Étapes suivantes

- [Ajouter des détails techniques à cette offre](azure-app-technical-configuration.md)
