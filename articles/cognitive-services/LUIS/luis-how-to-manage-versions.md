---
title: Gérer les versions
titleSuffix: Language Understanding - Azure Cognitive Services
description: Les versions vous permettent de générer et de publier différents modèles. Une bonne pratique consiste à cloner le modèle actif en une version différente de l’application avant d’apporter des modifications au modèle.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: f919651cf39d1f2c48fca87da935e49e3affa79f
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678531"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Utiliser les versions pour modifier et tester sans impact sur les applications intermédiaires et de production

Les versions vous permettent de générer et de publier différents modèles. Une bonne pratique consiste à cloner le modèle actif en une [version](luis-concept-version.md) différente de l’application avant d’apporter des modifications au modèle. 

Pour utiliser des versions, ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications), sélectionnez **Manage** (Gérer) dans la barre supérieure, puis **Versions** dans le volet de navigation gauche. 

La liste des versions montre quelles versions sont publiées, où ils sont publiés, et quelle version est actuellement active. 

[![Section gérer, page versions](./media/luis-how-to-manage-versions/versions-import.png "Section gérer, page versions")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Cloner une version

1. Sélectionnez la version que vous souhaitez cloner, puis sélectionnez **Clone** dans la barre d’outils. 

2. Dans la boîte de dialogue **Cloner la version**, tapez un nom pour la nouvelle version, tel que « 0.2 ».

   ![Boîte de dialogue Cloner la version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > L’ID de la version doit être composé uniquement de caractères, chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.
 
   Une nouvelle version avec le nom spécifié est créée et définie en tant que version active.

## <a name="set-active-version"></a>Définir la version active

Sélectionnez une version dans la liste, puis sélectionnez **Make Active** (Activer) dans la barre d’outils. 

[![Section gérer, page versions, action effectuer une version](./media/luis-how-to-manage-versions/versions-other.png "Section gérer, page versions, action effectuer une version")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Importer une version

1. Sélectionnez **Import version** (Importer une version) dans la barre d’outils. 

2. Dans la fenêtre contextuelle **Import new version (Importer une nouvelle version)**, entrez le nouveau nom de version à dix caractères. Vous n’avez qu’à définir un ID de version si la version dans le fichier JSON existe déjà dans l’application.

    ![Section gérer, page versions, importation d’une nouvelle version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Une fois que vous importez une version, cette nouvelle version devient la version active.

### <a name="import-errors"></a>Erreurs d’importation

* Erreurs du Générateur de jetons : Si vous obtenez un **erreur de générateur de jetons** lors de l’importation, vous essayez d’importer une version qui utilise un autre [Générateur de jetons](luis-language-support.md#custom-tokenizer-versions) que l’application utilise actuellement. Pour résoudre ce problème, consultez [migration entre les versions du Générateur de jetons](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Autres actions

* Pour **supprimer** une version, sélectionnez une version dans la liste, puis sélectionnez **Delete** (Supprimer) dans la barre d’outils. Sélectionnez **OK**. 
* Pour **renommer** une version, sélectionnez une version dans la liste, puis sélectionnez **Rename** (Renommer) dans la barre d’outils. Entrez le nouveau nom, puis sélectionnez **Done** (Terminé). 
* Pour **exporter** une version, sélectionnez une version dans la liste, puis sélectionnez **Export app** (Exporter l’application) dans la barre d’outils. Choisissez JSON à exporter pour la sauvegarde, choisissez **exporter pour conteneur** à [utiliser cette application dans un conteneur de LUIS](luis-container-howto.md).  

