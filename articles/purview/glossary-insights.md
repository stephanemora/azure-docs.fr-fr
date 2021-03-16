---
title: Génération de rapports sur vos données à l’aide de Purview Insights
description: Ce guide pratique explique comment afficher et utiliser les rapports Purview d’insights sur les glossaires en lien avec vos données.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: eb1d59ae41b04be60dec90aaee4b2305b6d39ca6
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095848"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Insights sur les glossaires de vos données dans Azure Purview

Ce guide pratique explique comment accéder aux rapports d’insights relatifs aux glossaires Azure Purview en lien avec vos données, ainsi que comment les afficher et les filtrer.

Dans ce guide pratique, vous allez apprendre à effectuer les opérations suivantes :

> [!div class="checklist"]
> - Accéder à des insights à partir de votre compte Purview
> - Obtenir une vue globale de vos données

## <a name="prerequisites"></a>Prérequis

Avant de commencer à utiliser les insights Purview, assurez-vous d’avoir effectué les étapes suivantes :

- Configurer vos ressources Azure et renseigner le compte avec des données

- Configurer et effectuer une analyse sur le type de source

- Configurer un glossaire et attacher des ressources aux termes de glossaire

Pour plus d’informations, consultez [Gérer des sources de données dans Azure Purview (préversion)](manage-data-sources.md).

## <a name="use-purview-glossary-insights"></a>Utiliser des insights relatifs aux glossaires Purview

Dans Azure Purview, vous pouvez créer des termes de glossaire et les attacher à des ressources. Plus tard, vous pouvez afficher la répartition du glossaire dans les insights relatifs au glossaire. Cela vous indique l’état de votre glossaire par des termes attachés aux ressources. Cela vous indique également les termes par état et par distribution des rôles en fonction du nombre d’utilisateurs.

**Pour afficher des insights relatifs aux glossaires** :

1. Accédez à l’écran de l’instance **Azure Purview** [dans le Portail Azure](https://aka.ms/purviewportal) et sélectionnez votre compte Purview.

1. Sur la page **Vue d’ensemble**, dans la section **Démarrer**, sélectionnez la vignette **Lancer le compte Purview**.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Lancer Purview à partir du Portail Azure":::

1. Dans la page d’**accueil** de Purview, sélectionnez la vignette **View insights (Afficher les insights)** pour accéder à la zone **Insights** :::image type="icon" source="media/glossary-insights/ico-insights.png" border="false":::.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Afficher vos insights dans le Portail Azure":::

1. Dans la zone **Insights**:::image type="icon" source="media/glossary-insights/ico-insights.png" border="false":::, sélectionnez **Glossaire** pour afficher le rapport Purview **Insights relatifs aux glossaires**.

En votre qualité d’utilisateur professionnel, les **Insights relatifs aux glossaires** vous fournissent des informations précieuses pour conserver un glossaire bien défini pour votre organisation.

1. Le rapport commence par des **indicateurs de performance clé généraux** présentant le **_total des termes_ *_ dans votre compte Purview, les_* _Termes approuvés sans ressources_ *_ et les _* _Termes expirés avec ressources_**. Chacune de ces valeurs vous aide à identifier l’intégrité de votre glossaire.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="Indicateur de performance clé des insights relatifs aux glossaires"::: 


2. La section **Instantané des termes** (affichée ci-dessus) présente l’état des termes, tel que **_Brouillon_ *_, _* _Approuvé_ *_, _* _Alerte_ *_ et _* _Expiré_** pour les termes avec et sans ressources.

3. Cliquez sur **Afficher plus** pour afficher les noms de termes avec différents états et des détails supplémentaires sur les **_Administrateurs_ *_ et les _* _Experts_**. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="Capture instantanée de termes avec et sans ressources":::  

4. Lorsque vous cliquez sur « Afficher plus » pour ***Termes approuvés avec ressources** _, les Insights vous permettent d’accéder à la page de détail de terme _ *Glossaire**, à partir de laquelle vous pouvez accéder à la liste des ressources avec les termes attachés. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="Insights relatifs aux glossaires"::: 

4. Dans la page Insights relatifs aux glossaires, affichez une répartition des **Termes incomplets** par type d’information manquante. Le graphique affiche le nombre de termes avec les champs **_Définition manquante_ *_, _* _Expert manquant_ *_, _* _Administrateur manquant_ *_ et _* _Multiples manquants_**.

1. Cliquez sur ***Afficher plus** _ dans _ * Termes incomplets** pour afficher les termes pour lesquels des informations sont manquantes. Vous pouvez accéder à la page de détail de terme de Glossaire pour entrer les informations manquantes et vérifier que le terme est complet.

## <a name="next-steps"></a>Étapes suivantes

En savoir plus sur la création d’un terme de glossaire via le [Glossaire](./how-to-create-import-export-glossary.md)