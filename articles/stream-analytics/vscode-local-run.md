---
title: Tester les requêtes Azure Stream Analytique localement à l’aide de Visual Studio Code (version préliminaire)
description: Cet article décrit comment tester les requêtes localement à l’aide d’Azure Stream Analytique Tools pour Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: f477a0f99c3eaa82568d8188bfaae03818fb72dc
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827949"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Tester des requêtes Stream Analytique localement à l’aide de Visual Studio Code

Vous pouvez utiliser les outils Azure Stream Analytique pour Visual Studio Code pour tester vos travaux Stream Analytique localement à l’aide d’exemples de données.

Utilisez cette [quickstart](quick-create-vs-code.md) pour apprendre à créer un travail Stream Analytique à l’aide de Visual Studio Code.

## <a name="run-queries-locally"></a>Exécuter des requêtes localement

Vous pouvez utiliser l’extension d’Azure Stream Analytique pour Visual Studio Code pour tester vos travaux Stream Analytique localement à l’aide d’exemples de données.

1. Une fois que vous avez créé votre travail Analytique de Stream, utilisez **Ctrl + Maj + P** pour ouvrir la palette de commandes. Puis tapez et sélectionnez **ASA : Ajouter une entrée**.

    ![Ajouter une entrée de ASA dans Visual Studio code](./media/vscode-local-run/add-input.png)

2. Sélectionnez **entrée locale**.

    ![Ajouter une entrée locale ASA dans Visual Studio code](./media/vscode-local-run/add-local-input.png)

3. Sélectionnez **+ nouvelle entrée Local**.

    ![Ajouter un nouveau ASA local d’entrée dans le code de Visual Studio](./media/vscode-local-run/add-new-local-input.png)

4. Entrez le même alias d’entrée que vous avez utilisé dans votre requête.

    ![Ajouter un nouvel alias d’entrée local ASA](./media/vscode-local-run/new-local-input-alias.png)

5. Dans le **LocalInput_DefaultLocalStream.json** de fichier, entrez le chemin d’accès du fichier où se trouve votre fichier de données local.

    ![Entrez le chemin d’accès du fichier local dans Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Revenez à votre éditeur de requête, puis cliquez **exécuter localement**.

    ![Sélectionnez exécuter localement dans l’éditeur de requête](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créez un travail de cloud d’Azure Stream Analytique dans Visual Studio Code (version préliminaire)](quick-create-vs-code.md)

* [Explorez les travaux Azure Stream Analytique avec Visual Studio Code (version préliminaire)](vscode-explore-jobs.md)
