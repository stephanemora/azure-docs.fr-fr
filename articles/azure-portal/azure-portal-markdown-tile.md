---
title: Utiliser une vignette Markdown personnalisée sur les tableaux de bord Azure
description: Découvrez comment ajouter une vignette Markdown à un tableau de bord Azure pour afficher un contenu statique
services: azure-portal
keywords: ''
author: mblythe
ms.author: mblythe
ms.date: 01/25/2019
ms.topic: conceptual
ms.service: azure-portal
manager: mtillman
ms.openlocfilehash: 3d2e6d2d0bde76a35a18373fabf64ce36c6c320e
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75640140"
---
# <a name="use-a-markdown-tile-on-azure-dashboards-to-show-custom-content"></a>Utiliser une vignette Markdown sur les tableaux de bord Azure pour afficher un contenu personnalisé

Vous pouvez ajouter une vignette Markdown à vos tableaux de bord Azure pour afficher un contenu statique personnalisé. Par exemple, vous pouvez afficher des instructions de base, une image ou un ensemble de liens hypertexte avec une vignette Markdown.

## <a name="add-a-markdown-tile-to-your-dashboard"></a>Ajouter une vignette Markdown à votre tableau de bord

1. Sélectionnez **Tableau de bord** à partir de la barre latérale du portail Azure. Si vous avez créé des tableaux de bord personnalisés, dans la vue de tableau de bord, utilisez la liste déroulante pour sélectionner le tableau de bord où doit apparaître la vignette Markdown personnalisée. Sélectionnez l’icône Modifier pour ouvrir la **Galerie de vignettes**.

   ![Capture d’écran montrant la vue de tableau de bord en mode modification](./media/azure-portal-markdown-tile/azure-portal-dashboard-edit.png)

2. Dans la **Galerie de vignettes**, recherchez la vignette appelée **Markdown** et cliquez sur **Ajouter**. La vignette est ajoutée au tableau de bord et le volet **Modifier le balisage Markdown** s’ouvre.

1. Modifiez les champs **Titre**, **Sous-titre** et **Contenu** afin de personnaliser la vignette. Dans l’exemple présenté ici, la vignette Markdown a été modifiée pour afficher des informations de support technique personnalisées.

   ![Capture d’écran montrant la vue d’édition de la vignette Markdown](./media/azure-portal-markdown-tile/azure-portal-edit-markdown-tile.png)

4. Sélectionnez **Terminé** pour faire disparaître le volet **Modifier le balisage Markdown**. Votre contenu s’affiche sur la vignette Markdown, que vous pouvez ensuite redimensionner en faisant glisser la poignée dans le coin inférieur droit.

   ![Capture d’écran montrant la vignette Markdown personnalisée](./media/azure-portal-markdown-tile/azure-portal-custom-markdown-tile.png)

## <a name="markdown-content-capabilities-and-limitations"></a>Limitations et fonctionnalités du contenu Markdown

Vous pouvez utiliser n’importe quelle combinaison de texte brut, syntaxe Markdown et contenu HTML sur la vignette Markdown. Le portail Azure utilise une bibliothèque open source appelée _marked_ pour transformer votre contenu en syntaxe HTML qui est affichée sur la vignette. La syntaxe HTML générée par _marked_ est prétraitée par le portail avant d’être affichée. Ainsi, votre personnalisation n’affecte pas la sécurité ou la disposition du portail. Pendant ce prétraitement, toute partie de la syntaxe HTML qui représente une menace potentielle est supprimée. Les types de contenu suivants ne sont pas autorisés par le portail :

* JavaScript : les balises `<script>` et les évaluations JavaScript inline sont supprimées.
* IFrames : les balises `<iframe>` sont supprimées.
* Style : les balises `<style>` sont supprimées. Les attributs de style intraligne sur les éléments HTML ne sont pas officiellement pris en charge. Certains éléments de style intraligne peuvent sembler fonctionner, mais s’ils interfèrent avec la disposition du portail, ils peuvent cesser de fonctionner à tout moment. La vignette Markdown est destinée à du contenu statique de base qui utilise les styles par défaut du portail.

## <a name="next-steps"></a>Étapes suivantes

* Pour créer un tableau de bord personnalisé, consultez [Créer et partager des tableaux de bord dans le portail Azure](../azure-portal/azure-portal-dashboards.md).
