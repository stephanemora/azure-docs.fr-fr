---
title: Configurer un mappage de jeu de données dans Azure Data Share (préversion)
description: Découvrez comment configurer un mappage de jeu de données pour un partage reçu dans Azure Data Share (préversion).
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 035235116240bdc6de3bc689c2430fee018b202d
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169138"
---
# <a name="how-to-configure-a-dataset-mapping-for-a-received-share-in-azure-data-share-preview"></a>Comment configurer un mappage de jeu de données pour un partage reçu dans la préversion d’Azure Data Share

Cet article explique comment configurer un mappage de jeu de données pour un partage reçu dans la préversion d’Azure Data Share. Vous souhaiterez le faire si vous avez accepté une invitation de partage de données mais que vous avez opté pour « Accepter et configurer ultérieurement ». Sinon, vous souhaiterez peut-être simplement modifier le compte de stockage de destination pour vos données reçues. 

## <a name="navigate-to-a-received-data-share"></a>Accéder à un partage de données reçues

Dans le service Azure Data Share, accédez à votre partage reçu et sélectionnez l’onglet **Détails**. 

![Mappage de jeu de données](./media/dataset-mapping.png "Mappage de jeu de données") 

Cochez la case à côté du jeu de données auquel vous souhaitez affecter une destination, puis cliquez sur **+ Mapper sur la cible**. Vous devrez peut-être d’abord annuler le mappage si vous aviez déjà configuré un compte de stockage de destination et que vous souhaitez modifier le mappage sur un autre compte de stockage. 

![Mapper sur la cible](./media/dataset-map-target.png "Mapper sur la cible") 

## <a name="select-a-new-storage-account"></a>Sélectionner un nouveau compte de stockage 

Sélectionnez un compte de stockage dans lequel vous souhaitez que les données arrivent. Notez que toutes les données existant déjà dans les comptes de stockage précédemment mappés ne sont pas automatiquement déplacées vers le nouveau compte de stockage.

![Compte de stockage cible](./media/map-target.png "Stockage cible") 

## <a name="next-steps"></a>Étapes suivantes

Pour découvrir comment commencer à partager des données, passez au tutoriel [Partager vos données](share-your-data.md).



