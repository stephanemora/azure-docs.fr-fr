---
title: Présentation des jeux de ressources
description: Cet article explique ce que sont les jeux de ressources et comment Azure Purview les crée.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200816"
---
# <a name="understanding-resource-sets"></a>Présentation des jeux de ressources

Cet article vous aide à comprendre comment Azure Purview utilise des jeux de ressources pour mapper des ressources de données à des ressources logiques.
## <a name="background-info"></a>Informations générales

Les systèmes de traitement de données à l’échelle stockent généralement une table unique sur un disque en tant que fichiers multiples. Ce concept est représenté dans Azure Purview à l’aide de jeux de ressources. Un jeu de ressources est un objet unique dans le catalogue qui représente un grand nombre de ressources dans le stockage.

Supposons par exemple que votre cluster Spark a rendu persistant un DataFrame dans une source de données Azure Data Lake Storage (ADLS) Gen2. Bien que dans Spark, le tableau ressemble à une ressource logique unique, sur le disque, il y a probablement des milliers de fichiers Parquet, chacun représentant une partition du contenu total de DataFrame. Les données IoT et les données de journal web sont confrontées au même défi. Imaginez que vous avez un capteur qui génère des fichiers journaux plusieurs fois par seconde. Cela ne prendra pas trop de temps tant que vous n’aurez pas de centaines de milliers de fichiers journaux à partir de ce capteur unique.

Pour résoudre le problème de mappage d’un grand nombre de ressources de données à une seule ressource logique, Azure Purview utilise des jeux de ressources.

## <a name="how-azure-purview-detects-resource-sets"></a>Comment Azure Purview détecte les jeux de ressources

Azure Purview prend en charge la détection des jeux de ressources dans Stockage Blob Azure, ADLS Gen1 et ADLS Gen2.

Azure Purview détecte automatiquement les jeux de ressources lors de l’analyse. Cette fonctionnalité examine toutes les données qui sont ingérées par le biais de l’analyse et les compare à un ensemble de modèles définis.

Par exemple, supposons que vous analysiez une source de données dont l’URL est `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet`. Azure Purview examine les segments de ligne et détermine s’ils correspondent à des modèles intégrés. Il possède des modèles intégrés pour les GUID, les nombres, les formats de date, les codes de localisation (par exemple, en-us), etc. Dans ce cas, le modèle de nombre correspond à *23*. Azure Purview part du principe que ce fichier fait partie d’un jeu de ressources nommé `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet`.

Ou, pour une URL telle que `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json`, Azure Purview correspond à la fois au modèle de localisation et au modèle de nombre, ce qui génère un jeu de ressources nommé `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`.

À l’aide de cette stratégie, Azure Purview mappe les ressources suivantes au même jeu de ressources, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Types de fichiers qui ne seront pas détectés par Azure Purview en tant que jeux de ressources

Purview ne tente pas de classer la plupart des types de fichiers de document tels que Word, Excel ou PDF comme jeux de ressources. L’exception est le format CSV, car il s’agit d’un format de fichier partitionné courant.

## <a name="how-azure-purview-scans-resource-sets"></a>Comment Azure Purview analyse les jeux de ressources

Quand Azure Purview détecte des ressources qu’il considère comme faisant partie d’un jeu de ressources, il passe d’une analyse complète à un exemple d’analyse. Dans un exemple d’analyse, il ouvre uniquement un sous-ensemble des fichiers qu’il pense se trouver dans le jeu de ressources. Pour chaque fichier qu’il ouvre, il utilise son schéma et exécute ses classifieurs. Azure Purview recherche ensuite la ressource la plus récente parmi les ressources ouvertes et utilise le schéma et les classifications de cette ressource dans l’entrée pour l’ensemble des ressources dans le catalogue.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Contenu stocké par Azure Purview sur les jeux de ressources

Outre le schéma unique et les classifications, Azure Purview stocke les informations suivantes sur les groupes de ressources :

- Données de la dernière ressource de partition analysée en profondeur.
- Informations agrégées sur les ressources de partition qui composent le jeu de ressources.
- Nombre de partitions qui indique le nombre de ressources de partition trouvées.
- Nombre de schémas qui indique le nombre de schémas uniques trouvés dans l’ensemble d’exemples sur lequel les analyses approfondies ont été réalisées. Cette valeur est un nombre compris entre 1 et 5, ou pour les valeurs supérieures à 5, 5+.
- Liste de types de partition lorsque plusieurs types de partition sont inclus dans l’ensemble de ressources. Par exemple, un capteur IoT peut produire à la fois des fichiers XML et JSON, bien que les deux soient logiquement intégrés au même jeu de ressources.

## <a name="built-in-resource-set-patterns"></a>Modèles de jeu de ressources intégrés

Azure Purview prend en charge les modèles de jeu de ressources suivants. Ces modèles peuvent apparaître sous la forme d’un nom dans un répertoire ou en tant que partie d’un nom de fichier.
### <a name="regex-based-patterns"></a>Modèles basés sur une expression régulière

| Nom du modèle | Nom d’affichage | Description |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Identificateur global unique, tel que défini dans la norme [RFC 4122](https://tools.ietf.org/html/rfc4122). |
| Nombre       | {N}          | Un ou plusieurs chiffres. |
| Formats de date et heure | {Year}{Month}{Day}{N}     | Nous prenons en charge différents formats de date et d’heure, mais tous sont représentés par {Year}[delimiter]{Month}[delimiter]{Day} ou une série de {N}. |
| 4ByteHex     | {HEX}        | Nombre hexadécimal à quatre chiffres. |
| Localisation | {LOC}        | Une balise de langue telle que définie dans la norme [BCP 47](https://tools.ietf.org/html/bcp47), les noms contenant les caractères « - » et « _ » sont pris en charge (par exemple, en_ca et en-ca). |

### <a name="complex-patterns"></a>Modèles complexes

| Nom du modèle | Nom d’affichage | Description |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Identificateur du fichier de partition Spark |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | Modèle année/mois/jour couvrant plusieurs dossiers |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Affichage des jeux de ressources dans le catalogue Azure Purview

Quand Azure Purview correspond à un groupe de ressources dans un jeu de ressources, il tente d’extraire les informations les plus utiles à utiliser comme nom d’affichage dans le catalogue. Voici quelques exemples de la convention d’affectation de noms par défaut : 

### <a name="example-1"></a>Exemple 1

Nom qualifié : `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Nom d’affichage : « nom de la sortie Spark »

### <a name="example-2"></a>Exemple 2

Nom qualifié : `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Nom d’affichage : « mes données partitionnées »

### <a name="example-3"></a>Exemple 3

Nom qualifié : `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Nom d’affichage : « données »

## <a name="known-issues-with-resource-sets"></a>Problèmes connus liés aux jeux de ressources

Bien que les jeux de ressources fonctionnent bien dans la plupart des cas, vous pouvez rencontrer les problèmes suivants, dans lesquels Azure Purview :

- marque de manière incorrecte une ressource en tant que jeu de ressources ;
- place une ressource dans le mauvais jeu de ressources ;
- marque de manière incorrecte une ressource comme n’étant pas un jeu de ressources.

## <a name="next-steps"></a>Étapes suivantes

Pour prendre en main Azure Purview, consultez [Démarrage rapide : Créer un compte Azure Purview](create-catalog-portal.md).
