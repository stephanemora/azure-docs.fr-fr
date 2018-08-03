---
title: Forum Aux Questions sur Language Understanding (LUIS) dans Azure | Microsoft Docs
description: Découvrez les réponses aux questions fréquemment posées sur Language Understanding (LUIS)
author: diberry
manager: cjgronlund
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: diberry
ms.openlocfilehash: 8e0d834b94ff902eb0c1e0ada2fb32d374cee12b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239115"
---
# <a name="language-understanding-faq"></a>FAQ sur Language Understanding

Cet article contient des réponses aux questions fréquemment posées sur Language Understanding (LUIS).

## <a name="luis-authoring"></a>Création LUIS

### <a name="what-are-the-luis-best-practices"></a>Quelles sont les meilleures pratiques de LUIS ? 
Commencez par le [Cycle de création](luis-concept-app-iteration.md), puis lisez les [meilleures pratiques](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Quel est la meilleure manière de commencer à créer mon application dans LUIS ?

La meilleure façon de créer votre application consiste à recourir à un [traitement incrémentiel](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Qu’est une bonne pratique pour modéliser les intentions de mon application ? Dois-je créer des intentions plus spécifiques ou plus génériques ?

Choisissez des intentions qui ne soient ni générales au point de se chevaucher, ni spécifiques au point de rendre difficile pour LUIS de faire la distinction entre des intentions similaires. La création d’intentions spécifiques discriminantes est l’une des meilleures pratiques pour la modélisation de LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Est-il important de former l’intention None ?

Oui, il est conseillé de former votre intention **None** avec davantage d’énoncés à mesure que vous ajoutez des étiquettes à d’autres intentions. Un bon rapport est d’ajouter 1 ou 2 étiquettes à l’intention **None** pour chaque dizaine d’étiquettes ajoutées à une intention. Cet rapport augmente la puissance discriminante de LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Comment corriger les fautes d’orthographe dans des énoncés ?

Consultez le didacticiel sur l’[API Vérification orthographique Bing v7](luis-tutorial-bing-spellcheck.md). LUIS applique les limites imposées par l’API Vérification orthographique v7. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Comment faire pour modifier mon application LUIS par programmation ?
Pour modifier votre application LUIS par programmation, utilisez l’[API Création](https://aka.ms/luis-authoring-apis). Pour obtenir des exemples d’appel de l’API Création, voir [Appel de l’API Création de LUIS](./luis-quickstart-node-add-utterance.md) et [Créer une application LUIS par programmation à l’aide de Node.js](./luis-tutorial-node-import-utterances-csv.md). L’API Création exige que vous utilisiez une [clé de création](luis-concept-keys.md#authoring-key) plutôt qu’une clé de point de terminaison. La création par programmation permet jusqu’à 1 000 000 d’appels par mois et cinq transactions par seconde. Pour plus d’informations sur les clés à utiliser avec LUIS, voir [Gérer les clés](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Où est la fonctionnalité Modèle qui assurait la mise en correspondance d’expression régulière ?
La **fonctionnalité Modèle** précédente est actuellement désapprouvée. Elle est remplacée par **[Modèles](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Comment utiliser une entité pour extraire les données correctes ? 
Voir [Entités](luis-concept-entity-types.md) et [Extraction de données](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Les variantes d’un exemple d’énoncé incluent-elles des signes de ponctuation ? 
Ajoutez les différentes variantes en tant qu’exemples d’énoncés à l’intention ou ajoutez le modèle de l’exemple d’énoncé avec la [syntaxe pour ignorer](luis-concept-patterns.md#pattern-syntax) les signes de ponctuation. 

### <a name="does-luis-currently-support-cortana"></a>LUIS prend-il en charge Cortana ?

Les applications prédéfinies Cortana sont déconseillées depuis 2017. Elles ne sont plus prises en charge. 

## <a name="luis-endpoint"></a>Point de terminaison LUIS

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Pourquoi LUIS ajoute-t-il des espaces à la requête autour ou au milieu de mots ?
LUIS [tokénise](luis-glossary.md#token) l’énoncé en fonction de la [culture](luis-supported-languages.md#tokenization). Tant la valeur d’origine que la valeur tokénisée sont disponibles pour l’[extraction de données](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Comment faire pour créer et affecter une clé de point de terminaison LUIS ?
[Créez la clé de point de terminaison](luis-how-to-azure-subscription.md#create-luis-endpoint-key) dans Azure pour votre niveau de [service](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). [Affectez la clé](luis-how-to-manage-keys.md#assign-endpoint-key) sur la page  **[Publier](luis-how-to-publish-app.md)**. Il n’existe d’API pour cette action. Ensuite, vous devez modifier la requête HTTP adressée au point de terminaison de façon à [utiliser la nouvelle clé de point de terminaison](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Comment interpréter les scores de LUIS ? 
Votre système doit utiliser l’intention de score le plus élevée, quelle qu’en soit la valeur. Par exemple, un score inférieur à 0,5 (moins de 50 %) ne signifie pas nécessairement que la confiance de LUIS est faible. La fourniture de données d’apprentissage supplémentaires peut aider à augmenter le score de l’intention la plus probable.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Pourquoi ne vois-je pas les accès à mon point de terminaison sur le tableau de bord de mon application ?
Le nombre total d’accès de point de terminaison est régulièrement mis à jour sur le tableau de bord de l’application, mais la fréquence de mise à jour est plus élevée pour les métriques associées à la clé du point de terminaison LUIS sur le Portail Azure. 

Si vous ne voyez pas les accès de point de terminaison mis à jour sur le tableau de bord, connectez-vous au Portail Azure et trouvez la ressource associée à la clé de votre point de terminaison LUIS, puis ouvrez **Métriques** pour sélectionner la métrique **Nombre total d’appels**. Si la clé de point de terminaison est utilisée pour plusieurs applications LUIS, la métrique du Portail Azure indique le nombre agrégé d’appels provenant de toutes les applications LUIS qui l’utilisent.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Mon application LUIS fonctionnait hier, et aujourd’hui j’obtiens des erreurs 403. Je n’ai pas modifié l’application. Comment la corriger ? 
En suivant les [instructions](#how-do-i-create-and-assign-a-luis-endpoint-key) du FAQ suivant pour créer une clé de point de terminaison LUIS et l’affecter à l’application. Ensuite, vous devez modifier la requête HTTP adressée au point de terminaison de façon à [utiliser la nouvelle clé de point de terminaison](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Comment faire pour sécuriser mon point de terminaison LUIS ? 
Voir [Sécurisation du point de terminaison](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Respect des limites de LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Quel est le nombre maximal d’intentions et d’entités qu’une application LUIS peut prendre en charge ?
Voir la référence sur les [limites](luis-boundaries.md).

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Je souhaite créer une application LUIS avec un nombre d’intentions supérieur au nombre maximal. Que dois-je faire ?

Voir [Meilleures pratiques pour les intentions](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Je souhaite créer une application dans LUIS avec un nombre d’entités supérieur au nombre maximal. Que dois-je faire ?

Voir [Meilleures pratiques pour les entités](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities).

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Quelles sont les limites de nombre et de taille des listes de phrases ?
Pour connaître la longueur maximale d’une [liste de phrases](./luis-concept-feature.md), voir la référence [limites](luis-boundaries.md).

### <a name="what-are-the-limits-on-example-utterances"></a>Quelles sont les limites applicables aux exemples d’énoncés ?
Voir la référence sur les [limites](luis-boundaries.md).

## <a name="testing-and-training"></a>Test et apprentissage

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Je constate des erreurs dans le volet des tests par lot pour certains modèles dans mon application. Comment puis-je résoudre ce problème ?

Les erreurs indiquent l’existence d’incohérences entre vos étiquettes et les prédictions effectuées à partir de vos modèles. Pour résoudre le problème, accomplissez une ou plusieurs des tâches suivantes :
* Pour aider LUIS à améliorer la discrimination entre les intentions, ajoutez des étiquettes.
* Pour aider LUIS à apprendre plus rapidement, ajoutez des fonctionnalités de liste de phrases qui introduisent le vocabulaire spécifique d’un domaine.

Voir le didacticiel [Tests par lot](luis-tutorial-batch-testing.md).

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Quand une application est exportée, puis réimportée dans une nouvelle application (avec un nouvel ID d’application), les scores de prédiction LUIS sont différents. Pourquoi cela se produit-il ? 

Voir [Différences de prédiction entre les copies d’une même application](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Publication d’application

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Qu’est-ce que l’ID de locataire dans la fenêtre « Add a key to your app » (Ajouter une clé à votre application) ?
Dans Azure, un locataire représente le client ou l’organisation associés à un service. Cherchez votre ID de locataire sur le portail Azure dans la zone **ID de répertoire** en sélectionnant **Azure Active Directory** > **Gérer** > **Propriétés**.

![ID de locataire sur le portail Azure](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
### <a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Pourquoi y a-t-il plus de clés de point de terminaison sur la page de publication de mon application que je ne lui en ai attribué ? 
Chaque application LUIS possède la clé authoring/starter. Les clés de point de terminaison LUIS créées pendant la période de disponibilité générale sont visibles sur votre page de publication, que vous les ayez ajoutées ou non à l’application. Cela a été conçu pour faciliter la migration pendant la période de disponibilité générale. Les nouvelles clés de point de terminaison LUIS n’apparaissent pas sur la page de publication. 

## <a name="app-management"></a>Gestion des applications

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Comment faire pour transférer la propriété d’une application LUIS ?
Pour transférer une application LUIS vers un autre abonnement Azure, exportez l’application LUIS et importez-la à l’aide d’un nouveau compte. Mettez à jour l’ID de l’application LUIS dans l’application cliente qui l’appelle. La nouvelle application peut renvoyer des scores LUIS légèrement différents de ceux renvoyés par l’application d’origine. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Comment faire pour télécharger un journal d’énoncés d’utilisateurs ?
Par défaut, votre application LUIS journalise les énoncés des utilisateurs. Pour télécharger un journal des énoncés que les utilisateurs envoient à votre application LUIS, accédez à **Mes applications**, puis cliquez sur les points de suspension (***...*** ) dans la liste correspondant à votre application. Cliquez ensuite sur **Export Endpoint Logs** (Exporter les journaux du point de terminaison). Un journal est mis en forme comme un fichier de valeurs séparées par des virgules (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Comment puis-je désactiver la journalisation des énoncés ?
Vous pouvez désactiver la journalisation des énoncés des utilisateurs en définissant `log=false` dans l’URL de point de terminaison que votre application cliente utilise pour la interroger LUIS. Toutefois, la désactivation de la journalisation désactive la capacité de votre application LUIS à suggérer des énoncés ou à améliorer les performances sur la base d’un [apprentissage actif](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Si vous définissez `log=false` en raison de problèmes de confidentialité des données, vous ne pouvez pas télécharger un enregistrement de ces énoncés d’utilisateurs à partir de LUIS ou utiliser ces énoncés pour améliorer votre application.

La journalisation est l’unique mode de stockage des énoncés. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Qu’est-ce qui peut justifier que je ne journalise pas tous les énoncés de mon point de terminaison ?
Si vous utilisez votre journal à des fins d’analyse prédictive, ne capturez pas énoncés de test dans votre journal.

## <a name="data-management"></a>Gestion des données

### <a name="can-i-delete-data-from-luis"></a>Puis-je supprimer des données de LUIS ? 

* Vous pouvez toujours supprimer des exemples d’énoncés utilisés pour l’apprentissage de LUIS. Si vous supprimez un exemple d’énoncé de votre application LUIS, il est supprimé du service web LUIS et n’est plus disponible pour l’exportation.
* Vous pouvez supprimer des énoncés de la liste des énoncés d’utilisateurs que LUIS suggère dans la page **Review endpoint utterances** (Examiner les énoncés du point de terminaison). La suppression d’énoncés de cette liste a pour effet d’empêcher leur suggestion, mais pas de les supprimer des journaux.
* Si vous supprimez un compte, toutes les applications sont supprimées, ainsi que leurs exemples d’énoncés et journaux. Les données sont conservées sur les serveurs pendant 60 jours avant leur suppression définitive.

## <a name="language-and-translation-support"></a>Prise en charge des langues et de la traduction 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Je dispose d’une application dans une langue et souhaite créer une application parallèle dans une autre langue. Quel est le moyen le plus simple de procéder ?
1. Exportez votre application.
2. Traduisez les énoncés étiquetés dans le fichier JSON de l’application exportée vers la langue cible.
3. Vous pouvez modifier les noms des intentions et entités ou les laisser tels quels.
4. Enfin, importez l’application pour obtenir une application LUIS dans la langue cible.

## <a name="app-notification"></a>Notification d’application

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Pourquoi ai-je reçu un e-mail indiquant que je suis presque hors quota ?
Votre clé authoring/starter n’autorise que 1 000 requêtes sur le point de terminaison par mois. Créez une clé de point de terminaison LUIS (gratuite ou payante) et utilisez-la pour effectuer des requêtes sur le point de terminaison. Si vous effectuez des requêtes sur le point de terminaison à partir d’un bot ou d’une autre application cliente, vous devez y modifier la clé de point de terminaison LUIS. 

## <a name="integrating-luis"></a>Intégration de LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Où est l’application LUIS que j’ai créée durant le processus d’abonnement de bot d’application web Azure ?
Si vous choisissez un modèle LUIS, puis le bouton **Sélectionner** dans le volet des modèles, le volet du côté gauche change pour inclure le type de modèle, et vous êtes invité à spécifier la région dans laquelle vous voulez créer le modèle LUIS. Le processus d’abonnement de bot d’application web ne crée cependant pas d’abonnement LUIS.

![Région du bot d’application web de modèle LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Quelles régions LUIS prennent en charge la préparation vocale de Bot Framework ?
La [préparation vocale](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) est prise en charge uniquement pour les applications LUIS dans l’instance centrale (États-Unis). 

## <a name="luis-service"></a>Service LUIS 

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>LUIS est-il disponible en local ou dans un cloud privé ?
Non. 

## <a name="changes-to-the-docs"></a>Modifications apportées à la documentation

### <a name="where-did-the-tutorials-go"></a>Où sont les didacticiels ? 
Les articles qui étaient précédemment accessibles dans la section Didacticiel de la documentation sont désormais dans la section Procédures. 

|Didacticiel|
|--|
|Intégrer LUIS avec un bot à l’aide de [C#](luis-csharp-tutorial-build-bot-framework-sample.md) et [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Ajouter Application Insights à un bot à l’aide de [C#](luis-tutorial-bot-csharp-appinsights.md) et [Node.js](luis-tutorial-function-appinsights.md)|
|Créer une application LUIS par programmation à l’aide de [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Utiliser une [entité composite](luis-tutorial-composite-entity.md) pour extraire des données groupées|
|Ajouter une [entité list](luis-tutorial-list-entity.md) pour accroître la détection d’entité à l’aide de Node.js|
|Améliorer la précision de prédiction avec une [liste de phrases](luis-quickstart-primary-and-secondary-data.md), des [modèles](luis-tutorial-pattern.md), et des [tests par lot](luis-tutorial-batch-testing.md)|
|[Corriger l’orthographe](luis-tutorial-batch-testing.md) avec l’API Vérification orthographique Bing v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>Lors de la conférence Build 2018, j’ai entendu parler d’une fonctionnalité ou d’une démonstration de Language Understanding, mais je ne me rappelle pas son nom 

Les fonctionnalités publiées lors de la conférence Build 2018 sont les suivantes :

|NOM|Contenu|
|--|--|
|Améliorations|Entité [Expression régulière](luis-concept-data-extraction.md##regular-expression-entity-data) et entité [Phrase clé](luis-concept-data-extraction.md#key-phrase-extraction-entity-data)
|Modèles|Modèles [concept](luis-concept-patterns.md), [didacticiel](luis-tutorial-pattern.md), [procédure](luis-how-to-model-intent-pattern.md)<br>Concept d’entité [Patterns.Any](luis-concept-entity-types.md) incluant une [Liste explicite](luis-concept-patterns.md#explicit-lists) pour les exceptions<br>Concept [Rôles](luis-concept-roles.md)|
|Intégrations|Intégration dans l’[analyse de texte](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) de l’[analyse des sentiments](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>Intégration dans [Speech](https://docs.microsoft.com/azure/cognitive-services/speech) de la [préparation vocale](luis-how-to-publish-app.md#enable-speech-priming) ainsi que du [Kit de développement logiciel (SDK) Speech](https://aka.ms/SpeechSDK)|
|Outil Répartition|Intégré dans [BotBuilder-tools](https://github.com/Microsoft/botbuilder-tools), l’[outil](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) en ligne de commande Répartition permet de combiner plusieurs applications LUIS et QnA Maker dans une application LUIS unique pour améliorer la reconnaissance des intentions dans un bot

Des [itinéraires d’API](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) de création supplémentaires ont été inclus. 

Vidéos : 
* [Azure Friday At Build 2018: Cognitive Services - Language (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - What’s New with Language Understanding Service](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Build 2018 Session - Bot intelligence, Speech Capabilities, and NLU best practices](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS Updates](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projets : 
* Démo du [bot de Contoso Cafe](https://github.com/botbuilderbuild2018/build2018demo) - code source sur GitHub

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur LUIS, voir les ressources suivantes :
* [Questions de Stack Overflow balisées avec LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Forum Language Understanding Intelligent Services (LUIS) de MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 