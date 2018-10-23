---
title: Fichier Include
description: Fichier Include
services: azure-digital-twins
author: alinamstanciu
ms.service: azure-digital-twins
ms.topic: include
ms.date: 09/19/2018
ms.author: alinast
ms.custom: include file
ms.openlocfilehash: d08b7a1256a26d1d7d39481ba15a8637339063ea
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322779"
---
1. Dans le [Portail Azure](https://portal.azure.com), ouvrez **Azure Active Directory** dans le panneau de navigation sur le côté gauche, puis ouvrez le volet **Propriétés**. Copiez l’**ID de répertoire** dans un fichier temporaire. Cette valeur vous permettra de configurer un exemple d’application dans la section suivante.

    ![ID de répertoire Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-tenant.png)

1. Ouvrez le volet **Inscriptions d’applications**, puis cliquez sur **Nouvelle inscription d’application**.
    
    ![Nouvelle inscription d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Dans le champ **Nom**, attribuez un nom convivial à l’inscription de cette application. Sélectionnez **Type d’application** comme étant **_Natif_**, et **URI de redirection** comme étant **_https://microsoft.com_**. Cliquez sur **Créer**.

    ![Créer l’inscription d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Ouvrez l’application inscrite, puis copiez la valeur du champ **ID d’application** dans un fichier temporaire. Cette valeur identifie votre application Azure Active Directory. L’ID d’application vous permettra de configurer votre exemple d’application dans les sections suivantes.

    ![Id d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Ouvrez le volet d’inscription de votre application, et puis cliquez sur **Paramètres** > **Autorisations requises** :
    - Cliquez sur **Ajouter** en haut à gauche pour ouvrir le volet **Ajouter un accès d’API**.
    - Cliquez sur **Sélectionner une API**, puis sélectionnez **Azure Digital Twins**. Si votre recherche ne trouve pas cette API, sélectionnez **Azure Smart Spaces** à la place.
    - Sélectionnez l’option **Azure Digital Twins (Azure Smart Spaces Service)**, puis cliquez sur **Sélectionner**.
    - Cliquez sur **Sélectionner les autorisations**. Cochez la case des autorisations déléguées **Accès en lecture/écriture**, puis cliquez sur **Sélectionner**.
    - Cliquez sur **Terminé** dans le volet **Ajouter un accès d’API**.
    - Dans le volet **Autorisations requises**, cliquez sur le bouton **Accorder des autorisations**, puis acceptez l’accusé réception qui s’affiche.

       ![Api d’ajout d’inscription d’application Azure Active Directory](./media/digital-twins-permissions/aad-app-req-permissions.png)
