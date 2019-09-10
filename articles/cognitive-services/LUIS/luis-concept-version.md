---
title: Contrôle de version – LUIS
titleSuffix: Azure Cognitive Services
description: Les versions dans LUIS sont similaires aux versions dans la programmation traditionnelle. Chaque version est un instantané dans le temps de l’application. Avant d’apporter des modifications à l’application, créez une nouvelle version. Il est plus facile de revenir à l’application exacte, puis de tenter de rétablir les intentions et énoncés de l’application dans un état antérieur.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: diberry
ms.openlocfilehash: c519b030aaee58397766ecb8658e7af08b5986e1
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256875"
---
# <a name="understand-how-and-when-to-use-a-luis-version"></a>Comprendre comment et quand utiliser une version de LUIS

Les versions dans LUIS sont similaires aux versions dans la programmation traditionnelle. Chaque version est un instantané dans le temps de l’application. Avant d’apporter des modifications à l’application, créez une nouvelle version. Il est plus facile de revenir à la version exacte, puis de tenter de supprimer les intentions et énoncés dans un état antérieur.

Créez des modèles différents de la même application avec des [versions](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID de version
L’ID de version est composé de caractères, de chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.

## <a name="initial-version"></a>Version d’origine
La version d’origine (0.1) est la version active par défaut. 

## <a name="active-version"></a>Version active
[Définir une version](luis-how-to-manage-versions.md#set-active-version) en tant que version active signifie qu’elle est actuellement modifiée et testée dans le site web [LUIS](luis-reference-regions.md). Définissez une version en tant que version active pour accéder à ses données, effectuer des mises à jour, ainsi que pour la tester et la publier.

Le nom de la version active est affiché dans le volet supérieur gauche, après le nom de l’application. 

[![Modifier la version active](./media/luis-concept-version/version-in-nav-bar-inline.png)](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versions et emplacements de publication
Vous publiez dans l’emplacement intermédiaire ou l’emplacement produit. Chaque emplacement peut avoir une version différente ou la même version. Cela est utile pour vérifier les modifications entre les versions du modèle via le point de terminaison, qui est disponible pour les bots ou autres applications appelantes LUIS. 

## <a name="clone-a-version"></a>Cloner une version
Clonez une version pour créer une copie d’une version existante et l’enregistrer en tant que nouvelle version. Clonez une version pour utiliser le même contenu que la version existante comme point de départ pour la nouvelle version. Une fois que vous clonez une version, la nouvelle version devient la version **active**. 

## <a name="import-and-export-a-version"></a>Importer et exporter une version
Vous pouvez importer une version au niveau de l’application. Cette version devient la version active et utilise l’ID de version dans la propriété « versionId » du fichier de l’application. Vous pouvez également importer au niveau de la version dans une application existante. La nouvelle version devient la version active. 

Vous pouvez exporter une version au niveau de l’application ou vous pouvez exporter une version au niveau de la version. La seule différence est que la version exportée au niveau de l’application est la version active, alors qu’au niveau de la version, vous pouvez choisir n’importe quelle version à exporter dans la page **[Paramètres](luis-how-to-manage-versions.md)** . 

Le fichier exporté ne contient pas d’informations issues de l’apprentissage automatique, car l’application est reformée après son importation. Le fichier exporté ne contient pas d’informations de contributeur.

## <a name="export-each-version-as-app-backup"></a>Exporter chaque version en tant que sauvegarde de l’application
Pour sauvegarder votre application LUIS, exportez chaque version dans la page **[Paramètres](luis-how-to-manage-versions.md)** .

## <a name="delete-a-version"></a>Supprimer une version
Vous pouvez supprimer toutes les versions à l’exception de la version active dans la liste Versions sur la page Paramètres. 

## <a name="version-availability-at-the-endpoint"></a>Disponibilité de la version sur le point de terminaison
Les versions formées ne sont pas automatiquement disponibles sur le [point de terminaison](luis-glossary.md#endpoint) de votre application. Vous devez [publier](luis-how-to-publish-app.md) ou republier une version afin qu’elle soit disponible sur le point de terminaison de votre application. Vous pouvez publier dans **Intermédiaire** et **Production**, ce qui vous donne deux versions de l’application disponibles sur le point de terminaison. Si vous avez besoin de davantage de versions de l’application disponibles sur un point de terminaison, vous devez exporter la version et la réimporter dans une nouvelle application. La nouvelle application a un ID d’application différent.

## <a name="manage-multiple-versions-inside-the-same-app"></a>Gérer plusieurs versions dans la même application
Commencez par le [clonage](luis-how-to-manage-versions.md#clone-a-version), à partir d’une version de base, pour chaque auteur. 

Chaque auteur apporte des modifications à sa propre version de l’application. Une fois que chaque auteur est satisfait du modèle, exportez les nouvelles versions sous forme de fichiers JSON.  

Les applications exportées sont des fichiers au format JSON, qui peuvent être utilisés pour comparer les modifications. Combinez les fichiers pour créer un seul fichier JSON de la nouvelle version. Modifiez la propriété **versionId** dans le fichier JSON pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine. 

Cette méthode vous permet d’avoir une version active, une version intermédiaire et une version publiée. Vous pouvez comparer les résultats de la version active avec une version publiée (de phase ou de production) dans le [volet de test interactif](luis-interactive-test.md).

## <a name="manage-multiple-versions-as-apps"></a>Gérer plusieurs versions en tant qu’applications
[Exportez](luis-how-to-manage-versions.md#export-version) la version de base. Chaque auteur importe la version. La personne qui importe l’application est le propriétaire de la version. Une fois la modification de l’application terminée, exportez la version. 

Les applications exportées sont des fichiers au format JSON, qui peuvent être utilisés pour comparer les modifications avec l’exportation de base. Combinez les fichiers pour créer un seul fichier JSON de la nouvelle version. Modifiez la propriété **versionId** dans le fichier JSON pour indiquer la nouvelle version fusionnée. Importez cette version dans l’application d’origine.

## <a name="contributions-from-collaborators"></a>Contributions de collaborateurs

Apprenez-en davantage sur la création de contributions de [collaborateurs](luis-how-to-collaborate.md).

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter le [contrôle de version](luis-how-to-manage-versions.md) sur la page Paramètres de l’application. 

Découvrez comment concevoir des [intentions](luis-concept-intent.md) dans le modèle.
