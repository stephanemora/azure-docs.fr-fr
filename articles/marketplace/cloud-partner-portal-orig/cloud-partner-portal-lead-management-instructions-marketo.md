---
title: Marketo | Microsoft Docs
description: Configurez la gestion des prospects pour Marketo.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 482d7a9662a79503bb2b197d5a6c63c9fa3c1c96
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58113110"
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
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>

1. Copiez les valeurs visibles dans le code incorporé de façon à configurer **Server Id**, **Munchkin Id** et **Form Id** de Marketo sur le Portail Microsoft Cloud Partner.

Utilisez l’exemple suivant comme guide pour obtenir les identifiants dont vous avez besoin dans l’exemple de code incorporé Marketo.

- Server Id = **ys12**
- Munchkin Id = **123-PQR-789**
- Form Id = **1179**\
