---
title: Révoquer un abonnement de partage dans Azure Data Share
description: Découvrez comment révoquer un abonnement de partage d’un destinataire à l’aide d’Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 97f672b266c2df6956282aea6dec8d5207944943
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73476373"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share"></a>Comment révoquer l’abonnement de partage d’un consommateur dans Azure Data Share

Cet article explique comment révoquer un abonnement de partage à partir d’un ou de plusieurs de vos consommateurs à l’aide d’Azure Data Share. Cette révocation empêche un consommateur de déclencher d’autres instantanés. Si le consommateur concerné par la révocation n’a pas encore déclenché d’instantané, il ne recevra jamais les données une fois l’abonnement de partage révoqué. S’il a déjà déclenché un instantané, les données les plus récentes qu’il obtient seront conservées dans son compte.

## <a name="navigate-to-a-sent-data-share"></a>Accéder à un partage de données envoyé

Dans Azure Data Share, accédez à votre partage envoyé et sélectionnez l’onglet **Abonnements de partage**.

![Révoquer un abonnement de partage](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Cochez les cases en regard des destinataires dont vous souhaitez supprimer les abonnements de partage, puis cliquez sur **Révoquer**. Le consommateur ne recevra plus les mises à jour pour ses données.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [surveiller vos partages de données](how-to-monitor.md).