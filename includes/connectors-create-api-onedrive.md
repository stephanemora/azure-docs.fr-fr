---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524203"
---
## <a name="prerequisites"></a>Conditions préalables requises

* Un compte Azure (que vous pouvez [créer gratuitement)](https://azure.microsoft.com/free)
* Un compte [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Pour pouvoir utiliser votre compte OneDrive dans une application logique, autorisez cette dernière à se connecter à votre compte OneDrive.  Cette opération est très simple à effectuer depuis votre application logique, sur le Portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte OneDrive, procédez comme suit :

1. Créez une application logique. Dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez « onedrive » dans la zone de recherche. Sélectionnez l’un des déclencheurs ou actions :  
   ![A dialog box titled "Show Microsoft managed APIs" has a search box that contains "onedrive". Below that is a list of four triggers. First on the list is "OneDrive - When a file is created", which is selected.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Si vous n’avez créé aucune connexion à OneDrive auparavant, vous êtes invité à vous connecter avec vos informations d’identification OneDrive :  
   ![Une boîte de dialogue intitulée « OneDrive – Quand un fichier est créé » comporte un bouton nommé « Connexion ».](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Sélectionnez **Connexion**, puis indiquez votre nom d’utilisateur et votre mot de passe. Sélectionnez **Connexion** :  
   ![A dialog box titled "Sign in" instructs you to "Use your Microsoft account". It has two text boxes labeled "Email or phone" and "Password" It also has a check box labeled "Keep me signed in", and a button labeled "Sign in".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte OneDrive. 
4. Sélectionnez **Oui** pour autoriser l’application logique à utiliser votre compte OneDrive :  
   ![A dialog box titled "Let this app access your info?" asks for permission to do the following four things: 1) "Sign in automatically", 2) "Access your email addresses", 3) "Access your info anytime", and 4) "Access OneDrive files." There is a "Yes" button to give permission, and a "No" button to deny it. There is a link to change these application permissions.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Vous pouvez voir que la connexion a été créée. Maintenant, effectuez les autres étapes dans votre application logique :  
   ![Une boîte de dialogue intitulée « Quand un fichier est créé » contient une zone nommée « DOSSIER » avec un bouton Parcourir associé.](./media/connectors-create-api-onedrive/onedrive-5.png)

