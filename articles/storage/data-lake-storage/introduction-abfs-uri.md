---
title: Utiliser l’URI Azure Data Lake Storage Gen2 Preview
description: Utiliser l’URI Azure Data Lake Storage Gen2 Preview
services: storage
keywords: ''
author: jamesbak
ms.topic: article
ms.author: jamesbak
ms.date: 06/27/2018
ms.service: storage
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 71c3343db592162bcfa7ec46a29e0d680ca83079
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900704"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Utiliser l’URI Azure Data Lake Storage Gen2

Le pilote du [système de fichiers Hadoop](http://www.aosabook.org/en/hdfs.html) compatible avec Azure Data Lake Storage Gen2 Preview est connu par son identificateur de schéma `abfs` (Azure Blob File System). Conformément à d’autres pilotes de système de fichiers Hadoop, le pilote ABFS emploie un format d’URI pour traiter les fichiers et les répertoires dans un compte Data Lake Storage Gen2.

## <a name="uri-syntax"></a>Syntaxe d’URI

La syntaxe de l’URI Data Lake Storage Gen2 varie selon que votre compte de stockage est configuré ou non avec Data Lake Storage Gen2 comme système de fichiers par défaut.

Si le compte Data Lake Storage Gen2 que vous ciblez **n’est pas** défini comme système de fichiers par défaut à la création du compte, la syntaxe d’URI raccourcie est la suivante :

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Identificateur de schéma** : Le protocole `abfs` est utilisé comme identificateur de schéma. Vous pouvez vous connecter avec ou sans connexion SSL. Utilisez `abfss` pour vous connecter avec une connexion SSL.

2. **Système de fichiers** : Emplacement parent qui contient les fichiers et dossiers. C’est l’équivalent des conteneurs du service Stockage Blob Azure.

3. **Nom de compte** : Nom donné à votre compte de stockage à sa création.

4. **Chemins** : Représentation délimitée par des barres obliques (`/`) de la structure de répertoires.

5. **Nom de fichier** : Nom du fichier individuel. Ce paramètre est facultatif si vous utilisez un répertoire.

Toutefois, si le compte que vous ciblez est défini comme système de fichiers par défaut à la création du compte, la syntaxe d’URI raccourcie est la suivante :

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Chemin** : Représentation délimitée par des barres obliques (`/`) de la structure de répertoires.

2. **Nom de fichier** : Nom du fichier individuel.


## <a name="next-steps"></a>Étapes suivantes

- [Utiliser Azure Data Lake Storage Gen2 avec des clusters Azure HDInsight](use-hdi-cluster.md)
