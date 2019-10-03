---
title: Révoquer un abonnement de partage dans la préversion d’Azure Data Share
description: Révoquer un abonnement de partage
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 08a48202c26df1c24216572b1a52ac45506c6229
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326531"
---
# <a name="how-to-revoke-a-consumers-share-subscription-in-azure-data-share-preview"></a>Comment révoquer l’abonnement de partage d’un consommateur dans la préversion d’Azure Data Share

Cet article explique comment révoquer un abonnement de partage à partir pour un ou plusieurs de vos consommateurs à l’aide de la préversion d’Azure Data Share. Cette révocation empêche un consommateur de déclencher d’autres instantanés. Si le consommateur concerné par la révocation n’a pas encore déclenché d’instantané, il ne recevra jamais les données une fois l’abonnement de partage révoqué. S’il a déjà déclenché un instantané, les données les plus récentes qu’il obtient seront conservées dans son compte.

## <a name="navigate-to-a-sent-data-share"></a>Accéder à un partage de données envoyé

Dans la préversion d’Azure Data Share, accédez à votre partage envoyé et sélectionnez l’onglet **Abonnements de partage**.

![Révoquer un abonnement de partage](./media/how-to/how-to-revoke-share-subscription/revoke-share-subscription.png) 

Cochez les cases en regard des destinataires dont vous souhaitez supprimer les abonnements de partage, puis cliquez sur **Révoquer**. Le consommateur ne recevra plus les mises à jour pour ses données.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [surveiller vos partages de données](how-to-monitor.md).