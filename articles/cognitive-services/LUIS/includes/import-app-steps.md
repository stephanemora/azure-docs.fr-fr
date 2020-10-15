---
title: Procédure d’importation d’application
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/22/2020
ms.openlocfilehash: aa0ba1de63cbcf1d9485c71b6bec7a29afb5140f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91545160"
---
1. Sur le [portail LUIS](https://www.luis.ai), dans la page **Mes applications**, sélectionnez **+Nouvelle application de conversation**, puis **Importer en tant que JSON**. Recherchez le fichier JSON enregistré à l’étape précédente. Vous n’avez pas besoin de modifier le nom de l’application. Sélectionnez **Terminé**

1. Dans la section **Gérer**, sous l’onglet **Versions**, sélectionnez la version `0.1`, puis choisissez **Cloner** pour cloner la version. Attribuez-lui ensuite un nouveau nom de `ml-entity`, puis sélectionnez **Terminer** pour terminer le processus de clonage. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

    > [!TIP]
    > Le clonage dans une nouvelle version est considéré comme une bonne pratique avant la modification de votre application. Quand vous en avez terminé avec la modification d’une version, exportez-la (sous forme de fichier .json ou .lu), puis archivez le fichier dans votre système de contrôle de code source.

1. Sélectionnez **Générer** puis **Intentions** pour voir les intentions, qui sont les principaux composants d’une application LUIS.

    ![Passez de la page Versions à la page Intentions.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)
