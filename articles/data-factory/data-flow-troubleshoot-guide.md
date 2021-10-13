---
title: Résoudre les problèmes liés aux flux de données de mappage
description: Découvrez comment résoudre les problèmes de flux de données dans Azure Data Factory.
ms.author: makromer
author: kromerm
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.openlocfilehash: f41d53207eb00e4044e523e481de444a615a5d88
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129389396"
---
# <a name="troubleshoot-mapping-data-flows-in-azure-data-factory"></a>Résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes liés aux flux de données de mappage dans Azure Data Factory.  Reportez-vous aux [codes et messages d’erreur courants relatifs au flux de données](data-flow-troubleshoot-errors.md) pour obtenir des messages d’erreur spécifiques et leurs causes et recommandations associées.

## <a name="miscellaneous-troubleshooting-tips"></a>Conseils pour la résolution de problèmes divers
- **Problème** : Une exception inattendue s’est produite et l’exécution a échoué.
    - **Message** : Lors de l’exécution de l’activité de flux de données : Exception inattendue et échec de l’exécution.
    - **Cause** : Cette erreur est une erreur de service back-end. Retentez l’opération et redémarrez votre session de débogage.
    - **Recommandation** : Si la nouvelle tentative et le redémarrage ne résolvent pas le problème, contactez le support technique.

-  **Problème** : Aucune donnée de sortie lors de la jointure pendant l’aperçu des données de débogage.
    - **Message** : Il existe un nombre élevé de valeurs Null ou manquantes qui peuvent être dues à l’échantillonnage d’un trop petit nombre de lignes. Essayez de mettre à jour la limite de lignes de débogage et d’actualiser les données.
    - **Cause** : La condition de jointure ne correspond à aucune ligne ou a généré un nombre élevé de valeurs Null au cours de l’aperçu des données.
    - **Recommandation** : Dans **Paramètres de débogage**, augmentez le nombre de lignes dans la limite de lignes sources. Veillez à sélectionner un runtime d’intégration Azure disposant d’un cluster de flux de données suffisamment grand pour gérer davantage de données.
    
- **Problème** : Erreur de validation à la source avec des fichiers CSV multilignes. 
    - **Message** : Vous pourriez voir l’un des messages d’erreur suivants :
        - La dernière colonne est null ou manquante.
        - Échec de la validation de schéma à la source.
        - L’importation de schéma ne s’affiche pas correctement dans l’interface utilisateur et la dernière colonne contient un caractère de nouvelle ligne dans le nom.
    - **Cause** : Dans le flux de données de mappage, les fichiers sources CSV multilignes ne fonctionnent pas quand \r\n est utilisé comme séparateur de lignes. Parfois, des lignes supplémentaires au niveau des retours chariot peuvent provoquer des erreurs. 
    - **Recommandation** : Générez le fichier à la source avec \n comme séparateur de lignes plutôt que \r\n. Vous pouvez aussi utiliser l’activité de copie pour convertir le fichier CSV afin d’utiliser \n comme séparateur de lignes.

## <a name="general-troubleshooting-guidance"></a>Instructions générales pour la résolution des problèmes
1. Vérifiez l’état de vos connexions de jeu de données. Dans chaque transformation de la source et du récepteur, accédez au service lié pour chaque jeu de données que vous utilisez et testez les connexions.
2. Vérifiez l’état de vos connexions aux fichiers et aux tables dans le concepteur de flux de données. En mode de débogage, sélectionnez **Aperçu des données** dans vos transformations sources pour vérifier que vous pouvez accéder à vos données.
3. Si tout semble correct dans l’aperçu des données, accédez au concepteur de pipeline et mettez votre flux de données dans une activité de pipeline. Déboguez le pipeline pour un test de bout en bout.

### <a name="improvement-on-csvcdm-format-in-data-flow"></a>Amélioration du format CSV/CDM dans le Data Flow 

Si vous utilisez le **format Texte délimité ou CDM pour le flux de données de mappage dans Azure Data Factory v2**, vous pourrez être confronté à des changements de comportement de vos pipelines existants en raison de l’amélioration du format Texte délimité/CDM dans le flux de données à partir du **1er mai 2021**. 

Vous pouvez rencontrer les problèmes suivants avant l’amélioration, mais après l’amélioration, les problèmes seront corrigés. Lisez le contenu suivant pour déterminer si cette amélioration vous concerne. 

#### <a name="scenario-1-encounter-the-unexpected-row-delimiter-issue"></a>Scénario 1 : Vous rencontrez un problème de délimiteur de ligne inattendu

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité avec le paramètre Multiline défini sur True ou CDM comme source.
 - La première ligne contient plus de 128 caractères. 
 - Le délimiteur de ligne dans les fichiers de données n’est pas `\n`.

 Avant l’amélioration, le délimiteur de ligne par défaut `\n` peut être utilisé de manière inattendue pour analyser les fichiers texte délimité, car, lorsque le paramètre Multiline est défini sur True, il invalide le paramètre de délimiteur de ligne et le délimiteur de ligne est automatiquement détecté en fonction des 128 premiers caractères. Si vous ne parvenez pas à détecter le délimiteur de ligne réel, le système revient à `\n`.  

 Après l’amélioration, l’un des trois délimiteurs de ligne, `\r`, `\n` ou `\r\n`, devrait fonctionner.
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>
   Pour la colonne suivante :<br/>
    `C1, C2, {long first row}, C128\r\n `<br/>
    `V1, V2, {values………………….}, V128\r\n `<br/>
 
   Avant l’amélioration, `\r` est conservé dans la valeur de la colonne. Le résultat de la colonne analysée est le suivant :<br/>
   `C1 C2 {long first row} C128`**`\r`**<br/>
   `V1 V2 {values………………….} V128`**`\r`**<br/> 

   Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
   `C1 C2 {long first row} C128`<br/>
   `V1 V2 {values………………….} V128`<br/>
  
