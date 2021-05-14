---
title: Comment contribuer aux widgets du portail des développeurs
titleSuffix: Azure API Management
description: Découvrez les instructions recommandées à suivre lorsque vous contribuez à un widget dans le référentiel du portail des développeurs Gestion des API.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741205"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Comment contribuer aux widgets sur le portail des développeurs Gestion des API

Si vous souhaitez fournir un widget au [référentiel GitHub](https://github.com/Azure/api-management-developer-portal) du portail des développeurs Gestion des API, suivez ce processus en trois étapes :

1. Dupliquez (fork) le dépôt.

1. Implémentez le widget.

1. Ouvrez une demande de tirage pour inclure votre widget dans le référentiel officiel.

Votre widget héritera de la licence du référentiel. Il sera disponible pour l’[installation avec abonnement](developer-portal-use-community-widgets.md) dans la version auto-hébergée du portail. L’équipe du portail des développeurs peut décider de l’inclure également dans la version gérée du portail.

Reportez-vous au tutoriel sur l’[implémentation des widgets](developer-portal-implement-widgets.md) pour obtenir un exemple de développement de votre propre widget.

## <a name="contribution-guidelines"></a>Instructions de contribution

Ce guide est destiné à garantir la sécurité et la confidentialité de nos clients et des visiteurs de leurs portails. Suivez ces instructions pour vous assurer que votre contribution est acceptée :

1. Placez votre widget dans le dossier `community/widgets/<your-widget-name>`.

1. Le nom de votre widget doit être en caractères minuscules et alphanumériques, des tirets séparant les mots. Par exemple : `my-new-widget`.

1. Le dossier doit contenir une capture d’écran de votre widget dans un portail publié.

1. Le dossier doit contenir un fichier `readme.md`, qui suit le modèle du fichier `/scaffolds/widget/readme.md`.

1. Le dossier peut contenir un fichier `npm_dependencies` avec des commandes npm pour installer ou gérer les dépendances du widget.

    Spécifiez explicitement la version de chaque dépendance. Par exemple :  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Votre widget doit exiger des dépendances minimales. Toutes les dépendances seront soigneusement inspectées par les réviseurs. En particulier, la logique principale de votre widget doit être open source dans le dossier de votre widget. Ne l’encapsulez pas dans un package npm.

1. Les modifications apportées aux fichiers en dehors du dossier de votre widget ne sont pas autorisées dans le cadre d’une contribution de widget. Cela comprend notamment (mais pas seulement) le fichier `/package.json`.

1. L’injection de scripts de suivi ou l’envoi de données créées par le client à des services personnalisés n’est pas autorisé.

    > [!NOTE]
    > Vous pouvez uniquement collecter les données créées par le client par le biais de l’interface `Logger`.

## <a name="next-steps"></a>Étapes suivantes

- Pour plus d’informations sur les contributions, consultez le [référentiel GitHub](https://github.com/Azure/api-management-developer-portal/) du portail des développeurs Gestion des API.

- Consultez [Implémenter des widgets](developer-portal-implement-widgets.md) pour apprendre à développer votre propre widget, étape par étape.

- Consultez [Utiliser les widgets de la communauté](developer-portal-use-community-widgets.md) pour apprendre à utiliser les widgets fournis par la communauté.