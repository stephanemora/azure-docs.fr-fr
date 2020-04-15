---
title: Procédure d’importation d’application
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422757"
---
1. Sur le [portail LUIS en préversion](https://preview.luis.ai), dans la page **Mes applications**, sélectionnez **+ Nouvelle application de conversation**, puis **Importer en tant que JSON**. Recherchez le fichier JSON enregistré à l’étape précédente. Vous n’avez pas besoin de modifier le nom de l’application. Sélectionnez **Terminé**

1. Dans la section **Gérer**, sous l’onglet **Versions**, sélectionnez la version `0.1`, puis choisissez **Cloner** pour cloner la version. Attribuez-lui ensuite un nouveau nom de `ml-entity` de 10 caractères, puis sélectionnez **Terminer** pour terminer le processus de clonage. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

    > [!TIP]
    > Le clonage dans une nouvelle version est considéré comme une bonne pratique avant la modification de votre application. Quand vous en avez terminé avec la modification d’une version, exportez-la (sous forme de fichier .json ou .lu), puis archivez le fichier dans votre système de contrôle de code source.

1. Sélectionnez **Générer** puis **Intentions** pour voir les intentions, qui sont les principaux composants d’une application LUIS.

    ![Passez de la page Versions à la page Intentions.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)