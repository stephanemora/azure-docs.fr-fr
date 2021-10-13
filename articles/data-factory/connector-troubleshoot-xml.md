---
title: Résoudre les problèmes liés au connecteur de format XML
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes avec le connecteur de format XML liés aux connecteurs dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: d4a6d8c26b9441663cf67a1ea9a837091a51a624
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390623"
---
# <a name="troubleshoot-the-xml-format-connector-in-azure-data-factory-and-azure-synapse"></a>Dépanner le connecteur de format XML dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés au connecteur de format XML dans Azure Data Factory et Azure Synapse.

## <a name="error-code-xmlsinknotsupported"></a>Code d’erreur : XmlSinkNotSupported

- **Message** : `Write data in XML format is not supported yet, choose a different format!`

- **Cause** : Un jeu de données XML a été utilisé en tant que jeu de données récepteur dans votre activité de copie.

- **Recommandation** :  Utilisez un jeu de données dans un format différent de celui du jeu de données récepteur.


## <a name="error-code-xmlattributecolumnnameconflict"></a>Code d’erreur : XmlAttributeColumnNameConflict

- **Message** : `Column names %attrNames;' for attributes of element '%element;' conflict with that for corresponding child elements, and the attribute prefix used is '%prefix;'.`

- **Cause** : Un préfixe d’attribut a été utilisé, ce qui a provoqué le conflit.

- **Recommandation** :  Définissez une valeur différente de la propriété « attributePrefix ».


## <a name="error-code-xmlvaluecolumnnameconflict"></a>Code d’erreur : XmlValueColumnNameConflict

- **Message** : `Column name for the value of element '%element;' is '%columnName;' and it conflicts with the child element having the same name.`

- **Cause** : L’un des noms d’éléments enfants a été utilisé comme nom de colonne pour la valeur de l’élément.

- **Recommandation** :  Définissez une valeur différente de la propriété « valueColumn ».


## <a name="error-code-xmlinvalid"></a>Code d’erreur : XmlInvalid

- **Message** : `Input XML file '%file;' is invalid with parsing error '%error;'.`

- **Cause** : Le fichier XML d’entrée n’est pas bien formé.

- **Recommandation** :  Corrigez le fichier XML pour qu’il soit correctement formé.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