#### <a name="scenario-2-encounter-an-issue-of-incorrectly-reading-column-values-containing-rn"></a>Scénario 2 : Vous rencontrez un problème de lecture incorrecte des valeurs de colonne contenant « \r\n »

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité avec le paramètre Multiline défini sur True ou CDM comme source. 
 - Le délimiteur de ligne est `\r\n`.

 Avant l’amélioration, lors de la lecture de la valeur de colonne, le `\r\n` de celle-ci peut être incorrectement remplacé par `\n`. 

 Après l’amélioration, `\r\n` dans la valeur de colonne ne sera pas remplacé par `\n`.

 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :
 
 **Exemple** :<br/>
  
 Pour la colonne suivante :<br/>
  **`"A\r\n"`**`, B, C\r\n`<br/>

 Avant l’amélioration, le résultat de la colonne analysée est le suivant :<br/>
  **`A\n`**` B C`<br/>

 Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
  **`A\r\n`**` B C`<br/>  

#### <a name="scenario-3-encounter-an-issue-of-incorrectly-writing-column-values-containing-n"></a>Scénario 3 : Vous rencontrez un problème d’écriture incorrecte des valeurs de colonne contenant « \r\n »

 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité comme récepteur.
 - La valeur de colonne contient `\n`.
 - L’option Séparateur de lignes est définie sur `\r\n`.
 
 Avant l’amélioration, lors de l’écriture de la valeur de colonne, le `\n` de celle-ci peut être incorrectement remplacé par `\r\n`. 

 Après l’amélioration, `\n` dans la valeur de colonne ne sera pas remplacé par `\r\n`.
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>

 Pour la colonne suivante :<br/>
 **`A\n`**` B C`<br/>

 Avant l’amélioration, le récepteur CSV est :<br/>
  **`"A\r\n"`**`, B, C\r\n` <br/>

 Après l’amélioration, le récepteur CSV doit être :<br/>
  **`"A\n"`**`, B, C\r\n`<br/>

#### <a name="scenario-4-encounter-an-issue-of-incorrectly-reading-empty-string-as-null"></a>Scénario 4 : Vous rencontrez un problème de lecture incorrecte d’une chaîne vide comme NULL
 
 Vous êtes concerné dans les conditions suivantes :
 - Vous utilisez le format Texte délimité comme source. 
 - La valeur NULL est définie comme une valeur non vide. 
 - La valeur de colonne est une chaîne vide et est sans guillemets. 
 
 Avant l’amélioration, la valeur de colonne de la chaîne vide sans guillemets est lue comme NULL. 

 Après l’amélioration, la chaîne vide ne sera pas interprétée comme une valeur NULL. 
 
 L’exemple suivant montre un changement de comportement du pipeline après l’amélioration :

 **Exemple** :<br/>

 Pour la colonne suivante :<br/>
  `A, ,B, `<br/>

 Avant l’amélioration, le résultat de la colonne analysée est le suivant :<br/>
  `A null B null`<br/>

 Après l’amélioration, le résultat de la colonne analysée doit être :<br/>
  `A "" (empty string) B "" (empty string)`<br/>

###  <a name="internal-server-errors"></a>Erreurs de serveur internes

Des scénarios spécifiques qui peuvent provoquer des erreurs internes de serveurs sont présentés ci-dessous.

#### <a name="scenario-1-not-choosing-the-appropriate-compute-sizetype-and-other-factors"></a>Scénario 1 : Ne pas choisir la taille/le type de calcul approprié et d’autres facteurs

  La réussite de l’exécution des flux de données dépend de nombreux facteurs, parmi lesquels la taille/le type de calcul, le nombre de sources/récepteurs à traiter, la spécification de partition, les transformations impliquées, la taille des jeux de données, l’asymétrie des données, etc.<br/>
  
  Pour plus d’aide, consultez [Performance du Runtime d’intégration](concepts-integration-runtime-performance.md).

#### <a name="scenario-2-using-debug-sessions-with-parallel-activities"></a>Scénario 2 : Utilisation de sessions de débogage avec des activités parallèles

  Lors du déclenchement d’une exécution en utilisant une session de débogage de flux de données avec des constructions telles que ForEach dans le pipeline, plusieurs exécutions parallèles peuvent être soumises au même cluster. Cette situation peut entraîner des problèmes de défaillance du cluster au moment de l’exécution en raison de problèmes de ressources, tels qu’une mémoire insuffisante.<br/>
  
  Pour soumettre une exécution avec la configuration de runtime d’intégration appropriée définie dans l’activité de pipeline et après la publication des modifications, sélectionnez **Déclencher maintenant** ou **Déboguer** > **Utiliser le runtime de l'activité**.

#### <a name="scenario-3-transient-issues"></a>Scénario 3 : Problèmes temporaires

  Des problèmes temporaires avec des microservices impliqués dans l’exécution peuvent entraîner l’échec de l’exécution.<br/>
  
  La configuration de nouvelles tentatives dans l’activité de pipeline peut résoudre les problèmes dus à des problèmes temporaires. Pour plus d’informations, consultez la section [Stratégie d’activité](concepts-pipelines-activities.md#activity-json) de notre documentation.

## <a name="next-steps"></a>Étapes suivantes

Pour obtenir une aide supplémentaire sur la résolution des problèmes, consultez les ressources suivantes :

- [Messages et erreurs de flux de données de mappage courants](data-flow-troubleshoot-errors.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
