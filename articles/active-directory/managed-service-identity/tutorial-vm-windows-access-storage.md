---
title: Utiliser une identité gérée de machine virtuelle Windows pour accéder au stockage Azure
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité gérée de machine virtuelle Windows pour accéder au stockage Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: a9b0ddbd8d0a348d00e57526e6d248e59a8a1e79
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/07/2018
ms.locfileid: "37903954"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Didacticiel : utiliser une identité gérée de machine virtuelle Windows pour accéder au stockage Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous montre comment activer une identité gérée pour une machine virtuelle Windows et utiliser cette identité pour accéder au stockage Azure.  Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Création d'une machine virtuelle Windows dans un nouveau groupe de ressources 
> * Activer une identité gérée sur une machine virtuelle Windows
> * Créer un conteneur d’objets blob dans un compte de stockage
> * Accorder l’accès à votre identité gérée de machine virtuelle Windows à un compte de stockage 
> * Obtenir un accès et l’utiliser pour appeler le stockage Azure 

> [!NOTE]
> L’authentification Azure Active Directory pour le stockage Azure est en préversion publique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Création d'une machine virtuelle Windows dans un nouveau groupe de ressources

Dans cette section, vous créez une machine virtuelle Windows à laquelle une identité gérée est assignée ultérieurement.

1.  Cliquez sur le bouton **+/Créer un service** dans l’angle supérieur gauche du portail Azure.
2.  Sélectionnez **Compute**, puis **Windows Server 2016 Datacenter**. 
3.  Saisissez les informations de la machine virtuelle. Le **Nom d’utilisateur** et le **Mot de passe** créés ici sont les informations d’identification nécessaires pour vous connecter à la machine virtuelle.
4.  Choisissez un **Abonnement** approprié pour la machine virtuelle dans la liste déroulante.
5.  Pour sélectionner un nouveau **Groupe de ressources** dans lequel vous souhaitez créer une machine virtuelle, choisissez **Créer un nouveau**. Lorsque vous avez terminé, cliquez sur **OK**.
6.  Choisissez la taille de la machine virtuelle. Pour voir plus de tailles, sélectionnez **Afficher tout** ou modifiez le filtre **Type de disque pris en charge**. Conservez les valeurs par défaut dans le panneau des paramètres et cliquez sur **OK**.

    ![Texte de remplacement d’image](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>Activer une identité gérée sur votre machine virtuelle

L’identité gérée d’une machine virtuelle permet d’obtenir des jetons d’accès d’Azure AD sans avoir à placer des informations d’identification dans votre code. En arrière-plan, l’activation de l’identité gérée sur une machine virtuelle via le portail Azure effectue deux opérations : elle enregistre votre machine virtuelle avec Azure AD pour créer une identité managée et configure l’identité sur la machine virtuelle. 

1. Accédez au groupe de ressources de votre nouvelle machine virtuelle et sélectionnez la machine virtuelle que vous avez créée à l’étape précédente.
2. Sous la catégorie **Paramètres**, cliquez sur **Configuration**.
3. Pour activer l’identité gérée, sélectionnez **Oui**.
4. Cliquez sur **Enregistrer** pour appliquer la configuration. 

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Dans cette section, vous créez un compte de stockage. 

1. Cliquez sur le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis sur **Compte de stockage - blob, fichier, table, file d’attente**.
3. Sous **Nom**, entrez un nom pour le compte de stockage.  
4. **Modèle de déploiement** et **Type de compte** doivent être définis sur **Gestionnaire des ressources** et **Storage (general purpose v1)** (Stockage (usage général v1)). 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Créer**.

    ![Créer un nouveau compte de stockage](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Créer un conteneur d’objets blob et charger un fichier vers le compte de stockage

Les fichiers nécessitent un stockage d’objets blob, vous devez donc créer un conteneur d’objets blob dans lequel stocker le fichier. Vous chargez ensuite un fichier vers le conteneur d’objets blob dans le nouveau compte de stockage.

1. Revenez à votre compte de stockage nouvellement créé.
2. Sous **Service Blob**, cliquez sur **Conteneurs**.
3. Cliquez sur **+ Conteneur** en haut de la page.
4. Sous **Nouveau conteneur**, entrez un nom pour le conteneur puis, sous **Public access level** (Niveau d’accès public), conservez la valeur par défaut.

    ![Créer un conteneur de stockage](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. À l’aide de l’éditeur de votre choix, créez un fichier intitulé *hello world.txt* sur votre ordinateur local.  Ouvrez le fichier et ajoutez le texte (sans les guillemets) « Hello world! :) », puis enregistrez-le. 
6. Chargez le fichier vers le conteneur nouvellement créé en cliquant sur le nom du conteneur, puis sur **Charger**
7. Dans le volet **Charger l’objet blob**, sous **Fichiers**, cliquez sur l’icône de dossier et recherchez le fichier **hello_world.txt** sur votre ordinateur local, sélectionnez le fichier, puis cliquez sur **Charger**.
    ![Charger un fichier texte](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Accorder à votre machine virtuelle l’accès au conteneur de stockage Azure 

Vous pouvez utiliser les identités gérées de la machine virtuelle pour récupérer les données dans l’objet blob de stockage Azure.   

1. Revenez à votre compte de stockage nouvellement créé.  
2. Cliquez sur le lien **(IAM) de contrôle d’accès** dans le panneau de gauche.  
3. Cliquez sur **+ Ajouter** en haut de la page pour ajouter une nouvelle attribution de rôle pour votre machine virtuelle.
4. Sous **Rôle**, dans la liste déroulante, sélectionnez **Lecteur des données Blob du stockage (préversion)**. 
5. Dans la liste déroulante suivante, sous **Attribuer l’accès à**, choisissez **Machine virtuelle**.  
6. Ensuite, assurez-vous que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**, puis définissez **Groupe de ressources** sur **Tous les groupes de ressources**.  
7. Sous **Sélectionner**, choisissez votre machine virtuelle, puis cliquez sur **Enregistrer**. 

    ![Assigner des autorisations](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Obtenir un jeton d’accès et l’utiliser pour appeler le stockage Azure 

Le stockage Azure prend en charge l’authentification Azure AD en mode natif, il peut donc accepter directement des jetons d’accès obtenus à l’aide d’une identité gérée. Cela fait partie de l’intégration du stockage Azure avec Azure AD, et diffère de la fourniture d’informations d’identification sur la chaîne de connexion.

Voici un exemple de code .Net d’ouverture d’une connexion au stockage Azure à l’aide d’un jeton d’accès et de lecture du contenu du fichier que vous avez créé précédemment. Pour permettre l’accès au point de terminaison de l’identité gérée de machine virtuelle, ce code doit s’exécuter sur la machine virtuelle. Pour pouvoir utiliser la méthode de jeton d’accès, .NET framework 4.6 ou version ultérieure est requis. Remplacez la valeur de `<URI to blob file>` en conséquence. Vous pouvez obtenir cette valeur en accédant au fichier que vous avez créé et chargé vers le stockage d’objets blob et en copiant l’**URL** sous **Propriétés** de la page **Vue d’ensemble**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

La réponse contient le contenu du fichier :

`Hello world! :)`

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris comment activer l’identité gérée d’une machine virtuelle Windows pour accéder au stockage Azure.  Pour en savoir plus sur le stockage Azure, consultez :

> [!div class="nextstepaction"]
> [Stockage Azure](/azure/storage/common/storage-introduction)



