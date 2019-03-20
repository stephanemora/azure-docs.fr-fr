---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b06c57f50f759677770839ae06cb05e4de36f84c
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/25/2018
ms.locfileid: "58114947"
---
### <a name="prerequisites"></a>Conditions préalables
* Un compte [SendGrid](https://www.SendGrid.com/) 

Pour pouvoir utiliser votre compte SendGrid dans une application logique, vous devez autoriser l’application à se connecter à votre compte. Heureusement, cette opération est très simple à effectuer dans votre application logique sur le portail Azure. 

Pour autoriser votre application logique à se connecter à votre compte SendGrid, procédez comme suit :

1. Pour créer une connexion à SendGrid, dans le Concepteur d’applications logiques, sélectionnez **Afficher les API gérées par Microsoft** dans la liste déroulante, puis entrez *SendGrid* dans la zone de recherche. Sélectionnez le déclencheur ou l’action que vous allez utiliser :   
   ![SendGrid étape 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Si vous n’avez créé aucune connexion à SendGrid auparavant, vous êtes invité à indiquer vos informations d’identification SendGrid. Ces informations d’identification serviront à autoriser votre application logique à se connecter et à accéder aux données de votre compte SendGrid :  
   ![SendGrid étape 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Notez que la connexion a été créée et que vous pouvez maintenant poursuivre la procédure dans votre application logique :   
   ![SendGrid étape 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

