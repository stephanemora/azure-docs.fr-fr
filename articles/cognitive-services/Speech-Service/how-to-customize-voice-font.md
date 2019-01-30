---
title: Création d’une police de la voix personnalisée
titlesuffix: Azure Cognitive Services
description: Cet article présente la personnalisation de la voix de Synthèse vocale, qui vous permet de créer une voix reconnaissable et propre à la marque.
services: cognitive-services
author: PanosPeriorellis
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: panosper
ms.openlocfilehash: 181797cd8ba193d7200e5ece8b599ffa40f7185b
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/19/2019
ms.locfileid: "54413700"
---
# <a name="creating-custom-voice-fonts"></a>Création de polices de voix personnalisée

La personnalisation de la voix de Synthèse vocale vous permet de créer une voix reconnaissable et propre à la marque. C’est ce que l’on appelle une *police de la voix*.

Pour créer votre police de la voix, effectuez un enregistrement en studio et chargez les scripts associés en tant que données d’apprentissage. Le service crée ensuite un modèle vocal unique adapté à votre enregistrement. Vous pouvez alors utiliser cette police de la voix pour synthétiser la parole.

Vous pouvez commencer avec un petit volume de données afin de procéder à une démonstration de faisabilité. Mais plus vous fournissez de données, plus votre voix semblera naturelle et professionnelle.

## <a name="prerequisites"></a>Prérequis

