---
title: Application de classifications aux ressources
description: Ce document explique comment appliquer des classifications à des ressources.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8ec7d456c5bb2eb78c9e1729e38d918c6ac222ee
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210881"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Application de classifications aux ressources dans Azure Purview

Cet article explique comment appliquer des classifications à des ressources.

## <a name="introduction"></a>Introduction

Les classifications peuvent être de type système ou personnalisé. Les classifications système sont présentes par défaut dans Purview. Il est possible de créer des classifications personnalisées suivant un motif d’expression régulière. Les classifications peuvent être appliquées automatiquement ou manuellement aux ressources.

Ce document explique comment appliquer des classifications à des données.

## <a name="prerequisites"></a>Prérequis

- Créez des classifications personnalisées en fonction de vos besoins.
- Configurez l’analyse sur vos sources de données.

## <a name="apply-classifications"></a>Appliquer des classifications
Dans Azure Purview, vous pouvez appliquer des classifications système et personnalisées sur une ressource de type fichier, table ou colonne. Cet article décrit la procédure à suivre pour appliquer manuellement des classifications à des ressources.

### <a name="apply-classification-to-a-file-asset"></a>Application d’une classification à une ressource de type fichier
Azure Purview peut analyser et classer automatiquement des fichiers de documentation. Prenons par exemple un fichier nommé *multiple.docx* contenant un numéro d’identification national : Azure Purview ajoute la classification **Numéro d’identification national européen** à la page de détails de la ressource de type fichier.

Il peut être utile, dans certains scénarios, d’ajouter manuellement des classifications à une ressource de type fichier. Si plusieurs fichiers sont regroupés dans un jeu de ressources, ajoutez des classifications au niveau de ce jeu de ressources.

Procédez comme suit pour ajouter une classification personnalisée ou système à un jeu de ressources de partition :

1. Effectuez une recherche ou parcourez la partition pour accéder à la page de détails de la ressource.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="Capture d’écran montrant la page de détails de la ressource":::

1. Sous l’onglet **Vue d’ensemble**, consultez la section **Classifications** pour voir s’il existe déjà des classifications. Sélectionnez **Edit** (Modifier).

1. Dans la liste déroulante **Classifications**, sélectionnez les classifications qui vous intéressent, par exemple **Credit Card Number** (Numéro de carte de crédit), une classification système, et **CustomerAccountID** (IDCompteClient), une classification personnalisée.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="Capture d’écran montrant comment sélectionner des classifications à ajouter à une ressource":::

1. Sélectionnez **Enregistrer**.

1. Dans l’onglet **Vue d’ensemble**, vérifiez que les classifications sélectionnées s’affichent dans la section **Classifications**.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="Capture d’écran montrant comment vérifier que des classifications ont été ajoutées à une ressource":::

### <a name="apply-classification-to-a-table-asset"></a>Application d’une classification à une ressource de type table

Quand Azure Purview analyse des sources de données, il n’affecte pas automatiquement de classifications aux ressources de type table. Si vous souhaitez que votre ressource de type table comporte une classification, vous devez l’ajouter manuellement.

Pour ajouter une classification à une ressource de type table, procédez comme suit :

1. Recherchez une ressource de type table qui vous intéresse, par exemple la table **Customer** (Client).

1. Vérifiez qu’aucune classification n’est affectée à la table. Sélectionnez **Modifier**

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="Capture d’écran montrant comment afficher et modifier les classifications d’une ressource de type table":::

1. Dans la liste déroulante **Classifications**, sélectionnez une ou plusieurs classifications. Il s’agit dans cet exemple d’une classification personnalisée nommée **CustomerInfo** (InfosClient), mais vous pouvez sélectionner n’importe quelles classifications à cette étape.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="Capture d’écran montrant comment sélectionner des classifications à ajouter à une ressource de type table":::

1. Sélectionnez **Enregistrer** pour enregistrer les classifications.

1. Sur la page **Vue d’ensemble**, vérifiez qu’Azure Purview a ajouté vos nouvelles classifications.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="Capture d’écran montrant comment vérifier que des classifications ont été ajoutées à une ressource de type table":::

### <a name="add-classification-to-a-column-asset"></a>Ajout d’une classification à une ressource de type colonne

Azure Purview analyse et ajoute automatiquement des classifications à toutes les ressources de type colonne. Il est toutefois possible de modifier la classification au niveau de la colonne.

Pour ajouter une classification à une colonne, procédez comme suit :

1. Recherchez et sélectionnez la ressource de type colonne, puis sélectionnez **Modifier** dans l’onglet **Vue d’ensemble**.

1. Sélectionnez l’onglet **Schéma**.

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="Capture d’écran montrant comment modifier le schéma d’une colonne":::

1. Identifiez les colonnes qui vous intéressent et sélectionnez **Ajouter une classification**. Dans cet exemple est ajoutée une classification **Common Passwords** (Mots de passe courants) à la colonne **PasswordHash** (HachageMotdepasse).

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="Capture d’écran montrant comment ajouter une classification à une colonne":::

1. Sélectionnez **Enregistrer**.

1. Sélectionnez l’onglet **Schéma** et vérifiez que la classification a été ajoutée à la colonne.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="Capture d’écran montrant comment vérifier qu’une classification a été ajoutée à un schéma de colonne":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>Impact d’une nouvelle analyse sur des classifications existantes

Les classifications sont appliquées la première fois, en vérifiant le jeu de données d’exemple et en le comparant avec le modèle regex défini. Au moment de la nouvelle analyse, si de nouvelles classifications s’appliquent, la colonne reçoit ces classifications supplémentaires. Les classifications existantes restent et doivent être supprimées manuellement.

## <a name="next-steps"></a>Étapes suivantes
Pour savoir comment créer une classification personnalisée, consultez [Création d’une classification personnalisée](create-a-custom-classification-and-classification-rule.md).