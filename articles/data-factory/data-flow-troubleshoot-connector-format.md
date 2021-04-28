---
title: Résoudre les problèmes liés aux connecteurs et à la mise en forme dans le flux de données de mappage
description: Découvrez comment résoudre les problèmes de flux de données liés aux connecteurs et à la mise en forme dans Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739470"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Résoudre les problèmes liés aux connecteurs et à la mise en forme dans le flux de données de mappage dans Azure Data Factory


Cet article explore les méthodes de résolution des problèmes liés aux connecteurs et à la mise en forme pour les flux de données de mappage dans Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Prendre en charge les schémas personnalisés dans la source

#### <a name="symptoms"></a>Symptômes
Lorsque vous souhaitez utiliser le flux de données ADF pour déplacer ou transférer des données à partir de Cosmos DB/JSON vers d’autres magasins de données, certaines colonnes des données sources peuvent être ignorées. 

#### <a name="cause"></a>Cause 
Pour les connecteurs sans schéma (le numéro de colonne, le nom de colonne et le type de données de colonne de chaque ligne peuvent être différents lors de la comparaison avec d’autres), par défaut, ADF utilise un échantillon de lignes (par exemple les données des 100 ou 1000 premières lignes) pour déduire le schéma, et le résultat déduit sera utilisé comme schéma pour lire les données. Par conséquent, si vos magasins de données ont des colonnes supplémentaires qui n’apparaissent pas dans les échantillons de lignes, les données de ces colonnes supplémentaires ne sont pas lues, déplacées ou transférées dans les magasins de données du récepteur.

#### <a name="recommendation"></a>Recommandation
Pour remplacer le comportement par défaut et importer des champs supplémentaires, ADF fournit des options pour vous permettre de personnaliser le schéma source. Vous pouvez spécifier des colonnes supplémentaires/ignorées qui peuvent être manquantes dans le résultat déduit du schéma dans la projection source du flux de données afin de lire les données, et vous pouvez appliquer l’une des options suivantes pour définir le schéma personnalisé. En règle générale, l’**option 1** est préférable.

- **Option 1** : comparé aux données sources d’origine qui peuvent être un fichier, une table ou un conteneur volumineux contenant des millions de lignes avec des schémas complexes, vous pouvez créer une table/conteneur temporaire avec quelques lignes qui contiennent toutes les colonnes que vous souhaitez lire, puis passer à l’opération suivante : 

    1. Utilisez les **paramètres de débogage** de la source de flux de données pour effectuer une **projection d’importation** avec des exemples de fichiers/tables afin d’obtenir le schéma complet. Vous pouvez suivre les étapes décrites dans l’image suivante :<br/>

        ![Capture d’écran qui montre la première partie de la première option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Sélectionnez **Paramètres de débogage** dans le canevas du flux de données.
         1. Dans le volet contextuel, sélectionnez **Exemple de table** sous l’onglet **cosmosSource**, puis entrez le nom de votre table dans le bloc **Table**.
         1. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.
         1. Sélectionnez **Importer la projection**.<br/>  
    
    1. Remodifiez les **paramètres de débogage** de façon à utiliser le jeu de données source pour le déplacement/la transformation des données restantes. Vous pouvez passer à la procédure indiquée dans l’image suivante :<br/>

        ![Capture d’écran qui montre la deuxième partie de la première option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Sélectionnez **Paramètres de débogage** dans le canevas du flux de données.
         1. Dans le volet contextuel, sélectionnez **Jeu de données source** sous l’onglet **cosmosSource**.
         1. Sélectionnez **Save** (Enregistrer) pour enregistrer vos paramètres.<br/>
    
    Par la suite, le runtime de flux de données ADF honorera et utilisera le schéma personnalisé pour lire les données à partir du magasin de données d’origine. <br/>

- **Option 2** : si vous connaissez le schéma et le langage DSL des données sources, vous pouvez mettre à jour manuellement le script source du flux de données afin d’ajouter des colonnes supplémentaires/ignorées pour lire les données. Un exemple est présenté dans l’image suivante : 

    ![Capture d’écran qui montre la deuxième option pour personnaliser le schéma source.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir une aide supplémentaire sur la résolution des problèmes, consultez les ressources suivantes :

*  [Résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)