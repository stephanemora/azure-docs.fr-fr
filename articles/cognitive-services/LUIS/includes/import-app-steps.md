---
title: Procédure d’importation d’application
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 4c87ea9ebb2dd7aec36025e4b45f74e186ca1cd4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588776"
---
1. Sur le [portail LUIS](https://www.luis.ai), dans la page **Mes applications**, sélectionnez **+Nouvelle application de conversation**, puis **Importer en tant que JSON**. Recherchez le fichier JSON enregistré à l’étape précédente. Vous n’avez pas besoin de modifier le nom de l’application. Sélectionnez **Terminé**

1. Dans la section **Gérer**, sous l’onglet **Versions**, sélectionnez la version `0.1`, puis choisissez **Cloner** pour cloner la version. Attribuez-lui ensuite un nouveau nom de `ml-entity`, puis sélectionnez **Terminer** pour terminer le processus de clonage. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

    > [!TIP]
    > Le clonage dans une nouvelle version est considéré comme une bonne pratique avant la modification de votre application. Quand vous en avez terminé avec la modification d’une version, exportez-la (sous forme de fichier .json ou .lu), puis archivez le fichier dans votre système de contrôle de code source.

1. Sélectionnez **Générer** puis **Intentions** pour voir les intentions, qui sont les principaux composants d’une application LUIS.

    ![Passez de la page Versions à la page Intentions.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)