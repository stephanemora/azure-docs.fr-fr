---
title: Fichier Include
description: Fichier Include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/03/2019
ms.custom: include file
ms.openlocfilehash: 7e0396c032a9f3dc26b82648604624446d6ad191
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978641"
---
## <a name="azure-active-directory-legacy-registration"></a>Inscription Azure Active Directory héritée

1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de gauche, puis le volet **Propriétés**. Copiez l’**ID de répertoire** dans un fichier temporaire. Cette valeur vous permettra de configurer un exemple d’application dans la section suivante.

    [![ID d’annuaire Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png)](./media/digital-twins-permissions-legacy/aad-app-reg-tenant.png#lightbox)

1. Dans le [portail Microsoft Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de gauche, puis ouvrez le volet **Inscriptions d’applications (hérité)** . Cliquez sur le bouton **Nouvelle inscription d’application**.

1. Dans la zone **Nom**, donnez un nom convivial à l’inscription de cette application. Choisissez **Type d’application** comme **Natif** et **URI de redirection** comme `https://microsoft.com`. Sélectionnez **Create** (Créer).

    [![Volet Créer](./media/digital-twins-permissions-legacy/aad-app-reg-create.png)](./media/digital-twins-permissions-legacy/aad-app-reg-create.png#lightbox)

1.  Ouvrez l’application inscrite, puis copiez la valeur du champ **ID d’application** dans un fichier temporaire. Cette valeur identifie votre application Azure Active Directory. L’ID d’application vous permettra de configurer votre exemple d’application dans les sections suivantes.

    [![ID d’application Azure Active Directory](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png)](./media/digital-twins-permissions-legacy/aad-app-reg-app-id.png#lightbox)

1. Ouvrez votre volet des inscriptions d’applications. Sélectionnez **Paramètres** > **Autorisations requises**, puis :

   a. Sélectionnez **Ajouter** en haut à gauche pour ouvrir le volet **Ajouter un accès d’API**.

   b. Sélectionnez **Sélectionner une API** et recherchez **Azure Digital Twins**. Si votre recherche ne trouve pas cette API, sélectionnez **Azure Smart Spaces** à la place.

   c. Sélectionnez l’option **Azure Digital Twins (Azure Smart Spaces Service)** et choisissez **Sélectionner**.

   d. Choisissez **Sélectionner des autorisations**. Cochez la case des autorisations déléguées **Accès en lecture/écriture** et choisissez **Sélectionner**.

   e. Sélectionnez **Terminé** dans le volet **Ajouter un accès d’API**.

   f. Dans le volet **Autorisations requises**, sélectionnez le bouton **Accorder des autorisations**, puis acceptez l’accusé réception qui s’affiche. Si l’autorisation n’est pas accordée pour cette API, contactez l’administrateur.

      [![Volet des autorisations requises](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png)](./media/digital-twins-permissions-legacy/aad-app-req-permissions.png#lightbox)

 
