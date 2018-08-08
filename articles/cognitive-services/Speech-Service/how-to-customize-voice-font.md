---
title: Qu’est-ce que Custom Voice ? - Azure Cognitive Services
description: Cet article présente la personnalisation vocale Microsoft Text to Speech, qui vous permet de créer une voix reconnaissable et spécifique à la marque.
services: cognitive-services
author: noellelacharite
ms.service: cognitive-services
ms.topic: article
ms.date: 05/07/2018
ms.author: nolach
ms.openlocfilehash: 84493ae83515c0458bf5b9e9cf44603300a8b4f7
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284885"
---
# <a name="creating-custom-voice-fonts"></a>Création de polices de voix personnalisée

La personnalisation vocale Microsoft Text to Speech (TTS) vous permet de créer une voix reconnaissable et spécifique à la marque. C’est ce que l’on appelle une *police de la voix*. 

Pour créer votre police de la voix, effectuez un enregistrement en studio et chargez les scripts associés en tant que données d’apprentissage. Le service crée ensuite un modèle vocal unique adapté à votre enregistrement. Vous pouvez alors utiliser cette police vocale pour synthétiser la parole. 

Vous pouvez commencer avec un petit volume de données afin de procéder à une démonstration de faisabilité. Mais plus vous fournissez de données, plus votre voix semblera naturelle et professionnelle.

La personnalisation vocale est disponible pour l’anglais américain (en-US) et le chinois continental (zh-CN).

## <a name="prerequisites"></a>Prérequis

La fonctionnalité de personnalisation vocale Text to Speech est actuellement en préversion privée. [Remplissez le formulaire de demande](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0N8Vcdi8MZBllkZb70o6KdURjRaUzhBVkhUNklCUEMxU0tQMEFPMjVHVi4u) pour y avoir accès.

Vous avez besoin d’un compte Azure et d’un abonnement au service Speech. Si vous ne l’avez pas déjà fait, [créez-en un](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started). Connectez votre abonnement au portail Custom Voice en procédant comme suit.

