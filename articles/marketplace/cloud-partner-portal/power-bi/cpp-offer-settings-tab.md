---
title: Paramètres pour une offre d’application Power BI de l’offre | Place de marché Azure
description: Configurer les paramètres de l’offre pour une offre d’application Power BI pour la place de marché Microsoft AppSource.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: be2c2b4f5d9461aa0fdc6dde89931ed4b6418ced
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943463"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Onglet des paramètres de l'offre d'application Power BI

Lorsque vous ouvrez le **nouvelle offre** page pour les applications de service, vous voyez tout d’abord le **paramètres de l’offre** onglet. Vous fournir les identificateurs de principales et le nom de votre offre sur cet onglet. Un astérisque (*) indique un champ obligatoire.

![Onglet des paramètres de l’offre](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Champs Paramètres de l’offre 

Sur le **paramètres de l’offre** onglet, vous devez entrer des informations dans les champs requis suivants. Les champs obligatoires sont indiqués par un astérisque (*).

|  Champ        |  Description                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID de l’offre\***  | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. La longueur maximale est de 50 caractères. Il peut contenir uniquement des caractères alphanumériques minuscules et des tirets (-). Il ne peut pas se terminer par un tiret. Cet identificateur ne peut pas être modifié après la fin d’une offre en direct. ID de l’offre si Contoso publie une offre avec `sample-SvcApp`, l’offre est affectée à l’URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp`.      |
| **Publisher\*** | Identificateur unique de votre organisation dans [AppSource](https://appsource.microsoft.com). Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être changée après l’enregistrement de l’offre.                         |
| **Nom\***      | Nom d’affichage pour votre offre. Ce nom apparaît sur AppSource et sur le portail Cloud Partner. La longueur maximale est de 50 caractères. Utilisez un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre organisation, sauf si l’application est commercialisée portant ce nom. Si vous fournissez cette offre sur d’autres sites Web et les publications, utilisez le même nom dans toutes les publications.    <br/>Si vous libérez une offre pendant la période d’évaluation pour les applications Power BI, ajoutez la chaîne `(Preview)` à la fin du nom de votre application, comme suit : `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Étapes suivantes

Sur l’onglet suivant, vous allez spécifier [informations techniques](./cpp-technical-info-tab.md) pour votre offre.
