---
title: Guide pratique de surveillance d’Azure Data Share en préversion
description: Guide pratique de surveillance d’Azure Data Share en préversion
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 869c1ed41d7f78df184461bc1d8cab6c6eb8d426
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/10/2019
ms.locfileid: "67788520"
---
# <a name="monitor-azure-data-share-preview"></a>Surveiller Azure Data Share en préversion 

Cet article explique comment surveiller vos partages de données à l’aide d’Azure Data Share en préversion. En tant que fournisseur de données, vous êtes en mesure de surveiller divers aspects de vos relations de partage de données. Vous pouvez surveiller divers détails, par exemple pour savoir si vos consommateurs de données ont accepté votre invitation de partage de données, ainsi que s’ils ont créé un abonnement de partage et commencé à utiliser vos données. 

En tant que consommateur de données, vous pouvez surveiller les captures instantanées qui ont été déclenchées dans votre abonnement Azure. 

## <a name="monitor-invitation-status"></a>Surveiller l’état d’invitation

Affichez l’état de vos invitations de partage de données en accédant à Partages envoyés -> Invitations. 

![État de l’invitation](./media/invitation-status.png "État de l’invitation") 

Il existe trois états que votre invitation peut avoir :

* En attente - Le destinataire du partage de données n'a pas encore accepté l’invitation.
* Accepté - Le destinataire du partage de données a accepté l’invitation.
* Rejeté - Le destinataire du partage de données a rejeté l’invitation.

> [!IMPORTANT]
> Si vous supprimez une invitation après qu’elle a déjà été acceptée, cela ne revient pas à révoquer l’accès. Si vous souhaitez empêcher la copie des instantanés futurs dans votre compte de stockage de consommateurs de données, vous devez révoquer l’accès via l’onglet *Abonnements de partage*. 

## <a name="monitor-share-subscriptions"></a>Surveiller les abonnements de partage

Affichez l’état de vos abonnements de partage en accédant à Partages envoyés -> Abonnements de partage. Cela vous donnera plus d’informations sur les abonnements actifs créés par les consommateurs de données après avoir accepté votre invitation. Vous pouvez empêcher les futures mises à jour à votre consommateur de données en sélectionnant l’abonnement de partage, puis *Révoquer*. 

## <a name="snapshot-history"></a>Historique des instantanés 

Dans l’onglet Historique, vous pouvez afficher les instantanés qui ont été copiés dans votre locataire de consommateur de données. Vous êtes en mesure de surveiller la fréquence et la durée de chaque intervalle d’instantané. 

![Historique des instantanés](./media/sent-shares.png "Historique des instantanés") 

Vous pouvez afficher plus de détails sur chaque exécution d’instantané en cliquant sur la date de début de l’exécution. 

## <a name="next-steps"></a>Étapes suivantes 

En savoir plus sur la [terminologie propre à Azure Data Share](terminology.md)