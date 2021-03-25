---
title: Inviter des utilisateurs à Video Indexer – Azure
titleSuffix: Azure Media Services
description: Cet article explique comment inviter des utilisateurs à Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: quickstart
ms.date: 02/03/2021
ms.author: juliako
ms.openlocfilehash: 7d3415ffad4820d8651841398ec6d47352869ce2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100534737"
---
# <a name="quickstart-invite-users-to-video-indexer"></a>Démarrage rapide : Inviter des utilisateurs à Video Indexer

Pour collaborer avec vos collègues, vous pouvez les inviter à votre compte Video Indexer. 

> [!NOTE]
> Seul l’administrateur du compte peut ajouter ou supprimer des utilisateurs.

## <a name="invite-new-users"></a>Inviter de nouveaux utilisateurs

1. Connectez-vous au site web [Video Indexer](https://www.videoindexer.ai/). Vérifiez que vous êtes connecté avec un compte d’administrateur.
1. Si vous êtes l’administrateur, vous voyez le bouton **Partager le compte** en haut à droite. Cliquez sur le bouton et vous pouvez inviter des utilisateurs. 

    :::image type="content" source="./media/invite-users/share-account.png" alt-text="Partager votre compte":::
1. Dans la boîte de dialogue **Partager ce compte avec d’autres utilisateurs**, entrez l’adresses e-mail d’une personne que vous voulez inviter à votre compte Video indexer :

    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Inviter des utilisateurs à ce compte":::  
1. Une fois que vous avez appuyé sur **Inviter**, la personne est ajoutée à la liste des invitations en attente. <br/>Vous avez le choix entre deux options pour chaque invité qui n’a pas encore rejoint le compte : **supprimer l’invitation** ou **copier l’URL de l’invitation**.

    :::image type="content" source="./media/invite-users/invites-pending.png" alt-text="Invitations en attente":::  
1. Une fois que l’invité rejoint le compte, vous voyez trois options parmi lesquelles choisir. Deux options pour les rôles : **contributeur** (la valeur par défaut) ou **propriétaire**. Vous pouvez aussi choisir de supprimer l’invité en appuyant sur **Supprimer**.

    :::image type="content" source="./media/invite-users/joined-invitee-options.png" alt-text="Invité joint":::  

    Les utilisateurs supprimés ne reçoivent pas de notification. Une fois supprimés, il ne sont plus autorisés à se connecter.

## <a name="manage-roles-invite-more-users"></a>Gérer les rôles, inviter plus d’utilisateurs

En plus d’afficher la boîte de dialogue **Partager ce compte avec d’autres utilisateurs** en cliquant sur **Partager le compte** (comme décrit ci-dessus), vous pouvez le faire à partir de **Paramètres**.

1. Cliquez sur le bouton **Paramètres** dans le compte ouvert. 

    :::image type="content" source="./media/invite-users/settings.png" alt-text="Paramètres de compte":::  
1. Cliquez sur le bouton **Gérer les rôles**.
1. Pour inviter un autre utilisateur, cliquez sur **Inviter d’autres personnes à ce compte**.

    :::image type="content" source="./media/invite-users/invite-more-people.png" alt-text="INVITE MORE USERS":::  
1. Une fois que vous avez cliqué sur **Inviter d’autres personnes à ce compte**, la boîte de dialogue d’invite s’affiche.
 
    :::image type="content" source="./media/invite-users/share-account-others.png" alt-text="Inviter des utilisateurs à ce compte":::  

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez désormais utiliser le [site web Video Indexer](video-indexer-view-edit.md) ou [Video Indexer Developer Portal](video-indexer-use-apis.md) pour afficher les insights de la vidéo.

## <a name="see-also"></a>Voir aussi

- [Présentation de Video Indexer](video-indexer-overview.md)
- [Comment s’inscrire et charger votre première vidéo](video-indexer-get-started.md)
- [Commencer à utiliser des API](video-indexer-use-apis.md)
