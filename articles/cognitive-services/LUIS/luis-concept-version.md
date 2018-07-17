---
title: Comprendre le contrôle de version dans LUIS - Azure | Microsoft Docs
description: Découvrir comment utiliser les versions pour gérer les modifications dans Language Understanding (LUIS)
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/13/2018
ms.author: v-geberr
ms.openlocfilehash: dabe7def2766770b686be3c43d4af4f331dd9577
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266071"
---
# <a name="versions"></a>Versions
Créez des modèles différents de la même application avec des [versions](luis-how-to-manage-versions.md). 

## <a name="version-id"></a>ID de version
L’ID de version est composé de caractères, de chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.

## <a name="initial-version"></a>Version d’origine
La version d’origine (0.1) est la version active par défaut. 

## <a name="active-version"></a>Version active
[Définir une version](luis-how-to-manage-versions.md#set-active-version) en tant que version active signifie qu’elle est actuellement modifiée et testée dans le site web [LUIS][LUIS]. Définissez une version en tant que version active pour accéder à ses données, effectuer des mises à jour, ainsi que pour la tester et la publier.

Le nom de la version active est affiché dans le volet supérieur gauche, après le nom de l’application. 

[ ![Modifier la version active](./media/luis-concept-version/version-in-nav-bar-inline.png) ](./media/luis-concept-version/version-in-nav-bar-expanded.png#lightbox)

## <a name="versions-and-publishing-slots"></a>Versions et emplacements de publication
Vous publiez dans l’emplacement intermédiaire ou l’emplacement produit. Chaque emplacement peut avoir une version différente ou la même version. Cela est utile pour vérifier les modifications entre les versions du modèle via le point de terminaison, qui est disponible pour les bots ou autres applications appelantes LUIS. 

## <a name="clone-a-version"></a>Cloner une version
Clonez une version pour créer une copie d’une version existante et l’enregistrer en tant que nouvelle version. Clonez une version pour utiliser le même contenu que la version existante comme point de départ pour la nouvelle version. Une fois que vous clonez une version, la nouvelle version devient la version **active**. 

## <a name="import-and-export-a-version"></a>Importer et exporter une version
Vous pouvez importer une version au niveau de l’application. Cette version devient la version active et utilise l’ID de version dans la propriété « versionId » du fichier de l’application. Vous pouvez également importer au niveau de la version dans une application existante. La nouvelle version devient la version active. 

Vous pouvez exporter une version au niveau de l’application ou vous pouvez exporter une version au niveau de la version. La seule différence est que la version exportée au niveau de l’application est la version active, alors qu’au niveau de la version, vous pouvez choisir n’importe quelle version à exporter dans la page **[Paramètres](luis-how-to-manage-versions.md)**. 

Le fichier exporté ne contient pas d’informations issues de l’apprentissage automatique, car l’application est reformée après son importation. Le fichier exporté ne contient pas de collaborateurs : vous devez en ajouter une fois que la version est importée dans la nouvelle application.

## <a name="export-each-version-as-app-backup"></a>Exporter chaque version en tant que sauvegarde de l’application
Pour sauvegarder votre application LUIS, exportez chaque version dans la page **[Paramètres](luis-how-to-manage-versions.md)**.

## <a name="delete-a-version"></a>Supprimer une version
Vous pouvez supprimer toutes les versions à l’exception de la version active dans la liste Versions sur la page Paramètres. 

## <a name="version-availability-at-the-endpoint"></a>Disponibilité de la version sur le point de terminaison
Les versions formées ne sont pas automatiquement disponibles sur le [point de terminaison](luis-glossary.md#endpoint) de votre application. Vous devez [publier](PublishApp.md) ou republier une version afin qu’elle soit disponible sur le point de terminaison de votre application. Vous pouvez publier dans **Intermédiaire** et **Production**, ce qui vous donne deux versions de l’application disponibles sur le point de terminaison. Si vous avez besoin de davantage de versions de l’application disponibles sur un point de terminaison, vous devez exporter la version et la réimporter dans une nouvelle application. La nouvelle application a un ID d’application différent.

## <a name="collaborators"></a>Collaborateurs
Le propriétaire et tous les [collaborateurs](luis-how-to-collaborate.md) ont un accès complet à toutes les versions de l’application.

## <a name="next-steps"></a>Étapes suivantes

Découvrez comment ajouter le [contrôle de version](luis-how-to-manage-versions.md) sur la page Paramètres de l’application. 

Découvrez comment concevoir des [intentions](luis-concept-intent.md) dans le modèle.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions