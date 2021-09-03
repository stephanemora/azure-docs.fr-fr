---
title: Gérer les comptes Azure Media Services v3
description: Pour commencer à gérer, chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. Cet article explique comment gérer les comptes Azure Media Services v3.
services: media-services
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 03/29/2021
ms.author: inhenkel
ms.openlocfilehash: cbda31a3aaaf741424db174aa6a676858b27a23c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114471209"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Gérer les comptes Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pour commencer à gérer, chiffrer, coder, analyser, gérer et diffuser en continu du contenu multimédia dans Azure, vous devez créer un compte Media Services. Lorsque vous créez un compte Media Services, vous devez indiquer le nom d’une ressource de compte de stockage Azure. Le compte de stockage spécifié est lié à votre compte Media Services. Le compte Media Services et tous les comptes de stockage associés doivent être faire partie du même abonnement Azure. Pour plus d’informations, consultez [Comptes de stockage](storage-account-concept.md).

[!INCLUDE [account creation note](./includes/note-2020-05-01-account-creation.md)]

## <a name="media-services-account-names"></a>Noms du compte Media Services

Les noms du compte Media Services doivent tous se composer de lettres en minuscules ou de chiffres, sans espaces. Sa longueur est comprise entre 3 et 24 caractères. Les noms de compte Media Services doivent être uniques au sein d’un emplacement Azure.

Lors de la suppression d’un compte Media Services, le nom du compte est réservé pour un an. Pendant une année après la suppression du compte, le nom du compte ne peut être réutilisé que dans le même emplacement Azure par l’abonnement qui contenait le compte d’origine.

Les noms du compte Media Services sont utilisés dans les noms DNS, y compris pour la livraison de clés, les événements en direct et les noms de points de terminaison de streaming. Si vous avez configuré des pare-feu ou des proxys afin d’autoriser les noms DNS Media Services, assurez-vous que ces configurations sont supprimées dans un délai d’une année après la suppression d’un compte Media Services.

## <a name="moving-a-media-services-account-between-subscriptions"></a>Déplacement d’un compte Media Services entre abonnements

Si vous devez déplacer un compte Media Services vers un nouvel abonnement, vous devez d’abord déplacer le groupe de ressources complet qui contient le compte Media Services vers le nouvel abonnement. Vous devez déplacer toutes les ressources jointes : comptes de stockage Azure, profils Azure CDN, etc. Pour plus d’informations, consultez la page [Déplacement de ressources vers un nouveau groupe de ressources ou un abonnement](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Comme avec toutes les ressources dans Azure, les déplacements de groupes de ressources peuvent prendre un certain temps.

### <a name="considerations"></a>Considérations

* Créez des sauvegardes de toutes les données de votre compte avant la migration vers un autre abonnement.
* Vous devez arrêter tous les points de terminaison de streaming et toutes les ressources de streaming en direct. Vos utilisateurs ne seront pas en mesure d’accéder à votre contenu pendant la durée du déplacement du groupe de ressources. 

> [!IMPORTANT]
> Ne démarrez pas le point de terminaison de streaming tant que le déplacement ne s’est pas terminé correctement.

### <a name="troubleshoot"></a>Dépanner

Si un compte Media Services ou un compte de stockage Azure associé devient « Déconnecté » suite au déplacement du groupe de ressources, essayez d’utiliser la rotation des clés de compte de stockage. Si la rotation des clés de compte de stockage ne résout pas l’état « Déconnecté » du compte Media Services, envoyez une nouvelle demande de support à partir du menu « Support + dépannage » dans le compte Media Services.  

## <a name="next-steps"></a>Étapes suivantes

[Créer un compte](./account-create-how-to.md)
