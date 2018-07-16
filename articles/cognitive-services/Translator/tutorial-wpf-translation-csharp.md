---
title: Comment écrire une application Microsoft Translator WPF avec C#, Azure Cognitive Services | Microsoft Docs
description: Découvrez comment utiliser le service de texte Translator pour traduire du texte, obtenir la liste localisée des langues prises en charge et bien plus encore.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35368656"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Comment écrire une application Microsoft Translator WPF dans C#

Dans ce didacticiel, nous allons construire un outil de traduction de texte interactif à l’aide d’API de traduction de texte Microsoft Translator Text (V3), qui fait partie de Microsoft Cognitive Services dans Azure. Vous découvrirez comment effectuer les actions suivantes :

> [!div class="checklist"]
> * Demander une liste des codes courts pour les langues prises en charge par le service
> * Récupérer une liste des noms de langue localisés correspondant à ces codes de langue
> * Obtenir la version traduite du texte entré par l’utilisateur d’une langue vers une autre

Cette application comprend également l’intégration avec deux autres services Microsoft Cognitive Services.

|||
|-|-|
|[Analyse de texte](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Permet de détecter automatiquement, si vous le souhaitez, la langue source du texte à traduire|
|[Vérification orthographique Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Pour le texte source en anglais, utilisé pour corriger les fautes d’orthographe afin d’obtenir une meilleure traduction

![[Programme du didacticiel en cours d’exécution]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Prérequis

Pour suivre ce didacticiel, vous avez besoin de n’importe quelle édition de Visual Studio 2017, y compris la Community Edition.

Vous avez également besoin des clés d’abonnement pour les trois services Azure utilisés dans le programme. Vous pouvez obtenir une clé pour le service Translator Text à partir du tableau de bord Azure. Un niveau tarifaire gratuit est disponible : il vous permet de traduire jusqu’à deux millions de caractères par mois sans aucun frais.

Les deux services Analyse de texte et Vérification orthographique Bing offrent des essais gratuits, pour lesquels vous pouvez vous inscrire sur [Essayez Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Vous pouvez également créer un abonnement pour chaque service via le tableau de bord Azure. Analyse de texte dispose d’un niveau gratuit.

Le code source pour ce didacticiel est disponible ci-dessous. Vos clés d’abonnement doivent être copiées dans le code source en tant que variables `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`, et ainsi de suite, dans `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Le service Analyse de texte est disponible dans plusieurs régions. L’URI dans notre code source du didacticiel se trouve dans la région `westus`, qui représente la région utilisée pour les essais gratuits. Si vous avez un abonnement dans une autre région, mettez à jour cet URI en conséquence.

## <a name="source-code"></a>Code source

Voici le code source pour l’API Microsoft Translator Text. Pour exécuter l’application, copiez le code source dans le fichier approprié dans un nouveau projet WPF dans Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Il s’agit du fichier code-behind qui fournit des fonctionnalités de l’application.

```cs
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog when we get an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Ce fichier définit l’interface utilisateur de l’application, un formulaire WPF. Si vous souhaitez créer votre propre version du formulaire, vous n’avez pas besoin de ce code XAML.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox" 
                HorizontalAlignment="Left" 
                Margin="306,88,0,0" 
                VerticalAlignment="Top" 
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox" 
            HorizontalAlignment="Left" 
            Margin="42,88,0,0" 
            VerticalAlignment="Top" 
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Points de terminaison de service

Le service Microsoft Translator possède plusieurs points de terminaison qui fournissent différentes fonctionnalités de traduction. Celles que nous utilisons dans ce didacticiel sont les suivantes :

|||
|-|-|
|`Languages`|Retourne l’ensemble de langues actuellement prises en charge par d’autres opérations de l’API Translator Text.|
|`Translate`|Selon le texte source, le code de langue source et le code de langue cible donnés, retourne une traduction du texte source dans la langue cible.|

## <a name="the-translation-app"></a>Application de traduction

L’interface utilisateur de notre application de traduction est générée à l’aide de Windows Presentation Foundation (WPF). Créez un nouveau projet WPF dans Visual Studio en suivant ces étapes.

* Dans le menu **Fichier**, choisissez **Nouveau > Projet**.
* Dans la fenêtre Nouveau projet, ouvrez **Installé > Modèles > Visual C#**. Une liste des modèles de projet disponibles s’affiche dans le centre de la boîte de dialogue.
* Assurez-vous que **.NET Framework 4.5.2** est choisi dans le menu déroulant au-dessus de la liste de modèles de projet.
* Cliquez sur **l’application WPF (.NET Framework)** dans la liste de modèles de projet.
* À l’aide des champs en bas de la boîte de dialogue, nommez le nouveau projet et la solution qui le contient.
* Cliquez sur **OK** pour créer le nouveau projet et la solution.

![[Création d’une nouvelle application WPF dans Visual Studio]](media/translator-text-csharp-new-project.png)

Ajoutez des références aux assemblys .NET Framework suivants à votre projet.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Installez également le package NuGet `Newtonsoft.Json` dans votre projet.

À présent, trouvez le fichier `MainWindow.xaml` dans l’Explorateur de solutions et ouvrez-le. Il est initialement vide. Voici ce l’apparence que l’interface utilisateur terminée devrait avoir, annotée avec les noms des contrôles en bleu. Utilisez les mêmes noms pour les contrôles dans votre interface utilisateur, dans la mesure où ils apparaissent également dans le code.

![[Vue annotée de la fenêtre principale dans Visual Studio Designer]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Le code source pour ce didacticiel inclut la source XAML pour ce formulaire. Vous pouvez le coller dans votre projet au lieu de générer le formulaire dans Visual Studio.

* `FromLanguageComboBox` *(zone de liste déroulante)* - Affiche une liste des langues prises en charge par Microsoft Translator pour la traduction de texte. L’utilisateur sélectionne la langue de son texte d’origine.
* `ToLanguageComboBox` *(zone de liste déroulante)* - Affiche la même liste de langues que `FromComboBox`, mais permet de sélectionner la langue cible du texte à traduire.
* `TextToTranslate` *(zone de texte)* - L’utilisateur entre le texte à traduire ici.
* `TranslateButton` *(bouton)* - L’utilisateur clique sur ce bouton (ou appuie sur Entrée) pour traduire le texte.
* `TranslatedTextLabel` *(étiquette)*  -La traduction du texte de l’utilisateur s’affiche ici.

Si vous créez votre propre version de ce formulaire, il n’est pas nécessaire de le présenter *exactement* comme le nôtre. Cependant, assurez-vous que les listes déroulantes des langues sont assez larges pour éviter de tronquer un nom de langue.

## <a name="the-mainwindow-class"></a>Classe MainWindow

Le fichier code-behind `MainWindow.xaml.cs` contient le code qui va ordonner au programme d’exécuter son opération. Le travail s’effectue à deux moments :

* Lorsque le programme démarre. Lorsque `MainWindow` est instancié, nous récupérons la liste des langues à l’aide des API `GetLanguagesForTranslate` et `GetLanguageNames` de Translator et remplissons ses menus déroulants avec ces dernières. Cette tâche est effectuée une seule fois, au début de chaque session.

* Lorsque l’utilisateur clique sur le bouton **Traduire**, nous récupérons les sélections de langue de l’utilisateur et le texte qu’il a entré. Puis, nous appelons l’API `Translate` pour effectuer la traduction. Nous pouvons également appeler d’autres fonctions pour déterminer la langue du texte et pour corriger son orthographe avant la traduction.

Voyons de quelle façon commencer notre classe :

```cs
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog when we get an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Deux variables membres déclarées ici contiennent des informations sur nos langues :

|||
|-|-|
|`languageCodes`<br>tableau de chaînes|Met en cache les codes de langue. Le service Translator utilise des codes courts, tel que `en` pour l’anglais, pour identifier les langues.|
|`languageCodesAndTitles`<br>SortedDictionary|Mappe les noms « conviviaux » dans l’interface utilisateur aux codes courts utilisés dans l’API. Reste trié par ordre alphabétique sans respecter la casse.|

Le premier code exécuté par notre application est le constructeur `MainWindow`. Tout d’abord, nous configurons la méthode `HandleExceptions` en tant que gestionnaire d’erreurs global. Ainsi, au moins, nous obtenons un message d’erreur si une exception n’est pas gérée.

Ensuite, nous vérifions que les clés d’abonnement à l’API comprennent toutes exactement 32 caractères. Si ce n’est pas le cas, la raison la plus probable serait que *quelqu’un* n’a pas collé ses clés de l’API (tsk). Dans ce cas, nous affichons un message d’erreur et abandonnons. (La réussite de ce test ne signifie pas que les clés sont valides, bien évidemment.)

Si nous avons des clés qui, au moins, possèdent la bonne longueur, l’appel `InitializeComponent()` propage l’interface utilisateur en recherchant, chargeant et instanciant la description XAML de la fenêtre principale de l’application.

Enfin, nous configurons les menus déroulants des langues. Cette tâche nécessite trois appels de méthode distincts. Chacune de ces méthodes est détaillée dans les sections suivantes.

## <a name="get-supported-languages"></a>Obtenir les langues prises en charge

Le service Microsoft Translator prend en charge un total de 61 langues à ce jour, et d’autres langues peuvent être ajoutées ponctuellement. Par conséquent, il est conseillé de pas coder en dur les langues prises en charge dans votre programme. À la place, demandez au service Translator les langues qu’il prend en charge. N’importe quelle langue prise en charge peut être traduite dans n’importe quelle autre langue prise en charge.

Appelez l’API `Languages` pour obtenir la liste des langues prises en charge.

L’API `Languages` prend un paramètre de requête GET facultatif, *scope*. *scope* peut avoir l’une des trois valeurs suivantes : `translation`, `transliteration` ou `dictionary`. Nous allons utiliser la valeur `translation`.

L’API `Languages` prend également un en-tête HTTP facultatif, `Accept-Language`. La valeur de cet en-tête détermine la langue dans laquelle les noms des langues prises en charge sont retournés. La valeur doit être une balise de langue BCP 47 bien formée. Nous allons utiliser la valeur `en` pour obtenir les noms de langue en anglais.

L’API `Languages` renvoie une réponse JSON qui ressemble à ce qui suit.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Nous souhaitons extraire les codes de langue (par exemple, `af`) et les noms de langue (par exemple, `Afrikaans`). Nous utilisons la méthode NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) pour effectuer ces opérations.

Avec ces connaissances du contexte, nous créons la méthode suivante pour récupérer les codes de langue et leurs noms.

```cs
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` crée d’abord la requête HTTP. Le paramètre de chaîne de requête `scope=translation` spécifie que nous souhaitons la prise en charge des langues pour la traduction du texte. Nous ajoutons la clé d’abonnement à l’API Traduction de texte pour les en-têtes de requête. Nous ajoutons également l’en-tête `Accept-Language` avec la valeur `en` pour indiquer que nous souhaitons renvoyer les langues prises en charge en anglais.

Une fois la requête terminée, nous analysons la réponse JSON et la convertissons en un dictionnaire. Nous ajoutons les codes de langue à la variable membre `languageCodes`. Nous exécutons ensuite une boucle sur les paires clé/valeur contenant les codes de langue et les noms de langue conviviaux et nous les ajoutons à la variable membre `languageCodesAndTitles`. (Les menus déroulants dans notre formulaire affichent les noms conviviaux, mais nous avons besoin des codes pour demander la traduction.)

## <a name="populate-the-language-menus"></a>Remplir les menus de langues

La majeure partie de notre interface utilisateur est définie en XAML, par conséquent, peu d’actions sont nécessaires pour la configurer, en dehors de l’appel à `InitializeComponent()`. La seule chose nous devons faire est ajouter les noms de langue conviviaux aux listes déroulantes des langues source et cible, ce qui est effectué dans `PopulateLanguageMenus()`.

```cs
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Remplir les menus consiste simplement à itérer sur le dictionnaire `languageCodesAndTitles` et à ajouter chaque clé, qui représente le nom « convivial », aux deux menus. Après avoir rempli les menus, nous définissons les langues source et cible par défaut à détecter (pour détecter automatiquement la langue) et l’anglais.

> [!TIP]
> Si nous ne définissons pas de sélection par défaut pour nos menus, l’utilisateur peut cliquer sur **Traduire** sans choisir de langue source ou cible. Les valeurs par défaut évitent d’avoir à résoudre ce problème.

Maintenant, `MainWindow` a été initialisé, créant ainsi l’interface utilisateur. Nous n’avons plus le contrôle tant que l’utilisateur ne clique pas sur le bouton **Traduire**.

## <a name="perform-translation"></a>Effectuer la traduction

Lorsque l’utilisateur clique sur **Traduire**, WPF appelle le gestionnaire d’événements `TranslateButton_Click()`, illustré ici.

```cs
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Ici, nous récupérons tout d’abord les langues source et cible, ainsi que le texte que l’utilisateur a entré, à partir du formulaire.

Si la langue source est définie sur Détecter, nous appelons `DetectLanguage()` pour déterminer la langue du texte. Le texte peut être dans une langue que les API de Translator ne prennent pas en charge (de nombreuses autres langues pouvant être traduites peuvent être détectées) ou l’API Analyse de texte peut ne pas être capable de le détecter. Dans ce cas, nous affichons un message pour informer l’utilisateur et effectuons un retour sans traduction.

Si la langue source est l’anglais (qu’elle soit spécifiée ou détectée), nous vérifions l’orthographe du texte avec `CorrectSpelling()` et appliquons les corrections. Le texte corrigé est replacé dans le champ d’entrée afin que l’utilisateur sache que la correction a été effectuée. (L’utilisateur peut faire précéder le texte en cours de traduction d’un trait d’union pour supprimer la correction orthographique.)

Si l’utilisateur n’a entré aucun texte, ou si les langues source et cible sont identiques, aucune traduction n’est nécessaire. Dans ce cas, nous évitons d’effectuer la requête.

Le code permettant d’effectuer la requête de traduction doit paraître familier. Nous construisons l’URI, nous créons une requête, nous l’envoyons et nous analysons la réponse. Pour afficher le texte, nous le stockons dans le contrôle `TranslatedTextLabel`.

Nous transmettons le texte à l’API `Translate` dans un tableau JSON sérialisé dans le corps d’une requête POST. Le tableau JSON peut contenir plusieurs textes à convertir, mais ici nous n’en incluons qu’un seul.

L’en-tête HTTP nommé `X-ClientTraceId` est facultatif. La valeur doit être un GUID. L’ID de suivi fourni par le client est utile pour suivre les requêtes lorsque les opérations ne se déroulent pas comme prévu. Toutefois, pour être utile, la valeur de X-ClientTraceID doit être enregistrée par le client. Un ID de suivi du client et la date des requêtes peuvent aider Microsoft à diagnostiquer les problèmes qui peuvent se produire.

> [!NOTE]
> Ce didacticiel étant axé sur le service Microsoft Translator, nous ne couvrons donc pas les méthodes `DetectLanguage()` et `CorrectSpelling()` en détail. Les services Analyse de texte et Vérification orthographique Bing fournissent des réponses en JSON plutôt qu’en XML, et Analyse de texte requiert que la requête soit au format JSON également. Ces caractéristiques représentent la plupart des différences de code existant avec les méthodes que nous avons déjà vues.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Référence de l’API Microsoft Translator Text](http://docs.microsofttranslator.com/text-translate.html)