Vous avez besoin d’un compte Azure et d’un abonnement au service Speech. Si vous ne l’avez pas déjà fait, [créez-en un](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Connectez votre abonnement au portail Custom Voice, comme indiqué ici.

1. Connectez-vous au [portail Custom Voice](https://customvoice.ai) à l’aide du compte Microsoft que vous avez utilisé pour faire votre demande d’accès.

2. Sous le nom de votre compte en haut à droite, accédez à **Abonnements**.

    ![Abonnements](media/custom-voice/subscriptions.png)

3. Dans la page Subscriptions (Abonnements), choisissez **Connect existing subscription** (Connecter un abonnement existant). Notez que les services de reconnaissance vocale prennent en charge différentes régions. Vérifiez la région où votre clé d’abonnement a été créée, et veillez à connecter votre clé au sous-portail correct.  

4. Collez votre clé d’abonnement dans le tableau, comme indiqué dans l’exemple suivant. Chaque abonnement dispose de deux clés et vous pouvez utiliser n’importe laquelle des deux.

     ![Ajouter un abonnement](media/custom-voice/add-subscription.png)

Vous êtes prêt !

## <a name="prepare-recordings-and-transcripts"></a>Préparer les enregistrements et les transcriptions

Un jeu de données d’apprentissage de la voix se compose d’un ensemble de fichiers audio et d’un fichier texte qui contient les transcriptions des fichiers audio.

Vous pouvez préparer ces fichiers de deux manières : soit en écrivant un script et en le faisant lire par une voix professionnelle, soit en utilisant un enregistrement audio disponible publiquement et en le transcrivant en texte. Dans ce dernier cas, éliminez les disfluences dans les fichiers audio telles que les « euh » et autres sons de remplissage, bégaiements, mots marmonnés ou erreurs de prononciation.

Pour produire une police de voix de qualité satisfaisante, effectuez les enregistrements dans une pièce silencieuse avec un microphone de haute qualité. Pour concevoir une voix numérique de qualité, il est essentiel de faire attention à l’homogénéité du volume, au débit et à la tonalité, ainsi que de s’exprimer de manière expressive.

Pour créer une voix à des fins de production, nous vous recommandons de faire appel aux services d’un studio d’enregistrement et d’une voix professionnelle. Pour plus d’informations, consultez le [Guide pratique pour enregistrer des exemples de voix pour une voix personnalisée](record-custom-voice-samples.md).

### <a name="audio-files"></a>Fichiers audio

Chaque fichier audio doit contenir un seul énoncé (par exemple, une seule phrase prononcée ou une seule réplique d’un dialogue). Tous les fichiers doivent être dans la même langue. (Les voix personnalisées multilingues ne sont pas prises en charge.) Chaque fichier audio doit également comporter un nom de fichier numérique unique portant l’extension de nom de fichier `.wav`.

Les fichiers audio doivent être préparés comme suit. Les autres formats ne sont pas pris en charge et seront refusés.

| **Propriété** | **Valeur** |
| ------------ | --------- |
| Format de fichier  | RIFF (.wav)|
| Taux d’échantillonnage| au moins 16 000 Hz |
| Format d’échantillonnage| PCM, 16 bits |
| Nom de fichier    | Numérique, avec l’extension `.wav` |
| Format d’archive| .zip      |
| Taille d’archive maximale|200 Mo|

> [!NOTE]
> Les fichiers .wav avec un taux d’échantillonnage inférieur à 16 000 Hertz seront rejetés. Si un fichier .zip contient des sons avec différents taux d’échantillonnage, seuls ceux égaux ou supérieurs à 16 000 Hz seront importés.
> Actuellement, le portail importe les archives .zip jusqu’à 200 Mo. Toutefois, il est possible de charger plusieurs archives. Le nombre maximal de jeux de données autorisés est de 10 fichiers .zip pour les utilisateurs d’abonnement gratuit et de 50 pour les utilisateurs d’abonnement standard.

### <a name="transcripts"></a>Transcriptions

Le fichier de transcription est un fichier texte brut (ANSI, UTF-8, UTF-8-BOM, UTF-16-LE ou UTF-16-BE). Chaque ligne du fichier de transcription doit contenir le nom d’un fichier audio, suivi d’un onglet (code de caractère 9) et enfin de la transcription. Aucune ligne vide n’est autorisée.

Par exemple : 

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Le système vocal personnalisé normalise les transcriptions en convertissant le texte en minuscules et en supprimant les signes de ponctuation superflus. Il est important que les transcriptions soient parfaitement fidèles aux enregistrements audio correspondants.

> [!TIP]
> Lors de la création de voix de synthèse vocale, sélectionnez des énoncés (ou rédigez les scripts) qui prennent en compte à la fois l’efficacité et la couverture phonétique. Des difficultés à obtenir les résultats que vous souhaitez ? [Contactez l’équipe Custom Voice](mailto:speechsupport@microsoft.com) pour en savoir plus sur le moyen de nous consulter.

## <a name="upload-your-datasets"></a>Charger vos jeux de données

Après avoir préparé votre archive de fichiers audio et les transcriptions, chargez-les par le biais du [portail du service Custom Voice](https://customvoice.ai).

> [!NOTE]
> Les jeux de données ne peuvent pas être modifiés après avoir été chargés. Si vous oubliez d’inclure les transcriptions de certains fichiers audio ou si vous choisissez le mauvais genre par inadvertance, vous devez recharger l’intégralité du jeu de données. Vérifiez soigneusement votre jeu de données et les paramètres avant de procéder au chargement.

1. Connectez-vous au portail.

2. Dans la page principale, sous **Custom Voice**, sélectionnez **Data** (Données).   

    Le tableau **My Voice** (Ma voix) s’affiche. Si vous n’avez pas encore chargé de jeux de données vocales, ce tableau est vide.

3. Pour ouvrir la page de chargement d’un nouveau jeu de données, cliquez sur **Import data** (Importer des données).

    ![Importer des données vocales](media/custom-voice/import-voice-data.png)

4. Entrez un nom et une description dans les champs correspondants.

5. Sélectionnez les paramètres régionaux correspondant à vos polices vocales. Assurez-vous que les informations des paramètres régionaux correspondent à la langue des données d’enregistrement et des scripts.

6. Sélectionnez le sexe de la personne dont vous utilisez la voix.

7. Sélectionnez le script et les fichiers audio à charger.

8. Sélectionnez **Import** pour charger vos données. Pour les jeux de données plus volumineux, cette opération peut prendre plusieurs minutes.

> [!NOTE]
> Les utilisateurs d’abonnement gratuit peuvent charger deux jeux de données à la fois. Les utilisateurs d’abonnement standard peuvent, quant à eux, charger cinq jeux de données simultanément. Si vous atteignez la limite, attendez au moins la fin de l’importation de l’un de vos jeux de données, puis réessayez.

Une fois le chargement terminé, le tableau **My Voice Data** (Mes données vocales) s’affiche à nouveau. Vous devez voir une entrée correspondant au jeu de données que vous venez de charger.

Les jeux de données sont automatiquement validés après le chargement. La validation des données inclut une série de vérifications sur les fichiers audio visant à contrôler le format, la taille et le taux d’échantillonnage. Les vérifications effectuées sur les fichiers de transcription contrôlent le format de fichier et effectuent quelques opérations de normalisation du texte. Les énoncés sont transcris à l’aide de la reconnaissance vocale. Le texte résultant est ensuite comparé à la transcription que vous avez fournie.

![Mes données vocales](media/custom-voice/my-voice-data.png)

Le tableau suivant présente les états de traitement des jeux de données importés :

| État | Signification
| ----- | -------
| `NotStarted` | Votre jeu de données a été reçu et est en attente de traitement.
| `Running` | Votre jeu de données est en cours de validation.
| `Succeeded` | Votre jeu de données a été validé et peut maintenant être utilisé pour générer une police de la voix.

Une fois la validation terminée, vous pouvez voir le nombre total d’énoncés correspondants pour chacun de vos jeux de données dans la colonne **Utterance** (Énoncé).

Vous pouvez télécharger un rapport pour vérifier les scores de prononciation et le niveau de bruit de chacun de vos enregistrements. Le score de prononciation est compris entre 0 et 100. Normalement, un score inférieur à 70 indique une erreur de prononciation ou un problème de correspondance du script. Un accent marqué peut réduire votre score de prononciation et avoir un impact sur la voix numérique générée.

Un rapport signal/bruit (SNR) supérieur indique un bruit plus faible dans votre contenu audio. Vous pouvez généralement atteindre un SNR supérieur à 50 en effectuant les enregistrements dans un studio professionnel. Un contenu audio dont le SNR est inférieur à 20 peut entraîner un niveau de bruit conséquent dans la voix générée.

N’hésitez pas à réenregistrer les énoncés affichant un faible score de prononciation ou un mauvais ratio signal / bruit. Si vous ne pouvez pas réenregistrer, vous pouvez exclure ces énoncés de votre jeu de données.

## <a name="build-your-voice-font"></a>Générer votre police vocale

Une fois votre jeu de données validé, vous pouvez l’utiliser pour générer votre police de la voix personnalisée.

1.  Dans le menu déroulant **Custom Voice**, choisissez **Models**.

    Le tableau **My Voice Fonts** (Mes polices de la voix) apparaît. Il répertorie toutes les polices de la voix personnalisées que vous avez déjà créées.

1. Sous le titre du tableau, cliquez sur **Create voices** (Créer des voix).

    La page de création d’une police vocale s’affiche. Les paramètres régionaux actuels figurent sur la première ligne du tableau. Modifiez les paramètres régionaux pour créer une voix dans une autre langue. Les paramètres régionaux doivent être identiques à ceux des jeux de données utilisés pour créer la voix.

1. Comme lorsque vous avez chargé votre jeu de données, entrez un nom et une description pour vous aider à identifier ce modèle.

    Choisissez un nom avec soin. Le nom que vous entrez ici est celui que vous utiliserez pour spécifier la voix dans votre requête de synthèse vocale dans le cadre de l’entrée SSML. Seuls les lettres, les chiffres et quelques caractères de ponctuation tels que `-`, `_` et `(', ')` sont autorisés.

    Le champ **Description** sert souvent à enregistrer les noms des jeux de données qui ont été utilisés pour créer le modèle.

1. Choisissez le genre de votre police vocale. Il doit correspondre à celui du jeu de données.

1. Sélectionnez le ou les jeux de données que vous souhaitez utiliser pour l’apprentissage de la police vocale. Tous les jeux de données utilisés doivent provenir du même orateur.

1. Cliquez sur **Créer** pour commencer à créer votre police vocale.

    ![Création de modèle](media/custom-voice/create-model.png)

Votre nouveau modèle apparaît dans le tableau **My Voice Fonts** (Mes polices de la voix).

![Mes polices vocales](media/custom-voice/my-voice-fonts.png)

L’état indiqué reflète le processus de conversion de votre jeu de données en police de la voix, comme indiqué ici.

| État | Signification
| ----- | -------
| `NotStarted` | Votre requête de création de police de la voix est en attente de traitement.
| `Running` | Votre police de la voix est en cours de création.
| `Succeeded` | Votre police de la voix a été créée et peut être déployée.

Le temps d’apprentissage varie selon le volume de données audio traitées. Il varie généralement d’environ 30 minutes pour plusieurs centaines d’énoncés à 40 heures pour 20 000 énoncés.

> [!NOTE]
> Les utilisateurs d’abonnements gratuits peuvent former une police vocale à la fois. Les utilisateurs d’abonnement standard peuvent, quant à eux, former trois polices vocales simultanément. Si vous atteignez la limite, attendez au moins la fin de la formation de l’une de vos polices de la voix, puis réessayez.

## <a name="test-your-voice-font"></a>Tester votre police vocale

Une fois votre police de la voix créée, vous pouvez la tester avant de procéder à son déploiement. Dans la colonne **Operations**, sélectionnez **Test**. La page de test de la police vocale sélectionnée s’affiche. Le tableau est vide si vous n’avez encore envoyé aucune demande de test pour la voix.

Pour afficher un menu contextuel permettant d’envoyer des demandes de test, sélectionnez le bouton **Test with text** (Tester avec le texte) sous le titre du tableau. Vous pouvez envoyer votre demande de test au format texte brut ou SSML. La taille d’entrée maximale est de 1024 caractères, balises de requête SSML comprises. La langue de votre texte doit être identique à celle de votre police vocale.

Après avoir renseigné la zone de texte et confirmé le mode d’entrée, sélectionnez **Yes** pour envoyer votre demande de test et revenir à la page de test. Le tableau inclut désormais une entrée qui correspond à votre nouvelle demande, ainsi que la colonne d’état. La synthétisation vocale peut prendre plusieurs minutes. Quand la colonne d’état affiche le message **Succeeded** (Réussite), vous pouvez télécharger l’entrée de texte (un fichier `.txt`) et la sortie audio (un fichier `.wav`), et écouter ce dernier pour vérifier la qualité.

## <a name="create-and-use-a-custom-endpoint"></a>Créer et utiliser un point de terminaison personnalisé

Une fois que vous avez créé et testé votre modèle vocal, déployez-le sur un point de terminaison de synthèse vocale personnalisé. Vous pouvez ensuite utiliser ce point de terminaison à la place du point de terminaison habituel quand vous effectuez des requêtes de synthèse vocale par le biais de l’API REST. Votre point de terminaison personnalisé peut être appelé uniquement par l’abonnement que vous avez utilisé pour déployer la police.

Pour créer un point de terminaison personnalisé, choisissez **Endpoints** (Points de terminaison) dans le menu **Custom Voice** en haut de la page. La page **My Deployed Voices** (Mes voix déployées) s’affiche avec, le cas échéant, son tableau des points de terminaison vocaux personnalisés actifs. Les paramètres régionaux actuels figurent à la première ligne du tableau. Pour créer un déploiement dans une autre langue, modifiez les paramètres régionaux qui s’affichent. (Ils doivent correspondre à la voix que vous déployez.)

Pour créer un point de terminaison, cliquez sur le bouton **Deploy voices** (Déployer les voix). Entrez le nom et la description de votre point de terminaison personnalisé.

Dans le menu **Subscription** (Abonnement), choisissez l’abonnement que vous souhaitez utiliser. Les utilisateurs d’abonnement gratuit ne peuvent déployer qu’un seul modèle à la fois. Les utilisateurs d’abonnement standard, quant à eux, peuvent créer jusqu’à 20 points de terminaison, chacun avec sa propre voix personnalisée.

![Création d’un point de terminaison](media/custom-voice/create-endpoint.png)

Après avoir sélectionné le modèle à déployer, sélectionnez **Create** (Créer). La page **My Deployed Voices** (Mes voix déployées) s’affiche à nouveau et contient désormais une entrée pour votre nouveau point de terminaison. L’instanciation d’un nouveau point de terminaison peut prendre quelques minutes. Quand l’état du déploiement affiche **Succeeded** (Réussite), le point de terminaison est prêt à être utilisé.

![Mes voix déployées](media/custom-voice/my-deployed-voices.png)

Quand l’état du déploiement affiche **Succeeded** (Réussite), le point de terminaison de votre police de la voix déployée apparaît dans le tableau **My Deployed Voices** (Mes voix déployées). Vous pouvez utiliser cet URI directement dans une requête HTTP.

Il est également possible d’effectuer un test en ligne du point de terminaison via le portail Custom Voice. Pour tester votre point de terminaison, choisissez **Endpoints testing** (Test des points de terminaison) dans le menu déroulant **Custom Voice**. La page de test des points de terminaison s’affiche. Choisissez une voix personnalisée déployée et saisissez le texte à lire (au format texte brut ou SSML) dans la zone de texte.

> [!NOTE]
> Quand vous utilisez SSML, la balise `<voice>` doit spécifier le nom que vous avez donné à votre voix personnalisée lors de sa création. Si vous envoyez un texte brut, la voix personnalisée est toujours utilisée.

Pour écouter le texte parlé dans votre police de la voix personnalisée, sélectionnez **Play** (Lecture).

![Test des points de terminaison](media/custom-voice/endpoint-testing.png)

D’un point de vue fonctionnel, le point de terminaison personnalisé est identique au point de terminaison standard utilisé pour les demandes de synthèse vocale. Consultez [API REST](rest-apis.md) pour en savoir plus.

## <a name="language-support"></a>Support multilingue

La personnalisation de la voix est disponible dans les langues suivantes :

| Langage | Paramètres régionaux |
|----------|--------|
| Chinois (continent) | zh-CN |
| Anglais (US) | en-US |
| Anglais | fr-FR |
| Allemand | de-DE |
| Italien | it-IT |

> [!NOTE]
> L’entraînement de la voix française, allemande et italienne commence par un jeu de données de plus de 2 000 énoncés. Les modèles bilingues chinois-anglais sont également pris en charge avec un jeu de données de plus de 2 000 énoncés.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Enregistrer vos échantillons vocaux](record-custom-voice-samples.md)
