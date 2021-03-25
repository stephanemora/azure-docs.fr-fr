---
title: Résolution des problèmes liés à l’importation et à l’exportation dans Azure Import/Export | Microsoft Docs
description: Découvrez comment gérer des problèmes courants lors de l’utilisation d’Azure Import/Export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98705918"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Résoudre des problèmes dans Azure Import/Export
Cet article explique comment résoudre des problèmes courants lors de l’importation et de l’exportation de données dans Azure Import/Export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Une session de copie a échoué. Que dois-je faire ?  

Quand une session de copie échoue, vous disposez de deux options :  
* Si l’erreur offre la possibilité d’effectuer une nouvelle tentative, par exemple, si le partage réseau a été hors connexion pendant une courte période et est de nouveau en ligne, vous pouvez reprendre la session de copie.
* Si l’erreur n’offre pas la possibilité d’effectuer une nouvelle tentative, par exemple si vous avez spécifié un répertoire de fichier source erroné dans les paramètres de ligne de commande, vous devez abandonner la session de copie.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Je n’arrive pas à reprendre ou à abandonner une session de copie.

S’il s’agit de la première session de copie d’un lecteur, le message d’erreur doit indiquer : « La première session de copie ne peut pas être reprise ou abandonnée ». Dans ce cas, vous pouvez supprimer l’ancien fichier journal et réexécuter la commande.  

Si la session de copie n’est pas la première pour un lecteur, il est toujours possible de la reprendre ou de l’abandonner.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>J’ai perdu le fichier journal. Puis-je quand même créer le travail ?

Le fichier journal pour un lecteur contient des informations complètes sur la session de copie de données. Lorsque vous ajoutez des fichiers au lecteur, le fichier journal est utilisé pour créer un travail d’importation. Si vous perdez le fichier journal, vous devez répéter toutes les sessions de copie pour le lecteur.

## <a name="next-steps"></a>Étapes suivantes

* [Configurer l’outil Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Préparer des disques durs pour un travail d’importation](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Examiner l’état d’un travail avec les fichiers journaux de copie](storage-import-export-tool-reviewing-job-status-v1.md)
* [Réparer un travail d’importation](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Réparer un travail d’exportation](storage-import-export-tool-repairing-an-export-job-v1.md)