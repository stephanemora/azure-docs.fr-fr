---
title: Comparaison des fonctionnalités Azure Stream Analytics
description: Cet article compare les fonctionnalités prises en charge pour les tâches cloud et IoT Edge Azure Stream Analytics dans le portail Azure, Visual Studio et Visual Studio Code.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 22d7ef90ee0cf4d09467516b7bb0664327b7dabe
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509689"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Comparaison des fonctionnalités Azure Stream Analytics

Avec Azure Stream Analytics, vous pouvez créer des solutions de diffusion en continu dans le cloud et IoT Edge à l’aide du [portail Azure](stream-analytics-quick-create-portal.md), de [Visual Studio](stream-analytics-quick-create-vs.md), et de [Visual Studio Code](quick-create-vs-code.md). Les tableaux de cet article indiquent les fonctionnalités prises en charge par chaque plateforme pour les deux types de tâches.

## <a name="cloud-job-features"></a>Fonctionnalités des tâches du cloud


|Fonctionnalité  |Portail  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Multiplateforme     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|Création de script     |OUI         |OUI         |OUI         |
|Script Intellisense     |Mise en surbrillance de la syntaxe         |Mise en surbrillance de la syntaxe</br>Complétion de code</br>Marqueur d’erreur         |Mise en surbrillance de la syntaxe</br>Complétion de code</br>Marqueur d’erreur         |
|Définir des entrées, des sorties et des configurations de projet     |OUI         |OUI         |OUI         |
|Partitionnement de la sortie des objets blob     |OUI         |OUI         |OUI         |
|Power BI comme sortie     |OUI         |OUI         |Non         |
|Données de référence SQL Database     |OUI         |OUI         |OUI         |
|Propriétés de message personnalisées     |OUI         |Non         |Non         |
|Partager des entrées et des sorties entre plusieurs requêtes     |Non         |OUI         |OUI         |
|UDF et UDA JavaScript     |OUI         |OUI         |Windows uniquement         |
|Légendes de Machine Learning     |Oui, mais la requête ne peut pas être testée        |Oui, mais ne peut pas être testé localement         |Non         |
|Niveau de compatibilité     |1.0</br>1.1</br>1.2         |1.0</br>1.1</br>1.2          |1.0</br>1.1</br>1.2          |
|Fonctions de détection d’anomalie ML intégrées     |OUI         |OUI         |OUI         |
|Fonctions géospatiales intégrées     |OUI         |OUI         |OUI         |
|Test de requête avec un exemple de fichier     |OUI         |OUI         |OUI         |
|Test local des données actives     |Non         |OUI         |Non         |
|Répertorier les travaux et afficher les entités de travail     |OUI         |OUI         |OUI         |
|Exporter un travail vers un projet local     |Non         |OUI         |OUI         |
|Envoyer, lancer et arrêter des tâches     |OUI         |OUI         |OUI         |
|Contrôle de code source     |Non         |OUI         |OUI         |
|Prise en charge de CI/CD     |Partiel         |OUI         |OUI         |
|Afficher les métriques de tâche et le diagramme     |OUI         |OUI         |Ouvrir dans le portail         |
|Afficher les erreurs d’exécution de tâches     |OUI         |OUI         |Non         |
|Journaux de diagnostic     |OUI         |Non         |Non         |


## <a name="iot-edge-job-features"></a>Fonctionnalités des tâches IoT Edge

|Fonctionnalité  |Portail  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|Création de tâches     |OUI         |OUI         |Non         |
|Contrôle de code source     |Non         |OUI         |Non         |
|Exporter un travail vers un projet local     |Non         |OUI         |Non         |
|Test de requête avec un exemple de fichier     |OUI         |OUI         |Non         |
|Partager des entrées et des sorties entre plusieurs requêtes     |Non         |OUI         |Non         |
|C# UDF     |Non         |OUI         |Non         |
|Envoyer, lancer et arrêter des tâches     |OUI         |OUI         |Non         |
|Répertorier les travaux et afficher les entités de travail     |OUI         |OUI         |Non         |
|Afficher les métriques de tâche et le diagramme     |OUI         |Partiel         |Non         |
|Afficher les erreurs d’exécution de tâches     |OUI         |Partiel         |Non         |
|Prise en charge de CI/CD     |Non         |Non         |Non         |


## <a name="next-steps"></a>Étapes suivantes

* [Azure Stream Analytics sur IoT Edge](stream-analytics-edge.md)
* [Tutoriel : Écrire une fonction C# définie par l’utilisateur pour une tâche IoT Edge Azure Stream Analytics (préversion)](stream-analytics-edge-csharp-udf.md)
* [Développer des tâches IoT Edge Azure Stream Analytics avec les outils Visual Studio](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Utiliser Visual Studio pour afficher les tâches Azure Stream Analytics](stream-analytics-vs-tools.md)
* [Explorer Azure Stream Analytics avec Visual Studio Code (préversion)](vscode-explore-jobs.md)


