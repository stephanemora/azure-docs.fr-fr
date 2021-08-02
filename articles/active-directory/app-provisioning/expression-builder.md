---
title: Comprendre le fonctionnement du générateur d’expressions avec l’approvisionnement d’application dans Azure Active Directory
description: Comprenez le fonctionnement du générateur d’expressions avec l’approvisionnement d’application dans Azure Active Directory.
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 06/02/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 336005a94d4403d6a9e3fb125001b78a68179aa4
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111962085"
---
# <a name="understand-how-expression-builder-in-application-provisioning-works"></a>Comprendre le fonctionnement du générateur d’expressions dans l’approvisionnement d’application

Vous pouvez utiliser des [expressions](functions-for-customizing-application-data.md) pour [mapper des attributs](./customize-application-attributes.md). Auparavant, vous deviez créer ces expressions manuellement et les entrer dans la zone Expression. Le générateur d’expressions est un outil qui peut vous aider à créer des expressions.

:::image type="content" source="media/expression-builder/expression-builder.png" alt-text="Page montrant le générateur d’expressions par défaut avant la sélection d’une fonction." lightbox="media/expression-builder/expression-builder.png":::

Pour obtenir des informations de référence sur la génération d’expressions, consultez [Informations de référence sur l’écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md). 

## <a name="finding-the-expression-builder"></a>Localisation du générateur d’expressions

Dans l’approvisionnement des applications, vous utilisez des expressions pour les mappages d’attributs. Vous accédez au générateur d’expressions via la page mappage d’attributs en sélectionnant **Afficher les options avancées**, puis **Générateur d’expressions**.

:::image type="content" source="media/expression-builder/accessing-expression-builder.png" alt-text="La case à cocher pour afficher les paramètres avancés est activée, et un lien indiquant le générateur d’expressions s’affiche" lightbox="media/expression-builder/accessing-expression-builder.png":::

## <a name="using-expression-builder"></a>Utilisation du générateur d’expressions

Pour utiliser le générateur d’expressions, sélectionnez une fonction et un attribut, puis entrez un suffixe si nécessaire. Sélectionnez ensuite **Ajouter une expression** pour ajouter l’expression à la zone de code. Pour en savoir plus sur les fonctions disponibles et leur utilisation, consultez [Informations de référence sur l’écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md).

Testez l’expression en recherchant un utilisateur ou en entrant des valeurs, puis en sélectionnant **Tester l’expression**. Le résultat du test d’expression s’affiche dans la zone **Afficher la sortie de l’expression**.

Lorsque vous êtes satisfait de l’expression, déplacez-la vers un mappage d’attributs. Copiez et collez-la dans la zone expression pour le mappage d’attributs sur lequel vous travaillez.

## <a name="known-limitations"></a>Limites connues
* Vous ne pouvez pas sélectionner d’attributs d’extension dans le générateur d’expressions. En revanche, vous pouvez utiliser des attributs d’extension dans l’expression de mappage d’attribut. 

## <a name="next-steps"></a>Étapes suivantes

[Informations de référence sur l’écriture d’expressions pour les mappages d’attributs dans Azure Active Directory](functions-for-customizing-application-data.md)