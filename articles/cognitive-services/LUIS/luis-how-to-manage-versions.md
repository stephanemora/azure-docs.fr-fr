---
title: Gérer les versions - LUIS
titleSuffix: Azure Cognitive Services
description: Les versions vous permettent de générer et de publier différents modèles. Une bonne pratique consiste à cloner le modèle actif en une version différente de l’application avant d’apporter des modifications au modèle.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 3cf4ce5dc7061065ea501ae1c00cd5b9e5404770
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540861"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Utiliser les versions pour modifier et tester sans impact sur les applications intermédiaires et de production

Les versions vous permettent de générer et de publier différents modèles. Une bonne pratique consiste à cloner le modèle actif en une [version](luis-concept-version.md) différente de l’application avant d’apporter des modifications au modèle.

La version active est la version que vous modifiez dans la section **Build** du portail LUIS avec des intentions, des entités, des fonctionnalités et des modèles. Lorsque vous utilisez les API de création, vous n’avez pas besoin de définir la version active, car les appels d’API REST spécifiques à la version incluent la version de l’itinéraire.

Pour utiliser des versions, ouvrez votre application en sélectionnant son nom dans la page **My Apps** (Mes applications), sélectionnez **Manage** (Gérer) dans la barre supérieure, puis **Versions** dans le volet de navigation gauche.

La liste des versions montre quelles versions sont publiées, où elles sont publiées et quelle version est actuellement active.

## <a name="clone-a-version"></a>Cloner une version

1. Sélectionnez la version que vous souhaitez cloner, puis sélectionnez **Clone** dans la barre d’outils.

2. Dans la boîte de dialogue **Cloner la version**, tapez un nom pour la nouvelle version, tel que « 0.2 ».

   ![Boîte de dialogue Cloner la version](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)

     > [!NOTE]
     > L’ID de la version doit être composé uniquement de caractères, chiffres ou de « . » et ne peut pas contenir plus de 10 caractères.

   Une nouvelle version avec le nom spécifié est créée et définie en tant que version active.

## <a name="set-active-version"></a>Définir la version active

Sélectionnez une version dans la liste, puis sélectionnez **Activer** dans la barre d’outils.

## <a name="import-version"></a>Importer une version

Vous pouvez importer une version `.json` ou `.lu` de votre application.

1. Sélectionnez **Importer** dans la barre d’outils, puis sélectionnez le format.

2. Dans la fenêtre contextuelle **Import new version (Importer une nouvelle version)** , entrez le nouveau nom de version à dix caractères. Vous n’avez qu’à définir un ID de version si la version dans le fichier existe déjà dans l’application.

    ![Section gérer, page versions, importation d’une nouvelle version](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    Une fois que vous importez une version, cette nouvelle version devient la version active.

### <a name="import-errors"></a>Erreurs d’importation

* Erreurs de générateur de jetons : Si vous obtenez une **erreur de générateur de jetons** lors de l'importation, vous essayez d'importer une version qui utilise un [générateur de jetons](luis-language-support.md#custom-tokenizer-versions) différent de celui actuellement utilisé par l'application. Pour résoudre ce problème, consultez [Changer de version du générateur de jetons](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Autres actions

* Pour **supprimer** une version, sélectionnez une version dans la liste, puis sélectionnez **Delete** (Supprimer) dans la barre d’outils. Sélectionnez **OK**.
* Pour **renommer** une version, sélectionnez une version dans la liste, puis sélectionnez **Rename** (Renommer) dans la barre d’outils. Entrez le nouveau nom, puis sélectionnez **Done** (Terminé).
* Pour **exporter** une version, sélectionnez une version dans la liste, puis sélectionnez **Export app** (Exporter l’application) dans la barre d’outils. Choisissez JSON ou LU pour exporter en vue d’une sauvegarde ou pour un enregistrement dans le contrôle de code source, choisissez **Export for container** (Exporter pour conteneur) pour [utiliser cette application dans un conteneur LUIS](luis-container-howto.md).

