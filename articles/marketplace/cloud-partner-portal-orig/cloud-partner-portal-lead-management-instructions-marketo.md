---
title: Configurer la gestion des prospects dans Marketo | Place de marché Azure
description: Configurez la gestion des prospects dans Marketo pour les clients de la place de marché Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: dsindona
ms.openlocfilehash: 9fa05eae2d297cbd6ae7243d191cae5a7a3f990e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288527"
---
# <a name="configure-lead-management-in-marketo"></a>Configurer la gestion des prospects dans Marketo

Cet article explique comment configurer Marketo pour gérer les prospects Microsoft.

1. Connectez-vous à Marketo.
2. Sélectionnez **Design Studio**.
    ![Marketo Design Studio](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo1.png)

3.  Sélectionnez **Nouveau formulaire**.
    ![Nouveau formulaire Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo2.png)

4.  Renseignez les champs requis dans le nouveau formulaire, puis sélectionnez **Créer**.
    ![Nouveau formulaire de création Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo3.png)

4.  Dans Détails du champ, sélectionnez **Terminer**.
    ![Formulaire d’achèvement Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo4.png)

5.  Approuvez et fermez.

6.  Sous l’onglet MarketplaceLeadBacked, sélectionnez **Code incorporé**.
    ![Option de code incorporé Marketo](./media/cloud-partner-portal-lead-management-instructions-marketo/marketo5.png)

7.  Le code incorporé Marketo affiche du code qui ressemble à l’exemple suivant.

`<script src="//app-ys12.marketo.com/js/forms2/js/forms2.min.js"></script>`

    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copiez les valeurs visibles dans le code incorporé de façon à configurer **Server Id**, **Munchkin Id** et **Form Id** de Marketo sur le Portail Microsoft Cloud Partner.

Utilisez l’exemple suivant comme guide pour obtenir les identifiants dont vous avez besoin dans l’exemple de code incorporé Marketo.

- Server Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Form Id = **1179**
