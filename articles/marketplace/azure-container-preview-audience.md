---
title: Définissez le public de la préversion pour une offre de conteneur Azure dans Microsoft AppSource.
description: Définissez le public de la préversion pour une offre de conteneur Azure dans Microsoft AppSource.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 03/30/2021
ms.openlocfilehash: 0d8bd02e247a3db0429f227e396abbfbc7cdfd91
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/22/2021
ms.locfileid: "107892380"
---
# <a name="set-the-preview-audience-for-an-azure-container-offer"></a>Définir le public de la préversion pour une offre de conteneur Azure

Cet article explique comment configurer un public de préversion pour une offre Azure Container sur le marketplace commercial à l’aide d’Espace partenaires. Le public de préversion peut évaluer votre offre avant sa mise en ligne.

## <a name="define-a-preview-audience"></a>Définir un public de préversion

Sur la page **Public de préversion**, définissez un public limité pouvant consulter votre offre Container avant sa publication en ligne pour le public le plus large de la place de marché. Vous définissez le public de la préversion à l’aide d’ID d’abonnement Azure et d’une description facultative pour chacun. Aucun de ces champs ne peut être vu par les clients. Vous pouvez trouver votre ID d’abonnement Azure sur la page **Abonnements** du portail Azure.

Ajoutez au moins un ID d’abonnement Azure, que ce soit individuellement (jusqu’à 10) ou en chargeant un fichier CSV (jusqu’à 100) pour définir qui peut accéder à la préversion de votre offre. Si votre offre est déjà publiée, vous pouvez toujours définir un public de préversion pour tester les mises à jour de votre offre.

## <a name="add-email-addresses-manually"></a>Ajouter des adresses e-mail manuellement

1. Sur la page **Public de préversion**, ajoutez un ID d’abonnement Azure et une description facultative dans les zones fournies.
1. Pour ajouter une autre adresse e-mail, sélectionnez le lien **Ajouter ID (max 10)** .
1. Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante pour configurer les plans.

## <a name="add-email-addresses-using-a-csv-file"></a>Ajouter des adresses e-mail à l’aide d’un fichier CSV

1. Sur la page **Public de préversion**, sélectionnez le lien **Exporter le public (csv)** .
1. Ouvrez le fichier CSV. Dans la colonne **ID**, entrez les ID d’abonnement Azure que vous souhaitez ajouter au public de préversion.
1. Dans la colonne **Description**, vous avez la possibilité d’ajouter une description pour chaque entrée.
1. Dans la colonne **Type**, ajoutez **SubscriptionId** à chaque ligne ayant un ID.
1. Enregistrez le fichier au format CSV.
1. Sur la page **Public de préversion**, sélectionnez le lien **Importer le public (csv)** .
1. Dans la boîte de dialogue **Confirmer**, sélectionnez **Oui**, puis chargez le fichier CSV.
1. Sélectionnez **Enregistrer le brouillon** avant de passer à la section suivante pour configurer les plans.

> [!IMPORTANT]
> Une fois que vous avez affiché votre offre en préversion, vous devez sélectionner **Mise en service** afin de publier votre offre pour le public.

## <a name="next-steps"></a>Étapes suivantes

- [Créer et gérer les plans](azure-container-plan-overview.md)