1. Connectez-vous au [portail Custom Voice](https://customvoice.ai) à l’aide du compte Microsoft que vous avez utilisé pour faire votre demande d’accès.

2. Accédez à « Abonnements » sous le nom de votre compte en haut à droite.

    ![Abonnements](media/custom-voice/subscriptions.png)

3. Sur la page « Abonnements », choisissez « Connecter l’abonnement existant ».

     ![Connecter l’abonnement existant](media/custom-voice/connect-existing-sub.png)

4. Collez votre clé d’abonnement dans le tableau, comme indiqué ci-dessous. Chaque abonnement dispose de deux clés et vous pouvez utiliser n’importe laquelle des deux.

     ![Ajouter un abonnement](media/custom-voice/add-subscription.png)

Vous êtes prêt !

## <a name="prepare-recordings-and-transcripts"></a>Préparer les enregistrements et les transcriptions

Un jeu de données d’apprentissage vocal se compose d’un ensemble de fichiers audio, ainsi que d’un fichier texte contenant les transcriptions de tous ces fichiers audio.

Vous pouvez préparer ces fichiers dans l’ordre de votre choix : soit écrire un script et le faire lire par une voix professionnelle, ou bien utiliser un enregistrement audio disponible publiquement et le transcrire en texte. Dans ce dernier cas, éliminez les disfluences des fichiers audio telles que les « euh» et autres sons de remplissage, bégaiements, mots marmonnés ou erreurs de prononciation.

Pour produire une police vocale de qualité satisfaisante, il est important que les enregistrements soient effectués dans une salle silencieuse avec un microphone de haute qualité. Pour concevoir une voix numérique de qualité, il est essentiel de faire attention à l’homogénéité du volume, au débit et à la tonalité, ainsi que de s’exprimer de manière expressive. Pour créer une voix à des fins de production, nous vous recommandons de faire appel aux services d’un studio d’enregistrement et d’une voix professionnelle. Pour plus de détails, consultez le [Guide pratique pour enregistrer des exemples de voix pour une voix personnalisée](record-custom-voice-samples.md).

### <a name="audio-files"></a>Fichiers audio

Chaque fichier audio doit contenir un seul énoncé (par exemple, une seule phrase prononcée ou une seule réplique d’un dialogue). Tous les fichiers doivent être dans la même langue (les voix personnalisées multilingues ne sont pas prises en charge). Chaque fichier audio doit également comporter un nom de fichier numérique unique avec l’extension de nom de fichier `.wav`.

Les fichiers audio doivent être préparés comme suit. Les autres formats ne sont pas pris en charge et seront refusés.

| **Propriété** | **Valeur** |
| ------------ | --------- |
| Format de fichier  | RIFF (WAV)|
| Taux d’échantillonnage| au moins 16 000 Hz |
| Format d’échantillonnage| PCM, 16 bits |
| Nom de fichier    | Numérique, avec l’extension `.wav` |
| Format d’archive| Zip      |
| Taille d’archive maximale|200 Mo|

Placez l’ensemble des fichiers audio dans un même dossier sans sous-répertoires. Tous les fichiers doivent se trouver dans un même fichier d’archive ZIP.

> [!NOTE]
> Les fichiers son avec un taux d’échantillonnage inférieur à 16 000 Hz seront rejetés. Si un fichier zip contient des sons avec différents taux d’échantillonnage, seuls ceux égaux ou supérieurs à 16 000 Hz seront importés.
> Actuellement, le portail importe les archives ZIP jusqu’à 200 Mo. Toutefois, il est possible de charger plusieurs archives. Le nombre maximal de jeux de données autorisés est de 10 fichiers ZIP pour les utilisateurs d’abonnement gratuit et de 50 pour les utilisateurs d’abonnement standard.

### <a name="transcripts"></a>Transcriptions

Le fichier de transcription est un fichier texte brut (ANSI/UTF-8/UTF-8-BOM/UTF-16-LE/UTF-16-BE). Chaque ligne du fichier de transcription doit contenir le nom d’un fichier audio, suivi d’un onglet (code de caractère 9) et enfin de la transcription. Aucune ligne vide n’est autorisée.

Par exemple : 

```
0000000001  This is the waistline, and it's falling.
0000000002  We have trouble scoring.
0000000003  It was Janet Maslin.
```

Le système vocal personnalisé normalise les transcriptions en convertissant le texte en minuscules et en supprimant les signes de ponctuation superflus. Il est important que les transcriptions soient parfaitement fidèles aux enregistrements audio correspondants.

> [!TIP]
> Lors de la création de voix de synthèse vocale, sélectionnez les énoncés (ou rédigez les scripts) en prenant en compte à la fois l’efficacité et la couverture phonétique.

## <a name="upload-your-datasets"></a>Charger vos jeux de données

Après avoir préparé votre archive de fichiers audio et les transcriptions, chargez-les sur le [portail du service Custom Voice](https://customvoice.ai).

> [!NOTE]
> Les jeux de données ne peuvent pas être modifiés après avoir été chargés. Si vous avez oublié inclure les transcriptions de certains fichiers audio, par exemple, ou si vous avez choisi le mauvais genre par inadvertance, vous devez charger à nouveau l’intégralité du jeu de données. Vérifiez soigneusement votre jeu de données et les paramètres avant de procéder au chargement.

1. Connectez-vous au portail.

2. Choisissez **Données** sous Custom Voice sur la page principale. 

    ![Mes projets](media/custom-voice/my-projects.png)

    Le tableau Mes données vocales s’affiche. Si vous n’avez pas encore chargé de jeux de données vocales, ce tableau est vide.

3. Cliquez sur **Importer des données** pour ouvrir la page de chargement d’un nouveau jeu de données.

    ![Importer des données vocales](media/custom-voice/import-voice-data.png)

4. Entrez un nom et une description dans les champs correspondants. 

5. Sélectionnez les paramètres régionaux correspondant à vos polices vocales. Assurez-vous que les informations des paramètres régionaux correspondent à la langue des données d’enregistrement et des scripts. 

6. Sélectionnez le sexe de la personne dont vous utilisez la voix.

7. Choisissez le script et les fichiers audio à charger. 

8. Cliquez sur **Importer** pour charger vos données. Pour les jeux de données plus volumineux, cette opération peut prendre plusieurs minutes.

> [!NOTE]
> Les utilisateurs d’abonnement gratuit peuvent charger deux jeux de données à la fois. Les utilisateurs d’abonnement standard peuvent, quant à eux, charger cinq jeux de données simultanément. Si vous atteignez la limite, attendez au moins la fin de l’importation de l’un de vos jeux de données, puis recommencez l’opération.

Une fois le chargement terminé, le tableau Mes données vocales s’affiche à nouveau. Vous devez voir une entrée correspondant au jeu de données que vous venez de charger. 

Les jeux de données sont automatiquement validés après le chargement. La validation des données inclut une série de vérifications sur les fichiers audio visant à contrôler le format, la taille et le taux d’échantillonnage. Les vérifications effectuées sur les fichiers de transcription contrôlent le format de fichier et effectuent quelques opérations de normalisation du texte. Les énoncés sont transcrits à l’aide de la reconnaissance vocale, et le texte obtenu est comparé à la transcription que vous avez fournie.

![Mes données vocales](media/custom-voice/my-voice-data.png)

Le tableau suivant présente les états de traitement des jeux de données importés. 

| État | Signification
| ----- | -------
| `NotStarted` | Votre jeu de données a été reçu et est en attente de traitement
| `Running` | Votre jeu de données est en cours de validation
| `Succeeded` | Votre jeu de données a été validé et peut maintenant être utilisé pour générer une police vocale

Une fois la validation terminée, vous pouvez voir le nombre total d’énoncés correspondants pour chacun de vos jeux de données dans la colonne Énoncé.

Vous pouvez télécharger un rapport pour vérifier les scores de prononciation et le niveau de bruit de chacun de vos enregistrements. Le score de prononciation est compris entre 0 et 100. Normalement, un score inférieur à 70 indique une erreur de prononciation ou un problème de correspondance du script. Un accent marqué peut réduire votre score de prononciation et avoir un impact sur la voix numérique générée.

Un rapport signal/bruit (SNR) supérieur indique un bruit plus faible dans votre contenu audio. Vous pouvez généralement atteindre un SNR de plus de 50 en faisant appel aux services d’un studio professionnel. Un contenu audio dont le SNR est inférieur à 20 peut entraîner un niveau de bruit conséquent dans la voix générée.

N’hésitez pas à réenregistrer les énoncés affichant un faible score de prononciation ou un mauvais ratio signal / bruit. S’il n’est pas possible d’effectuer un nouvel enregistrement, vous pouvez exclure ces énoncés de votre jeu de données.

## <a name="build-your-voice-font"></a>Générer votre police vocale

Une fois votre jeu de données validé, vous pouvez l’utiliser pour générer votre police vocale personnalisée. 

1. Choisissez **Modèles** dans le menu déroulant Custom Voice. 
 
    Le tableau Mes polices vocales apparaît. Il contient toutes les polices vocales personnalisées que vous avez déjà créées.

1. Cliquez sur **Créer des voix** sous le titre du tableau. 

    La page de création d’une police vocale s’affiche. Les paramètres régionaux actuels figurent sur la première ligne du tableau. Modifiez les paramètres régionaux pour créer une voix dans une autre langue. Les paramètres régionaux doivent être identiques aux jeux de données utilisés pour créer la voix.

1. Comme lorsque vous avez chargé votre jeu de données, entrez un nom et une description pour vous aider à identifier ce modèle. 

    Le nom que vous entrez ici est le nom que vous allez utiliser pour spécifier la voix dans votre requête de synthèse vocale dans le cadre de l’entrée SSML. Veillez donc à le choisir avec soin. Seuls les lettres, les chiffres et quelques caractères de ponctuation tels que « - », « _ », « ( » et « ) » sont autorisés.

    Le champ Description est souvent utilisé pour enregistrer les noms des jeux de données qui ont été utilisés pour créer le modèle.

1. Choisissez le genre de votre police vocale. Il doit correspondre à celui du jeu de données.

1. Sélectionnez le ou les jeux de données que vous souhaitez utiliser pour l’apprentissage de la police vocale. Tous les jeux de données utilisés doivent provenir du même orateur.

1. Cliquez sur **Créer** pour commencer à créer votre police vocale.

    ![Création de modèle](media/custom-voice/create-model.png)

Votre nouveau modèle apparaît dans le tableau Mes polices vocales. 

![Mes polices vocales](media/custom-voice/my-voice-fonts.png)

L’état indiqué reflète le processus de conversion de votre jeu de données à une police vocale, comme indiqué ici.

| État | Signification
| ----- | -------
| `NotStarted` | Votre requête de création de police vocale est en attente de traitement
| `Running` | Votre police vocale est en cours de création
| `Succeeded` | Votre police vocale a été créée et peut être déployée

Le temps d’apprentissage varie selon le volume de données audio traitées. Il varie généralement d’environ 30 minutes pour plusieurs centaines d’énoncés à 40 heures pour 20 000 énoncés.

> [!NOTE]
> Les utilisateurs d’abonnements gratuits peuvent former une police vocale à la fois. Les utilisateurs d’abonnement standard peuvent, quant à eux, former trois polices vocales simultanément. Si vous atteignez la limite, attendez au moins la fin de la formation de l’une de vos polices vocales, puis recommencez l’opération.

## <a name="test-your-voice-font"></a>Tester votre police vocale

Une fois votre police vocale créée, vous pouvez la tester avant de procéder à son déploiement. Cliquez sur **Tester** dans la colonne Opérations. La page de test de la police vocale sélectionnée s’affiche. Le tableau est vide si vous n’avez encore envoyé aucune demande de test pour la voix.

![Mes polices vocales, 2ème partie](media/custom-voice/my-voice-fonts2.png)

Cliquez sur le bouton **Tester avec le texte** sous le titre du tableau pour afficher un menu contextuel permettant d’envoyer les demandes de test. Vous pouvez envoyer votre demande de test au format texte brut ou SSML. La taille d’entrée maximale est de 1024 caractères, balises de requête SSML comprises. La langue de votre texte doit être identique à celle de votre police vocale.

![Test de la police vocale](media/custom-voice/voice-font-testing.png)

Après avoir renseigné la zone de texte et confirmé le mode d’entrée, cliquez sur **Oui** pour envoyer votre demande de test et revenir à la page de test. Le tableau inclut désormais une entrée qui correspond à votre nouvelle demande, ainsi que la colonne d’état désormais familière. La synthétisation vocale peut prendre plusieurs minutes. Lorsque la colonne État affiche le message Réussi, vous pouvez télécharger l’entrée de texte (un fichier `.txt`) et la sortie audio (un fichier `.wav`), et écouter ce dernier pour vérifier la qualité.

![Test de la police vocale, 2ème partie](media/custom-voice/voice-font-testing2.png)

## <a name="create-and-use-a-custom-endpoint"></a>Créer et utiliser un point de terminaison personnalisé

Une fois que vous avez créé et testé votre modèle vocal, déployez-le dans un point de terminaison de synthèse vocale personnalisé. Vous pouvez ensuite utiliser ce point de terminaison à la place du point de terminaison habituel lorsque vous effectuez des requêtes de synthèse vocale via l’API REST. Votre point de terminaison personnalisé peut être appelé uniquement par l’abonnement que vous avez utilisé pour déployer la police.

Pour créer un nouveau point de terminaison, choisissez **Points de terminaison** dans le menu Custom Voice en haut de la page. La page Mes voix déployées s’affiche avec, le cas échéant, son tableau des points de terminaison vocaux personnalisés en cours. Les paramètres régionaux actuels figurent à la première ligne du tableau. Pour créer un déploiement dans une autre langue, modifiez les paramètres régionaux qui s’affichent. (Ils doivent correspondre à la voix que vous déployez.)

Cliquez sur le bouton **Déployer les voix** pour créer un nouveau point de terminaison. Entrez le nom et la description de votre point de terminaison personnalisé.

Dans le menu Abonnement, choisissez l’abonnement que vous souhaitez utiliser. Les utilisateurs d’abonnement gratuit ne peuvent déployer qu’un seul modèle à la fois. Les utilisateurs d’abonnement standard, quant à eux, peuvent créer jusqu’à 20 points de terminaison, chacun avec sa propre voix personnalisée.

![Créer un point de terminaison](media/custom-voice/create-endpoint.png)

Après avoir sélectionné le modèle à déployer, cliquez sur **Créer**. La page Mes voix déployées s’affiche à nouveau et contient désormais une entrée pour votre nouveau point de terminaison. L’instanciation d’un nouveau point de terminaison peut prendre quelques minutes. Lorsque l’état du déploiement affiche Réussi, le point de terminaison est prêt à être utilisé.

![Mes voix déployées](media/custom-voice/my-deployed-voices.png)

Lorsque l’état du déploiement affiche Réussi, le point de terminaison de votre police vocale déployée apparaît dans le tableau Mes voix déployées. Vous pouvez utiliser cet URI directement dans une requête HTTP.

Il est également possible d’effectuer un test en ligne du point de terminaison via le portail Custom Voice. Pour tester votre point de terminaison, choisissez **Test des points de terminaison** dans le menu déroulant de Custom Voice. La page de test des points de terminaison s’affiche. Choisissez une voix personnalisée déployée et saisissez le texte à lire (au format texte brut ou SSML) dans la zone de texte.

> [!NOTE] 
> Lorsque vous utilisez SSML, la balise `<voice>` doit spécifier le nom que vous avez donné à votre voix personnalisée lors de sa création.

Cliquez sur **Lire** pour écouter le texte parlé dans votre police vocale personnalisée.

![Test des points de terminaison](media/custom-voice/endpoint-testing.png)

D’un point de vue fonctionnel, le point de terminaison personnalisé est identique au point de terminaison standard utilisé pour les demandes de synthèse vocale. Consultez [API REST](rest-apis.md) pour en savoir plus.

## <a name="next-steps"></a>Étapes suivantes

- [Obtenir votre abonnement d’essai gratuit au service Speech](https://azure.microsoft.com/try/cognitive-services/)
- [Reconnaissance vocale dans C#](quickstart-csharp-dotnet-windows.md)
