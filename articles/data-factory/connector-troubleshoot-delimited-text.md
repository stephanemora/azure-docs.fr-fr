---
title: Résoudre les problèmes liés au connecteur Format texte délimité
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés au connecteur Format texte délimité dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 42346a5922b0f607e467e495455166057248db5e
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390592"
---
# <a name="troubleshoot-the-delimited-text-format-connector-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés au connecteur Format texte délimité dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur Format texte délimité dans Azure Data Factory et Azure Synapse.

## <a name="error-code-delimitedtextcolumnnamenotallownull"></a>Code d’erreur : DelimitedTextColumnNameNotAllowNull

- **Message** : `The name of column index %index; is empty. Make sure column name is properly specified in the header row.`

- **Cause** : Lorsque 'firstRowAsHeader' est défini dans l’activité, la première ligne est utilisée comme nom de colonne. Cette erreur signifie que la première ligne contient une valeur vide (par exemple, « ColonneA, ColonneB »).

- **Recommandation** :  Vérifiez la première ligne et corrigez la valeur si elle est vide.


## <a name="error-code-delimitedtextmorecolumnsthandefined"></a>Code d’erreur : DelimitedTextMoreColumnsThanDefined

- **Message** : `Error found when processing '%function;' source '%name;' with row number %rowCount;: found more columns than expected column count: %expectedColumnCount;.`

- **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

  | Analyse de la cause                                               | Recommandation                                               |
  | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | Le nombre de colonnes de la ligne problématique est plus important que le nombre de colonnes de la première ligne. Cela peut être dû à un problème de données ou à des paramètres de délimiteur de colonne ou de guillemet incorrects. | Récupérez le nombre de lignes dans le message d’erreur, vérifiez la colonne de la ligne et corrigez les données. |
  | Si le nombre de colonnes attendu est « 1 » dans le message d’erreur, vous avez peut-être spécifié des paramètres de compression ou de mise en forme incorrects, ce qui a provoqué l’analyse incorrecte des fichiers. | Vérifiez les paramètres de mise en forme pour vous assurer qu’ils correspondent à vos fichiers sources. |
  | Si votre source est un dossier, il est possible que les fichiers du dossier spécifié aient un schéma différent. | Assurez-vous que les fichiers dans le dossier spécifié ont un schéma identique. |

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
