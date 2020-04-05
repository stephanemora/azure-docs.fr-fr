---
title: Mapper une structure de dossiers à une topologie Azure File Sync
description: Mappage d’une structure de fichiers et de dossiers existante à des partages de fichiers Azure à des fins d'utilisation avec Azure File Sync. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 25c333aae49bff8d0596d4f5403c18576bf198b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124729"
---
Au cours de cette étape, vous allez évaluer le nombre de partages de fichiers Azure dont vous avez besoin. Un seul serveur Windows Server (ou cluster) peut synchroniser jusqu’à 30 partages de fichiers Azure.

Vous pouvez avoir plus de dossiers dans vos volumes que ce que vous partagez actuellement localement en tant que partages SMB pour vos utilisateurs et applications. La solution la plus simple consiste à envisager un partage local à des fins de mappage 1:1 à un partage de fichiers Azure. Si vous avez un nombre suffisamment petit, inférieur à 30 pour un seul serveur Windows, un mappage de 1:1 est recommandé.

Si vous disposez de plus de 30 partages, il est souvent inutile de mapper un partage local 1:1 à un partage de fichiers Azure.
Considérez les options suivantes :

#### <a name="share-grouping"></a>Regroupement de partages

Par exemple, si votre service RH dispose d'un total de 15 partages, vous pouvez envisager de stocker toutes les données RH dans un seul partage de fichiers Azure. Le stockage de plusieurs partages locaux dans un partage de fichiers Azure ne vous empêche pas de créer les 15 partages SMB habituels sur votre instance Windows Server locale. Cela signifie simplement que vous organisez les dossiers racine de ces 15 partages en sous-dossiers sous un dossier commun. Vous synchronisez ensuite ce dossier commun avec un partage de fichiers Azure. Ainsi, un seul partage de fichiers Azure dans le cloud est nécessaire pour ce groupe de partages locaux.

#### <a name="volume-sync"></a>Synchronisation de volume

Azure File Sync prend en charge la synchronisation de la racine d’un volume avec un partage de fichiers Azure.
Si vous synchronisez le dossier racine, tous les sous-dossiers et fichiers se retrouvent dans le même partage de fichiers Azure.

La synchronisation de la racine du volume ne constitue pas toujours la meilleure réponse. La synchronisation de plusieurs emplacements présente des avantages et permet notamment de conserver un nombre d’éléments plus bas par étendue de synchronisation. La configuration d’Azure File Sync avec un nombre d’éléments inférieur n’est pas seulement avantageuse pour la synchronisation de fichiers. Un nombre inférieur d’éléments présente également des avantages pour d’autres scénarios comme :

* la restauration côté cloud à partir d’un instantané de partage de fichiers Azure pris comme sauvegarde ;
* la récupération d’urgence d’un serveur local peut accélérer considérablement ;
* les modifications apportées directement dans un partage de fichiers Azure (en dehors de la synchronisation) peuvent être détectées et synchronisées plus rapidement.

#### <a name="a-structured-approach-to-a-deployment-map"></a>Approche structurée d’un plan de déploiement

Avant de déployer un stockage cloud lors d’une étape ultérieure, il est important de créer un mappage entre des dossiers locaux et des partages de fichiers Azure. Ce mappage indiquera ensuite le nombre et les ressources de « groupe de synchronisation » Azure File Sync que vous allez approvisionner. Un groupe de synchronisation lie le partage de fichiers Azure et le dossier sur votre serveur et établit une connexion de synchronisation.

Pour prendre la décision sur le nombre de partages de fichiers Azure dont vous avez besoin, passez en revue les limites et les meilleures pratiques suivantes. Cela vous permet d’optimiser votre mappage :

* Un serveur sur lequel l’agent Azure File Sync est installé peut se synchroniser avec un maximum de 30 partages de fichiers Azure.
* Un partage de fichiers Azure est déployé dans un compte de stockage. Ainsi, le compte de stockage est une cible de mise à l’échelle pour les numéros de performance tels que les IOPS et le débit. Deux partages de fichiers Azure standard (non Premium) peuvent théoriquement saturer les performances maximales qu’un compte de stockage peut fournir. Si vous envisagez de joindre uniquement Azure File Sync à ces partages de fichiers, le regroupement de plusieurs partages de fichiers Azure dans le même compte de stockage ne crée pas de problème. Examinez les objectifs de performances du partage de fichiers Azure pour obtenir un aperçu approfondi sur les mesures pertinentes à prendre en compte. Si vous envisagez d’intégrer une application dans Azure qui utilisera le partage de fichiers Azure en mode natif, vous devrez peut-être augmenter les performances de votre partage de fichiers Azure. S’il s’agit d’une éventualité, même future, le mappage d’un partage de fichiers Azure à son propre compte de stockage est préférable.
* Il existe une limite de 250 comptes de stockage par abonnement dans une seule région Azure.

