---
title: Guide de dépannage de Microsoft Genomics
titleSuffix: Azure
description: Découvrez les stratégies de dépannage
keywords: dépannage, erreur, débogage
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: article
ms.date: 07/18/2018
ms.openlocfilehash: bd946f84023345c68a01a48a4dc310b7afb68397
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/17/2018
ms.locfileid: "45735384"
---
# <a name="troubleshooting-guide-for-microsoft-genomics"></a>Guide de dépannage pour Microsoft Genomics
Ce guide décrit les stratégies permettant de résoudre les problèmes courants liés à l’utilisation du service Microsoft Genomics. Pour consulter les questions fréquentes (FAQ), consultez [Questions courantes](frequently-asked-questions-genomics.md). 


## <a name="how-do-i-check-my-job-status"></a>Comment vérifier l’état de ma tâche ?
Vous pouvez vérifier l’état de votre flux de travail en appelant `msgen status` à partir de la ligne de commande, comme indiqué. 

```
msgen status -u URL -k KEY -w ID [-f CONFIG] 
```

Il existe trois arguments obligatoires :
* URL : URL de base de l’API
* KEY : clé d’accès de votre compte Genomics 
* ID : ID du flux de travail

Pour trouver votre URL et votre clé, accédez au portail Azure et ouvrez la page de votre compte Microsoft Genomics. Sous le titre **Gestion**, choisissez **Clés d’accès**. Vous y trouvez à la fois l’URL de l’API et vos clés d’accès.

Vous pouvez également inclure le chemin du fichier config au lieu d’entrer directement l’URL et la clé. Notez que si vous incluez ces arguments dans la ligne de commande en même temps que le fichier config, les arguments de la ligne de commande sont prioritaires. 

Après l’appel de `msgen status`, un message convivial s’affiche, indiquant la réussite du flux de travail ou la raison de l’échec. 


## <a name="get-more-information-about-my-workflow-status"></a>Obtenir des informations supplémentaires sur l’état d’un flux de travail

Pour obtenir plus d’informations sur la raison de l’échec d’une tâche, vous pouvez explorer les fichiers journaux générés pendant le flux de travail. Dans votre conteneur de sortie, vous devez voir un dossier `[youroutputfilename].logs.zip`.  Quand vous décompressez ce dossier, vous voyez les éléments suivants :

* outputFileList.txt : liste des fichiers de sortie générés pendant le flux de travail
* standarderror.txt : ce fichier est vide.
* standardoutput.txt : contient la journalisation de niveau supérieur du flux de travail. 
* Fichiers journaux GATK : tous les autres fichiers du dossier `logs`

Le fichier `standardoutput.txt` est un bon point de départ pour déterminer la raison de l’échec de votre flux de travail, car il contient davantage d’informations détaillées sur le flux de travail. 

## <a name="common-issues-and-how-to-resolve-them"></a>Problèmes courants et comment les résoudre
Cette section présente brièvement les problèmes courants et explique comment les résoudre.

### <a name="fastq-files-are-unmatched"></a>Les fichiers FASTQ ne correspondent pas
Dans l’exemple d’identificateur, la seule différence qui peut exister entre les fichiers FASTQ est le /1 ou le /2 de fin. Si vous avez envoyé par erreur des fichiers FASTQ qui ne correspondent pas, vous pouvez voir les messages d’erreur suivants lorsque vous appelez `msgen status`.
* `Encountered an unmatched read`
* `Error reading a FASTQ file, make sure the input files are valid and paired correctly` 

Pour résoudre ce problème, vérifiez que les fichiers FASTQ envoyés au flux de travail constituent réellement un ensemble. 


### <a name="error-uploading-bam-file-output-blob-already-exists-and-the-overwrite-option-was-set-to-false"></a>Erreur lors du chargement du fichier .bam. L’objet blob de sortie existe déjà et l’option de remplacement a été définie sur False.
Si vous voyez le message d’erreur `Error uploading .bam file. Output blob already exists and the overwrite option was set to False`, cela signifie que le dossier de sortie contient déjà un fichier de sortie du même nom.  Supprimez le fichier de sortie existant ou activez l’option de remplacement dans le fichier config. Ensuite, renvoyez votre flux de travail.

### <a name="when-to-contact-microsoft-genomics-support"></a>Quand contacter le support Microsoft Genomics
Si vous voyez les messages d’erreur suivants, cela signifie qu’une erreur interne s’est produite. 

* `Error locating input files on worker machine`
* `Process management failure`

Essayez de renvoyer votre flux de travail. Si vos tâches continuent d’échouer, ou si vous avez d’autres questions, contactez le support Microsoft Genomics sur le portail Azure. Pour en savoir plus sur la procédure d’envoi d’une demande de support, cliquez [ici](file-support-ticket-genomics.md).

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris à résoudre les problèmes courants liés au service Microsoft Genomics. Pour plus d’informations et pour consulter les questions fréquentes (FAQ) plus générales, consultez [Questions courantes](frequently-asked-questions-genomics.md). 
