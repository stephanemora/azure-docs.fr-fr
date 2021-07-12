---
title: Créer une voix personnalisée Custom Voice - Service Speech
titleSuffix: Azure Cognitive Services
description: Pour charger vos données, accédez au portail Custom Voice. Créez ou sélectionnez un projet Custom Voice. Il doit avoir des paramètres régionaux et des propriétés de langue et de sexe adaptés aux données qui seront utilisées pour l’apprentissage de votre voix.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 449975a6f0b5799ce93dcb31f42e1a43a1d183f3
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111412130"
---
# <a name="create-and-use-your-voice-model"></a>Créer et utiliser votre modèle vocal

Dans [Préparer des données d’entraînement](how-to-custom-voice-prepare-data.md), vous avez découvert les différents types de données que vous pouvez utiliser pour entraîner une voix neuronale personnalisée et les différents formats requis. Une fois que vous avez préparé vos données et la déclaration verbale du narrateur, vous pouvez commencer à les charger dans [Speech Studio](https://aka.ms/custom-voice-portal). Dans cet article, vous allez apprendre à entraîner une voix neuronale personnalisée dans le portail Speech Studio. Consultez les [langues prises en charge](language-support.md#customization) pour une voix neuronale personnalisée.

## <a name="prerequisites"></a>Prérequis

* Suivre [Bien démarrer avec la voix neuronale personnalisée](how-to-custom-voice.md)
* [Préparer des données d’entraînement](how-to-custom-voice-prepare-data.md)

## <a name="set-up-voice-talent"></a>Configurer les talents de voix

Un talent vocal est un orateur individuel ou cible dont la voix est enregistrée et utilisée pour créer des modèles vocaux neuronaux. Avant de créer une voix, définissez votre personnage vocal et sélectionnez un talent vocal adéquat. Pour plus d’informations sur l’enregistrement d’échantillons vocaux, consultez [le tutoriel](record-custom-voice-samples.md).

Pour entraîner une voix neuronale, vous devez créer un profil de talent vocal à l’aide d’un fichier audio enregistré par un talent vocal qui consent à l’utilisation de ses données vocales pour entraîner un modèle vocal personnalisé. Quand vous préparez votre script d’enregistrement, veillez à inclure la phrase suivante :

**« Je, soussigné(e) [indiquez votre nom et votre prénom], avoir compris que les enregistrements de ma voix seront utilisés par [indiquez le nom de la société] pour créer et utiliser une version synthétique de ma voix. »**
Cette phrase est utilisée pour vérifier si les données d’entraînement correspondent à l’audio de la déclaration de consentement. > Découvrez-en davantage sur la [vérification des talents vocaux](/legal/cognitive-services/speech-service/custom-neural-voice/data-privacy-security-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) ici.

> [!NOTE]
> La voix neuronale personnalisée est disponible avec un accès limité. Veillez à bien comprendre les [conditions d’une IA responsable](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) avant d’effectuer votre [demande d’accès](https://aka.ms/customneural). 

Les étapes suivantes partent du principe que vous avez préparé les fichiers de consentement verbal des talents vocaux.  Accédez à [Speech Studio](https://aka.ms/custom-voice-portal) pour sélectionner un projet de voix neuronale personnalisée, puis suivez les étapes ci-après pour créer un profil de talent vocal.

1. Accédez à **Synthèse vocale** > **Voix personnalisée** > **sélectionnez un projet** > **Configurer les talents de voix**.

2. Cliquez sur **Ajouter un talent de voix**.

3. Ensuite, pour définir des caractéristiques vocales, cliquez sur le **scénario cible** à utiliser. Décrivez ensuite vos **caractéristiques vocales**.

> [!NOTE]
> Les scénarios que vous fournissez doivent être conformes à ce que vous avez demandé dans le formulaire de candidature.

4. Ensuite, accédez à **Charger l’instruction de talent de voix**, suivez l’instruction pour charger la déclaration du talent vocal que vous avez préparée au préalable.

> [!NOTE]
> Veillez à ce que la déclaration verbale soit enregistrée avec les mêmes paramètres que vos données d’entraînement, notamment l’environnement d’enregistrement et le style d’élocution.

5. Enfin, accédez à **Vérifier et soumettre**. Vous pouvez passer en revue les paramètres et cliquer sur **Envoyer**.

## <a name="upload-your-datasets"></a>Charger vos jeux de données

Quand vous êtes prêt à charger vos données, accédez à l’onglet **Préparer des données d’entraînement** pour ajouter votre premier jeu d’entraînement et charger des données.  Un jeu d’entraînement est un ensemble d’énoncés audio et de scripts correspondants utilisés pour entraîner un modèle vocal. Vous pouvez utiliser un jeu d’entraînement pour organiser vos données d’entraînement. La vérification de l’état de préparation des données s’effectue par jeu d’entraînement. Vous pouvez importer plusieurs jeux de données dans un jeu d’entraînement.

Vous pouvez effectuer les opérations suivantes pour créer et passer en revue vos données d’entraînement.

1. Sous l’onglet **Préparer des données d’entraînement**, cliquez sur **Ajouter un jeu d’entraînement**, entrez les valeurs **Nom** et **Description** et cliquez sur **Créer** pour ajouter un nouveau jeu d’entraînement.

   Une fois le jeu d’entraînement correctement créé, vous pouvez commencer à charger vos données. 

2. Pour charger des données, cliquez sur **Charger des données** > **Choisir le type de données** > **Charger des données** et **Spécifier l’ensemble de formations cible** > entrez les valeurs **Nom** et **Description** de votre jeu de données > passez en reveu les paramètres, puis cliquez sur **Charger**.

> [!NOTE]
>- Les noms audio en double seront retirés de l’apprentissage. Veillez à ce que les jeux de données sélectionnés ne comportent pas les mêmes noms audio au sein du fichier .zip ou dans plusieurs fichiers .zip. Si des ID d’énoncé (dans des fichiers audio ou de script) sont dupliqués, ils seront rejetés.
>- Si vous avez créé des jeux de données dans la version précédente de Speech Studio, vous devez spécifier un jeu d’entraînement pour vos jeux de données avant de les utiliser. Sinon, un point d’exclamation est ajouté au nom du jeu de données et celui-ci ne peut pas être utilisé.

Chaque jeu de données que vous chargez doit respecter les exigences associé au type de données choisi. Il est important de formater correctement vos données avant leur chargement, ce qui permet au service de voix neuronale personnalisée de les traiter correctement. Accédez à [Préparer des données d’entraînement](how-to-custom-voice-prepare-data.md) pour vérifier que vos données sont au bon format.

> [!NOTE]
> - Ceux qui disposent d’un abonnement standard (S0) peuvent, quant à eux, en charger cinq simultanément. Si vous atteignez la limite, attendez au moins la fin de l’importation de l’un de vos jeux de données, puis réessayez.
> - Le nombre maximal de jeux de données qu’il est autorisé d’importer par abonnement est de 10 fichiers .zip pour les utilisateurs disposant d’un abonnement gratuit (F0) et de 500 pour ceux qui disposent d’un abonnement standard (S0).

Les jeux de données sont validés automatiquement après avoir appuyé sur le bouton **Charger**. La validation des données passe par une série de vérifications des fichiers audio visant à contrôler leur format, leur taille et leur taux d’échantillonnage. Corrigez les erreurs éventuelles et soumettez-les à nouveau. 

Une fois les données chargées, vous pouvez vérifier les détails dans la vue détaillée du jeu d’entraînement. Sous l’onglet **Vue d’ensemble**, vous pouvez vérifier plus précisément les scores de prononciation et le niveau de bruit de chacun de vos jeux de données. Le score de prononciation est compris entre 0 et 100. Normalement, un score inférieur à 70 indique une erreur de prononciation ou un problème de correspondance du script. Un accent marqué peut réduire votre score de prononciation et avoir un impact sur la voix numérique générée.

Un rapport signal/bruit (SNR) supérieur indique un bruit plus faible dans votre contenu audio. Vous pouvez généralement atteindre un SNR supérieur à 50 en effectuant les enregistrements dans un studio professionnel. Un contenu audio dont le SNR est inférieur à 20 peut entraîner un niveau de bruit conséquent dans la voix générée.

N’hésitez pas à réenregistrer les énoncés affichant un faible score de prononciation ou un mauvais ratio signal / bruit. Si vous ne pouvez pas réenregistrer, envisagez d’exclure ces énoncés de votre jeu de données.

Dans les **détails des données**, vous pouvez vérifier les détails du jeu d’entraînement. En cas de problèmes classiques avec les données, suivez les instructions figurant dans le message affiché pour les corriger avant l’entraînement.

Les problèmes sont divisés en trois types. Reportez-vous aux trois tableaux suivants pour vérifier leurs types d’erreurs respectifs.

Les erreurs du premier type listées dans le tableau ci-dessous doivent être corrigées manuellement, sans quoi les données concernées par ces erreurs sont exclues lors de l’entraînement. 

| Category | Nom | Description | Suggestion |
| --------- | ----------- | ----------- | --------------------------- |
| Script | Séparateur non valide| Ces lignes de script n’ont pas de séparateur valide TAB:{}.| Utilisez TAB pour séparer l’ID du contenu.|
| Script | ID de script non valide| Le format de l’ID de script n’est pas valide.| L’ID de ligne de script doit être numérique.|
| Script | Contenu de script dupliqué| Le contenu de ligne de script {} est dupliqué à la ligne {}.| Le contenu de ligne de script doit être unique.|
| Script | Contenu de script trop long| Le contenu de ligne de script dépasse le maximum de 1 000 caractères.| La longueur du contenu de ligne de script doit être inférieure à 1 000 caractères.|
| Script | Le script n’a pas d’audio correspondant| L’ID de ligne de script n’a pas d’audio correspondant.| L’ID de ligne de script doit correspondre à l’ID audio.|
| Script | Aucun script valide| Aucun script valide n’a été trouvé dans ce jeu de données.| Corrigez les lignes de script problématiques d’après la liste de problèmes détaillée.|
| Audio | L’audio n’a pas de script correspondant| Le fichier audio ne correspond pas à l’ID de script.| Le nom de fichier Wav doit correspondre à l’ID figurant dans le fichier de script.|
| Audio | Format audio non valide| Le format du fichier Wav est illisible et non valide.| Vérifiez le format du fichier Wav avec un outil audio comme sox.|
| Audio | Taux d’échantillonnage faible| Le taux d’échantillonnage audio est inférieur à 16 KHz. | Le taux d’échantillonnage du fichier Wav doit être supérieur ou égal à 16 KHz. |
| Audio | Durée audio trop longue| La durée audio est supérieure à 30 secondes.| Fractionnez un audio de longue durée en plusieurs fichiers pour veiller à ce que chacun soit inférieur à 15 secondes.|
| Audio | Aucun audio valide| Aucun audio valide n’a été trouvé dans ce jeu de données.| Corrigez l’audio problématique d’après la liste de problèmes détaillée.|

Les erreurs du deuxième type listées dans le tableau ci-dessous se corrigent automatiquement, mais il est recommandé de revérifier que les données sont corrigées. 

| Category | Nom | Description | Suggestion |
| --------- | ----------- | ----------- | --------------------------- |
| Audio | Audio stéréo | Un seul canal en audio stéréo est utilisé pour l’entraînement de modèles de synthèse vocale.|     Utilisez un mode mono pour l’enregistrement de synthèse vocale ou la préparation des données. Cet audio est converti en mono. Téléchargez le jeu de données normalisé et vérifiez.|
| Volume | Volume maximal hors limites |Le volume maximal n’est pas compris dans la plage -3 dB (70 % du volume maximal) et -6 dB (50 %). Il s’ajuste automatiquement à -4 dB (65 %) maintenant.|  Contrôlez le volume maximal à la plage appropriée lors de l’enregistrement ou de la préparation des données. Cet audio est mis à l’échelle linéaire pour s’ajuster à la plage maximale. Téléchargez le jeu de données normalisé et vérifiez.|
|Incompatibilité | Long silence détecté avant le premier mot | Long silence détecté avant le premier mot.| Le silence de début est réduit à 200 ms. Téléchargez le jeu de données normalisé et vérifiez. |
| Incompatibilité | Long silence détecté après le dernier mot | Long silence détecté après le dernier mot. | Le silence de fin est réduit à 200 ms. Téléchargez le jeu de données normalisé et vérifiez. |
| Incompatibilité |Silence de début trop court | Le silence de début est inférieur à 100 ms. | Le silence de début est allongé jusqu’à 100 ms. Téléchargez le jeu de données normalisé et vérifiez. |
| Incompatibilité | Silence de fin trop court | Le silence de fin est inférieur à 100 ms. | Le silence de fin est allongé jusqu’à 100 ms. Téléchargez le jeu de données normalisé et vérifiez. |

Si vous ne corrigez pas les erreurs du troisième type figurant dans le tableau ci-dessous, quand bien même les données concernées par ces erreurs ne sont pas exclues pendant l’entraînement, la qualité de l’entraînement s’en ressent. Pour que la qualité de l’entraînement soit optimale, il est recommandé de corriger manuellement ces erreurs. 

| Category | Nom | Description | Suggestion |
| --------- | ----------- | ----------- | --------------------------- |
| Script | Contient des chiffres de 0 à 9| Ces lignes de script contiennent des chiffres de 0 à 9.| Les lignes de script contiennent des chiffres de 0 à 9. Écrivez-les en lettres normalisées et faites-les correspondre à l’audio. Par exemple, « 123 » devient « cent vingt-trois ».|
| Script | Mot prononcé de façon ambigüe « {} » | Le script contient un mot prononcé de façon ambigüe : {}.| Développez le mot dans sa prononciation réelle. Par exemple : {}.|
| Script | Énoncés interrogatifs trop peu nombreux| Les lignes de script interrogatives représentent moins de 1/6 du nombre total de lignes de script.| Les lignes de script interrogatives doivent représenter au moins 1/6 du nombre total des lignes pour que la police de la voix exprime correctement le ton interrogatif.|
| Script | Énoncés exclamatifs trop peu nombreux| Les lignes de script exclamatives représentent moins de 1/6 du nombre total de lignes de script.| Les lignes de script exclamatives doivent représenter au moins 1/6 du nombre total des lignes pour que la police de la voix exprime correctement le ton exclamatif.|
| Audio| Taux d’échantillonnage faible pour la voix neuronale | Le taux d’échantillonnage audio est inférieur à 24 KHz.|    Le taux d’échantillonnage du fichier Wav doit être supérieur ou égal à 24 KHz pour une voix neuronale de haute qualité.|
| Volume | Volume global trop faible | Le volume des {} échantillons est inférieur à -18 dB (10 % du volume maximal).|     Vérifiez que le niveau moyen du volume se trouve dans la plage appropriée pendant l’enregistrement ou la préparation des données.|
| Volume | Coupure du volume| Une coupure du volume est détectée à {} s.| Réglez le matériel d’enregistrement pour éviter une coupure du volume à sa valeur maximale.|
| Volume | Le silence de début n’est pas propre | Le silence des 100 premières ms n’est pas propre. Détectez un volume supérieur à -40 dB (1 % du volume maximal).|    Réduisez le niveau du bruit fond de l’enregistrement et laissez un silence de début de 100 ms.|
| Volume| Le silence de fin n’est pas propre| Le silence pendant les 100 dernières ms n’est pas propre. Détectez un volume supérieur à -40 dB (1 % du volume maximal).|     Réduisez le niveau du bruit de fond de l’enregistrement et laissez un silence de fin de 100 ms.|
| Incompatibilité | Incompatibilité audio du script détectée| Il existe une incompatibilité entre le script et le contenu audio. |     Examinez le script et le contenu audio pour vérifier qu’ils correspondent et contrôlez le niveau du bruit de fond. Réduisez la longueur des silences ou fractionnez le contenu en plusieurs énoncés.|
| Incompatibilité | Énergie audio supplémentaire détectée avant le premier mot |    Énergie audio supplémentaire détectée avant le premier mot. Cela peut également être dû à un silence de début trop court avant le premier mot.|    Examinez le script et le contenu audio pour vérifier qu’ils correspondent et contrôlez le niveau du bruit de fond. Laissez aussi un silence de 100 ms avant le premier mot.|
| Incompatibilité | Énergie audio supplémentaire détectée après le dernier mot| Énergie audio supplémentaire détectée après le dernier mot. Cela peut également être dû à un silence trop court après le dernier mot.|    Examinez le script et le contenu audio pour vérifier qu’ils correspondent et contrôlez le niveau du bruit de fond. Laissez aussi un silence de 100 ms après le dernier mot.|
| Incompatibilité | Ratio signal-bruit faible | Le niveau du ratio signal-bruit de l’audio est inférieur à {} dB.| Réduisez le niveau du bruit de fond pendant l’enregistrement ou la préparation des données.|
| Incompatibilité | Échec de la reconnaissance du contenu vocal | Impossible d’effectuer une reconnaissance vocale sur cet audio.|  Vérifiez le contenu audio et le contenu du script pour vous assurer que l’audio est valide et qu’il correspond au script.|

## <a name="train-your-custom-neural-voice-model"></a>Entraîner votre modèle vocal neuronal personnalisé

Une fois votre jeu de données validé, vous pouvez l’utiliser pour créer votre modèle vocal neuronal personnalisé.

1. Sous l’onglet **Entraîner un modèle**, cliquez sur **Entraîner un modèle** pour créer un modèle vocal avec les données que vous avez chargées.

2. Sélectionnez la méthode d’entraînement neuronal pour votre modèle et votre langue cible.

Par défaut, votre modèle vocal est entraîné dans la même langue que vos données d’entraînement. Vous pouvez aussi choisir de créer une langue secondaire (fonctionnalité en préversion) pour votre modèle vocal.  Vérifiez les langues prises en charge pour la voix neuronale personnalisée et la fonctionnalité multilingue : [langue de personnalisation](language-support.md#customization).

3. Ensuite, choisissez le jeu de données à utiliser pour l’entraînement et spécifiez le fichier d’un locuteur.

>[!NOTE]
>- Vous devez sélectionner au moins 300 énoncés pour créer une voix neuronale personnalisée.
>- Pour entraîner une voix neuronale, vous devez spécifier un profil de talent vocal avec le fichier de consentement audio fourni par le talent vocal qui a accepté que ses données vocales soient utilisées pour entraîner un modèle vocal personnalisé. La voix neuronale personnalisée est disponible avec un accès limité. Veillez à bien comprendre les [conditions de l’IA responsable](/legal/cognitive-services/speech-service/custom-neural-voice/limited-access-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext) et [demandez l’accès ici](https://aka.ms/customneural).
>- Dans cette page, vous pouvez également choisir de charger votre script à des fins de test. Le script de test doit être un fichier .txt d’une taille inférieure à 1 Mo. ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE et UTF-16-BE sont des formats d’encodage pris en charge. Chaque paragraphe de l’énoncé produira fera l’objet d’un audio distinct. Si vous préférez combiner toutes les phrases dans un seul audio, incluez-les toutes dans un même paragraphe.

4. Ensuite, entrez un **nom** et une **nescription** pour identifier plus facilement ce modèle.

Choisissez un nom avec soin. Le nom que vous entrez ici est celui que vous utiliserez pour spécifier la voix dans votre requête de synthèse vocale dans le cadre de l’entrée SSML. Seuls les lettres, les chiffres et quelques caractères de ponctuation comme -, \_ et (', ') sont autorisés. Utilisez un nom différent par modèle vocal neuronal.

Le champ **Description** sert souvent à enregistrer les noms des jeux de données qui ont été utilisés pour créer le modèle.

5. Passez en revue les paramètres, puis cliquez sur **Envoyer** pour commencer l’entraînement du modèle.

> [!NOTE]
> Les noms audio en double seront retirés de l’apprentissage. Vérifiez que les jeux de données sélectionnés ne comportent pas les mêmes noms audio dans plusieurs fichiers .zip.

Le Tableau **Entraîner un modèle** comporte une nouvelle entrée correspondant à ce nouveau modèle. Elle indique également l’état : Processing, Succeeded, Failed (En cours de traitement, Réussite, Échec).

L’état indiqué reflète le processus de conversion du jeu de données en modèle vocal :

| State | Signification |
| ----- | ------- |
| Traitement en cours | Le modèle vocal est en cours de création. |
| Opération réussie | Le modèle vocal a été créé et peut être déployé. |
| Échec | L’entraînement du modèle vocal a échoué pour plusieurs raisons possibles, par exemple, des problèmes inédits de données ou de réseau. |

La durée d’entraînement varie en fonction de la quantité de données que vous êtes en train d’entraîner. En moyenne, 40 heures de calcul sont nécessaires pour entraîner une voix neuronale personnalisée. 

> [!NOTE]
> L’entraînement des voix neuronales personnalisées n’est pas gratuit. Consultez les [tarifs](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) ici. Ceux qui disposent d’un abonnement standard (S0) peuvent, quant à eux, en entraîner trois simultanément. Si vous atteignez la limite, attendez au moins la fin de la formation de l’une de vos polices de la voix, puis réessayez. 

6. Une fois que vous avez fini d’entraîner le modèle, vous pouvez passer en revue ses détails.

Chaque formation génère automatiquement 100 exemples de fichiers audio pour vous aider à tester le modèle. Une fois votre modèle vocal créé, vous pouvez le tester avant de procéder à son déploiement.

La qualité de la voix dépend de nombreux facteurs, parmi lesquels la taille des données d’entraînement, la qualité de l’enregistrement, la précision du fichier de transcription, la correspondance entre la voix enregistrée dans les données d’entraînement et la personnalité de la voix créée pour le cas d’usage prévu, etc. [Consultez cette page pour en savoir plus sur les capacités et les limites de notre technologie, ainsi que sur les bonnes pratiques permettant d’améliorer la qualité de votre modèle](/legal/cognitive-services/speech-service/custom-neural-voice/characteristics-and-limitations-custom-neural-voice?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext). 

## <a name="create-and-use-a-custom-neural-voice-endpoint"></a>Créer et utiliser un point de terminaison de voix neuronale personnalisée

Une fois que vous avez créé et testé votre modèle vocal, déployez-le sur un point de terminaison de synthèse vocale personnalisé. Vous pouvez ensuite utiliser ce point de terminaison à la place du point de terminaison habituel quand vous effectuez des requêtes de synthèse vocale par le biais de l’API REST. Votre point de terminaison personnalisé peut être appelé uniquement par l’abonnement que vous avez utilisé pour déployer la police.

Vous pouvez effectuer les opérations suivantes pour créer un point de terminaison de voix neuronale personnalisée.

1. Sous l’onglet **Déployer le modèle**, cliquez sur **Déployer les modèles**. 
2. Ensuite, entrez un **nom** et une **description** pour votre point de terminaison personnalisé.
3. Ensuite, sélectionnez un modèle vocal à associer à ce point de terminaison. 
4. Enfin, cliquez sur **Déployer** pour créer votre point de terminaison.

Dès que vous avez cliqué sur le bouton **Déployer** dans la table du point de terminaison, l’entrée de votre nouveau point de terminaison apparaît. L’instanciation d’un nouveau point de terminaison peut prendre quelques minutes. Quand l’état du déploiement affiche **Succeeded** (Réussite), le point de terminaison est prêt à être utilisé.

Vous pouvez **interrompre** et **reprendre** un point de terminaison que vous utilisez par intermittence. Quand un point de terminaison est réactivé après avoir été interrompu, son URL ne change pas, ce qui vous évite d’avoir à modifier le code dans vos applications. 

Vous pouvez également mettre à jour le point de terminaison pour utiliser un nouveau modèle. Si vous changez de modèle, assurez-vous que le nouveau modèle a le même nom que le modèle à mettre à jour. 

> [!NOTE]
>- Ceux qui disposent d’un abonnement standard (S0) peuvent, quant à eux, créer jusqu’à 50 points de terminaison, chacun ayant sa propre voix neuronale personnalisée.
>- Pour utiliser votre voix neuronale personnalisée, vous devez spécifier le nom du modèle vocal, utiliser directement l’URI personnalisé dans une requête HTTP et vous servir du même abonnement pour effectuer l’authentification du service de synthèse vocale.

Une fois le point de terminaison déployé, son nom s’affiche sous forme de lien. Cliquez sur ce lien pour afficher les informations qui concernent spécifiquement votre point de terminaison, comme sa clé, son URL et l’exemple de code.

D’un point de vue fonctionnel, le point de terminaison personnalisé est identique au point de terminaison standard utilisé pour les demandes de synthèse vocale.  Pour plus d’informations, consultez le [SDK Speech](./get-started-text-to-speech.md) ou l’[API REST](rest-text-to-speech.md).

Nous fournissons également un outil en ligne, [Création de contenu audio](https://speech.microsoft.com/audiocontentcreation), qui vous permet de faire des réglages sur la sortie audio par le biais d’une interface utilisateur conviviale.

## <a name="next-steps"></a>Étapes suivantes

- [Guide pratique pour enregistrer des échantillons vocaux](record-custom-voice-samples.md)
- [Référence API Synthèse vocale](rest-text-to-speech.md)
- [API Audio long](long-audio-api.md)