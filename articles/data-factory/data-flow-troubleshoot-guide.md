---
title: Résoudre les problèmes de flux de données
description: Découvrez comment résoudre les problèmes de flux de données dans Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2edd5b661240b6156cf8a02059b2b9a668c402f3
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829118"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Résoudre les problèmes de flux de données dans Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Cet article présente des méthodes couramment employées pour résoudre les problèmes de flux de données dans Azure Data Factory.

## <a name="common-errors-and-messages"></a>Erreurs et messages courants

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Code d’erreur : DF-Executor-SourceInvalidPayload
- **Message** : L’exécution de l’aperçu des données, du débogage et du flux de données de pipeline a échoué car le conteneur n’existe pas
- **Causes** : Se produit quand le jeu de données contient un conteneur qui n’existe pas dans le stockage
- **Recommandation** : Vérifiez que le conteneur référencé dans votre jeu de données existe ou est accessible.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Code d’erreur : DF-Executor-SystemImplicitCartesian

- **Message** : Le produit cartésien implicite pour la jointure INNER n’est pas pris en charge. Utilisez plutôt CROSS JOIN. Les colonnes utilisées dans la jointure doivent créer une clé unique pour les lignes.
- **Causes** : Le produit cartésien implicite pour la jointure INNER entre plans logiques n’est pas pris en charge. Si les colonnes utilisées dans la jointure créent la clé unique, vous avez besoin d’au moins une colonne provenant de chaque côté de la relation.
- **Recommandation** : Pour les jointures non basées sur l’égalité, vous devez opter pour CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Code d’erreur : DF-Executor-SystemInvalidJson

- **Message** : Erreur d’analyse JSON, encodage ou multiligne non pris en charge.
- **Causes** : Problèmes possibles avec le fichier JSON (encodage non pris en charge, octets endommagés ou utilisation de la source JSON comme document unique sur de nombreuses lignes imbriquées).
- **Recommandation** : Vérifiez que l’encodage du fichier JSON est pris en charge. Sur la transformation source qui utilise un jeu de données JSON, développez « Paramètres JSON » et activez « Un seul document ».
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Code d’erreur : DF-Executor-BroadcastTimeout

- **Message** : Erreur liée à l’expiration de la diffusion de la jointure. Vérifiez que la diffusion en streaming produit des données en moins de 60 s dans les exécutions de débogage et en moins de 300 s dans les exécutions de travaux.
- **Causes** : Le délai d’expiration par défaut pour la diffusion est de 60 s dans les exécutions de débogage et de 300 s dans les exécutions de travaux. Le flux choisi pour la diffusion semble être trop volumineux pour produire des données respectant cette limite.
- **Recommandation** : Sous l’onglet Optimiser, vérifiez les transformations de jointure, d’existence et de recherche de votre flux de données. L’option par défaut pour Diffusion est « Auto ». Si cette option est définie ou que vous définissez manuellement la diffusion du côté gauche ou droit sous « Fixe », vous pouvez soit définir une configuration Azure Integration Runtime plus grande, soit désactiver la diffusion. L’approche recommandée pour des performances optimales dans les flux de données consiste à autoriser Spark à diffuser à l’aide de l’option « Auto » et à utiliser un Azure IR à mémoire optimisée.

### <a name="error-code-df-executor-conversion"></a>Code d’erreur : DF-Executor-Conversion

- **Message** : Échec de conversion en date ou heure en raison d’un caractère non valide
- **Causes** : Les données ne sont pas au format attendu
- **Recommandation** : Utilisez le type de données correct

### <a name="error-code-df-executor-invalidcolumn"></a>Code d’erreur : DF-Executor-InvalidColumn

- **Message** : Le nom de colonne doit être spécifié dans la requête, définissez un alias si vous utilisez une fonction SQL
- **Causes** : Aucun nom de colonne n’a été spécifié
- **Recommandation** : Définissez un alias si vous utilisez une fonction SQL telle que min()/max(), etc.

### <a name="error-code-getcommand-outputasync-failed"></a>Code d’erreur : Échec de GetCommand OutputAsync

- **Message** : Lors du débogage du flux de données et de l’aperçu des données : Échec de GetCommand OutputAsync avec...
- **Causes** : Il s’agit d’une erreur de service principal. Vous pouvez retenter l’opération et redémarrer votre session de débogage.
- **Recommandation** : Si la nouvelle tentative et le redémarrage ne permettent pas de résoudre le problème, contactez le support technique.

### <a name="error-code-hit-unexpected-exception-and-execution-failed"></a>Code d’erreur : Exception inattendue et échec de l’exécution

- **Message** : Lors de l’exécution de l’activité de flux de données : Exception inattendue et échec de l’exécution.
- **Causes** : Il s’agit d’une erreur de service principal. Vous pouvez retenter l’opération et redémarrer votre session de débogage.
- **Recommandation** : Si la nouvelle tentative et le redémarrage ne permettent pas de résoudre le problème, contactez le support technique.

## <a name="general-troubleshooting-guidance"></a>Instructions générales pour la résolution des problèmes

1. Vérifiez l’état de vos connexions de jeu de données. Dans chaque transformation de source et récepteur, visitez le service lié pour chaque jeu de données que vous utilisez et testez les connexions.
1. Vérifiez l’état de vos connexions aux fichiers et aux tables à partir du concepteur de flux de données. Activez le débogage, puis cliquez sur Aperçu des données dans vos transformations de la source pour vérifier que vous pouvez accéder à vos données.
1. Si tout semble correct dans l’aperçu des données, accédez au concepteur de pipeline et mettez votre flux de données dans une activité de pipeline. Déboguez le pipeline pour un test de bout en bout.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :
*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Demandes de fonctionnalités Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Page de questions Microsoft Q&R](https://docs.microsoft.com/answers/topics/azure-data-factory.html)
*  [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Guide de performances du mappage de flux de données ADF](concepts-data-flow-performance.md)
