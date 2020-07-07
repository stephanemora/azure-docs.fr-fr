---
title: Utiliser Visual Studio Code pour créer des modèles
description: Découvrez comment installer et utiliser Visual Studio Code et l’extension Azure Resource Manager Tools.
author: mumian
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: jgao
ms.openlocfilehash: 585e2773ea5d6af184f85e65b63d39b60d632146
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85847619"
---
# <a name="use-visual-studio-code-to-create-azure-resource-manager-templates"></a>Utiliser Visual Studio Code pour créer des modèles Azure Resource Manager

Visual Studio Code est un éditeur léger, multiplateforme et open source. L’extension Azure Resource Manager Template Tools est un plug-in pour le développement de modèles Resource Manager. L’extension ajoute la prise en charge linguistique des modèles pour vous fournir IntelliSense, la coloration syntaxique, l’aide en ligne et de nombreuses autres fonctions de langage. Ensemble, ils procurent l’expérience de développement de modèle recommandée.

## <a name="install-visual-studio-code"></a>Installation de Visual Studio Code

Visual Studio Code prend en charge MacOS, Windows et Linux.  Il peut être installé à partir de [Visual Studio Code](https://code.visualstudio.com/).

## <a name="install-resource-manager-tools-extension"></a>Installer l’extension Resource Manager Tools

1. Ouvrez Visual Studio Code.
1. Sélectionnez **Extensions** dans le menu de gauche. Ou, dans le menu **Affichage**, sélectionnez **Extensions** pour ouvrir le volet Extensions.

    ![Installer l’extension Visual Studio Code Resource Manager Tools](./media/use-vs-code-to-create-template/resource-manager-visual-studio-code-tools-extension.png)
1. Recherchez **Resource Manager**.
1. Sélectionnez **Installer** sous **Azure Resource Manager Tools**.

## <a name="the-extension-features"></a>Fonctionnalités de l’extension

### <a name="colorization-for-template-language-expressions"></a>Colorisation pour les expressions de langage de modèle

Les paramètres, les variables, les fonctions, les noms et les expressions sont codés par couleur, comme indiqué dans la capture d’écran suivante :

![Extension Visual Studio Code Resource Manager Tools : colorisation](./media/use-vs-code-to-create-template/resource-manager-tools-extension-colorization.png)

La vue de plan de modèle facilite la navigation dans les grands modèles.

### <a name="intellisense"></a>Intellisense

L’extension de modèle Resource Manager connaît les saisies semi-automatiques possibles pour les noms de fonction, les paramètres, les variables et les références. Les suggestions IntelliSense s’affichent au fur et à mesure que vous tapez. Si vous continuez à taper des caractères, la liste des membres (variables, méthodes, etc.) est filtrée pour inclure uniquement les membres qui contiennent les caractères que vous tapez. Appuyez sur **Tab** ou **Entrée** pour insérer le membre sélectionné.

- Noms de fonctions intégrées

    ![Extension Visual Studio Code Resource Manager Tools : fonctions intelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-functions.png)

- Références de paramètres

    ![Extension Visual Studio Code Resource Manager Tools : paramètres intelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-parameters.png)

- Références de variables

    ![Extension Visual Studio Code Resource Manager Tools : variables intelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-variables.png)

- Propriétés resourceGroup()

    ![Extension Visual Studio Code Resource Manager Tools : fonctions intelliSense](./media/use-vs-code-to-create-template/resource-manager-tools-extension-intellisense-resourcegroup.png)

De plus, IntelliSense fonctionne avec les propriétés subscription() et les propriétés de références aux variables qui sont des objets.

### <a name="signature-help-for-function-parameters"></a>Aide sur les signatures pour les paramètres de fonction

Quand vous pointez sur des noms de fonctions, l’extension affiche l’aide sur les signatures pour les paramètres de fonction.

![Extension Visual Studio Code Resource Manager Tools : fonction de signature](./media/use-vs-code-to-create-template/resource-manager-tools-extension-signature-function.png)

### <a name="go-to-definition-for-variable-and-parameter-references"></a>Atteindre la définition pour les références de variable et de paramètre

Vous pouvez accéder à la définition en appuyant sur **Ctrl + clic** ou en utilisant le menu contextuel comme indiqué dans la capture d’écran : ![Extension Visual Studio Code Resource Manager Tools : atteindre la définition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-context-menu.png)

Vous pouvez ouvrir la définition sur le côté avec **Ctrl + Alt + clic**.

### <a name="peek-for-variable-and-parameter-definitions"></a>Aperçu des définitions de variables et de paramètres

Pour ouvrir l’éditeur d’aperçu, utilisez le menu contextuel, comme indiqué dans la capture d’écran précédente.

La capture d’écran qui suit présente l’éditeur d’aperçu :

![Extension Visual Studio Code Resource Manager Tools : éditeur d’aperçu](./media/use-vs-code-to-create-template/resource-manager-tools-extension-peek-editor.png)

### <a name="find-all-references-for-variables-and-parameters"></a>Rechercher toutes les références de variables et de paramètres

Pour rechercher toutes les références, utilisez le menu contextuel, comme indiqué dans la capture d’écran précédente.

La capture d’écran suivante montre comment les références sont mises en surbrillance :

![Extension Visual Studio Code Resource Manager Tools : rechercher toutes les références](./media/use-vs-code-to-create-template/resource-manager-tools-extension-find-all-references.png)

### <a name="rename-all-references-for-variables-and-parameters"></a>Renommer toutes les références de variables et de paramètres

Pour renommer toutes les références de variables et de paramètres, utilisez le menu contextuel comme indiqué dans la capture d’écran précédente.

### <a name="hover-for-parameter-description"></a>Pointer pour obtenir la description d’un paramètre

![Extension Visual Studio Code Resource Manager Tools : pointer pour obtenir une définition](./media/use-vs-code-to-create-template/resource-manager-tools-extension-hover-parameters.png)

### <a name="brace-matching"></a>Correspondance d’accolade

Les crochets correspondants sont mis en surbrillance dès que le curseur est proche de l’un d’eux. Quand vous cliquez sur une accolade, l’accolade correspondante est également mise en surbrillance comme indiqué dans la capture d’écran suivante :

![Extension Visual Studio Code Resource Manager Tools : correspondance d’accolade](./media/use-vs-code-to-create-template/resource-manager-tools-extension-brace-matching.png)

### <a name="show-errors-and-warnings"></a>Afficher les erreurs et les avertissements

Les erreurs identifiées par l’extension sont les suivantes :

- Références de paramètres non définis
- Références de variables non définies
- Noms de fonction non reconnus
- Utilisation de la fonction reference() dans la définition d’une variable
- Nombre incorrect d’arguments dans des fonctions

Les avertissements sont les suivants :

- Paramètres inutilisés
- Variables inutilisées

## <a name="next-steps"></a>Étapes suivantes

- Pour en savoir plus sur la création de modèles Azure Resource Manager, consultez [Didacticiel : Créer et déployer votre premier modèle Azure Resource Manager](template-tutorial-create-first-template.md)
- Pour parcourir un guide de démarrage rapide à l’aide de Visual Studio Code, consultez [Démarrage rapide : Créer des modèles Azure Resource Manager à l’aide de Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md)
