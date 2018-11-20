---
title: Fichier Include
description: Fichier Include
services: digital-twins
author: alinamstanciu
ms.service: digital-twins
ms.topic: include
ms.date: 11/13/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: 216e2db82d5a07bd8e4cae8b9f357ac7dcee330a
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51626400"
---
1. Dans le [portail Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de gauche, puis le volet **Propriétés**. Copiez l’**ID de répertoire** dans un fichier temporaire. Cette valeur vous permettra de configurer un exemple d’application dans la section suivante.

    ![ID d’annuaire Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Ouvrez le volet **Inscriptions d’applications** et sélectionnez le bouton **Nouvelle inscription d’application**.

    ![Volet des inscriptions d’applications](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Dans la zone **Nom**, donnez un nom convivial à l’inscription de cette application. Choisissez **Type d’application** comme **Natif** et **URI de redirection** comme `https://microsoft.com`. Sélectionnez **Créer**.

    ![Volet de création](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Ouvrez l’application inscrite, puis copiez la valeur du champ **ID d’application** dans un fichier temporaire. Cette valeur identifie votre application Azure Active Directory. L’ID d’application vous permettra de configurer votre exemple d’application dans les sections suivantes.

    ![ID d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Ouvrez votre volet des inscriptions d’applications. Sélectionnez **Paramètres** > **Autorisations requises**, puis :

   a. Sélectionnez **Ajouter** en haut à gauche pour ouvrir le volet **Ajouter un accès d’API**.

   b. Sélectionnez **Sélectionner une API** et recherchez **Azure Digital Twins**. Si votre recherche ne trouve pas cette API, sélectionnez **Azure Smart Spaces** à la place.

   c. Sélectionnez l’option **Azure Digital Twins (Azure Smart Spaces Service)** et choisissez **Sélectionner**.

   d. Choisissez **Sélectionner des autorisations**. Cochez la case des autorisations déléguées **Accès en lecture/écriture** et choisissez **Sélectionner**.

   e. Sélectionnez **Terminé** dans le volet **Ajouter un accès d’API**.

   f. Dans le volet **Autorisations requises**, sélectionnez le bouton **Accorder des autorisations**, puis acceptez l’accusé réception qui s’affiche.

      ![Volet des autorisations requises](./media/digital-twins-permissions/aad-app-req-permissions.png)
