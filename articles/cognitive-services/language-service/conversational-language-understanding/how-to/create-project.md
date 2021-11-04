---
title: Guide pratique pour créer des projets dans la compréhension du langage courant
titleSuffix: Azure Cognitive Services
description: Dans cet article, découvrez comment créer des projets dans la compréhension du langage courant.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: c837912ea60b0caf91e2d04382af343bd4b6a96a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097587"
---
# <a name="how-to-create-projects-in-conversational-language-understanding"></a>Guide pratique pour créer des projets dans la compréhension du langage courant

La compréhension du langage courant vous permet de créer deux types de projets : des projets **Conversation** et des projets **Flux de travail d’orchestration**.

## <a name="sign-in-to-language-studio"></a>Connexion à Language Studio
Pour commencer, vous devez d’abord vous connecter à [Language Studio](https://aka.ms/languageStudio) et créer une ressource de langue. Sélectionnez **Terminé** une fois la sélection effectuée.

## <a name="navigate-to-conversational-language-understanding"></a>Accès à la compréhension du langage courant

Dans Language Studio, recherchez la section **Comprendre le langage courant**, puis sélectionnez **Compréhension du langage courant**.

La page des projets de compréhension du langage courant s’affiche.

:::image type="content" source="../media/projects-page.png" alt-text="Capture d’écran montrant la page des projets de compréhension du langage courant." lightbox="../media/projects-page.png":::

## <a name="create-a-conversation-project"></a>Création d’un projet de conversation
Après avoir sélectionné Conversation, vous devez fournir les informations suivantes :
- Nom : nom du projet
- Description : description du projet (facultative)
- Langue principale du texte : langue principale de votre projet. Vos données d’apprentissage doivent être principalement dans cette langue.
- Activer plusieurs langues : si vous souhaitez autoriser votre projet à prendre en charge plusieurs langues à la fois.

:::image type="content" source="../media/clu-project-modal.png" alt-text="Capture d’écran montrant la fenêtre de création de projets de conversation de compréhension du langage courant." lightbox="../media/clu-project-modal.png":::

Une fois que vous avez terminé, cliquez sur Suivant, vérifiez les informations, puis cliquez sur Créer le projet pour terminer le processus. 

## <a name="create-an-orchestration-workflow-project"></a>Création d’un projet de flux de travail d’orchestration

Après avoir sélectionné Orchestration, vous devez fournir les informations suivantes :
- Nom : nom du projet
- Description : description du projet (facultative)
- Langue principale du texte : langue principale de votre projet. Vos données d’apprentissage doivent être principalement dans cette langue.
- Activer plusieurs langues : si vous souhaitez autoriser votre projet à prendre en charge plusieurs langues à la fois.

Une fois que vous avez terminé, vous avez la possibilité de vous connecter aux autres projets et services que vous souhaitez orchestrer. Chaque connexion est représentée par son type et par les données appropriées. L’intention doit avoir un **nom**, un **type de projet** (LUIS, réponses aux questions personnalisées (QnA) ou compréhension du langage courant), puis le nom du projet auquel vous souhaitez vous connecter. 

> [!NOTE]
> La liste des projets possibles se compose uniquement de ceux qui appartiennent à la ressource de langage utilisée pour créer le projet d’orchestration.

Cette étape est facultative. Vous aurez toujours la possibilité d’ajouter des connexions à des intentions après avoir créé le projet.

:::image type="content" source="../media/orchestration-project-detail.png" alt-text="Capture d’écran montrant la fenêtre modale des projets de flux de travail d’orchestration de compréhension du langage courant." lightbox="../media/orchestration-project-detail.png":::

## <a name="import-a-project"></a>Importer un projet

Vous pouvez à tout moment exporter un projet de compréhension du langage courant sous forme de fichier JSON. Pour cela, accédez à la page des projets de conversation, sélectionnez un projet, puis appuyez sur **Exporter**.
Ce projet peut être réimporté comme nouveau projet. Si vous importez un projet portant exactement le même nom, les données du projet sont remplacées par les données du nouveau projet importé.

:::image type="content" source="../media/export.png" alt-text="Capture d’écran montrant le bouton Exporter de la compréhension du langage courant." lightbox="../media/export.png":::

Si vous disposez d’une application LUIS, vous pouvez _importer_ directement le fichier JSON de cette application dans la compréhension du langage courant, et créer un projet de conversation avec tous les éléments actuellement disponibles : intentions, entités ML et énoncés. Pour plus d’informations, consultez [Compatibilité descendante avec LUIS](../concepts/backwards-compatibility.md).

Cliquez sur le bouton fléché à côté de **Créer un nouveau projet** et sélectionnez **Importer**, puis sélectionnez le fichier JSON de LUIS ou de la compréhension du langage courant.

:::image type="content" source="../media/import.png" alt-text="Capture d’écran montrant le bouton Importer de la compréhension du langage courant." lightbox="../media/import.png":::

## <a name="next-steps"></a>Étapes suivantes

[Générer le schéma](./build-schema.md)
