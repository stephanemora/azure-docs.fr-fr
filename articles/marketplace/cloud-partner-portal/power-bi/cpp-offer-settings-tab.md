---
title: Paramètres d’une offre d’application Power BI | Place de marché Azure
description: Configurez les paramètres d’une offre d’application Power BI pour la Place de marché Microsoft AppSource.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: ddedc15b41b2b163f85f5443275daf9fbb56bac8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143026"
---
# <a name="power-bi-apps-offer-settings-tab"></a>Onglet des paramètres de l'offre d'application Power BI

>[!Important]
>À partir du 13 avril 2020, nous commencerons à déplacer la gestion de vos offres d’applications Power BI vers l’Espace partenaires. Après la migration, vous créerez et gérerez vos offres dans l’Espace partenaires. Suivez les instructions indiquées dans [Vue d’ensemble de la création d’applications Power BI](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-power-bi-app-offer) pour gérer vos offres migrées.

Lorsque vous ouvrez la page **Nouvelle offre** pour les applications de service, vous voyez tout d’abord l’onglet **Paramètres de l’offre**. Sous cet onglet, vous devez fournir les principaux identificateurs et le nom de votre offre. Un astérisque (*) indique un champ obligatoire.

![Onglet des paramètres de l’offre](./media/offer-settings-tab.png)


## <a name="offer-settings-fields"></a>Champs Paramètres de l’offre 

Sous l’onglet **Paramètres de l’offre**, vous devez renseigner les champs obligatoires suivants. Les champs obligatoires sont indiqués par un astérisque (*).

|  Champ        |  Description                                                               |
|---------------|----------------------------------------------------------------------------|
| **ID de l’offre\***  | Un identificateur unique (avec un profil d’éditeur) pour l’offre. Cet identificateur est visible dans les URL de produits, les modèles Azure Resource Manager et les rapports de facturation. La longueur maximale est de 50 caractères. Seuls les caractères alphanumériques en minuscules et les tirets (-) sont autorisés. Il ne peut pas se terminer par un tiret. Cet identificateur ne peut pas être modifié après la mise en ligne d’une offre. Si Contoso publie une offre avec l’ID d’offre `sample-SvcApp`, l’URL AppSource `https://appsource.microsoft.com/marketplace/apps/contoso.sample-SvcApp` lui est attribuée.      |
| **Éditeur\*** | Identificateur unique de votre organisation dans [AppSource](https://appsource.microsoft.com). Votre ID d’éditeur doit être associé à toutes vos offres. Cette valeur ne peut pas être changée après l’enregistrement de l’offre.                         |
| **Nom\***      | Nom d’affichage de votre offre. Ce nom apparaît dans AppSource et sur le Portail Cloud Partner. La longueur maximale est de 50 caractères. Utilisez un nom de marque reconnaissable pour votre produit. N’indiquez pas le nom de votre organisation, sauf s’il s’agit du nom sous lequel l’application est commercialisée. Si vous proposez cette offre via d’autres sites web et publications, utilisez le même nom dans toutes les publications.    <br/>Si vous publiez une offre pendant la période de préversion de Power BI Apps, ajoutez la chaîne `(Preview)` à la fin du nom de votre application, comme ceci : `Sample Scv App (Preview)`. |
|     |     |


## <a name="next-steps"></a>Étapes suivantes

Dans l’onglet suivant, vous allez spécifier les [Informations techniques](./cpp-technical-info-tab.md) de votre offre.
