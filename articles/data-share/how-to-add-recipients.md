---
title: Ajouter des destinataires dans Azure Data Share
description: Découvrez comment ajouter des destinataires à un partage de données existant dans Azure Data Share.
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: how-to
ms.date: 12/17/2020
ms.openlocfilehash: a8e3dac620873ab11ae24395310066037f6d2df4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97680629"
---
# <a name="how-to-add-a-recipient-to-your-share"></a>Ajouter un destinataire à votre partage

Vous pouvez ajouter un destinataire à un partage existant ou lorsque vous créez un partage. Dans l’interface utilisateur d’Azure Data Share, vous pouvez ajouter un destinataire à l’aide de son adresse e-mail de connexion Azure.  Dans l’API, vous pouvez combiner un utilisateur/principal de service et un ID de locataire. Lorsqu’un ID de locataire est spécifié, l’invitation ne peut être acceptée que dans ce locataire. Toujours dans l’API, vous pouvez également créer une invitation sans envoyer d’e-mail au destinataire. 

## <a name="add-recipient-to-an-existing-share"></a>Ajouter un destinataire à un partage existant

Dans Azure Data Share, accédez à votre partage envoyé et sélectionnez l’onglet **Invitations**. Les destinataires des invitations à ce partage de données sont répertoriés ici. Pour en ajouter un, cliquez sur **Ajouter un destinataire**.

![Capture d’écran de l’option Ajouter un destinataire sélectionnée](./media/how-to/how-to-add-recipients/add-recipient.png)

Un panneau s’affiche sur le côté droit de la page. Cliquez sur **Ajouter un destinataire**, puis entrez l’adresse de votre nouveau destinataire sur la ligne vide. Veillez à utiliser l’adresse e-mail de connexion Azure du destinataire (son alias de courrier ne fonctionnera pas). 

![Capture d’écran du volet Ajouter un destinataire permettant d’ajouter et d’envoyer une invitation](./media/how-to/how-to-add-recipients/add-recipient-side.png)

Cliquez sur **Valider et envoyer une invitation**. Le ou les nouveaux destinataires recevront des messages d’invitation à ce partage.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la [suppression d’une invitation à un partage](how-to-delete-invitation.md).
