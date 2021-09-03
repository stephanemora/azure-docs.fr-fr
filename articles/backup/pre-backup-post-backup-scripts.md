---
title: Utilisation de scripts de pré-sauvegarde et de post-sauvegarde
description: Cet article contient la procédure permettant de spécifier des scripts de pré-sauvegarde et de post-sauvegarde. Serveur de sauvegarde Azure (MABS).
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568585"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>Utilisation de scripts de présauvegarde et de postsauvegarde

S’applique à : Serveur de sauvegarde Microsoft Azure (MABS)

Un _script de pré-sauvegarde_ est un script présent sur l'ordinateur protégé, exécuté avant chaque tâche de sauvegarde MABS et qui prépare la source de données protégée en vue de sa sauvegarde.

Un _script de post-sauvegarde_ est un script qui s’exécute après une tâche de sauvegarde MABS pour tout traitement de post-sauvegarde, comme remettre une machine virtuelle en ligne.

Lorsque vous installez un agent de protection sur un ordinateur, un fichier ScriptingConfig.xml est ajouté au dossier _installer le chemin_ \Microsoft Data Protection Manager\DPM\Scripting de l'ordinateur protégé. Pour chaque source de données protégée de l'ordinateur, vous pouvez spécifier un script de présauvegarde et un script de postsauvegarde dans ScriptingConfig.xml.

>[!Note]
>Les scripts de pré-sauvegarde et de post-sauvegarde ne peuvent pas être des VBScripts. Vous devez donc utiliser une commande wrapper sur le script contenant **cscript myscript.vbs**.

Lorsque MABS exécute une tâche de protection, le fichier ScriptingConfig.xml présent sur l'ordinateur protégé fait l'objet d'une vérification. Si un script de pré-sauvegarde est spécifié, MABS l'exécute, puis termine la tâche. Si un script post-sauvegarde est spécifié, MABS termine la tâche, puis exécute le script.

>[!Note]
>Les tâches de protection comprennent la création de réplicas, les sauvegardes complètes rapides, les synchronisations et les vérifications de cohérence.

MABS exécute les scripts de pré-sauvegarde et de post-sauvegarde en utilisant le compte système local. Pour de meilleurs résultats, vous devez vous assurer que seuls les comptes administrateur et système local disposent des autorisations Lecture et exécution sur les scripts. Ce niveau d’autorisation empêche les utilisateurs non autorisés de modifier les scripts.

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

Pour spécifier des scripts de présauvegarde et de postsauvegarde

1. Sur l'ordinateur protégé, ouvrez le fichier ScriptingConfig.xml dans un éditeur XML ou un éditeur de texte.

   >[!Note]
   >L'attribut DataSourceName doit être fourni sous la forme **Disque :** (par exemple, D: si la source de données se trouve sur le disque D).

1. Pour chaque source de données, configurez l'élément DatasourceScriptConfig de la façon suivante :


   1. Pour l'attribut DataSourceName, entrez le volume de la source de données (pour les sources de données de fichiers) ou le nom de la source de données (pour toutes les autres sources de données). Le nom de source de données des données d'application doit être au format _Instance\Base de données_ pour SQL, _Nom du groupe de stockage_ pour Exchange, _Chemin d'accès logique\Nom du composant_ pour Virtual Server et _Batterie de serveurs SharePoint\Nom du serveur SQL Server\Nom de l'instance SQL\Base de données de configuration SharePoint_ pour Windows SharePoint Services.
   1. Dans la balise _PreBackupScript_, entrez le chemin et le nom du script.
   1. Dans la balise _PreBackupCommandLine_, entrez les paramètres de ligne de commande à passer aux scripts, séparés par des espaces.
   1. Dans la balise _PostBackupScript_, entrez le chemin et le nom du script.
   1. Dans la balise _PostBackupCommandLine_, entrez les paramètres de ligne de commande à passer aux scripts, séparés par des espaces.
   1. Dans l’étiquette _Délai d’attente_, entrez la durée en minutes que MABS doit attendre après invocation du script, avant d'expirer et de signaler le script comme ayant échoué.

1. Enregistrez le fichier ScriptingConfig.xml.

>[!Note]
>MABS ajoutera un paramètre booléen (true/false) supplémentaire au bout de la commande de script de post-sauvegarde, indiquant l'état de la tâche de sauvegarde MABS.
