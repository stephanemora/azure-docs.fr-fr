---
title: Comment utiliser des étiquettes de tableau pour effectuer l’apprentissage de votre module de formulaire personnalisé – Form Recognizer
titleSuffix: Azure Applied AI Services
description: Apprenez à utiliser efficacement l’étiquetage supervisé des tableaux.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 03/15/2021
ms.author: lajanuar
ms.openlocfilehash: 6cbf96a050124fe0d72bb0fc24be4ad7ea265fed
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2021
ms.locfileid: "110374049"
---
# <a name="use-table-tags-to-train-your-custom-form-model"></a>Utiliser des étiquettes de tableau pour effectuer l’apprentissage de votre modèle de formulaire personnalisé

Dans cet article, vous allez apprendre à effectuer l’apprentissage de votre modèle de formulaire personnalisé à l’aide d’étiquettes de tableau. Certains scénarios nécessitent un étiquetage plus complexe que le simple alignement des paires clé-valeur. Ces scénarios incluent l’extraction d’informations de formulaires aux structures hiérarchiques complexes ou la rencontre d’éléments qui ne sont pas automatiquement détectés et extraits par le service. Dans ces cas, vous pouvez utiliser les étiquettes de tableau pour effectuer l’apprentissage de votre modèle de formulaire personnalisé.

## <a name="when-should-i-use-table-tags"></a>Quand dois-je utiliser des étiquettes de tableau ?

Voici quelques exemples de cas où l’utilisation d’étiquettes de tableau serait appropriée :

- Les données que vous souhaitez extraire sont présentées sous forme de tableaux dans vos formulaires, et la structure des tableaux a une signification. Par exemple, chaque ligne du tableau représente un élément et chaque colonne de la ligne représente une caractéristique spécifique de cet élément. Dans ce cas, vous pouvez utiliser une étiquette de tableau dans laquelle une colonne représente des caractéristiques et une ligne représente des informations sur chaque caractéristique.
- Les données que vous souhaitez extraire ne sont pas présentées dans des champs de formulaire spécifiques, mais, d’un point de vue sémantique, elles pourraient s’insérer dans une grille bidimensionnelle. Par exemple, votre formulaire contient une liste de personnes, avec un prénom, un nom et une adresse e-mail. Vous voulez extraire ces informations. Dans ce cas, vous pouvez utiliser une étiquette de tableau dont les colonnes sont le prénom, le nom et l’adresse e-mail et dont chaque ligne contient des informations sur une personne de votre liste.

> [!NOTE]
> Form Recognizer recherche et extrait automatiquement tous les tableaux de vos documents, que les tableaux soient étiquetés ou non. Par conséquent, il n’est pas nécessaire d’étiqueter chaque tableau de votre formulaire avec une étiquette de tableau ,et vos étiquettes de tableau n’ont pas à reproduire la structure de chaque tableau trouvé dans votre formulaire. Les tableaux extraits automatiquement par Form Recognizer seront inclus dans la section pageResults de la sortie JSON.

## <a name="create-a-table-tag-with-form-ocr-test-tool-fott"></a>Créer une étiquette de tableau avec Form OCR Test Tool (FOTT)
<!-- markdownlint-disable MD004 -->
* Déterminez si vous souhaitez une étiquette de tableau **dynamique** ou **de taille fixe**. Si le nombre de lignes varie d’un document à l’autre, utilisez une étiquette de tableau dynamique. Si le nombre de lignes est constant d’un document à l’autre, utilisez une étiquette de tableau de taille fixe.
* Si votre étiquette de tableau est dynamique, définissez les noms des colonnes ainsi que le type et le format des données pour chaque colonne.
* Si votre tableau est de taille fixe, définissez le nom de la colonne, le nom de la ligne, le type de données et le format pour chaque étiquette.
:::image type="content" source="./media/table-tag-configure.png" alt-text="Configurer une étiquette de tableau":::

## <a name="label-your-table-tag-data"></a>Étiqueter les données de vos étiquettes de tableau

* Si votre projet comporte une étiquette de tableau, vous pouvez ouvrir le panneau d’étiquetage et remplir l’étiquette comme vous le feriez pour des champs de type clé-valeur.
:::image type="content" source="media/table-labeling.png" alt-text="Étiqueter avec des étiquettes de tableau":::

## <a name="next-steps"></a>Étapes suivantes

Suivez notre démarrage rapide pour effectuer l’apprentissage de votre modèle Form Recognizer personnalisé et l’utiliser :

> [!div class="nextstepaction"]
> [Entraînement avec des étiquettes à l’aide de l’outil Exemple d’étiquetage](label-tool.md)

## <a name="see-also"></a>Voir aussi

* [Qu’est-ce que Form Recognizer ?](overview.md)
>
