---
title: Résoudre les problèmes liés au connecteur Hive
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Hive dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 8412d5d49a0e457b42fce408440693fa5ffc6d4c
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067488"
---
# <a name="troubleshoot-the-hive-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Hive dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Hive dans Azure Data Factory et Azure Synapse.

## <a name="the-performance-difference-between-the-odbc-connector-and-the-hive-connector"></a>Différence de performances entre le connecteur ODBC et le connecteur Hive

- **Symptômes** : préoccupations relative à la différence de performances entre le connecteur ODBC et le connecteur Hive. 

- **Cause** : le connecteur ODBC a besoin de son propre pilote, ce qui peut entraîner la différence de performances en raison de la qualité du pilote tiers.

- **Recommandation** : utilisez d’abord le connecteur Hive. 


## <a name="unexpected-response-received-from-the-server-when-connecting-to-the-hive-server-via-odbc"></a>Réponse inattendue reçue du serveur lors de la connexion au serveur Hive via ODBC

- **Symptômes** : lors de la connexion au serveur Hive à l’aide du service lié ODBC, le message d’erreur suivant s’affiche : `ERROR [HY000] [Cloudera][DriverSupport] (1110) Unexpected response received from server. Please ensure the server host and port specified for the connection are correct and confirm if SSL should be enabled for the connection.`

- **Cause** : vous utilisez l’authentification Kerberos, qui n’est pas prise en charge dans Azure Data Factory.

- **Recommandation** : procédez comme suit. S’ils ne fonctionnent pas, vérifiez le pilote fourni pour résoudre ce problème.
    1. Le fichier **kr5.ini** se trouve dans le dossier **C:\Program Files\MIT\Kerberos\bin**.
    2. Ajoutez également `KRB5_CONFIG` et `KRB5CCNAME` à la variable système.
    3. Modifiez le fichier **krb.ini**.
    4. Arrêtez et redémarrez la machine virtuelle et le SHIR à partir de la machine.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)