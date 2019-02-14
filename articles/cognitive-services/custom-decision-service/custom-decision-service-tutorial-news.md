---
title: 'Didacticiel : Personnalisation des articles - Service Décision personnalisée'
titlesuffix: Azure Cognitive Services
description: Tutoriel sur la personnalisation des articles pour une prise de décision contextuelle.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: tutorial
ms.date: 05/08/2018
ms.author: slivkins
ms.openlocfilehash: d8ddafe20ff93e7ae4d51e2180bbd40447729234
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/09/2019
ms.locfileid: "55983030"
---
# <a name="tutorial-article-personalization-for-contextual-decision-making"></a>Tutoriel : Personnalisation des articles pour la prise de décision contextuelle

Ce didacticiel est axé sur la personnalisation de la sélection d’articles sur la première page d’un site web. Le Service Décision personnalisée affecte *plusieurs* listes d’articles sur la première page par exemple. La page peut être un site web d’actualités qui traite uniquement de politique et de sport. Elle afficherait trois listes classées d’articles : politique, sports et nouveautés.

## <a name="applications-and-action-sets"></a>Applications et ensembles d’actions

Voici comment adapter votre scénario dans l’infrastructure. Imaginons trois applications, une pour chaque liste en cours d’optimisation : app-politics, app-sports et app-recent. Pour spécifier les articles possibles pour chaque application, il existe deux ensembles d’actions : un pour la politique et un pour les sports. L’ensemble d’actions pour app-recent provient automatiquement de l’union des deux autres ensembles.

> [!TIP]
> Les ensembles d’actions peuvent être partagés entre les applications du Service Décision personnalisée.

## <a name="prepare-action-set-feeds"></a>Préparer les flux d’ensemble d’actions

Le Service Décision personnalisée utilise des ensembles d’actions via des flux RSS ou Atom fournis par le client. Vous fournissez deux flux : un pour la politique et un pour les sports. Supposons qu’ils sont pris en charge à partir de `http://www.domain.com/feeds/<feed-name>`.

Chaque flux fournit une liste d’articles. Dans RSS, chacun est spécifié par un élément `<item>` comme suit :

```xml
<rss version="2.0"><channel>
   <item>
      <title><![CDATA[article title]]></title>
      <link>"article url"</link>
      <pubDate>publication date</pubDate>
    </item>
</channel></rss>
```

L’ordre des sujets d’articles. Il indique le classement par défaut, ce qui constitue la meilleure estimation possible de classement des articles. Le classement par défaut est ensuite utilisé pour comparer les performances dans le tableau de bord.

