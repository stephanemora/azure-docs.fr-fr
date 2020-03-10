---
title: Cycle de vie d’une base de connaissances – QnA Maker
description: QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 98fbd81baa717c981486f33cfb2b3a608cec27c7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914950"
---
# <a name="knowledge-base-lifecycle-in-qna-maker"></a>Cycle de vie d’une base de connaissances dans QnA Maker
QnA Maker apprend mieux dans un cycle itératif de modifications du modèle, d’énoncés d’exemples, de publication et de collecte des données à partir de requêtes du point de terminaison.

![Cycle de création](../media/qnamaker-concepts-lifecycle/kb-lifecycle.png)

## <a name="creating-a-qna-maker-knowledge-base"></a>Création d’une base de connaissances QnA Maker
Le point de terminaison de la base de connaissances QnA Maker recherche une réponse correspondent le mieux à une requête utilisateur dans le contenu de la base de connaissances. La création d’une base de connaissances est une action ponctuelle destinée à créer un référentiel contenant des questions, des réponses et des métadonnées associées. Vous pouvez créer une base de connaissances en y intégrant du contenu existant, tel que des pages FAQ, des manuels de produits ou des paires structurées de questions-réponses. Découvrez comment [créer une base de connaissances](../quickstarts/create-publish-knowledge-base.md).

## <a name="testing-and-updating-the-knowledge-base"></a>Test et mise à jour de la base de connaissances

La base de connaissances peut être testée dès qu’elle contient des données, qui ont été ajoutées manuellement ou par le biais d’une extraction automatique. Vous pouvez effectuer un test interactif à partir du panneau **Test** du portail QnA Maker en entrant les requêtes utilisateur courantes et en vérifiant que les réponses retournées sont correctes et affichent un score de confiance suffisant.

* **Pour améliorer les scores de confiance faibles** : ajoutez des questions alternatives.
* **Si une requête retourne par erreur la [réponse par défaut](../How-to/change-default-answer.md)**  : ajoutez de nouvelles réponses à la bonne question.

Poursuivez cette boucle de test-mise à jour jusqu’à ce que vous soyez satisfait des résultats. Découvrez comment [tester votre base de connaissances](../How-To/test-knowledge-base.md).

Pour les grandes bases de connaissances, utilisez les tests automatisés avec l’[API generateAnswer](../how-to/metadata-generateanswer-usage.md#get-answer-predictions-with-the-generateanswer-api) et la propriété de corps `isTest`, qui interroge la base de connaissances `test` au lieu de la base de connaissances publiée.

```json
{
  "question": "example question",
  "top": 3,
  "userId": "Default",
  "isTest": true
}
```

## <a name="publish-the-knowledge-base"></a>Publier la base de connaissances
Une fois que vous avez testé la base de connaissances, vous pouvez la publier. Le processus de publication envoie (push) la dernière version de la base de connaissances testée à un index de Recherche cognitive Azure dédié, qui représente la base de connaissances **publiée**. Il crée également un point de terminaison qui peut être appelé dans votre application ou bot conversationnel.

De cette manière, les modifications apportées à la version de test de la base de connaissances n’impactent pas la version publiée qui est potentiellement utilisée dans une application de production.

Chacune de ces bases de connaissances peut être ciblée séparément pour le test. À l’aide des API, vous pouvez cibler la version de test de la base de connaissances en utilisant la propriété de corps `isTest` dans l’appel generateAnswer.

Découvrez comment [publier votre base de connaissances](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="monitor-usage"></a>Surveiller l’utilisation
Pour pouvoir enregistrer les journaux d’activité de conversations du service, vous devez activer Application Insights quand vous [créez votre service QnA Maker](../How-To/set-up-qnamaker-service-azure.md).

Cela vous permet d’obtenir diverses analytiques sur l’utilisation de votre service. Découvrez comment utiliser les insights de l’application pour obtenir des [analytiques sur votre service QnA Maker](../How-To/get-analytics-knowledge-base.md).

Selon ce que vous apprenez des analytiques, effectuez les [mises à jour appropriées pour votre base de connaissances](../How-To/edit-knowledge-base.md).

## <a name="version-control-for-data-in-your-knowledge-base"></a>Contrôle de version des données de votre base de connaissances

La gestion des versions des données s’effectue par le biais de la fonctionnalité d’importation/exportation dans la page **Paramètres** du portail QnA Maker.

Vous pouvez sauvegarder une base de connaissances en l’exportant au format `.tsv` ou `.xls`. Une fois l’exportation effectuée, incluez ce fichier dans le cadre de votre vérification de contrôle de code source standard.

Si vous avez besoin de revenir à une version spécifique, vous devez importer ce fichier à partir de votre système local. Une base de connaissances exportée **doit** uniquement être utilisée par le biais de l’importation dans la page **Paramètres**. Elle ne peut pas être utilisée comme source de données de fichier ou de document d’URL. Les questions et réponses qui se trouvent actuellement dans la base de connaissances sont remplacées par le contenu du fichier importé.

## <a name="test-and-production-knowledge-base"></a>Base de connaissances de test et de production
Une base de connaissances est le référentiel d’ensembles de questions-réponses créés, tenus à jour et utilisés par le biais de QnA Maker. Chaque ressource QnA Maker peut contenir plusieurs bases de connaissances.

Une base de connaissances a deux états : *test* et *publiée*.

### <a name="test-knowledge-base"></a>Base de connaissances de test

La *base de connaissances de test* est la version actuellement modifiée, enregistrée et testée en lien avec la précision et l’exhaustivité des réponses. Les modifications apportées à la base de connaissances de test n’affectent pas l’utilisateur final de votre application ou bot conversationnel. La base de connaissances de test est connue comme `test` dans la requête HTTP. La base de connaissances de `test` est disponible dans le volet **Test** interactif du portail QnA Maker.

### <a name="production-knowledge-base"></a>Base de connaissances de production

La *base de connaissances publiée* est la version utilisée dans votre bot conversationnel ou application. La publication d’une base de connaissances a pour effet de placer le contenu de la base de connaissances de test dans la base de connaissance publiée. Étant donné que la base de connaissances publiée est la version que l’application utilise via le point de terminaison, assurez-vous que le contenu est correct et dûment testé. La base de connaissances de test est connue comme `prod` dans la requête HTTP.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Suggestions d’apprentissage actif](./active-learning-suggestions.md)