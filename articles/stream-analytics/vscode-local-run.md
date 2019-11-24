---
title: Tester des requêtes Azure Stream Analytics avec Visual Studio Code
description: Cet article décrit comment tester des requêtes localement avec Azure Stream Analytics Tools pour Visual Studio Code.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 05/15/2019
ms.topic: conceptual
ms.openlocfilehash: 2791fb923f193815d718dbd2269cbcd11583a4ea
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72924968"
---
# <a name="test-stream-analytics-queries-locally-with-visual-studio-code"></a>Tester des requêtes Stream Analytics localement avec Visual Studio Code

Vous pouvez utiliser Azure Stream Analytics Tools pour Visual Studio Code afin de tester vos travaux Stream Analytics localement à l’aide d’exemples de données.

Utilisez ce [démarrage rapide](quick-create-vs-code.md) pour apprendre à créer un travail Stream Analytics à l’aide de Visual Studio Code.

## <a name="prerequisites"></a>Prérequis
* Installez le [SDK .NET Core](https://dotnet.microsoft.com/download).
* Redémarrez Visual Studio Code.
 
## <a name="run-queries-locally"></a>Exécuter des requêtes localement

Vous pouvez utiliser l’extension Azure Stream Analytics pour Visual Studio Code afin de tester vos travaux Stream Analytics localement à l’aide d’exemples de données.

1. Une fois que vous avez créé votre travail Stream Analytics, utilisez **Ctrl+Maj+P** pour ouvrir la palette de commandes. Saisissez et sélectionnez ensuite **ASA : Ajouter une entrée**.

    ![Ajouter une entrée ASA dans Visual Studio Code](./media/vscode-local-run/add-input.png)

2. Sélectionnez **Entrée locale**.

    ![Ajouter une entrée locale ASA dans Visual Studio Code](./media/vscode-local-run/add-local-input.png)

3. Sélectionnez **+ New Local Input (Nouvelle entrée locale)** .

    ![Ajouter une entrée locale ASA dans Visual Studio Code](./media/vscode-local-run/add-new-local-input.png)

4. Entrez le même alias d’entrée que pour votre requête.

    ![Ajouter un alias d’entrée locale ASA](./media/vscode-local-run/new-local-input-alias.png)

5. Dans le fichier **LocalInput_DefaultLocalStream.json**, entrez le chemin d’accès du fichier où se trouve votre fichier de données local.

    ![Entrez le chemin d’accès du fichier local dans Visual Studio](./media/vscode-local-run/local-file-path.png)

6. Revenez à votre éditeur de requête, puis sélectionnez **Exécuter localement**.

    ![Dans l’éditeur de requête, sélectionnez Exécuter localement](./media/vscode-local-run/run-locally.png)

## <a name="next-steps"></a>Étapes suivantes

* [Créer un travail cloud Azure Stream Analytics dans Visual Studio Code (préversion)](quick-create-vs-code.md)

* [Explorer des travaux Azure Stream Analytics avec Visual Studio Code (préversion)](vscode-explore-jobs.md)
