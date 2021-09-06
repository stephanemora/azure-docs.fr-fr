---
title: Ajouter un public de préversion pour une offre de service géré
description: Ajoutez un public de préversion pour une offre de service géré sur la Place de marché Azure.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 7/16/2021
ms.openlocfilehash: 38fbf04f9e8f961cca99bd3e0b23e05e664f4848
ms.sourcegitcommit: d2738669a74cda866fd8647cb9c0735602642939
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2021
ms.locfileid: "113649714"
---
# <a name="add-a-preview-audience-for-a-managed-service-offer"></a>Ajouter un public de préversion pour une offre de service géré

Cet article explique comment configurer un public de préversion pour une offre Service géré sur le marketplace commercial à l’aide d’Espace partenaires. Le public de préversion peut évaluer votre offre avant sa mise en ligne.

## <a name="define-a-preview-audience"></a>Définir un public de préversion

Dans la page **Public de préversion**, vous pouvez définir un public limité pouvant consulter votre offre Service géré avant sa publication en ligne pour le public plus large du marketplace. Vous définissez le public de la préversion à l’aide d’ID d’abonnement Azure et d’une description facultative pour chacun. Aucun de ces champs ne peut être vu par les clients. Vous pouvez trouver votre ID d’abonnement Azure sur la page **Abonnements** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, que ce soit individuellement (jusqu’à 10) ou en chargeant un fichier CSV (jusqu’à 100) pour définir qui peut accéder à la préversion de votre offre avant sa publication officielle. Si votre offre est déjà publiée, vous pouvez toujours définir un public de préversion pour tester les mises à jour de votre offre.

> [!NOTE]
> Un public *de préversion* diffère d’un public *privé*. Un public de préversion peut accéder à votre offre avant sa publication dans les magasins en ligne. Il peut voir et valider tous les plans, y compris les plans qui seront disponibles uniquement pour un public privé une fois que votre offre sera entièrement publiée sur le marketplace. Vous pouvez rendre un plan disponible uniquement pour un public privé. Un public privé (défini sous l’onglet Disponibilité du plan) dispose d’un accès exclusif à un plan particulier.

## <a name="add-email-addresses-manually"></a>Ajouter des adresses e-mail manuellement

1. Sur la page **Public de préversion**, ajoutez un ID d’abonnement Azure et une description facultative dans les zones fournies.
2. Pour ajouter une autre adresse e-mail, sélectionnez le lien **Ajouter ID (max 10)** .
3. Avant de passer à l’onglet suivant, sélectionnez **Enregistrer le brouillon**.

## <a name="add-email-addresses-using-a-csv-file"></a>Ajouter des adresses e-mail à l’aide d’un fichier CSV

1. Sur la page **Public de préversion**, sélectionnez le lien **Exporter le public (csv)** .
2. Ouvrez le fichier CSV. Dans la colonne **ID**, entrez les ID d’abonnement Azure que vous souhaitez ajouter au public de préversion.
3. Dans la colonne **Description**, vous avez la possibilité d’ajouter une description pour chaque entrée.
4. Dans la colonne **Type**, ajoutez **SubscriptionId** à chaque ligne ayant un ID.
5. Enregistrez le fichier au format CSV.
6. Sur la page **Public de préversion**, sélectionnez le lien **Importer le public (csv)** .
7. Dans la boîte de dialogue **Confirmer**, sélectionnez **Oui**, puis chargez le fichier CSV.

Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante, **Vue d’ensemble du plan**.

## <a name="next-steps"></a>Étapes suivantes

* [Créer des plans](create-managed-service-offer-plans.md)
