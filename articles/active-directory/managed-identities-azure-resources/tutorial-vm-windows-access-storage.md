---
title: Utiliser une identité managée affectée par le système de machine virtuelle Windows pour accéder au Stockage Azure
description: Ce didacticiel vous guide tout au long de l’utilisation d’une identité managée affectée par le système de machine virtuelle Windows pour accéder au Stockage Azure.
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
ms.openlocfilehash: 46be9469e67a4f456be100823d475b8720262b1b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44163173"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Didacticiel : Utiliser une identité managée affectée par le système de machine virtuelle Windows pour accéder au Stockage Azure

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Ce didacticiel vous indique comment utiliser une identité managée affectée par le système pour une machine virtuelle Windows afin d’accéder au Stockage Azure. Vous allez apprendre à effectuer les actions suivantes :

> [!div class="checklist"]
> * Créer un conteneur d’objets blob dans un compte de stockage
> * Accorder l’accès à votre identité managée affectée par le système de machine virtuelle Windows à un compte de stockage 
> * Obtenir un accès et l’utiliser pour appeler le stockage Azure 

> [!NOTE]
> L’authentification Azure Active Directory pour le stockage Azure est en préversion publique.

## <a name="prerequisites"></a>Prérequis

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Connectez-vous au Portail Azure](https://portal.azure.com).

- [Créez une machine virtuelle Windows](/azure/virtual-machines/windows/quick-create-portal).

- [Activer l’identité managée affectée par le système sur votre machine virtuelle](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="create-a-storage-account"></a>Créez un compte de stockage. 

Dans cette section, vous créez un compte de stockage. 

1. Cliquez sur le bouton **+ Créer une ressource** dans le coin supérieur gauche du portail Azure.
2. Cliquez sur **Stockage**, puis sur **Compte de stockage - blob, fichier, table, file d’attente**.
3. Sous **Nom**, entrez un nom pour le compte de stockage.  
4. **Modèle de déploiement** et **Type de compte** doivent être définis sur **Gestionnaire des ressources** et **Storage (general purpose v1)** (Stockage (usage général v1)). 
5. Assurez-vous que les champs **Abonnement** et **Groupe de ressources** correspondent à ceux que vous avez spécifiés lorsque vous avez créé votre machine virtuelle à l’étape précédente.
6. Cliquez sur **Créer**.

    ![Créer un nouveau compte de stockage](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Créer un conteneur d’objets blob et charger un fichier vers le compte de stockage

Les fichiers nécessitent un stockage d’objets blob, vous devez donc créer un conteneur d’objets blob dans lequel stocker le fichier. Vous chargez ensuite un fichier vers le conteneur d’objets blob dans le nouveau compte de stockage.

1. Revenez à votre compte de stockage nouvellement créé.
2. Sous **Service Blob**, cliquez sur **Conteneurs**.
3. Cliquez sur **+ Conteneur** en haut de la page.
4. Sous **Nouveau conteneur**, entrez un nom pour le conteneur puis, sous **Public access level** (Niveau d’accès public), conservez la valeur par défaut.

    ![Créer un conteneur de stockage](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. À l’aide de l’éditeur de votre choix, créez un fichier intitulé *hello world.txt* sur votre ordinateur local.  Ouvrez le fichier et ajoutez le texte (sans les guillemets) « Hello world! :) », puis enregistrez-le. 
6. Chargez le fichier vers le conteneur nouvellement créé en cliquant sur le nom du conteneur, puis sur **Charger**
7. Dans le volet **Charger l’objet blob**, sous **Fichiers**, cliquez sur l’icône de dossier et recherchez le fichier **hello_world.txt** sur votre ordinateur local, sélectionnez le fichier, puis cliquez sur **Charger**.
    ![Charger un fichier texte](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Accorder à votre machine virtuelle l’accès au conteneur de stockage Azure 

Vous pouvez utiliser l’identité managée affectée par le système de machine virtuelle pour récupérer les données dans l’objet blob de stockage Azure.   

1. Revenez à votre compte de stockage nouvellement créé.  
2. Cliquez sur le lien **(IAM) de contrôle d’accès** dans le panneau de gauche.  
3. Cliquez sur **+ Ajouter** en haut de la page pour ajouter une nouvelle attribution de rôle pour votre machine virtuelle.
4. Sous **Rôle**, dans la liste déroulante, sélectionnez **Lecteur des données Blob du stockage (préversion)**. 
5. Dans la liste déroulante suivante, sous **Attribuer l’accès à**, choisissez **Machine virtuelle**.  
6. Ensuite, assurez-vous que l’abonnement approprié est répertorié dans la liste déroulante **Abonnement**, puis définissez **Groupe de ressources** sur **Tous les groupes de ressources**.  
7. Sous **Sélectionner**, choisissez votre machine virtuelle, puis cliquez sur **Enregistrer**. 

    ![Assigner des autorisations](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Obtenir un jeton d’accès et l’utiliser pour appeler le stockage Azure 

Le Stockage Azure prend en charge l’authentification Azure AD en mode natif, il peut donc accepter directement des jetons d’accès obtenus à l’aide d’une identité managée. Cela fait partie de l’intégration du stockage Azure avec Azure AD, et diffère de la fourniture d’informations d’identification sur la chaîne de connexion.

Voici un exemple de code .Net d’ouverture d’une connexion au stockage Azure à l’aide d’un jeton d’accès et de lecture du contenu du fichier que vous avez créé précédemment. Pour permettre l’accès au point de terminaison de l’identité managée de machine virtuelle, ce code doit s’exécuter sur la machine virtuelle. Pour pouvoir utiliser la méthode de jeton d’accès, .NET framework 4.6 ou version ultérieure est requis. Remplacez la valeur de `<URI to blob file>` en conséquence. Vous pouvez obtenir cette valeur en accédant au fichier que vous avez créé et chargé vers le stockage d’objets blob et en copiant l’**URL** sous **Propriétés** de la page **Vue d’ensemble**.

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

Dans ce didacticiel, vous avez appris à activer une identité managée affectée par le système de machine virtuelle Windows pour accéder au Stockage Azure.  Pour en savoir plus sur le stockage Azure, consultez :

> [!div class="nextstepaction"]
> [Stockage Azure](/azure/storage/common/storage-introduction)



