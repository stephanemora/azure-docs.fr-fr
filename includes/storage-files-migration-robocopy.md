---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 6158964c04a689ed421f216c2910f47d7a39e2be
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106081019"
---
```console
Robocopy /MT:16 /NP /NFL /NDL /B /MIR /IT /COPY:DATSO /COPY:DATSO /DCOPY:DAT /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Commutateur              | Signification |
|---------------------|---------|
| /MT                 | Permet à RoboCopy de s’exécuter en multithread. La valeur par défaut est 8 et la valeur maximale est 128. Faites correspondre cette valeur au nombre de cœurs de processeur et au nombre de threads par cœur. Déterminez si les cœurs doivent être réservés pour les autres tâches qu’un serveur de production peut prendre en charge. |
| /NP                 | La progression de la copie de chaque fichier et dossier ne s’affiche pas. L’affichage de la progression réduit considérablement les performances de copie. |
| /NFL                | Indique que les noms de fichiers ne sont pas enregistrés dans le journal. Améliore les performances de copie. |
| /NDL                | Indique que les noms de répertoires ne sont pas enregistrés dans le journal. Améliore les performances de copie. |
| /B                  | Exécute RoboCopy dans le même mode qu’une application de sauvegarde. Cela permet à RoboCopy de déplacer des fichiers pour lesquels l’utilisateur actuel n’a pas d’autorisations. |
| /MIR                | *Mettre en miroir la source sur la cible* permet à Robocopy de n’avoir à copier que les deltas entre la source et la cible. Les sous-répertoires vides sont copiés. Les éléments (fichiers ou dossiers) qui ont été modifiés ou qui n’existent pas sur la cible sont copiés. Les éléments qui existent sur la cible, mais pas sur la source, sont vidés (supprimés) de la cible. Lorsque vous utilisez ce commutateur, vous devez impérativement respecter la structure des dossiers source et cible. « Correspondance » signifie que vous copiez du niveau de source et de dossier qui convient vers le niveau de dossier correspondant sur la cible. C’est la conditions requise pour qu’une copie de « rattrapage » aboutisse. Si la source et la cible ne correspondent pas, l’utilisation de `/MIR` entraîne des suppressions et des recopies à grande échelle. |
| /IT                 | Garantit la fidélité dans certains scénarios de mise en miroir. </br>*Exemple* : si entre deux exécutions Robocopy, un fichier fait l’objet d’une modification de liste de contrôle d’accès et d’une mise à jour d’attribut, par exemple, il est également marqué *masqué*. Sans /IT, la modification de la liste de contrôle d’accès peut être ignorée par Robocopy et pas transférée vers l’emplacement cible. |
|/COPY:`[copyflags]`  | Fidélité de copie des fichiers (la valeur par défaut si elle n’est pas spécifiée est `/COPY:DAT`), indicateurs de copie : `D`=Data, `A`=Attributes, `T`=Timestamps, `S`=Security=NTFS ACLs, `O`=Owner information, `U`=A<u>u</u>diting information. Les informations d’audit ne peuvent pas être stockées dans un partage de fichiers Azure. |
| /DCOPY:`[copyflags]`| Fidélité de copie des répertoires (la valeur par défaut si elle n’est pas spécifiée est `/DCOPY:DA`), indicateurs de copie : `D`=Data, `A`=Attributes, `T`=Timestamps. |
| /UNILOG:<file name> | Renvoie l’état au fichier LOG au format UNICODE (remplace le journal existant). |
| /LFSM               | **uniquement pour les cibles avec stockage hiérarchisé** </br>Utilisation de requêtes /LFSM RoboCopy pour fonctionner en « mode de faible espace disponible ». Ce commutateur est utile uniquement pour les cibles avec stockage hiérarchisé, susceptibles de manquer de capacité locale avant que RoboCopy puisse se terminer. Ce commutateur a été spécifiquement ajouté à des fins d’utilisation avec une cible de hiérarchisation cloud Azure File Sync activée. Il peut être utilisé indépendamment d’Azure File Sync. Dans ce mode, RoboCopy s’interrompt chaque fois qu’une copie de fichier réduit l’espace libre du volume de destination en dessous d’une valeur « plancher ». Cette valeur peut être spécifiée par la forme `/LFSM:n` de l’indicateur. Le paramètre `n` est spécifié en base 2 : `nKB`, `nMB` ou `nGB`. Si `/LFSM` est spécifié sans valeur plancher explicite, le plancher est défini sur 10 % de la taille du volume de destination. Le mode de faible espace disponible est incompatible avec /MT, /EFSRAW, /B et /ZB. |
| /Z                  | **À utiliser avec précaution** </br>Copie les fichiers en mode de redémarrage. Il est conseillé de limiter son utilisation à un environnement réseau instable. Cette option réduit considérablement les performances de copie en raison d’une journalisation supplémentaire. |
| /ZB                 | **À utiliser avec précaution** </br>Utilise le mode de redémarrage. En cas d’accès refusé, cette option utilise le mode de sauvegarde. Cette option réduit considérablement les performances de copie en raison des points de contrôle. |
   