---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 11/25/2018
ms.author: crdun
ms.openlocfilehash: d71d52257b6e8cfa243207c9bfdb5c7de7d3dd37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "67177744"
---
1. Ouvrez le fichier de projet MainPage.xaml.cs et ajoutez les instructions **using** suivantes :
   
        using System.Linq;        
        using Windows.Security.Credentials;
2. Remplacez la méthode **AuthenticateAsync** par le code suivant :
   
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
   
            // This sample uses the Facebook provider.
            var provider = MobileServiceAuthenticationProvider.Facebook;
   
            // Use the PasswordVault to securely store and access credentials.
            PasswordVault vault = new PasswordVault();
            PasswordCredential credential = null;
   
            try
            {
                // Try to get an existing credential from the vault.
                credential = vault.FindAllByResource(provider.ToString()).FirstOrDefault();
            }
            catch (Exception)
            {
                // When there is no matching resource an error occurs, which we ignore.
            }
   
            if (credential != null)
            {
                // Create a user from the stored credentials.
                user = new MobileServiceUser(credential.UserName);
                credential.RetrievePassword();
                user.MobileServiceAuthenticationToken = credential.Password;
   
                // Set the user from the stored credentials.
                App.MobileService.CurrentUser = user;
   
                // Consider adding a check to determine if the token is 
                // expired, as shown in this post: https://aka.ms/jww5vp.
   
                success = true;
                message = string.Format("Cached credentials for user - {0}", user.UserId);
            }
            else
            {
                try
                {
                    // Sign in with the identity provider.
                    user = await App.MobileService
                        .LoginAsync(provider, "{url_scheme_of_your_app}");
   
                    // Create and store the user credentials.
                    credential = new PasswordCredential(provider.ToString(),
                        user.UserId, user.MobileServiceAuthenticationToken);
                    vault.Add(credential);
   
                    success = true;
                    message = string.Format("You are now signed in - {0}", user.UserId);
                }
                catch (MobileServiceInvalidOperationException)
                {
                    message = "You must sign in. Sign-In Required";
                }
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
   
            return success;
        }
   
    Dans cette version d’**AuthenticateAsync**, l’application essaie d’utiliser les informations d’identification stockées dans le **coffre de mots de passe** pour accéder au service. Une connexion normale est également effectuée quand il n'y a pas d'informations d'identification stockées.
   
   > [!NOTE]
   > Un jeton en cache peut avoir expiré et l'expiration des jetons peut également survenir après l'authentification, alors que l'application est en cours d'utilisation. Pour savoir comment déterminer si un jeton est arrivé à expiration, consultez [Rechercher les jetons d'authentification expirés](https://aka.ms/jww5vp). Pour une solution permettant de gérer les erreurs d'autorisation liées à des jetons expirés, consultez le post [Mise en cache et gestion des jetons expirés dans le Kit de développement logiciel (SDK) managé d'Azure Mobile Services](https://blogs.msdn.com/b/carlosfigueira/archive/2014/03/13/caching-and-handling-expired-tokens-in-azure-mobile-services-managed-sdk.aspx). 
   > 
   > 
3. Redémarrez l'application deux fois.
   
    Notez que lors du premier démarrage, la connexion avec le fournisseur est à nouveau requise. Cependant, lors du second redémarrage, les informations d'identification mises en cache sont utilisées et l'étape de connexion est ignorée. 

