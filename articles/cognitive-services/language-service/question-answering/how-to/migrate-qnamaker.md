---
title: Migrer des bases de connaissances QnA Maker vers des réponses aux questions personnalisées
description: Migrez vos bases de connaissances QnAMaker héritées vers des questions aux réponses personnalisées pour tirer parti des fonctionnalités les plus récentes.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: how-to
ms.author: diagarw
author: DishaAgarwal
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 2ffd43a2747d462257b4223285b12b5f81e3e4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131097725"
---
# <a name="migrate-from-qna-maker-to-custom-question-answering"></a>Migrer de QnA Maker vers les réponses aux questions personnalisées

La fonctionnalité de réponses aux questions personnalisées a été introduite en mai 2021 avec plusieurs nouvelles fonctionnalités, dont une meilleure pertinence à l’aide d’un ranker de Deep Learning, des réponses précises et une prise en charge régionale de bout en bout. Chaque projet de réponses aux questions personnalisées est équivalent à une base de connaissances dans QnA Maker. Vous pouvez facilement migrer des bases de connaissances d’une ressource QnA Maker vers des projets de réponses aux questions personnalisées au sein d’une [ressource de langue](https://aka.ms/create-language-resource). Vous pouvez également choisir de migrer des bases de connaissances de plusieurs ressources QnA Maker vers une ressource de langue spécifique.

Pour migrer les bases de connaissances, **le compte qui effectue la migration a besoin d’un accès Contributeur à l’instance QnA Maker et à la ressource de langue sélectionnées**. Quand une base de connaissances est migrée, les détails suivants sont copiés dans le nouveau projet de réponses aux questions personnalisées :

- Paires QnA, notamment les suggestions d’apprentissage actif.
- Synonymes et réponse par défaut de la ressource QnA Maker.
- Le nom de la base de connaissances est copié dans le champ Description du projet.

Les paramètres au niveau des ressources, tels que le contrôle d’accès en fonction du rôle (RBAC), ne sont pas migrés vers la nouvelle ressource. Ces paramètres au niveau des ressources doivent être reconfigurés pour la ressource de langue après la migration. Vous devez également [réactiver les analytiques](analytics.md) pour la ressource de langue.

## <a name="steps-to-migrate"></a>Étapes de migration

Vous pouvez suivre les étapes ci-dessous pour migrer les bases de connaissances :

1. Créez une [ressource de langue](https://aka.ms/create-language-resource) avec les réponses aux questions personnalisées activées à l’avance. Lorsque vous créez la ressource de langue dans le portail Azure, vous voyez l’option permettant d’activer les réponses aux questions personnalisées. Lorsque vous sélectionnez cette option et que vous continuez, vous êtes invité à indiquer les détails de Recherche Azure pour enregistrer les bases de connaissances.

2. Si vous souhaitez ajouter des bases de connaissances en plusieurs langues à votre ressource de langue, visitez [Language Studio](https://lanuage.azure.com) pour créer votre premier projet de réponses aux questions personnalisées et sélectionnez la première option, comme indiqué ci-dessous. Les paramètres de langue de la ressource de langue ne peuvent être spécifiés que lors de la création d’un projet. Si vous souhaitez migrer des bases de connaissances existantes dans une langue unique vers la ressource de langue, vous pouvez ignorer cette étape.

   > [!div class="mx-imgBorder"]
   > ![Capture d’écran de l’écran Choisir la langue de l’interface utilisateur](../media/migrate-qnamaker/choose-language.png)

3. Visitez [https://www.qnamaker.ai](https://www.qnamaker.ai) et sélectionnez **Démarrer la migration** dans la note de migration de la page de la base de connaissances. Une boîte de dialogue s’ouvre pour lancer la migration.

   :::image type="content" source="../media/migrate-qnamaker/start-migration.png" alt-text="Bouton Démarrer la migration qui apparaît dans une bannière sur qnamaker.ai" lightbox="../media/migrate-qnamaker/start-migration.png":::

4. Renseignez les détails nécessaires pour lancer la migration. Le locataire est sélectionné automatiquement. Vous pouvez choisir de changer de locataire.

   > [!div class="mx-imgBorder"]
   > ![Migrer QnAMaker avec la zone de sélection rouge autour de l’option de sélection de locataire](../media/migrate-qnamaker/tenant-selection.png)

5. Sélectionnez la ressource QnA Maker, qui contient les bases de connaissances à migrer.

   > [!div class="mx-imgBorder"]
   > ![Migrer QnAMaker avec la zone de sélection rouge autour de l’option de sélection de ressource QnAMaker](../media/migrate-qnamaker/select-resource.png)

6. Sélectionnez la ressource de langue vers laquelle vous souhaitez migrer les bases de connaissances. Vous ne pouvez voir que les ressources de langue pour lesquelles des réponses aux questions personnalisées sont activées. Le paramètre de langue de la ressource de langue s’affiche dans les options. Vous ne pouvez pas migrer des bases de connaissances en plusieurs langues de ressources QnA Maker vers une ressource de langue si le paramètre de langue n’est pas spécifié.

   > [!div class="mx-imgBorder"]
   > ![Migrer QnAMaker avec la zone de sélection rouge autour de l’option de ressource de langue. La ressource actuellement sélectionnée contient les informations indiquant que la langue n’est pas spécifiée](../media/migrate-qnamaker/language-setting.png)

    Si vous souhaitez migrer des bases de connaissances en plusieurs langues vers la ressource de langue, vous devez activer le paramètre de langue multiple lors de la création du premier projet de réponses aux questions personnalisées pour la ressource de langue. Pour ce faire, suivez les instructions de l’étape 2. **Si le paramètre de langue de la ressource de langue n’est pas spécifié, la langue de la ressource QnA Maker sélectionnée lui est attribuée**.

7. Sélectionnez toutes les bases de connaissances que vous souhaitez migrer, puis sélectionnez **Suivant**.

   > [!div class="mx-imgBorder"]
   > ![Migrer QnAMaker avec la zone de sélection rouge autour de l’option de sélection de la base de connaissances avec une liste déroulante affichant trois noms de la base de connaissances](../media/migrate-qnamaker/select-knowledge-bases.png)

8. Vous pouvez examiner les bases de connaissances que vous envisagez de migrer. Il peut y avoir des erreurs de validation dans les noms de projet, car nous suivons des règles de validation plus strictes pour les projets de réponses aux questions personnalisées. 

    > [!CAUTION]
    > Si vous migrez une base de connaissances portant le même nom qu’un projet qui existe déjà dans la ressource de langue cible, **le contenu du projet est remplacé** par le contenu de la base de connaissances sélectionnée.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran d’un message d’erreur indiquant que les noms de projets ne peuvent pas contenir de caractères spéciaux](../media/migrate-qnamaker/special-characters.png)

9. Après avoir résolu les erreurs de validation, sélectionnez **Suivant**.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran avec suppression des caractères spéciaux](../media/migrate-qnamaker/validation-errors.png)

10. La migration peut prendre quelques minutes. N’annulez pas la migration pendant qu’elle est en cours. Vous pouvez accéder aux projets migrés dans [Language Studio](https://lanuage.azure.com) après la migration.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran de la migration réussie de bases de connaissances avec des informations que vous pouvez publier à l’aide de Language Studio](../media/migrate-qnamaker/migration-success.png)

    Si la migration d’une base de connaissances vers des projets de réponses aux questions personnalisées échoue, une erreur s’affiche. Les erreurs de migration les plus courantes se produisent dans les cas suivants :
    
    - Vos ressources source et cible ne sont pas valides.
    - Vous essayez de migrer une base de connaissances vide.
    - Vous avez atteint la limite d’une instance Recherche Azure liée à vos ressources cibles.

    > [!div class="mx-imgBorder"]
    > ![Capture d’écran d’un échec de migration avec un exemple d’erreur](../media/migrate-qnamaker/migration-errors.png)

    Une fois ces erreurs résolues, vous pouvez relancer la migration.

11. La migration copie uniquement les instances de test de vos bases de connaissances. Une fois la migration terminée, vous devez déployer manuellement les bases de connaissances pour copier l’index de test dans l’index de production.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment réactiver les analytiques avec les [journaux de diagnostic Azure Monitor](analytics.md).