Pour plus d’informations sur le format de flux, consultez les [informations de référence sur les API](custom-decision-service-api-reference.md#action-set-api-customer-provided).

## <a name="register-a-new-app"></a>Inscription d’une nouvelle application

1. Connectez-vous à votre [compte Microsoft](https://portal.ds.microsoft.com/). Dans le ruban, cliquez sur **My Portal** (Mon portail).

2. Pour inscrire une nouvelle application, cliquez sur le bouton **Nouvelle application**.

    ![Portail du Service Décision personnalisée](./media/custom-decision-service-tutorial/portal.png)

3. Entrez un nom unique pour votre application dans la zone de texte **ID de l’application**. Si ce nom est déjà utilisé par un autre client, le système vous demande de choisir un autre ID d’application. Sélectionnez la case à cocher **Avancé**, puis entrez la [chaîne de connexion](../../storage/common/storage-configure-connection-string.md) de votre compte de stockage Azure. Vous utilisez généralement le même compte de stockage pour toutes vos applications.

    ![Boîte de dialogue Nouvelle application](./media/custom-decision-service-tutorial/new-app-dialog.png)

    Après avoir inscrit les trois applications dans le scénario ci-dessus, elles sont répertoriées :

    ![Liste des applications](./media/custom-decision-service-tutorial/apps.png)

    Vous pouvez revenir à cette liste en cliquant sur le bouton **Applications**.

4. Dans la boîte de dialogue **Nouvelle application**, spécifiez un flux d’actions. Les flux d’actions peuvent également être spécifiés en cliquant sur le bouton **Flux** puis en cliquant sur le bouton **Nouveau flux**. Entrez un **Nom** pour le nouveau flux, l’**URL** à partir de laquelle il est pris en charge, ainsi que la **Refresh Time** (Durée d’actualisation). La durée d’actualisation spécifie la fréquence à laquelle le Service Décision personnalisée doit actualiser le flux.

    ![Boîte de dialogue Nouveau flux](./media/custom-decision-service-tutorial/new-feed-dialog.png)

    Les flux d’actions peuvent être utilisés par n’importe quelle application, quel que soit l’emplacement où ils sont spécifiés. Lorsque vous avez spécifié les deux flux d’actions dans un scénario, ils sont répertoriés :

    ![Liste des flux](./media/custom-decision-service-tutorial/feeds.png)

    Vous pouvez revenir à cette liste en cliquant sur le bouton **Flux**.

## <a name="use-the-apis"></a>Utiliser les API

Le Service Décision personnalisée classe les articles via l’API de classement. Pour utiliser cette API, insérez le code suivant dans l’en-tête HTML de la première page :

```html
<!-- Define the "callback function" to render UI -->
<script> function callback(data) { … } </script>

<!--  call Ranking API after callback() is defined, separately for each app -->
<script src="https://ds.microsoft.com/api/v2/app-politics/rank/feed-politics" async></script>
<script src="https://ds.microsoft.com/api/v2/app-sports/rank/feed-sports" async></script>
<script src="https://ds.microsoft.com/api/v2/app-recent/rank/feed-politics/feed-sports" async></script>
<!-- NB: action feeds for 'app-recent' are listed one after another. -->
```

La réponse HTTP de l’API de classement est une chaîne au format JSONP. Pour app-politics, par exemple, la chaîne ressemble à :

```json
callback({
   "ranking":[{"id":"url1"}, {"id":"url2"}, {"id":"url3"}],
   "eventId":"<opaque event string>",
   "appId":"app-politics",
   "actionSets":[{"id":"feed-politics","lastRefresh":"date"}] });
```

Le navigateur exécute ensuite cette chaîne en tant qu’appel à la fonction `callback()`. L’argument `data` dans la fonction `callback()` contient l’ID de l’application et le classement des URL à afficher. `callback()` doit plus particulièrement utiliser `data.appId` pour faire la distinction entre les trois applications. `eventId` est utilisé en interne par le Service Décision personnalisée pour faire correspondre le classement fourni avec le clic correspondant, le cas échéant.

> [!TIP]
> `callback()` peut vérifier l’actualisation de chaque flux d’actions à l’aide du champ `lastRefresh`. Si un flux donné n’est pas suffisamment actuel, `callback()` peut ignorer le classement fourni, appeler ce flux directement et utiliser le classement par défaut pris en charge par le flux.

Pour plus d’informations sur les spécifications et options supplémentaires fournies par l’API de classement, consultez les [informations de référence sur les API](custom-decision-service-api-reference.md).

Les premiers choix d’articles de l’utilisateur sont retournés en appelant l’API de récompense. Lorsqu’un premier choix d’article principal est reçu, le code suivant doit être appelé sur la première page :

```javascript
$.ajax({
    type: "POST",
    url: '//ds.microsoft.com/api/v2/<appId>/reward/<eventId>',
    contentType: "application/json" })
```

L’utilisation de `appId` et `eventId` dans le code de gestion des clics requiert une attention particulière. Par exemple, vous pouvez implémenter la fonction `callback()` comme suit :

```javascript
function callback(data) {
    $.map(data.ranking, function (element) {
        //custom rendering function given content info
        render({appId:data.appId,
                article:element,
                onClick: element.id != data.rewardAction ? null :
                   function() {
                       $.ajax({
                       type: "POST",
                       url: '//ds.microsoft.com/api/v2/' + data.appId + '/reward/' + data.eventId,
                       contentType: "application/json" })}
                });
}}
```

Dans cet exemple, implémentez la fonction `render()` pour afficher un article donné pour une application donnée. Cette fonction transmet l’ID de l’application et l’article (dans le format de l’API de classement). Le paramètre `onClick` correspond à la fonction qui doit être appelée à partir de `render()` pour gérer un clic. Il vérifie si le clic se trouve sur le premier emplacement. Il appelle ensuite l’API de récompense avec l’ID de l’application et l’ID d’événement appropriés.

## <a name="next-steps"></a>Étapes suivantes

* Consultez les [informations de référence sur les API](custom-decision-service-api-reference.md) pour en savoir plus sur les fonctionnalités fournies.
