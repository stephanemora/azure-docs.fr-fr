---
title: Résoudre les problèmes liés au connecteur Liste SharePoint Online
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Liste SharePoint Online dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 2407521ef6abcea5a27f7810af527e47a2739410
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390575"
---
# <a name="troubleshoot-the-sharepoint-online-list-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Liste SharePoint Online dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Liste SharePoint Online dans Azure Data Factory et Azure Synapse.

## <a name="error-code-sharepointonlineauthfailed"></a>Code d’erreur : SharePointOnlineAuthFailed

- **Message** : `The access token generated failed, status code: %code;, error message: %message;.`

- **Cause** : L’ID du principal du service et la clé peuvent ne pas être correctement définis.

- **Recommandation** :  Vérifiez votre application inscrite (ID du principal du service) et la clé pour voir si elles sont correctement définies.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
