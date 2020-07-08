---
title: Gestion d’Azure Data Lake Analytics à l’aide du kit de développement logiciel (SDK) Azure pour Node.js
description: Cet article décrit comment utiliser le kit de développement logiciel (SDK) Azure pour Node.js pour gérer des comptes Azure Data Lake Analytics, des sources de données, des travaux et des utilisateurs.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 9de1bcf4-b15b-4d0b-9284-8889ecf0c438
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: f821f8f8fddfafbdba060fd3f9940ce32c138755
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/08/2020
ms.locfileid: "86121348"
---
# <a name="manage-azure-data-lake-analytics-using-azure-sdk-for-nodejs"></a>Gestion d’Azure Data Lake Analytics à l’aide du kit de développement logiciel (SDK) Azure pour Node.js
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Cet article décrit comment gérer des comptes Azure Data Lake Analytics, des sources de données, des utilisateurs et des travaux utilisant une application écrite à l’aide du kit de développement logiciel (SDK) Azure pour Node.js. 

Les versions suivantes sont prises en charge :
* **Node.js version 0.10.0 ou supérieure**
* **Version de l’API REST pour le compte : 2015-10-01-preview**
* **Version de l’API REST pour le catalogue : 2015-10-01-preview**
* **Version de l’API REST pour la tâche : 2016-03-20-preview**

## <a name="features"></a>Fonctionnalités
* Gestion de compte : créer, obtenir, répertorier, mettre à jour et supprimer.
* Gestion de travail : envoyer, obtenir, répertorier et annuler.
* Gestion de catalogue : obtenir et répertorier.

## <a name="how-to-install"></a>Procédure d’installation
```bash
npm install azure-arm-datalake-analytics
```

## <a name="authenticate-using-azure-active-directory"></a>S’authentifier à l’aide d’Azure Active Directory
 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-analytics-client"></a>Créer le client Data Lake Analytics
```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var accountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-jobs"></a>Obtenir une liste des tâches
```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="get-a-list-of-databases-in-the-data-lake-analytics-catalog"></a>Obtenir une liste des bases de données dans le catalogue Data Lake Analytics
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Voir aussi
* [Kit de développement logiciel (SDK) Microsoft Azure pour Node.js](https://github.com/azure/azure-sdk-for-node)
