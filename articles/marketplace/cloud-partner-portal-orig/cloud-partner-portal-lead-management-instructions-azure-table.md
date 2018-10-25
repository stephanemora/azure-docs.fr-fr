---
title: Table Azure | Microsoft Docs
description: Configurer la gestion des prospects pour Azure Table.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/14/2018
ms.author: pbutlerm
ms.openlocfilehash: 60e3e3d81b07bf7ae681b5cef2d6d9681877a35f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48806670"
---
<a name="lead-management-instructions-for-azure-table"></a>Instructions de gestion des prospects pour une table Azure
============================================

Cet article explique comment configurer Table Azure pour le stockage des prospects commerciaux. Table Azure vous permet de stocker et de personnaliser les informations sur le client.

## <a name="to-configure-azure-table"></a>Configurer Table Azure

1.  Si vous ne possédez pas de compte Azure, vous pouvez [créer un compte d’évaluation gratuit](https://azure.microsoft.com/pricing/free-trial/).

2.  Une fois que votre compte Azure est actif, connectez-vous au [portail Azure](https://portal.azure.com).
3.  Sur le portail Azure, créez un compte de stockage. La capture d’écran suivante montre comment créer un compte de stockage. Pour plus d’informations sur la tarification du stockage, consultez [Tarification de Storage](https://azure.microsoft.com/pricing/details/storage/).

    ![Procédure de création d’un compte Azure Storage](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragecreate.png)

4.  Copiez la chaîne de connexion du compte de stockage pour la clé, puis collez-la dans le champ **Chaîne de connexion de compte de stockage** sur le portail Cloud Partner. Voici un exemple de chaîne de connexion : `DefaultEndpointsProtocol=https;AccountName=myAccountName;AccountKey=myAccountKey;EndpointSuffix=core.windows.net `
    
    ![Clé de stockage Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/azurestoragekeys.png)

Vous pouvez utiliser l’[Explorateur de stockage Microsoft Azure](http://azurestorageexplorer.codeplex.com/) ou tout autre outil pour afficher de votre table de stockage. Vous pouvez également exporter les données de Table Azure.
données.

## <a name="optional-to-use-azure-functions-with-an-azure-table"></a>**(Facultatif)** Utiliser Azure Functions avec une table Azure

Si vous souhaitez personnaliser la façon dont vous recevez des prospects, utilisez [Azure Functions](https://azure.microsoft.com/services/functions/) avec une table Azure. Le service Azure Functions vous permet d’automatiser le processus de génération de prospects.

Les étapes suivantes expliquent comment créer une fonction Azure qui utilise un minuteur. Toutes les cinq minutes, la fonction recherche de nouveaux enregistrements dans la table Azure et utilise ensuite le service SendGrid pour envoyer une notification par e-mail.


1.  [Créez](https://portal.azure.com/#create/SendGrid.SendGrid) un compte gratuit du service SendGrid dans votre abonnement Azure.

    ![Créer SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/createsendgrid.png)

2.  Créer une clé d’API SendGrid 
    - Sélectionnez **Gérer** pour accéder à l’interface utilisateur SendGrid
    - Sélectionnez **Paramètres**, **Clés d’API**, puis créez une clé avec Mail Send (Envoi de courrier) -\> Full Access (Accès total)
    - Enregistrer la clé API


    ![Clé API SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridkey.png)


3.  [Créez](https://portal.azure.com/#create/Microsoft.FunctionApp) une application Azure Function à l’aide de l’option Plan d’hébergement nommée « Plan Consommation ».

    ![Créer une application de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/createfunction.png)


4.  Créez une définition de fonction.

    ![Créer une définition Azure Function](./media/cloud-partner-portal-lead-management-instructions-azure-table/createdefinition.png)
 

5.  Pour que la fonction envoie une mise à jour à une heure spécifique, sélectionnez ici **TimerTrigger-CSharp** comme option de démarrage.

     ![Option de déclencheur de temps de la fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/timetrigger.png)


6.  Remplacez le code « Développement » par l’exemple de code suivant. Remplacez les adresses e-mail par les adresses à utiliser pour l’expéditeur et le destinataire.

        #r "Microsoft.WindowsAzure.Storage"
        #r "SendGrid"
        using Microsoft.WindowsAzure.Storage.Table;
        using System;
        using SendGrid;
        using SendGrid.Helpers.Mail;
        public class MyRow : TableEntity
        {
            public string Name { get; set; }
        }
        public static void Run(TimerInfo myTimer, IQueryable<MyRow> inputTable, out Mail message, TraceWriter log)
        {
            // UTC datetime that is 5.5 minutes ago while the cron timer schedule is every 5 minutes
            DateTime dateFrom = DateTime.UtcNow.AddSeconds(-(5 * 60 + 30));
            var emailFrom = "YOUR EMAIL";
            var emailTo = "YOUR EMAIL";
            var emailSubject = "Azure Table Notification";
            // Look in the table for rows that were added recently
            var rowsList = inputTable.Where(r => r.Timestamp > dateFrom).ToList();
            // Check how many rows were added
            int rowsCount = rowsList.Count;
            if (rowsCount > 0)
            {
                log.Info($"Found {rowsCount} rows added since {dateFrom} UTC");
                // Configure the email message describing how many rows were added
                message = new Mail
                {
                    From = new Email(emailFrom),
                    Subject = emailSubject + " (" + rowsCount + " new rows)"
                };
                var personalization = new Personalization();
                personalization.AddTo(new Email(emailTo));
                message.AddPersonalization(personalization);
                var content = new Content
                {
                    Type = "text/plain",
                    Value = "Found " + rowsCount + " new rows added since " + dateFrom.ToString("yyyy-MM-dd HH:mm:ss") + " UTC"
                };
                message.AddContent(content);
            }
            else
            {
                // Do not send the email if no new rows were found
                message = null;
            }
        }

    ![Extrait de code de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/code.png)


7.  Cliquez sur **Intégrer** et **Entrées**  pour définir la connexion de la table Azure.

    ![Intégration de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/integrate.png)


8.  Entrez le nom de la table et définissez la chaîne de connexion en cliquant sur **Nouvelle**.


    ![Connexion de table de fonction Azure](./media/cloud-partner-portal-lead-management-instructions-azure-table/configtable.png)

9.  Définissez à présent la sortie en tant que SendGrid et conservez toutes les valeurs par défaut.

    ![Sortie de SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutput.png)

    ![Valeurs par défaut de sortie de SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridoutputdefaults.png)

10. Ajouter une clé API SendGrid aux paramètres d’application de fonction à l’aide du nom « SendGridApiKey » et de la valeur obtenue des clés API dans l’interface utilisateur de SendGrid

    ![Gérer SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanage.png)
    ![Gérer la clé SendGrid](./media/cloud-partner-portal-lead-management-instructions-azure-table/sendgridmanagekey.png)

Une fois que vous avez terminé la configuration de la fonction, le code dans la section Integrate doit ressembler à l’exemple suivant.

    {
      "bindings": [
        {
          "name": "myTimer",
          "type": "timerTrigger",
          "direction": "in",
          "schedule": "0 */5 * * * *"
        },
        {
          "type": "table",
          "name": "inputTable",
          "tableName": "MarketplaceLeads",
          "take": 50,
          "connection": "yourstorageaccount_STORAGE",
          "direction": "in"
        },
        {
          "type": "sendGrid",
          "name": "message",
          "apiKey": "SendGridApiKey",
          "direction": "out"
        }
      ],
      "disabled": false
    }

11. La dernière étape consiste à accéder à l’interface utilisateur Develop de la fonction, puis à sélectionner **Exécuter** pour démarrer la minuterie. Vous recevrez désormais une notification à chaque fois qu’un nouveau prospect se présentera.
