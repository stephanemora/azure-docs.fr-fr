---
title: 'Tutoriel : API de traduction de conversation Translator Speech en C#'
titleSuffix: Azure Cognitive Services
description: Utilisez l’API de traduction de conversation Translator Speech pour traduire du texte en temps réel.
services: cognitive-services
author: v-jerkin
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: tutorial
ms.date: 3/5/2018
ms.author: v-jerkin
ROBOTS: NOINDEX
ms.openlocfilehash: 2de56366c3204e77eb2e6775ddd88b6fc4f0c219
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993865"
---
# <a name="tutorial-translator-speech-application-in-c"></a>Tutoriel : Application de traduction de conversation Translator Speech en C#

Ce tutoriel présente un outil de traduction vocale interactif qui utilise l’API de traduction de conversation Translator Speech, qui fait partie d’Azure Cognitive Services. Vous apprendrez à :

> [!div class="checklist"]
> * Demander une liste des langues prises en charge par le service
> * Capturer de l’audio et le transmettre au service
> * Recevoir et afficher les traductions de la conversation sous forme de texte
> * Si vous le souhaitez, lire une version parlée (synthèse vocale) de la traduction

Un fichier solution Visual Studio pour cette application est [disponible sur GitHub](https://github.com/MicrosoftTranslator/SpeechTranslator).

## <a name="prerequisites"></a>Prérequis

Pour ce didacticiel, vous avez besoin de n’importe quelle édition de Visual Studio 2017, y compris la Community Edition. 

La solution Visual Studio génère également un programme d’installation pour l’application. Vous avez besoin de [l’ensemble d’outils WiX Toolset](http://wixtoolset.org/) et de [l’extension Visual Studio de l’ensemble d’outils WiX](https://marketplace.visualstudio.com/items?itemName=RobMensching.WixToolsetVisualStudio2017Extension) pour prendre en charge cette fonctionnalité.

Vous avez également besoin d’une clé d’abonnement pour le service Translator Speech, que vous pouvez obtenir à partir du tableau de bord Microsoft Azure. Un niveau tarifaire gratuit est disponible : il vous permet de traduire jusqu’à 10 heures de conversation par mois sans aucun frais. Ce niveau est suffisant pour ce didacticiel.

La [bibliothèque tierce JSON.Net](https://www.newtonsoft.com/json) (de Newtonsoft) est également requise. Cet assembly est installé automatiquement par NuGet si les deux cases à cocher Restauration du package sont activées dans les options de Visual Studio.

## <a name="trying-the-translation-app"></a>Essai de l’application de traduction

Après l’ouverture de la solution Speech Translator (`SpeechTranslator.sln`) dans Visual Studio, appuyez sur F5 pour générer et lancer l’application.  La fenêtre principale du programme s’affiche.

![[Fenêtre principale de Speech Translator]](media/speech-translator-main-window.png)

Dans la première exécution, choisissez **Paramètres du compte** à partir du menu **Paramètres** pour ouvrir la fenêtre illustrée ici.

![[Fenêtre principale de Speech Translator]](media/speech-translator-settings-window.png)

Collez votre clé d’abonnement de l’API de traduction de conversation Translator Speech dans cette fenêtre, puis cliquez sur **Enregistrer**. Votre clé est enregistrée entre chaque exécution.

Dans la fenêtre principale, choisissez les périphériques audio d’entrée et de sortie que vous souhaitez utiliser, ainsi que les langues source et cible. Si vous souhaitez écouter l’audio de la traduction, assurez-vous que l’option **TTS** (synthèse vocale) est activée. Si vous souhaitez voir les traductions spéculatives partielles au fil de la conversation, activez l’option **Résultats partiels**.

Enfin, cliquez sur **Démarrer** pour commencer la traduction. Dites quelque chose que vous souhaitez traduire et regardez le texte reconnu et la traduction s’afficher dans la fenêtre. Si vous avez activé l’option TTS, vous entendez également la traduction.

## <a name="obtaining-supported-languages"></a>Obtenir les langues prises en charge

Actuellement, le service de traduction de conversation Translator Speech prend en charge plus d’une cinquantaine de langues pour la traduction de texte. Un nombre de langues plus restreint est pris en charge pour la traduction vocale. Ces langues requièrent la prise en charge à la fois de la transcription (reconnaissance vocale) et, pour la sortie de synthèse vocale, de la synthèse.

En d’autres termes, pour la traduction vocale, la langue source doit être une langue prise en charge pour la transcription. La langue de sortie peut être toute langue prise en charge pour la traduction de texte, en supposant que vous souhaitiez un résultat sous forme de texte. Si vous souhaitez une sortie sous forme vocale, vous pouvez traduire uniquement dans une langue prise en charge pour la synthèse vocale.

Microsoft peut ajouter la prise en charge de nouvelles langues de façon ponctuelle. Pour cette raison, il est déconseillé de coder en dur toute connaissance des langues prises en charge dans votre application. À la place, l’API de traduction de conversation Translator Speech fournit un point de terminaison de langues qui vous permet de récupérer les langues prises en charge lors de l’exécution. Vous pouvez choisir de recevoir une ou plusieurs listes de langues : 

| | |
|-|-|
|`speech`|Les langues prises en charge pour la transcription vocale. Peuvent être des langues source pour la traduction vocale.|
|`text`|Les langues prises en charge pour la traduction de texte en texte. Peuvent être des langues cible pour la traduction vocale lorsque la sortie texte est utilisée.|
|`tts`|Les voix prises en charge pour la synthèse vocale, chacune associée à une langue particulière. Peuvent être des langues cible pour la traduction vocale lorsque la synthèse vocale est utilisée. Une langue donnée peut être prise en charge par plusieurs voix.|

Le point de terminaison des langues ne nécessite pas de clé d’abonnement, et son utilisation n’est pas comptée dans votre quota. Son URI est `https://dev.microsofttranslator.com/languages` et retourne ses résultats au format JSON.

La méthode `UpdateLanguageSettingsAsync()` dans `MainWindow.xaml.cs`, illustrée ici, appelle le point de terminaison des langues pour obtenir la liste des langues prises en charge. 

```csharp
private async Task UpdateLanguageSettingsAsync()
{
    Uri baseUri = new Uri("https://" + baseUrl);
    string fullUriString = "/Languages?api-version=1.0&scope=text,speech,tts";
    if (MenuItem_Experimental.IsChecked) fullUriString += "&flight=experimental";            
    Uri fullUri = new Uri(baseUri, fullUriString);

    using (HttpClient client = new HttpClient()) //'client' is the var - using statement ensures the dispose method is used even after an exception.
    {
        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, fullUri);

        // get language names for current UI culture:
        request.Headers.Add("Accept-Language", CultureInfo.CurrentUICulture.TwoLetterISOLanguageName);

        // add a client-side trace Id. In case of issues, one can contact support and provide this:
        //string traceId = "SpeechTranslator" + Guid.NewGuid().ToString();
        //request.Headers.Add("X-ClientTraceId", traceId);
        //Debug.Print("TraceId: {0}", traceId);

        client.Timeout = TimeSpan.FromMilliseconds(10000);
        HttpResponseMessage response = await client.SendAsync(request); //make the async call to the web using the client var and passing the built up URI
        response.EnsureSuccessStatusCode(); //causes exception if the return is false

        Debug.Print("Request Id returned: {0}", GetRequestId(response));

        //create dictionaries to hold the language specific data
        spokenLanguages = new Dictionary<string, string>();
        fromLanguages = new Dictionary<string, string>();
        textLanguages = new Dictionary<string, string>();
        isLTR = new Dictionary<string, bool>();
        voices = new Dictionary<string, List<TTsDetail>>();

        JObject jResponse = JObject.Parse(await response.Content.ReadAsStringAsync()); //get the json from the async call with the response var created above, parse it and put it in a var called jResponse - JObject is a newton class

        //Gather the set of TTS voices
        foreach (JProperty jTts in jResponse["tts"])
        {
            JObject ttsDetails = (JObject)jTts.Value;

            string code = jTts.Name;
            string language = ttsDetails["language"].ToString();
            string displayName = ttsDetails["displayName"].ToString();
            string gender = ttsDetails["gender"].ToString();

            if (!voices.ContainsKey(language)) //check dictionary for a specific key value
            {
                voices.Add(language, new List<TTsDetail>()); //add to the dictionary the locale key and a ttsDetail object
            }

            voices[language].Add(new TTsDetail() { Code = code, DisplayName = string.Format("{0} ({1})", displayName, gender) });
        }

        // Gather the set of speech translation languages
        foreach (JProperty jSpeech in jResponse["speech"])
        {
            JObject languageDetails = (JObject)jSpeech.Value;
            string code = jSpeech.Name;
            string simplecode = languageDetails["language"].ToString();
            string displayName = languageDetails["name"].ToString();
            spokenLanguages.Add(code, displayName);
            fromLanguages.Add(code,simplecode);
        }

        spokenLanguages = spokenLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        FromLanguage.Items.Clear();
        foreach (var language in spokenLanguages)
        {
            FromLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key});
        }

        // Gather the set of text translation languages
        foreach (JProperty jText in jResponse["text"])
        {
            JObject languageDetails = (JObject)jText.Value;
            string code = jText.Name;
            string displayName = languageDetails["name"].ToString();
            textLanguages.Add(code, displayName);

            string direction = languageDetails["dir"].ToString().ToLowerInvariant();
            bool LTR = true;
            if (direction.ToLowerInvariant() == "rtl") LTR = false;
            isLTR.Add(code, LTR);
        }

        textLanguages = textLanguages.OrderBy(x => x.Value).ToDictionary(x => x.Key, x => x.Value);
        ToLanguage.Items.Clear();
        foreach (var language in textLanguages)
        {
            ToLanguage.Items.Add(new ComboBoxItem() { Content = language.Value, Tag = language.Key });
        }

        if (Properties.Settings.Default.FromLanguageIndex >= 0) FromLanguage.SelectedIndex = Properties.Settings.Default.FromLanguageIndex;
        else
        {
            for(int i=0; i < FromLanguage.Items.Count; ++i)
            {
                ComboBoxItem item = (ComboBoxItem)FromLanguage.Items[i];
                if(CultureInfo.CurrentUICulture.Name.Equals((string)item.Tag, StringComparison.OrdinalIgnoreCase))
                {
                    FromLanguage.SelectedIndex = i;
                }
            }
        }
        if (Properties.Settings.Default.ToLanguageIndex >= 0) ToLanguage.SelectedIndex = Properties.Settings.Default.ToLanguageIndex;
        else
        {
            Random rnd = new Random(DateTime.Now.Millisecond);
            ToLanguage.SelectedIndex = (rnd.Next() % textLanguages.Count);
        }
    }
}
```

Cette méthode construit d’abord une requête HTTP pour le point de terminaison des langues, en demandant les trois listes de langues (`text`, `speech` et `tts`).

Le point de terminaison des langues utilise l’en-tête `Accept-Languages` de la requête pour déterminer la langue dans laquelle les noms des langues sont représentés. Par exemple, la langue allemande connue auprès des anglophones sous le nom « German » est appelée « Deutsch » en allemand et « Alemán » en espagnol, et la liste des langues reflète ces différences. La langue par défaut du système est utilisée pour cet en-tête.

Une fois que la requête a été envoyée et que la réponse JSON a été reçue, la réponse est analysée dans les structures de données internes. Ces structures sont ensuite utilisées pour construire les menus des langues source et cible. 

Étant donné que les voix disponibles dépendent de la langue cible choisie par l’utilisateur, il n’est pas encore possible de configurer le menu Voix. À la place, les voix disponibles pour chaque langue sont stockées pour une utilisation ultérieure. Le gestionnaire `ToLanguage_SelectionChanged` (dans le même fichier source) met à jour ultérieurement le menu Voix en appelant `UpdateVoiceComboBox()` lorsque l’utilisateur choisit une langue cible. 

De façon ludique, une langue cible est sélectionnée aléatoirement si l’utilisateur n’a pas exécuté l’application auparavant. (Les paramètres de menu sont enregistrés entre les sessions.)

## <a name="authenticating-requests"></a>Authentification des demandes

Pour vous authentifier auprès du service Microsoft Translator Speech, vous devez envoyer votre clé d’abonnement Azure dans l’en-tête comme valeur de `Ocp-Apim-Subscription-Key` dans la demande de connexion.

## <a name="translation-overview"></a>Vue d’ensemble de la traduction

L’API de traduction (point de terminaison WebSocket `wss://dev.microsofttranslator.com/speech/translate`) accepte la traduction de l’audio au format WAVE signé monophonique 16 kHz, 16 bits. Le service retourne une ou plusieurs réponses JSON contenant à la fois le texte reconnu et le texte traduit. Si la synthèse vocale a été demandée, un fichier audio est envoyé.

L’utilisateur choisit la source audio à l’aide du menu Microphone/Entrée de fichier. L’audio peut provenir d’un périphérique audio (par exemple, un microphone) ou d’un fichier `.WAV`.

La méthode `StartListening_Click` est appelée lorsque l’utilisateur clique sur le bouton Démarrer. Ce gestionnaire d’événements, à son tour, appelle `Connect()` pour commencer le processus d’envoi de l’audio au point de terminaison de l’API de service. La méthode `Connect()` effectue les tâches suivantes :


> [!div class="checklist"]
> * Obtention des paramètres utilisateur à partir de la fenêtre principale et validation de ces paramètres
> * Initialisation des flux d’entrée et de sortie audio
> * Appel de `ConnectAsync()` pour gérer le reste du travail

`ConnectAsync()`, à son tour, gère les tâches suivantes :

> [!div class="checklist"]
> * Authentification avec la clé d’abonnement Azure dans l’en-tête `Ocp-Apim-Subscription-Key`
> * Création d’une instance `SpeechClient` (trouvée dans `SpeechClient.cs`) pour communiquer avec le service
> * Initialisation des instances `TextMessageDecoder` et `BinaryMessageDecoder` (voir `SpeechResponseDecoder.cs`) pour gérer les réponses
> * Envoi de l’audio via l’instance `SpeechClient` au service Translator Speech
> * Réception et traitement des résultats de traduction

Les responsabilités de `SpeechClient` sont moins nombreuses :

> [!div class="checklist"]
> * Établissement d’une connexion WebSocket au service Translator Speech
> * Envoi des données audio et réception des réponses via le socket

## <a name="a-closer-look"></a>Examen approfondi

Vous devriez à présent mieux comprendre comment les parties de l’application fonctionnent ensemble pour effectuer la requête de traduction. Examinons un code, en mettant l’accent sur les parties pertinentes.

Voici une version partielle de `Connect()` qui affiche la configuration des flux audio :

```csharp
private void Connect()
{
    if (this.currentState != UiState.ReadyToConnect) return;

    Stopwatch watch = Stopwatch.StartNew();
    UpdateUiState(UiState.Connecting);

    // Omitted: code to validate UI settings

    string tag = ((ComboBoxItem)Mic.SelectedItem).Tag as string;
    string audioFileInputPath = null;
    if (tag == "File")
    {
        audioFileInputPath = this.AudioFileInput.Text;
        foreach (string currFile in audioFileInputPath.Split('|'))
        {
            if (!File.Exists(currFile))
            {
                SetMessage(String.Format($"Invalid audio source: selected file {currFile} does not exist."), "", MessageKind.Error);
                UpdateUiState(UiState.ReadyToConnect);
                return;
            }
        }
    }
    bool shouldSuspendInputAudioDuringTTS = this.CutInputAudioCheckBox.IsChecked.HasValue ? this.CutInputAudioCheckBox.IsChecked.Value : false;

    correlationId = Guid.NewGuid().ToString("D").Split('-')[0].ToUpperInvariant();

    // Setup speech translation client options
    SpeechClientOptions options;

    string voicename = "";
    if (this.Voice.SelectedItem != null)
    {
        voicename = ((ComboBoxItem)this.Voice.SelectedItem).Tag.ToString();
    }
    options = new SpeechTranslateClientOptions()
    {
        TranslateFrom = ((ComboBoxItem)this.FromLanguage.SelectedItem).Tag.ToString(),
        TranslateTo = ((ComboBoxItem)this.ToLanguage.SelectedItem).Tag.ToString(),
        Voice = voicename,
    };
    
    options.Hostname = baseUrl;
    options.AuthHeaderKey = "Authorization";
    options.AuthHeaderValue = ""; // set later in ConnectAsync.
    options.ClientAppId = new Guid("EA66703D-90A8-436B-9BD6-7A2707A2AD99");
    options.CorrelationId = this.correlationId;
    options.Features = GetFeatures().ToString().Replace(" ", "");
    options.Profanity = ((SpeechClient.ProfanityFilter)Enum.Parse(typeof(SpeechClient.ProfanityFilter), GetProfanityLevel(), true)).ToString();
    options.Experimental = MenuItem_Experimental.IsChecked;

    // Setup player and recorder but don't start them yet.
    WaveFormat waveFormat = new WaveFormat(16000, 16, 1);

    // WaveProvider for incoming TTS
    // We use a rather large BufferDuration because we need to be able to hold an entire utterance.
    // TTS audio is received in bursts (faster than real-time).
    textToSpeechBytes = 0;
    playerTextToSpeechWaveProvider = new BufferedWaveProvider(waveFormat);
    playerTextToSpeechWaveProvider.BufferDuration = TimeSpan.FromMinutes(5);

    ISampleProvider sampleProvider = null;
    if (audioFileInputPath != null)
    {
        // Setup mixing of audio from input file and from TTS
        playerAudioInputWaveProvider = new BufferedWaveProvider(waveFormat);
        var srce1 = new Pcm16BitToSampleProvider(playerTextToSpeechWaveProvider);
        var srce2 = new Pcm16BitToSampleProvider(playerAudioInputWaveProvider);
        var mixer = new MixingSampleProvider(srce1.WaveFormat);
        mixer.AddMixerInput(srce1);
        mixer.AddMixerInput(srce2);
        sampleProvider = mixer;
    }
    else
    {
        recorder = new WaveIn();
        recorder.DeviceNumber = (int)((ComboBoxItem)Mic.SelectedItem).Tag;
        recorder.WaveFormat = waveFormat;
        recorder.DataAvailable += OnRecorderDataAvailable;
        sampleProvider = playerTextToSpeechWaveProvider.ToSampleProvider();
    }

    if (!batchMode)
    {
        player = new WaveOut();
        player.DeviceNumber = (int)((ComboBoxItem)Speaker.SelectedItem).Tag;
        player.Init(sampleProvider);
    }

    this.audioBytesSent = 0;

    string logAudioFileName = null;
    if (LogSentAudio.IsChecked|| LogReceivedAudio.IsChecked)
    {
        string logAudioPath = System.IO.Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.ApplicationData), Properties.Settings.Default.OutputDirectory);
        try
        {
            Directory.CreateDirectory(logAudioPath);
        }
        catch
        {
            this.AddItemToLog(string.Format("Could not create folder {0}", logAudioPath));
        }

        if (LogSentAudio.IsChecked)
        {
            logAudioFileName = System.IO.Path.Combine(logAudioPath, string.Format("audiosent_{0}.wav", this.correlationId));
        }

        if (LogReceivedAudio.IsChecked)
        {
            string fmt = System.IO.Path.Combine(logAudioPath, string.Format("audiotts_{0}_{{0}}.wav", this.correlationId));
            this.audioReceived = new BinaryMessageDecoder(fmt);
        }
    }
}
```

Une partie importante de `Connect()` concerne la création d’une instance `SpeechClientOptions` (voir `SpeechClientOptions.cs`) devant contenir les options de traduction. Les options incluent les informations nécessaires pour se connecter au service (par exemple, la clé d’authentification et le nom d’hôte) et les fonctionnalités utilisées pour la traduction. Ici les champs sont mappés aux champs d’en-tête et aux paramètres HTTP exposés par [l’API de traduction de conversation Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference).

`Connect()` crée et initialise également le périphérique d’entrée audio (variable `sampleProvider`) qui sert de source pour le texte vocal devant être converti. Ce périphérique est soit un périphérique d’entrée matériel, par exemple un microphone, soit un fichier contenant des données audio WAVE.

Voici la méthode `ConnectAsync()` qui instancie la classe `speechClient` et connecte les fonctions anonymes pour gérer les réponses texte et binaires à partir du service.

```csharp
private async Task ConnectAsync(SpeechClientOptions options, bool suspendInputAudioDuringTTS)
{
    await ADMAuthenticate(options);
    
    TextMessageDecoder textDecoder;
    
    s2smtClient = new SpeechClient((SpeechTranslateClientOptions)options, CancellationToken.None);
    
    s2smtClient.OnBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnEndOfBinaryData += (c, a) => { AddSamplesToPlay(a, suspendInputAudioDuringTTS); };
    s2smtClient.OnTextData += (c, a) => { textDecoder.AppendData(a); lastReceivedPacketTick = DateTime.Now.Ticks; };
    s2smtClient.OnEndOfTextData += (c, a) =>
    {
        textDecoder.AppendData(a);
        lastReceivedPacketTick = DateTime.Now.Ticks;
        textDecoder
            .Decode()
            .ContinueWith(t =>
            {
                if (t.IsFaulted)
                {
                    Log(t.Exception, "E: Failed to decode incoming text message.");
                }
                else
                {
                    object msg = t.Result;
                    if (msg.GetType() == typeof(FinalResultMessage))
                    {
                        // omitted: code to process final binary result
                    }
                    if (msg.GetType() == typeof(PartialResultMessage))
                    {
                        // omitted: code to process partial binary result
                    }
                }
            });
    };
    s2smtClient.Failed += (c, ex) =>
    {
        Log(ex, "E: SpeechTranslation client reported an error.");
    };
    s2smtClient.Disconnected += (c, ea) =>
    {
        SafeInvoke(() =>
        {
            // We only care to react to server disconnect when our state is Connected. 
            if (currentState == UiState.Connected)
            {
                Log("E: Connection has been lost.");
                Log($"E: Errors (if any): \n{string.Join("\n", s2smtClient.Errors)}");
                Disconnect();
            }
        });
    };
    await s2smtClient.Connect();
}
```

Après l’authentification, la méthode crée l’instance `SpeechClient`. La classe `SpeechClient` (dans `SpeechClient.cs`) appelle les gestionnaires d’événements sur des données binaires et textuelles de réception. Des gestionnaires supplémentaires sont appelés lorsque la connexion échoue ou se déconnecte.

Les données binaires sont de l’audio (sortie vocale) envoyé par le service lorsque la synthèse vocale est activée. Les données de texte sont une traduction partielle ou totale du texte parlé. Après l’instanciation, la méthode intercepte les fonctions pour gérer ces messages : messages audio en les stockant pour une lecture ultérieure et messages texte en les affichant dans la fenêtre.

## <a name="next-steps"></a>Étapes suivantes

Cet exemple de code est une application riche en fonctionnalités illustrant l’utilisation de l’API Translator Speech. Aussi, un certain nombre d’éléments mobiles doivent être compris. Vous avez passé en revue les parties les plus importantes. Pour le reste, il peut être utile de définir quelques points d’arrêt dans Visual Studio et de suivre les étapes du processus de traduction. Lorsque vous comprenez l’exemple d’application, vous êtes prêt à utiliser le service Translator Speech dans vos propres applications.

> [!div class="nextstepaction"]
> [Référence de l’API Microsoft Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/reference)
