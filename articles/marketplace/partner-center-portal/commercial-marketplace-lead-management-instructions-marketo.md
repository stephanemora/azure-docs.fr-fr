---
title: Configurer la gestion des prospects dans Marketo | Place de marché Azure
description: Configurez la gestion des prospects dans Marketo pour les clients de la place de marché Azure.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: 35d57d117f6308863965ffd789c0e28bedd0f301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80281508"
---
# <a name="configure-lead-management-in-marketo"></a>Configurer la gestion des prospects dans Marketo

Cet article explique comment configurer votre système CRM Marketo de manière à traiter les prospects issus de votre offre de la Place de marché.

## <a name="set-up-your-marketo-crm-system"></a>Configurer votre système CRM Marketo

1. Connectez-vous à Marketo.
2. Sélectionnez **Design Studio**.
    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

3.  Sélectionnez **Nouveau formulaire**.
    ![Nouveau formulaire Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

4.  Renseignez les champs requis dans le nouveau formulaire, puis sélectionnez **Créer**.
    ![Nouveau formulaire de création Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

5.  Dans Détails du champ, sélectionnez **Terminer**.
    ![Formulaire d’achèvement Marketo](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

6.  Approuvez et fermez.

7. Sous l'onglet *MarketplaceLeadBacked*, sélectionnez **Code incorporé**. 

    ![Code incorporé](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

8. Le code incorporé Marketo affiche du code qui ressemble à l’exemple suivant.

    ```
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

9. Copiez les valeurs des champs ci-dessous affichés dans le formulaire Code incorporé. Vous utiliserez ces valeurs pour configurer votre offre afin de recevoir des prospects lors de l'étape suivante. Utilisez l’exemple suivant comme guide pour obtenir les identifiants dont vous avez besoin dans l’exemple de code incorporé Marketo.

    - Server ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - Form ID = **1179**

    **Autre façon d'interpréter ces valeurs**

    - L'ID du serveur se trouve dans l'URL de votre instance Marketo, par exemple, « `serverID.marketo.com` ».
    - Procurez-vous l’ID Munching de votre abonnement en accédant au menu Admin > Munchkin, champ « ID du compte Munchkin », ou en consultant la première partie du sous-domaine hôte de l’API REST Marketo : `https://{Munchkin ID}.mktorest.com`.
    - L'ID du formulaire correspond à l'ID du formulaire Code incorporé que vous avez créé à l'étape 7 pour diriger les prospects depuis notre place de marché.

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>Configurer votre offre pour envoyer des prospects à Marketo

Lorsque vous êtes prêt à configurer les informations de gestion des prospects pour votre offre sur le portail de publication, procédez comme suit : 

1. Accédez à la page **Configuration de l'offre** de votre offre.
1. Sélectionnez **Connexion** dans la section Gestion des prospects. 

    ![Gestion des prospects - Connexion](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. Dans la fenêtre indépendante Détails de la connexion, sélectionnez **Marketo** comme Destination du prospect.

    ![Choisir une destination de prospect](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

4. Fournissez l'**ID du serveur**, l'**ID du compte Munching** et l'**ID du formulaire**.

    >[!Note]
    >Vous devez terminer la configuration du reste de l'offre et la publier avant de pouvoir recevoir des prospects pour cette offre. 

