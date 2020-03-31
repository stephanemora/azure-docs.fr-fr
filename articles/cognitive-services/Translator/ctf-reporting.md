---
title: Compte-rendu CTF (Collaborative Translation Framework) - API de traduction de texte Translator Text
titleSuffix: Azure Cognitive Services
description: Explique comment utiliser le compte-rendu CTF (Collaborative Translation Framework).
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595941"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Guide pratique pour utiliser le compte-rendu CTF (Collaborative Translation Framework)

> [!NOTE]
> Cette méthode est déconseillée. Elle n’est pas disponible dans la version 3.0 de l’API de traduction de texte Translator Text.
> 
> L’infrastructure CTF (Collaborative Translation Framework), disponible pour la version 2.0 de l’API de traduction de texte Translator Text, a été dépréciée à compter du 1er février 2018. Les fonctions AddTranslation et AddTranslationArray permettent aux utilisateurs d’activer les corrections via l’infrastructure CTF. Depuis le 31 janvier 2018, ces deux fonctions n’ont pas accepté de nouvelles soumissions de phrases, et les utilisateurs reçoivent un message d’erreur. Ces fonctions ont été mises hors service et ne seront pas remplacées.

L’API de compte-rendu de l’infrastructure CTF (Collaborative Translation Framework) retourne des statistiques et le contenu réel du magasin CTF. Cette API est différente de la méthode GetTranslations(), car elle :
* Retourne le contenu traduit et son nombre total uniquement à partir de votre compte (compte Place de marché Microsoft Azure ou appId).
* Retourne le contenu traduit et son nombre total sans exiger une mise en correspondance de la phrase source.
* Ne retourne pas la traduction automatique (traduction automatique).

## <a name="endpoint"></a>Point de terminaison
Point de terminaison de l’API de compte-rendu CTF https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Méthodes
| Nom |    Description|
|:---|:---|
| Méthode GetUserTranslationCounts | Permet d’obtenir les nombres des traductions créées par l’utilisateur. |
| Méthode GetUserTranslations | Récupère les traductions créées par l’utilisateur. |

Ces méthodes vous permettent de :
* Récupérer l’ensemble complet des traductions de l’utilisateur et des corrections sous votre ID de compte pour les télécharger.
* Obtenir la liste des contributeurs fréquents. Assurez-vous que le nom d’utilisateur correct est fourni dans AddTranslation().
* Générer une interface utilisateur (IU) qui permet aux utilisateurs approuvés de voir tous les candidats disponibles, limités si nécessaire à une partie de votre site, en fonction du préfixe d’URI.

> [!NOTE]
> Ces deux méthodes sont relativement lentes et coûteuses. Il est recommandé de les utiliser avec parcimonie.

## <a name="getusertranslationcounts-method"></a>Méthode GetUserTranslationCounts

Cette méthode permet d’obtenir le nombre des traductions qui sont créées par l’utilisateur. Elle fournit la liste des nombres de traductions regroupés par les paramètres de demande uriPrefix, from, to, user, minRating et maxRating.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Paramètres**

| Paramètre | Description |
|:---|:---|
| appId | **Requis** Si l’en-tête Autorisation est utilisé, laissez le champ appid vide. Sinon, spécifiez une chaîne contenant « Beare » + « » + un jeton d’accès.|
| uriPrefix | **Facultatif** Chaîne contenant le préfixe d’URI de la traduction.|
| de | **Facultatif** Chaîne représentant le code de langue du texte de traduction. |
| to | **Facultatif** Chaîne représentant le code de la langue dans laquelle le texte doit être traduit.|
| minRating| **Facultatif** Entier représentant le niveau de qualité minimal pour le texte traduit. La valeur valide est comprise entre -10 et 10. La valeur par défaut est 1.|
| maxRating| **Facultatif** Entier représentant le niveau de qualité maximal pour le texte traduit. La valeur valide est comprise entre -10 et 10. La valeur par défaut est 1.|
| utilisateur | **Facultatif** Chaîne utilisée pour filtrer le résultat en fonction de la personne à l’origine de la soumission. |
| catégorie| **Facultatif** Chaîne contenant la catégorie ou le domaine de la traduction. Ce paramètre prend en charge uniquement l’option par défaut générale.|
| minDateUtc| **Facultatif** Date à partir de laquelle vous souhaitez récupérer les traductions. La date doit être au format UTC. |
| maxDateUtc| **Facultatif** Date jusqu’à laquelle vous souhaitez récupérer les traductions. La date doit être au format UTC. |
| skip| **Facultatif** Nombre de résultats que vous souhaitez ignorer sur une page. Par exemple, si vous souhaitez ignorer les 20 premières lignes des résultats et affichez les résultats à partir du 21e enregistrement, spécifiez 20 pour ce paramètre. La valeur par défaut pour ce paramètre est 0.|
| take | **Facultatif** Nombre de résultats que vous souhaitez récupérer. Le nombre maximal de chaque demande est 100. La valeur par défaut est 100.|

> [!NOTE]
> Les paramètres de demande skip et take permettent la pagination d’un grand nombre d’enregistrements de résultats.

**Valeur de retour**

Le jeu de résultats contient un tableau des éléments **UserTranslationCount**. Chaque élément UserTranslationCount comporte les éléments suivants :

