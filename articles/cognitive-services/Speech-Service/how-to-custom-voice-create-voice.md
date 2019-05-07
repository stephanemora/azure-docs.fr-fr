---
title: Créer une voix personnalisée - Services de reconnaissance vocale
titlesuffix: Azure Cognitive Services
description: Lorsque vous êtes prêt à charger vos données, accédez au portail personnalisé voix. Créez ou sélectionnez un projet personnalisé voix. Le projet doit partager la bonne langue/région et les propriétés de sexe que les données vous comptez utiliser pour l’apprentissage de votre voix.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: fad69c4108d747c44eccf37b81adf2c7c615cb58
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65156845"
---
# <a name="create-a-custom-voice"></a>Créer une voix personnalisée

Dans [préparer des données pour la voix personnalisée](how-to-custom-voice-prepare-data.md), nous avons décrit les différents types de données que vous pouvez utiliser pour former une voix personnalisée et les exigences de format différent. Une fois que vous avez préparé vos données, vous pouvez commencer à les télécharger sur le [portail personnalisé vocale](http://aka.ms/custom-voice-portal), ou via l’API de formation personnalisé voix. Ici, nous décrivons les étapes de l’apprentissage d’une voix personnalisée via le portail.

> [!NOTE]
> Cette page suppose que vous avez lu [prise en main personnalisé voix](how-to-custom-voice.md) et [préparer des données pour la fonctionnalité vocale personnalisée](how-to-custom-voice-prepare-data.md)et vous avez créé un projet personnalisé voix.

Vérifier les langues prises en charge pour la voix personnalisée : [langage pour la personnalisation](language-support.md#customization).

## <a name="upload-your-datasets"></a>Charger vos jeux de données

Lorsque vous êtes prêt à charger vos données, accédez à la [portail personnalisé vocale](http://aka.ms/custom-voice-portal). Créez ou sélectionnez un projet personnalisé voix. Le projet doit partager la bonne langue/région et les propriétés de sexe que les données vous comptez utiliser pour l’apprentissage de votre voix. Par exemple, sélectionnez `en-GB` si les enregistrements audio que vous avez est effectuée en anglais avec un accent Royaume-Uni.

Accédez à la **données** onglet et cliquez sur **charger des données**. Dans l’Assistant, sélectionnez le type de données correct qui correspond à ce que vous avez préparé.

Chaque jeu de données que vous téléchargez doit respecter la configuration requise pour le type de données que vous choisissez. Il est important de mettre correctement en forme vos données avant d’être téléchargée. Cela garantit que les données vont être traitées avec précision par le service vocal de personnalisé. Accédez à [préparer des données pour la fonctionnalité vocale personnalisée](how-to-custom-voice-prepare-data.md) et assurez-vous que vos données ont été mises en forme à juste titre.

> [!NOTE]
> Les utilisateurs d’abonnement gratuit (F0) peuvent télécharger les deux jeux de données simultanément. Utilisateurs d’abonnement standard (S0) peuvent télécharger des cinq jeux de données simultanément. Si vous atteignez la limite, attendez au moins la fin de l’importation de l’un de vos jeux de données, puis réessayez.

> [!NOTE]
> Le nombre maximal de jeux de données autorisés à être importé par abonnement est .zip 10 fichiers gratuitement aux utilisateurs d’abonnement (F0) et 500 pour les utilisateurs de l’abonnement standard (S0).

Jeux de données est validées automatiquement une fois que vous appuyez sur le bouton de téléchargement. Validation des données inclut la série de vérifications sur les fichiers audio pour vérifier leur format de fichier, la taille et le taux d’échantillonnage. Corrigez les erreurs éventuelles et soumettre à nouveau. Lorsque la demande d’importation de données est lancée avec succès, vous devez voir une entrée dans la table de données qui correspond au jeu de données que vous venez de télécharger.

Le tableau suivant présente les états de traitement des jeux de données importés :

| État | Signification |
| ----- | ------- |
| Traitement en cours | Votre jeu de données a été reçue et est en cours de traitement. |
| Succeeded | Votre jeu de données a été validé et peut-être désormais être utilisée pour générer un modèle de voix. |
| Échec | Votre jeu de données a échoué au cours du traitement de nombreuses raisons, par exemple les erreurs de fichier, des problèmes de données ou des problèmes de réseau. |

Une fois la validation terminée, vous pouvez voir le nombre total de mise en correspondance énoncés pour chacun de vos jeux de données dans le **énoncés** colonne. Si le type de données que vous avez sélectionné nécessite une segmentation de l’audio de longue durée, cette colonne reflète uniquement les énoncés que nous avons segmenté pour vous que soit en se basant sur vos relevés de notes ou via le service de transcription vocale. Vous pouvez télécharger plus le jeu de données validée pour afficher les résultats de détail des énoncés importés avec succès et les transcriptions de leur mappage. Conseil : segmentation de l’audio de longue durée peut prendre plus d’une heure pour terminer le traitement des données.

Pour les jeux de données en-US et zh-CN, vous pouvez encore télécharger un rapport pour vérifier les scores de prononciation et le niveau de bruit pour chacun de vos enregistrements. Le score de prononciation est compris entre 0 et 100. Normalement, un score inférieur à 70 indique une erreur de prononciation ou un problème de correspondance du script. Un accent marqué peut réduire votre score de prononciation et avoir un impact sur la voix numérique générée.

Un rapport signal/bruit (SNR) supérieur indique un bruit plus faible dans votre contenu audio. Vous pouvez généralement atteindre un SNR supérieur à 50 en effectuant les enregistrements dans un studio professionnel. Un contenu audio dont le SNR est inférieur à 20 peut entraîner un niveau de bruit conséquent dans la voix générée.

N’hésitez pas à réenregistrer les énoncés affichant un faible score de prononciation ou un mauvais ratio signal / bruit. Si vous ne pouvez pas réenregistrer, vous pouvez exclure ces énoncés de votre jeu de données.

## <a name="build-your-custom-voice-model"></a>Générer votre modèle vocal personnalisé

Une fois que votre jeu de données a été validée, vous pouvez l’utiliser pour générer votre modèle vocal personnalisé.

1.  Accédez à **synthèse vocale > vocaux personnalisés > Formation**.

2.  Cliquez sur **former le modèle**.

3.  Ensuite, entrez un **nom** et **Description** pour vous aider à identifier ce modèle.

    Choisissez un nom avec soin. Le nom que vous entrez ici est celui que vous utiliserez pour spécifier la voix dans votre requête de synthèse vocale dans le cadre de l’entrée SSML. Uniquement des lettres, des chiffres et quelques caractères de ponctuation tels que - \_et (',') sont autorisés. Utiliser des noms différents pour les modèles de voix différente.

    Le champ **Description** sert souvent à enregistrer les noms des jeux de données qui ont été utilisés pour créer le modèle.

4.  À partir de la **sélectionner les données d’apprentissage** page, choisissez un ou plusieurs jeux de données que vous souhaitez utiliser pour l’apprentissage. Vérifiez le nombre d’énoncés avant de les envoyer. Vous pouvez commencer avec un nombre quelconque d’énoncés pour les modèles de voix en-US et zh-CN. Pour les autres paramètres régionaux, vous devez sélectionner plus de 2 000 énoncés pour pouvoir effectuer l’apprentissage d’une voix.

    > [!NOTE]
    > Noms audio en double seront retirés de la formation. Assurez-vous que vous sélectionnez les jeux de données ne contiennent pas les mêmes noms audio dans plusieurs fichiers .zip.

    > [!TIP]
    > À l’aide de jeux de données à partir de l’orateur même est requis pour les résultats de meilleure qualité. Lorsque les jeux de données que vous avez soumis pour l’apprentissage contiennent un nombre total de moins de 6 000 énoncés distinctes, vous allez former votre modèle vocal via la technique statistique synthèse paramétriques. Dans le cas où vos données d’apprentissage dépassent le nombre total de 6 000 énoncés distinctes, vous allez lancer un processus d’apprentissage avec la technique de synthèse de la concaténation. Normalement la technologie de concaténation peut entraîner des résultats de ton plus naturelle et une meilleure fidélité. [Contactez l’équipe personnalisé voix](mailto:speechsupport@microsoft.com) si vous souhaitez former un modèle avec les dernières technologies TTS neuronal qui peuvent produire une voix numérique équivalente à disponibles publiquement [voix neuronal](language-support.md#neural-voices).

5.  Cliquez sur **Train** pour commencer à créer votre modèle de voix.

Le tableau de formation affiche une nouvelle entrée qui correspond à ce nouveau modèle. Le tableau affiche également l’état : Traitement, réussite, échec.

L’état est indiqué reflète le processus de conversion de votre jeu de données à un modèle de voix, comme illustré ici.

| État | Signification |
| ----- | ------- |
| Traitement en cours | Votre modèle vocal est en cours de création. |
| Succeeded | Votre modèle de voix a été créé et peut être déployé. |
| Échec | Votre modèle vocale a échoué dans la formation de nombreuses raisons, par exemple des données des problèmes ou des problèmes réseau. |

Le temps d’apprentissage varie selon le volume de données audio traitées. Il varie généralement d’environ 30 minutes pour plusieurs centaines d’énoncés à 40 heures pour 20 000 énoncés. Une fois que votre apprentissage du modèle est réussi, vous pouvez commencer à tester.

> [!NOTE]
> Les utilisateurs d’abonnement gratuit (F0) peuvent effectuer l’apprentissage d’une police de la voix simultanément. Les utilisateurs de l’abonnement standard (S0) peuvent former trois voix simultanément. Si vous atteignez la limite, attendez au moins la fin de la formation de l’une de vos polices de la voix, puis réessayez.

> [!NOTE]
> Le nombre maximal de modèles de voix autorisés à être formé par abonnement est de 10 modèles pour les utilisateurs de l’abonnement gratuit (F0) et 100 pour les utilisateurs de l’abonnement standard (S0).

## <a name="test-your-voice-model"></a>Tester votre modèle de voix

Une fois votre police de la voix créée, vous pouvez la tester avant de procéder à son déploiement.

1.  Accédez à **synthèse vocale > vocaux personnalisés > test**.

2.  Cliquez sur **ajouter un test**.

3.  Sélectionnez un ou plusieurs modèles que vous souhaitez tester.

4.  Fournir le texte que vous souhaitez que le voice(s) de parler. Si vous avez choisi de tester plusieurs modèles en même temps, le même texte sera utilisé pour le test pour différents modèles.

    > [!NOTE]
    > La langue de votre texte doit être identique à celle de votre police vocale. Seuls les modèles correctement formés peuvent être testées. Seul le texte brut est pris en charge dans cette étape.

5.  Cliquez sur **Créer**.

Une fois que vous avez envoyé votre demande de test, vous revenez à la page de test. Le tableau inclut désormais une entrée qui correspond à votre nouvelle demande, ainsi que la colonne d’état. La synthétisation vocale peut prendre plusieurs minutes. Lorsque la colonne État indique que **Succeeded**, vous pouvez lire le son, ou télécharger l’entrée de texte (un fichier .txt) et audio de sortie (un fichier .wav) et davantage écouté ce dernier pour la qualité.

Vous trouverez également les résultats des tests dans la page de détails de chaque modèles que vous avez sélectionné pour le test. Accédez à la **formation** onglet, puis cliquez sur le nom du modèle pour entrer la page de détails du modèle.

## <a name="create-and-use-a-custom-voice-endpoint"></a>Créer et utiliser un point de terminaison vocaux personnalisés

Une fois que vous avez créé et testé votre modèle vocal, déployez-le sur un point de terminaison de synthèse vocale personnalisé. Vous pouvez ensuite utiliser ce point de terminaison à la place du point de terminaison habituel quand vous effectuez des requêtes de synthèse vocale par le biais de l’API REST. Votre point de terminaison personnalisé peut être appelée uniquement par l’abonnement que vous avez utilisé pour déployer la police.

Pour créer un nouveau point de terminaison vocaux personnalisés, accédez à **synthèse vocale > vocaux personnalisés > déploiement**. Sélectionnez **ajouter le point de terminaison** et entrez un **nom** et **Description** pour votre point de terminaison personnalisé. Puis sélectionnez le modèle vocal personnalisé que vous souhaitez associer à ce point de terminaison.

Une fois que vous avez cliqué sur le **ajouter** bouton, dans la table de point de terminaison, vous verrez une entrée pour votre nouveau point de terminaison. L’instanciation d’un nouveau point de terminaison peut prendre quelques minutes. Quand l’état du déploiement affiche **Succeeded** (Réussite), le point de terminaison est prêt à être utilisé.

> [!NOTE]
> Les utilisateurs d’abonnement gratuit (F0) peuvent avoir qu’un seul modèle est déployé. Abonnement standard (S0) vous pouvez créer jusqu'à 50 points de terminaison, chacun avec son propre vocaux personnalisés.

> [!NOTE]
> Pour utiliser votre voix personnalisé, vous devez spécifier le nom du modèle vocal, utilisez l’URI personnalisé directement dans une requête HTTP et utiliser le même abonnement à passer à l’authentification du service de synthèse vocale.

Une fois votre point de terminaison est déployé, le nom de point de terminaison s’affiche sous forme de lien. Cliquez sur le lien pour afficher des informations spécifiques à votre point de terminaison, tels que la clé de point de terminaison, les URL de point de terminaison et exemple de code.

Il est également possible d’effectuer un test en ligne du point de terminaison via le portail Custom Voice. Pour tester votre point de terminaison, choisissez **vérifier le point de terminaison** à partir de la **détails du point de terminaison** page. La page de test des points de terminaison s’affiche. Entrez le texte à énoncer (en texte brut ou [format SSML](speech-synthesis-markup.md) dans la zone de texte. Pour écouter le texte parlé dans votre police de la voix personnalisée, sélectionnez **Play** (Lecture). Cette fonctionnalité de test est facturée sur votre utilisation de la synthèse vocale personnalisée.

D’un point de vue fonctionnel, le point de terminaison personnalisé est identique au point de terminaison standard utilisé pour les demandes de synthèse vocale. Consultez [API REST](rest-text-to-speech.md) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

* [Guide : Enregistrer vos exemples de voix](record-custom-voice-samples.md)
* [Référence de l’API synthèse vocale](rest-text-to-speech.md)
