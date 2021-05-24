---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 4/05/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 60702c23c32f99d19fbe4741ba3ab170a60dcae2
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645067"
---
```console
Robocopy /MT:32 /R:5 /W:5 /B /MIR /IT /COPY:DATSO /DCOPY:DAT /NP /NFL /NDL /UNILOG:<FilePathAndName> <SourcePath> <Dest.Path> 
```

| Commutateur                | Signification |
|-----------------------|---------|
| `/MT:n`               | Autorise Robocopy à fonctionner en multithread. La valeur par défaut de `n` est 8. La valeur maximale est de 128 threads. Démarrez avec un nombre élevé de threads pour une série initiale. Un nombre élevé de threads permet de saturer la bande passante disponible. Les exécutions suivantes de `/MIR` sont affectées progressivement lorsque vous traitez des éléments sur le transport réseau. Pour les exécutions suivantes, associez votre valeur de nombre de threads au nombre de cœurs du processeur et au nombre de threads par cœur. Déterminez si les cœurs doivent être réservés pour les autres tâches qu’un serveur de production peut prendre en charge. |
| `/R:n`                | Nombre maximal de tentatives pour un fichier dont la copie échoue à la première tentative. Vous pouvez améliorer la vitesse d’exécution d’un Robocopy en spécifiant un nombre maximal (`n`) de nouvelles tentatives avant que la copie du fichier échoue définitivement dans l’exécution. Le fonctionnement de ce commutateur est optimal quand il est déjà évident qu’il y aura d’autres exécutions de Robocopy. Si la copie du fichier échoue dans l’exécution actuelle, la tâche Robocopy suivante réessaiera. Les fichiers en échec, du fait de leur utilisation en cours ou de problèmes de délai d’attente, peuvent au final être correctement copiés si vous utilisez cette approche. |
| `/W:n`                | Spécifie la durée d’attente de Robocopy, avant de tenter la copie d’un fichier qui n’a pas pu être copié à la dernière tentative. `n` est le nombre de secondes d’attente entre les tentatives. `/W:n` est souvent utilisé avec `/R:n`. |
| `/B`                  | Exécute Robocopy dans le même mode qu’une application de sauvegarde. Ce commutateur permet à Robocopy de déplacer des fichiers pour lesquels l’utilisateur actuel n’a pas d’autorisations. |
| `/MIR`                | (Mettre en miroir la source sur la cible) Permet à Robocopy de n’avoir à copier que les deltas entre la source et la cible. Les sous-répertoires vides sont copiés. Les éléments (fichiers ou dossiers) qui ont été modifiés ou qui n’existent pas sur la cible sont copiés. Les éléments qui existent sur la cible, mais pas sur la source, sont vidés (supprimés) de la cible. Lorsque vous utilisez ce commutateur, faites correspondre exactement les structures de dossiers source et cible. *Correspondance* signifie que vous copiez du niveau de source et de dossier qui convient vers le niveau de dossier correspondant sur la cible. C’est la conditions requise pour qu’une copie de « rattrapage » aboutisse. Si la source et la cible ne correspondent pas, l’utilisation de `/MIR` entraîne des suppressions et des recopies à grande échelle. |
| `/IT`                 | Garantit la fidélité dans certains scénarios de mise en miroir. </br>Par exemple, si un fichier fait l’objet d’une modification de liste de contrôle d’accès et d’une mise à jour d’attribut entre deux exécutions de Robocopy, il est également marqué masqué. Sans `/IT`, la modification de la liste de contrôle d’accès peut être ignorée par Robocopy et pas transférée vers l’emplacement cible. |
|`/COPY:[copyflags]`    | Fidélité de la copie de fichier. Par défaut : `/COPY:DAT`. Indicateurs de copie : `D`=Données, `A`=Attributs, `T`=Horodatages, `S`=Sécurité=ACL NTFS, `O`=Informations propriétaire, `U`=Informations a<u>u</u>dit. Les informations d’audit ne peuvent pas être stockées dans un partage de fichiers Azure. |
| `/DCOPY:[copyflags]`  | Fidélité pour la copie de répertoires. Par défaut : `/DCOPY:DA`. Indicateurs de copie : `D` = Données, `A` = Attributs, `T` = Horodatages. |
| `/NP`                 | Spécifie que la progression de la copie de chaque fichier et dossier ne s’affiche pas. L’affichage de la progression réduit considérablement les performances de copie. |
| `/NFL`                | Indique que les noms de fichiers ne sont pas enregistrés dans le journal. Améliore les performances de copie. |
| `/NDL`                | Indique que les noms de répertoires ne sont pas enregistrés dans le journal. Améliore les performances de copie. |
| `/UNILOG:<file name>` | Écrit l’état dans le fichier journal au format Unicode. (Remplace le journal existant.) |
| `/LFSM`               | **Uniquement pour les cibles avec stockage hiérarchisé** </br>Spécifie que Robocopy fonctionne en mode « espace libre faible ». Ce commutateur est utile uniquement pour les cibles avec stockage hiérarchisé, susceptibles de manquer de capacité locale avant que Robocopy puisse se terminer. Il a été spécifiquement ajouté à des fins d’utilisation avec une cible de hiérarchisation cloud Azure File Sync activée. Il peut être utilisé indépendamment d’Azure File Sync. Dans ce mode, Robocopy s’interrompt chaque fois qu’une copie de fichier réduit l’espace libre du volume de destination en dessous d’une valeur « plancher ». Cette valeur peut être spécifiée par la forme `/LFSM:n` de l’indicateur. Le paramètre `n` est spécifié en base 2 : `nKB`, `nMB` ou `nGB`. Si `/LFSM` est spécifié sans valeur plancher explicite, le plancher est défini sur 10 % de la taille du volume de destination. Le mode d’espace libre faible n’est pas compatible avec `/MT`, `/EFSRAW`, `/B` ou `/ZB`. |
| `/Z`                  | **Utiliser avec prudence** </br>Copie les fichiers en mode redémarrage. Ce commutateur est recommandé uniquement dans un environnement réseau instable. Elle réduit considérablement les performances de copie en raison d’une journalisation supplémentaire. |
| `/ZB`                 | **Utiliser avec prudence** </br>Utilise le mode de redémarrage. En cas d’accès refusé, cette option utilise le mode de sauvegarde. Cette option réduit considérablement les performances de copie en raison des points de contrôle. |
   