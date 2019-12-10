---
title: Créer un modèle de locataire (préversion) – Service Speech
titleSuffix: Azure Cognitive Services
description: Générez automatiquement un service Tenant Model (Custom Speech avec données Office 365) qui tire parti de vos données Office 365 pour fournir une reconnaissance vocale sécurisée, conforme et optimale pour les termes propres à votre organisation.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816384"
---
# <a name="create-a-tenant-model-preview"></a>Créer un modèle de locataire (préversion)

Tenant Model (Custom Speech avec données Office 365) est un service d’activation pour les clients d’entreprise Office 365, il génère automatiquement un modèle de reconnaissance vocale personnalisé à partir des données Office 365 de votre organisation. Le modèle créé est optimisé pour les termes techniques, le jargon et les noms de personnes, le tout d’une manière sécurisée et conforme.

> [!IMPORTANT]
> Si votre organisation s’inscrit à Tenant Model, le service Speech peut accéder au modèle de langage de votre organisation, celui-ci est généré à partir d’e-mails et de documents de groupes publics Office 365 affichables par toutes les personnes de votre organisation. L’administrateur Office 365 de votre organisation peut activer/désactiver l’utilisation du modèle de langage à l’échelle de l’entreprise à l’aide du portail d’administration Office 365.

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * S’inscrire pour utiliser un modèle de locataire dans le Centre d’administration Microsoft 365
> * Obtenir une clé d’abonnement Speech
> * Créer un modèle de locataire
> * Déployer un modèle de locataire
> * Utiliser un modèle de locataire avec le Kit de développement logiciel (SDK) Speech

## <a name="enroll-using-the-microsoft-365-admin-center"></a>S’inscrire à l’aide du Centre d’administration Microsoft 365

Avant de pouvoir déployer votre modèle de locataire, vous devez d’abord vous inscrire à l’aide du Centre d’administration Microsoft 365. Cette tâche ne peut être effectuée que par votre administrateur Microsoft 365.

1. Connectez-vous au [Centre d’administration Microsoft 365](https://admin.microsoft.com ).
2. Dans le volet gauche, sélectionnez **Paramètres** puis **Applications**.

   ![Inscription à Tenant Model](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Recherchez et sélectionnez **Services de reconnaissance vocale Azure**.

   ![Inscription à Tenant Model 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Cliquez sur la case à cocher et enregistrez.

Si vous devez désactiver le modèle de locataire, revenez à cet écran, désactivez la case à cocher, puis enregistrez.

## <a name="get-a-speech-subscription-key"></a>Obtenir une clé d’abonnement Speech

Pour pouvoir utiliser un modèle de locataire avec le Kit de développement logiciel (SDK) Speech, vous avez besoin d’une ressource Speech et de sa clé d’abonnement associée.

1. Connectez-vous au [portail Azure](https://aka.ms/azureportal).
2. Sélectionnez **Créer une ressource**.
3. Dans la barre de recherche, tapez : **Speech**.
4. Sélectionnez **Speech**, puis cliquez sur **Créer**.
5. Suivez les instructions à l’écran pour créer votre ressource. Vérifiez les points suivants :
   * **Emplacement** est défini sur **eastus** ou **westus**.
   * **Niveau tarifaire** est défini sur **S0**.
6. Cliquez sur **Créer**.
7. Après quelques minutes, votre ressource est créée. La clé d’abonnement est disponible dans la section **Vue d’ensemble** de votre ressource.

## <a name="create-a-model"></a>Création d’un modèle

Une fois que votre administrateur a activé le modèle de locataire pour votre organisation, vous pouvez créer un modèle de langage basé sur vos données Office 365.

1. Connectez-vous à [Speech Studio](https://speech.microsoft.com/).
2. Dans le coin supérieur droit, recherchez et cliquez sur l’icône d’engrenage (paramètres), puis sélectionnez **Tenant Model settings** (Paramètres du modèle de locataire).

   ![Menu Paramètres](media/tenant-language-model/tenant-language-settings.png)

3. À ce stade, vous verrez un message vous indiquant si vous êtes qualifié pour créer un modèle de locataire.
   > [!NOTE]
   > Les clients Office 365 Enterprise en Amérique du Nord sont éligibles à la création d’un modèle de locataire (anglais). Si vous êtes un client Customer Lockbox (CLB) ou Customer Key (CK) ou Office 365 Secteur Public, cette fonctionnalité n’est pas disponible. Pour déterminer si vous êtes un client Customer Lockbox ou Customer Key, suivez les instructions ci-dessous :
   > * [Customer Lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Customer Key](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365 Secteur Public](https://www.microsoft.com/microsoft-365/government)

4. Ensuite, sélectionnez **Activer**. Vous recevrez un e-mail contenant des instructions lorsque votre modèle de locataire est prêt.

## <a name="deploy-your-model"></a>Déployer votre modèle

Lorsque votre modèle de locataire est prêt, procédez comme suit pour déployer votre modèle :

1. Cliquez sur le bouton **View Model** (Afficher le modèle) dans l’e-mail de confirmation que vous avez reçu ou connectez-vous à [Speech Studio](https://speech.microsoft.com/).
2. Dans le coin supérieur droit, recherchez et cliquez sur l’icône d’engrenage (paramètres), puis sélectionnez **Tenant Model settings** (Paramètres du modèle de locataire).

   ![Menu Paramètres](media/tenant-language-model/tenant-language-settings.png)

3. Cliquez sur **Deploy** (Déployer).
4. Lorsque votre modèle est déployé, l’état passe à **Deployed** (Déployé).

## <a name="use-your-model-with-the-speech-sdk"></a>Utiliser votre modèle avec le Kit de développement logiciel (SDK) Speech

Maintenant que vous avez déployé votre modèle, vous pouvez l’utiliser avec le Kit de développement logiciel (SDK) Speech. Dans cette section, vous allez utiliser l’exemple de code fourni pour appeler le service Speech à l’aide de l’authentification Azure AD.

Examinons le code que vous utiliserez pour appeler le Kit de développement logiciel (SDK) Speech dans C#. Dans cet exemple, vous allez effectuer la reconnaissance vocale à l’aide d’un modèle de locataire. Ce guide suppose que votre plateforme est déjà configurée. Si vous avez besoin d’aide pour la configuration, consultez [Démarrage rapide : Reconnaissance vocale, C# (.NET Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

Copiez ce code dans votre projet :

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // Note: ServiceApplicationId is a fixed value.  No need to change.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Vous devez ensuite régénérer et exécuter le projet à partir de la ligne de commande. Vous devez mettre à jour quelques paramètres avant d’exécuter la commande.

1. Remplacez `<Username>` et `<Password>` par les valeurs d’un utilisateur locataire valide.
2. Remplacez `<Subscription-Key>` par la clé d’abonnement de votre ressource Speech. Cette valeur est disponible dans la section **Vue d’ensemble** de votre ressource Speech dans le [portail Azure](https://aka.ms/azureportal).
3. Remplacez `<Endpoint-Uri>` par le point de terminaison ci-dessous. Veillez à remplacer `{your-region}` par la région dans laquelle votre ressource Speech a été créée. Ces régions sont prises en charge : `westus`, `westus2` et `eastus`. Vos informations de région sont disponibles dans la section **Vue d’ensemble** de votre ressource Speech dans le [portail Azure](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Exécutez la commande suivante :
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Étapes suivantes

* [Speech Studio](https://speech.microsoft.com/)
* [Kit de développement logiciel (SDK) de reconnaissance vocale](speech-sdk.md)