> [!TIP]
> Avec ces informations à l’esprit, il est souvent nécessaire de regrouper plusieurs dossiers de niveau supérieur sur vos volumes dans un répertoire racine commun et nouveau. Vous allez ensuite synchroniser ce nouveau répertoire racine et tous les dossiers que vous y avez regroupés dans un seul partage de fichiers Azure.                                                    

Cette technique vous permet de rester dans la limite de 30 synchronisations de partage de fichiers Azure par serveur.
Ce regroupement sous une racine commune n’a aucun impact sur l’accès à vos données. Vos listes de contrôle d’accès restent en l’état, vous n’avez besoin d’ajuster que les chemins de partage (tels que les partages SMB ou NFS) que vous pouvez avoir sur les dossiers de serveur que vous avez désormais modifiés dans une racine commune. Rien d’autre ne change.

Un autre aspect important d’Azure File Sync et une performance et une expérience équilibrées sont une compréhension des facteurs d’échelle pour les performances d’Azure File Sync. Évidemment, lorsque les fichiers sont synchronisés sur Internet, les fichiers plus volumineux prennent plus de temps et de bande passante pour la synchronisation.

> [!IMPORTANT]
> Le vecteur d’échelle le plus important pour Azure File Sync est le nombre d’éléments (fichiers et dossiers) qui doivent être synchronisés.

Azure File Sync prend en charge la synchronisation jusqu’à 100 000 éléments sur un partage de fichiers Azure unique. Cette limite peut être dépassée et indique uniquement ce que l’équipe Azure File Sync teste régulièrement.

Il est recommandé de conserver un faible nombre d’éléments par étendue de synchronisation. Cet aspect est un facteur important à prendre en compte dans votre mappage de dossiers aux partages de fichiers Azure.

Même si, dans votre situation, un ensemble de dossiers peuvent être synchronisés logiquement avec le même partage de fichiers Azure (à l’aide de la nouvelle approche courante du dossier racine ci-dessus), il peut quand même être préférable de regrouper les dossiers de manière à ce qu’ils se synchronisent à deux au lieu d’un partage de fichiers Azure. Cette approche peut être utilisée pour conserver l’équilibre entre le nombre de fichiers et de dossiers par partage de fichiers sur le serveur.

#### <a name="create-a-mapping-table"></a>Créer une table de mappage

:::row:::
    :::column:::
        [![](media/storage-files-migration-namespace-mapping/namespace-mapping.png "An example of a mapping table. Download the file below to experience and use the content of this image.")](media/storage-files-migration-namespace-mapping/namespace-mapping-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Combinez les concepts précédents pour déterminer le nombre de partages de fichiers Azure dont vous avez besoin, ainsi que les parties de vos données existantes finissant dans ces différents partages.
        
        Créez une table répertoriant vos réflexions afin de vous y référer lors de l’étape suivante. Il convient d'être organisé et de le rester afin de ne perdre aucun détail de votre plan de mappage lors de l’approvisionnement simultané de nombreuses ressources Azure. Pour vous aider à créer un mappage complet, vous pouvez télécharger un fichier Microsoft Excel en tant que modèle.

[//]: # (Le langage HTML se présente comme étant le seul moyen d’ajouter une table imbriquée à deux colonnes avec analyse des images de travail et texte/lien hypertexte sur la même ligne.)

<br>
<table>
    <tr>
        <td>
            <img src="media/storage-files-migration-namespace-mapping/excel.png" alt="Microsoft Excel file icon that helps to set the context for the type of file download for the link next to it.">
        </td>
        <td>
            <a href="https://download.microsoft.com/download/1/8/D/18DC8184-E7E2-45EF-823F-F8A36B9FF240/Azure File Sync - Namespace Mapping.xlsx">Téléchargez un modèle de mappage d’espace de noms.</a>
        </td>
    </tr>
</table>
    :::column-end:::
:::row-end:::
