---
title: Procédure d’importation d’application
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: c0253360c66ef6fd995f65e8a83ba5adcdf19543
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806409"
---
1. Dans le [portail LUIS en préversion](https://preview.luis.ai), dans la page **Mes applications**, sélectionnez **Importer**, puis **Importer en tant que JSON**. Recherchez le fichier JSON enregistré à l’étape précédente. Vous n’avez pas besoin de modifier le nom de l’application. Sélectionnez **Terminé**

1. Dans la section **Gérer**, sous l’onglet **Versions**, sélectionnez la version et sélectionnez **Cloner** pour cloner la version. Attribuez-lui un nom de 10 caractères, puis sélectionnez **Terminé** pour terminer le processus de clonage. Étant donné que le nom de la version est utilisé dans le cadre de la route d’URL, il ne peut pas contenir de caractères qui ne sont pas valides dans une URL.

    > [!TIP]
    > Le clonage d’une version dans une nouvelle version est considéré comme une bonne pratique avant la modification de votre application. Lorsque vous en avez terminé avec une version, exportez-la (au format .json ou .lu), puis vérifiez le fichier dans votre système de contrôle de code source.

1. Sélectionnez **Générer** puis **Intentions** pour voir les intentions, qui sont les principaux composants d’une application LUIS.

    ![Passez de la page Versions à la page Intentions.](../media/tutorial-machine-learned-entity/new-version-imported-app.png)