| Champ | Description |
|:---|:---|
| Count| Nombre de résultats récupérés|
| À partir | Langue source|
| Rating| Évaluation appliquée par l’expéditeur dans l’appel de méthode AddTranslation()|
| À| Langue cible|
| Uri| URI appliqué dans l’appel de méthode AddTranslation()|
| Utilisateur| Nom de l’utilisateur|

**Exceptions**

| Exception | Message | Conditions |
|:---|:---|:---|
| ArgumentOutOfRangeException | Le paramètre '**maxDateUtc**' doit être supérieur ou égal à '**minDateUtc**'.| La valeur du paramètre **maxDateUtc** est inférieure à la valeur du paramètre **minDateUtc**.|
| TranslateApiException | L’adresse IP a dépassé le quota.| <ul><li>La limite pour le nombre de demandes par minute est atteinte.</li><li>La taille de la demande reste limitée à 10 000 caractères.</li><li>Un quota horaire et un quota quotidien limitent le nombre de caractères acceptés par l’API Microsoft Translator.</li></ul>|
| TranslateApiException | AppId a dépassé le quota.| L’ID d’application a dépassé le quota horaire ou quotidien.|

> [!NOTE]
> Le quota est ajusté pour garantir l’équité entre tous les utilisateurs du service.

**Affichage d’exemples de code sur GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>Méthode GetUserTranslations

Cette méthode récupère les traductions créées par l’utilisateur. Elle fournit les traductions regroupées par les paramètres de demande uriPrefix, from, to, user, minRating et maxRating.

**Syntaxe**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Paramètres**

| Paramètre | Description |
|:---|:---|
| appId | **Requis** Si l’en-tête Autorisation est utilisé, laissez le champ appid vide. Sinon, spécifiez une chaîne contenant « Beare » + « » + un jeton d’accès.|
| uriPrefix| **Facultatif** Chaîne contenant le préfixe d’URI de la traduction.|
| de| **Facultatif** Chaîne représentant le code de langue du texte de traduction.|
| to| **Facultatif** Chaîne représentant le code de la langue dans laquelle le texte doit être traduit.|
| minRating| **Facultatif** Entier représentant le niveau de qualité minimal pour le texte traduit. La valeur valide est comprise entre -10 et 10. La valeur par défaut est 1.|
| maxRating| **Facultatif** Entier représentant le niveau de qualité maximal pour le texte traduit. La valeur valide est comprise entre -10 et 10. La valeur par défaut est 1.|
| utilisateur| **Facultatif. Chaîne utilisée pour filtrer le résultat en fonction de la personne à l’origine de la soumission**|
| catégorie| **Facultatif** Chaîne contenant la catégorie ou le domaine de la traduction. Ce paramètre prend en charge uniquement l’option par défaut générale.|
| minDateUtc| **Facultatif** Date à partir de laquelle vous souhaitez récupérer les traductions. La date doit être au format UTC.|
| maxDateUtc| **Facultatif** Date jusqu’à laquelle vous souhaitez récupérer les traductions. La date doit être au format UTC.|
| skip| **Facultatif** Nombre de résultats que vous souhaitez ignorer sur une page. Par exemple, si vous souhaitez ignorer les 20 premières lignes des résultats et affichez les résultats à partir du 21e enregistrement, spécifiez 20 pour ce paramètre. La valeur par défaut pour ce paramètre est 0.|
| take| **Facultatif** Nombre de résultats que vous souhaitez récupérer. Le nombre maximal de chaque demande est 100. La valeur par défaut est de 50.|

> [!NOTE]
> Les paramètres de demande skip et take permettent la pagination d’un grand nombre d’enregistrements de résultats.

**Valeur de retour**

Le jeu de résultats contient un tableau des éléments **UserTranslation**. Chaque élément UserTranslation comporte les éléments suivants :

| Champ | Description |
|:---|:---|
| CreatedDateUtc| Date de création de l’entrée utilisant AddTranslation()|
| À partir| Langue source|
| OriginalText| Texte dans la langue source qui est utilisé lors de l’envoi de la demande|
|Rating |Évaluation appliquée par l’expéditeur dans l’appel de méthode AddTranslation()|
|À|    Langue cible|
|TranslatedText|    Traduction telle qu’elle est soumise dans l’appel de méthode AddTranslation()|
|Uri|   URI appliqué dans l’appel de méthode AddTranslation()|
|Utilisateur   |Nom de l’utilisateur|

**Exceptions**

| Exception | Message | Conditions |
|:---|:---|:---|
| ArgumentOutOfRangeException | Le paramètre '**maxDateUtc**' doit être supérieur ou égal à '**minDateUtc**'.| La valeur du paramètre **maxDateUtc** est inférieure à la valeur du paramètre **minDateUtc**.|
| TranslateApiException | L’adresse IP a dépassé le quota.| <ul><li>La limite pour le nombre de demandes par minute est atteinte.</li><li>La taille de la demande reste limitée à 10 000 caractères.</li><li>Un quota horaire et un quota quotidien limitent le nombre de caractères acceptés par l’API Microsoft Translator.</li></ul>|
| TranslateApiException | AppId a dépassé le quota.| L’ID d’application a dépassé le quota horaire ou quotidien.|

> [!NOTE]
> Le quota est ajusté pour garantir l’équité entre tous les utilisateurs du service.

**Affichage d’exemples de code sur GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
