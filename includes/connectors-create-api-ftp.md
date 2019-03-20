---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "58114216"
---
### <a name="prerequisites"></a>Conditions préalables
* Un compte [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol)  

Avant de pouvoir utiliser votre compte FTP dans une application logique, vous devez autoriser l’application logique à se connecter à votre compte FTP. Heureusement, vous pouvez faire cela facilement à partir de votre application logique sur le portail Azure.  

Pour autoriser votre application logique à se connecter à votre compte FTP, procédez comme suit :  

1. Pour créer une connexion à FTP, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *FTP* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :   
   ![Étape de création de la connexion à FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Si vous n’avez créé aucune connexion à FTP auparavant, vous êtes invité à indiquer vos informations d’identification FTP. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte FTP :  
   ![étape de création de la connexion à FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Notez que la connexion a été créée et que vous pouvez désormais poursuivre la procédure dans votre application logique :  
   ![étape de création de la connexion à FTP](./media/connectors-create-api-ftp/ftp-3.png)  

