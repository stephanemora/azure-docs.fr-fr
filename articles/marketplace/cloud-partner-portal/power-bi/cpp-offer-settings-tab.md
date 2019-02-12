---
title: Paramètres d'une offre d'application Power BI - Place de marché Azure | Microsoft Docs
description: Configurez les paramètres d'une offre d'application Power BI pour la Place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: pbutlerm
ms.openlocfilehash: fef2455129d94913c5b51a08c04403834861bb48
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/02/2019
ms.locfileid: "55665810"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Onglet des paramètres de l'offre d'application Power BI

La page **Nouvelle offre** des applications de service s'ouvre dans le premier onglet nommé **Paramètres de l'offre**.  Dans cet onglet, vous devez fournir les identificateurs principaux et le nom de votre offre.  Un astérisque (*) en regard du nom du champ indique que ce champ est obligatoire.

![Onglet des paramètres de l’offre](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Champs Paramètres de l'offre 

Dans l’onglet **Paramètres de l’offre**, vous devez fournir les champs obligatoires suivants.

|  Champ        |  Description                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID de l’offre**  | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL de produits, les modèles Resource Manager et les rapports de facturation. Il doit contenir 50 caractères maximum, être uniquement composé de minuscules, de caractères alphanumériques et de tiret, mais ne peut pas se terminer par un tiret. Ce champ ne peut pas être modifié après la mise en ligne d’une offre. Par exemple, si Contoso publie une offre avec l'ID d'offre `sample-SvcApp`, l'URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp` lui est attribuée.      |
| **Publisher** | Identificateur unique de votre organisation dans [AppSource](https://appsource.microsoft.com). Votre ID d’éditeur doit être associé à toutes vos offres. Une fois l’offre enregistrée, cette valeur n’est pas modifiable.                         |
| **Nom**      | Nom d’affichage de votre offre. Ce nom apparaît dans AppSource et sur le Portail Cloud Partner. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N'indiquez pas le nom de votre organisation ici, sauf s'il s'agit du nom sous lequel l'offre est commercialisée. Si vous commercialisez cette offre sur d'autres sites web et dans d'autres publications, vérifiez que le nom est le même dans toutes les publications.    <br/>Si vous publiez une offre au cours de la période d'évaluation de Power BI Apps (mode préversion), ajoutez la chaîne `(Preview)` au nom de votre application, par exemple `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Étapes suivantes

Dans l'onglet suivant, vous spécifierez [Infos techniques](./cpp-technical-info-tab.md) pour votre offre.
