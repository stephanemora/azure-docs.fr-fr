---
title: Évaluer et améliorer la justesse de Custom Speech - Service Speech
titleSuffix: Azure Cognitive Services
description: Dans ce document, vous allez apprendre à mesurer et à améliorer de manière quantitative la qualité de notre modèle de conversion de parole en texte ou de votre modèle personnalisé. Des données de transcription audio et étiquetées à la main sont nécessaires pour tester la précision ; il faut également fournir entre 30 minutes et 5 heures d’audio représentatif.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: b7e4ea586098ea3eb0dfd684650f798d7988e18b
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634581"
---
# <a name="evaluate-and-improve-custom-speech-accuracy"></a>Évaluer et améliorer la justesse de Custom Speech

Dans cet article, vous allez apprendre à mesurer et à améliorer de manière quantitative la justesse des modèles de conversion de parole en texte de Microsoft ou de vos modèles personnalisés. Des données de transcription audio et étiquetées à la main sont nécessaires pour tester la précision ; il faut également fournir entre 30 minutes et 5 heures d’audio représentatif.

## <a name="evaluate-custom-speech-accuracy"></a>Évaluer la précision de Custom Speech

Le standard de mesure de la précision d’un modèle est le [taux d’erreur de mots](https://en.wikipedia.org/wiki/Word_error_rate)  (WER, de l’anglais « Word Error Rate »). Il compte le nombre de mots incorrects identifiés lors de la reconnaissance, puis le divise par le nombre total de mots fournis dans la transcription étiquetée à la main (il s’agit de « N » ci-dessous). Enfin, ce nombre est multiplié par 100 % pour calculer le taux WER.

![Formule du taux WER](./media/custom-speech/custom-speech-wer-formula.png)

Les mots mal identifiés se décomposent en trois catégories :

* Insertion (I) : mots ajoutés à tort à l’hypothèse de transcription
* Suppression (D) : mots non détectés dans l’hypothèse de transcription
* Substitution (S) : mots substitués entre la référence et l’hypothèse

Voici un exemple :

![Exemple de mots mal identifiés](./media/custom-speech/custom-speech-dis-words.png)

Si vous souhaitez répliquer des mesures WER en local, vous pouvez utiliser SCLITE à partir de [SCTK](https://github.com/usnistgov/SCTK).

## <a name="resolve-errors-and-improve-wer"></a>Résoudre les erreurs et améliorer le taux WER

Le taux WER peut être utilisé à partir des résultats de la reconnaissance automatique pour évaluer la qualité du modèle d’une application, d’un outil ou d’un produit. Un taux WER compris entre 5 et 10 % est considéré comme étant de bonne qualité et prêt à l’emploi. Un taux de 20 % est acceptable, mais un apprentissage supplémentaire est envisageable. Un taux supérieur ou égal à 30 % est signe de qualité médiocre et demande une personnalisation et un apprentissage.

La distribution des erreurs est importante. La présence de nombreuses erreurs de suppression est généralement due à une puissance faible du signal audio. Pour résoudre ce problème, vous devrez collecter des données audio plus près de la source. Les erreurs d’insertion signifient que l’audio a été enregistré dans un environnement bruyant et qu’il y a un risque de diaphonie, ce qui pose des problèmes de reconnaissance. Les erreurs de substitution se rencontrent souvent lorsque l’échantillon de termes propres à un domaine fourni sous la forme de transcriptions étiquetées à la main ou de texte lié est insuffisant.

En analysant les fichiers un par un, vous pouvez identifier les différents types d’erreurs, ainsi que les erreurs propres à un fichier donné. Il est essentiel de comprendre les problèmes au niveau du fichier pour fixer des objectifs d’amélioration.

## <a name="create-a-test"></a>Créer un test

Si vous souhaitez tester la qualité du modèle de reconnaissance vocale de référence de Microsoft ou d’un modèle personnalisé entraîné par vos soins, vous pouvez comparer deux modèles côte à côte pour évaluer la précision. La comparaison englobe le taux WER et les résultats de reconnaissance. On compare en général un modèle personnalisé avec le modèle de référence de Microsoft.

Pour évaluer des modèles côte à côte :

1. Connectez-vous au [portail Custom Speech](https://speech.microsoft.com/customspeech).
2. Accédez à **Synthèse vocale > Custom Speech > [nom du projet] > Test en cours**.
3. Cliquez sur **Add Test** (Ajouter un test).
4. Sélectionnez **Évaluer la précision**. Donnez au test un nom et une description et sélectionnez votre jeu de données de transcription audio + étiquetées à la main.
5. Sélectionnez les modèles que vous voulez tester (deux au maximum).
6. Cliquez sur **Créer**.

Une fois votre test créé, vous pouvez comparer les résultats côte à côte.

### <a name="side-by-side-comparison"></a>Comparaison côte à côte

Une fois terminé (ce qu’indique le passage à l’état *Réussi*), le test comporte un nombre WER pour les deux modèles. Cliquez sur le nom du test pour afficher la page de détails correspondante. Cette page de détails liste tous les énoncés de votre jeu de données, indiquant les résultats de la reconnaissance des deux modèles avec la transcription du jeu de données soumis. Pour inspecter plus facilement la comparaison côte à côte, vous pouvez voir les différents types d’erreurs (insertion, suppression et substitution). En écoutant l’audio et en comparant les résultats de la reconnaissance dans les deux colonnes (transcription étiquetée à la main et résultats des deux modèles de reconnaissance vocale), vous pouvez identifier le modèle qui répond à vos besoins et déterminer où un apprentissage et des améliorations s’imposent.

## <a name="improve-custom-speech-accuracy"></a>Améliorer la justesse de Custom Speech

Les scénarios de reconnaissance vocale varient en fonction de la qualité et de la langue de l’audio (vocabulaire et style d’élocution). Le tableau suivant examine quatre scénarios courants :

| Scénario | Qualité audio | Vocabulaire | Style d’élocution |
|----------|---------------|------------|----------------|
| Centre d’appels | Faible, 8 kHz, possibilité d’avoir 2 personnes sur 1 canal audio, peut être compressée | Étroit, propre au domaine et aux produits | Conversationnel, vaguement structuré |
| Assistant vocal (tel que Cortana ou une fenêtre de drive) | Élevée, 16 kHz | Lourd au niveau des entités (titres de chanson, produits, emplacements) | Mots et phrases clairement énoncés |
| Dictée (message instantané, notes, recherche) | Élevée, 16 kHz | Différentes possibilités | Prise de note |
| Sous-titrage vidéo | Variée (utilisation variée du microphone, ajout de musique, etc.) | Varié (réunions, discours, paroles de chanson, etc.) | Texte lu, préparé ou faiblement structuré |

Différents scénarios produisent des résultats de qualité différents. Le tableau suivant examine le [taux d’erreurs sur les mots](how-to-custom-speech-evaluate-data.md) (WER, Word Error rate) du contenu de ces quatre scénarios. Le tableau indique les types d’erreur les plus courants dans chaque scénario.

| Scénario | Qualité de la reconnaissance vocale | Nombre d’erreurs d’insertion | Nombre d’erreurs de suppression | Nombre d’erreurs de substitution |
|----------|----------------------------|------------------|-----------------|---------------------|
| Centre d’appels | Moyen (< 30 % WER) | Faible, sauf quand d’autres personnes parlent en arrière-plan | Peut être élevé Les centres d’appels peuvent être bruyants et les opérateurs qui parlent en même temps peuvent être source de confusion pour le modèle | Moyenne. Les noms de produits et de personnes peuvent entraîner ces erreurs |
| Assistant vocal | Élevé (peut être < 10 % WER) | Faible | Faible | Moyen, en raison de titres de chanson, de noms de produits ou de localisations |
| Dictation | Élevé (peut être < 10 % WER) | Faible | Faible | Élevé |
| Sous-titrage vidéo | Dépend du type de vidéo (peut être < 50 % WER) | Faible | Peut être élevé en raison de la musique, des bruits et de la qualité du microphone | Le jargon peut entraîner ces erreurs |

La détermination des composants du WER (nombre d’erreurs d’insertion, de suppression et de substitution) permet de décider du type de données à ajouter pour améliorer le modèle. Utilisez le [portail Custom Speech](https://speech.microsoft.com/customspeech) pour voir la qualité d’un modèle de référence. Le portail signale les taux d’erreur d’insertion, de substitution et de suppression qui sont combinés dans le taux de qualité WER.

## <a name="improve-model-recognition"></a>Améliorer la reconnaissance du modèle

Vous pouvez réduire les erreurs de reconnaissance en ajoutant des données d’entraînement dans le portail [Custom Speech](https://speech.microsoft.com/customspeech). 

Prévoyez de maintenir votre modèle personnalisé en ajoutant régulièrement des éléments sources. Votre modèle personnalisé a besoin d’un entraînement supplémentaire pour rester informé des modifications apportées à vos entités. Par exemple, vous devrez peut-être mettre à jour les noms de produits, noms de chansons ou nouveaux emplacements de service.

Les sections suivantes décrivent comment chaque type de données d’entraînement supplémentaires peut réduire les erreurs.

### <a name="add-related-text-sentences"></a>Ajouter des phrases de texte associées

Lorsque vous formez un nouveau modèle personnalisé, commencez par ajouter du texte associé pour améliorer la reconnaissance des mots et des expressions spécifiques à un domaine. L’ajout de phrases de texte associées permet principalement de réduire les erreurs de substitution liées à la reconnaissance erronée des mots courants et des mots spécifiques à un domaine en les présentant en contexte. Les mots spécifiques à un domaine peuvent être des mots rares ou inventés, mais leur prononciation doit être facile à reconnaître.

> [!NOTE]
> Évitez les phrases de texte associées qui incluent du bruit, comme des caractères ou des mots non reconnaissables.

### <a name="add-audio-with-human-labeled-transcripts"></a>Ajouter de l’audio avec des transcriptions étiquetées à la main

L’audio avec des transcriptions étiquetées à la main offre les plus fortes améliorations en termes de précision si l’audio provient du cas d’usage cible. Les exemples doivent couvrir l’intégralité des messages vocaux. Par exemple, la plupart des appels adressés à un centre d’appels pendant les mois d’été concernent les maillots de bain et les lunettes de soleil. Vérifiez que votre exemple comprend l’étendue complète des messages vocaux que vous souhaitez détecter.

Tenez compte des détails suivants :

* L’apprentissage de l’audio est plus avantageux si le contenu audio est également difficile à comprendre pour les êtres humains. Dans la plupart des cas, vous devez commencer l’apprentissage en utilisant simplement du texte associé.
* Si vous utilisez l’une des langues les plus utilisées, telles que l’anglais (États-Unis), il y a de fortes chances qu’il ne soit pas nécessaire d’effectuer l’entraînement avec des données audio. Pour ces langues, les modèles de base offrent déjà une très bonne reconnaissance pour la plupart des scénarios. Il est probablement suffisant d’effectuer un entraînement avec du texte associé.
* Custom Speech peut uniquement capturer le contexte des mots pour réduire les erreurs de substitution, pas les erreurs d’insertion ou de suppression.
* Évitez les exemples comprenant des erreurs de transcription, mais incluez une qualité audio diversifiée.
* Évitez les phrases qui ne sont pas liées au domaine de votre problème. Les phrases non liées peuvent endommager votre modèle.
* En cas de variation de la qualité des transcriptions, vous pouvez dupliquer des phrases exceptionnellement bonnes (par exemple, d’excellentes transcriptions qui incluent des expressions clés) pour augmenter leur poids.
* Le service vocal utilise automatiquement les transcriptions pour améliorer la reconnaissance des mots et des expressions spécifiques à un domaine, comme s’ils avaient été ajoutés en tant que texte associé.
* L’exécution d’une opération d’apprentissage peut prendre plusieurs jours. Pour améliorer la vitesse d’entraînement, veillez à créer votre abonnement au service Speech dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.

> [!NOTE]
> Tous les modèles de base ne prennent pas en charge l’audio. Si un modèle de base ne le prend pas en charge, le service vocal utilise uniquement le texte des transcriptions et ignore l’audio. Pour obtenir la liste des modèles de base prenant en charge l’entraînement avec des données audio, consultez les informations relatives à la [prise en charge des langues](language-support.md#speech-to-text). Même si un modèle de base prend en charge l’apprentissage avec des données audio, il est possible que le service n’utilise qu’une partie de l’audio. Il utilisera néanmoins toutes les transcriptions.

> [!NOTE]
> Si vous changez le modèle de base utilisé pour l’entraînement et si vous avez du contenu audio dans le jeu de données d’entraînement, vérifiez *toujours* si le nouveau modèle de base sélectionné [prend en charge l’entraînement avec des données audio](language-support.md#speech-to-text). Si le modèle de base utilisé jusqu’à maintenant ne prend pas en charge l’entraînement avec des données audio, et si le jeu de données d’entraînement contient de l’audio, le temps d’entraînement du nouveau modèle de base va **considérablement** augmenter. Il peut facilement passer de plusieurs heures à plusieurs jours, voire davantage. Cela est particulièrement vrai si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.
>
> Si vous êtes confronté au problème décrit dans le paragraphe ci-dessus, vous pouvez rapidement faire baisser le temps d’entraînement en réduisant la quantité du contenu audio dans le jeu de données, ou en supprimant complètement le contenu audio pour ne garder que le texte. Cette dernière option est fortement recommandée si votre abonnement au service Speech ne se situe **pas** dans une [région disposant du matériel dédié](custom-speech-overview.md#set-up-your-azure-account) à l’entraînement.

### <a name="add-new-words-with-pronunciation"></a>Ajouter de nouveaux mots avec la prononciation

Les mots inventés ou hautement spécialisés peuvent avoir des prononciations uniques. Ces mots peuvent être reconnus si le mot peut être décomposé en mots plus petits pour le prononcer. Par exemple, pour reconnaître **Xbox**, prononcez ce mot **X box**. Cette approche n’augmente pas la précision globale, mais elle peut accroître la reconnaissance de ces mots clés.

> [!NOTE]
> Cette technique n’est disponible que pour certaines langues à l’heure actuelle. Pour plus d’informations, consultez la personnalisation de la prononciation dans le [tableau de reconnaissance vocale](language-support.md).

## <a name="sources-by-scenario"></a>Sources par scénario

Le tableau suivant présente les scénarios de reconnaissance vocale et liste les documents sources à prendre en compte dans les trois catégories de contenu d’entraînement ci-dessus.

| Scénario | Phrases de texte associées | Transcriptions audio + étiquetées à la main | Nouveaux mots avec prononciation |
|----------|------------------------|------------------------------|------------------------------|
| Centre d’appels             | documents marketing, site web, évaluations de produits relatives à l’activité du centre d’appels | appels du centre d’appels transcrits à la main | termes qui ont des prononciations ambiguës (voir Xbox ci-dessus) |
| Assistant vocal         | lister les phrases à l’aide de toutes les combinaisons de commandes et d’entités | enregistrer les commandes vocales sur l’appareil et transcrire en texte | noms (films, chansons, produits) qui ont des prononciations uniques |
| Dictation               | entrée écrite, comme les messages instantanés ou les e-mails | semblable à ci-dessus | semblable à ci-dessus |
| Sous-titrage vidéo | scripts de programmes télévisés, films, contenu marketing, résumés vidéo | transcriptions de vidéos exactes | semblable à ci-dessus |

## <a name="next-steps"></a>Étapes suivantes

* [Entraîner et déployer un modèle](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* [Préparer et tester les données](./how-to-custom-speech-test-and-train.md)
* [Inspecter les données](how-to-custom-speech-inspect-data.md)