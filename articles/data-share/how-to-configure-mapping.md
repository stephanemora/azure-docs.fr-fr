---
title: Configurer un mappage de jeu de données dans Azure Data Share
description: Découvrez comment configurer un mappage de jeu de données pour un partage reçu dans Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: how-to
ms.date: 07/10/2019
ms.openlocfilehash: 52a8b9c5441f9261f8b9179c8100cdb01777bfd1
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119954"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share"></a>Comment configurer un mappage de jeu de données pour un partage reçu dans Azure Data Share

Cet article explique comment configurer un mappage de jeu de données pour un partage reçu à l’aide d’Azure Data Share. Vous souhaiterez le faire si vous avez accepté une invitation de partage de données, mais que vous avez opté pour « Accepter et configurer ultérieurement », ou si les données sont partagées sur place. Vous souhaiterez peut-être configurer un mappage de jeu de données si vous devez modifier la destination des données partagées avec vous ou si vous souhaitez recevoir des données dans un SQL Server. 

## <a name="navigate-to-a-received-data-share"></a>Accéder à un partage de données reçues

Dans le service Azure Data Share, accédez à votre partage reçu et sélectionnez l’onglet **Détails**. 

![Mappage de jeu de données](./media/dataset-mapping.png "Mappage de jeu de données") 

Cochez la case en regard du jeu de données auquel vous souhaitez affecter une destination. Sélectionnez **Démapper** pour annuler le mappage existant. Sélectionnez **+ Map to target** (+ Mapper sur cible) afin de choisir un nouveau magasin de destination. 

![Mapper sur cible](./media/dataset-map-target.png "Mapper sur cible") 

## <a name="select-a-new-target-store"></a>Sélectionner un nouveau magasin cible

Sélectionnez un type de données cibles dans lequel vous souhaitez que les données arrivent. Pour les partages basés sur une capture instantanée, toutes les données existant déjà dans les comptes de stockage précédemment mappés ne sont pas automatiquement déplacées vers le nouveau magasin cible. Pour les partages sur place, sélectionnez un magasin de données à l’emplacement spécifié. L’emplacement est le centre de données Azure où se trouve le magasin de données source du fournisseur de données.

![Compte de stockage cible](./media/dataset-map-target-sql.png "Stockage cible") 

## <a name="select-a-file-format-sql-sources-only"></a>Sélectionner un format de fichier (sources SQL uniquement)

Si les données sources proviennent d’une source basée sur SQL, vous pouvez choisir le format dans lequel elles sont reçues. 

![Choisir le format](./media/sql-file-formats.png "Formats de fichiers SQL")

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).



