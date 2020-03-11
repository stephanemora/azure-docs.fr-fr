---
title: Mapper une structure de dossiers à une topologie Azure File Sync
description: Mappage d’une structure de fichiers et de dossiers existante à des partages de fichiers Azure à des fins d'utilisation avec Azure File Sync. Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209320"
---
Au cours de cette étape, vous allez évaluer le nombre de partages de fichiers Azure dont vous avez besoin. Un seul serveur Windows Server (ou cluster) peut synchroniser jusqu’à 30 partages de fichiers Azure.

Vous pouvez avoir plus de dossiers sur vos StorSimple que vous en partagez actuellement localement en tant que partages SMB pour vos utilisateurs et applications. La solution la plus simple consiste à envisager un partage local à des fins de mappage 1:1 à un partage de fichiers Azure. Si ce nombre est trop petit, à savoir inférieur à 30 pour une seule instance Windows Server, ou si vous envisagez de disposer de deux instances Windows Server (60), et ainsi de suite, un mappage de 1:1 est recommandé.

Si vous disposez de plus de 30 partages, il est souvent inutile de mapper un partage local 1:1 à un partage de fichiers Azure.
Considérez les options suivantes :

#### <a name="share-grouping"></a>Regroupement de partages

Par exemple, si votre service RH dispose d'un total de 15 partages, vous pouvez envisager de stocker toutes les données RH dans un seul partage de fichiers Azure. Le stockage de plusieurs partages locaux dans un partage de fichiers Azure ne vous empêche pas de créer les 15 partages SMB habituels sur votre instance Windows Server locale. Cela signifie simplement que vous organisez les dossiers racine de ces 15 partages en sous-dossiers sous un dossier commun. Vous synchronisez ensuite ce dossier commun avec un partage de fichiers Azure. Ainsi, un seul partage de fichiers Azure dans le cloud est nécessaire pour ce groupe de partages locaux.

#### <a name="volume-sync"></a>Synchronisation de volume

Azure File Sync prend en charge la synchronisation de la racine d’un volume avec un partage de fichiers Azure.
Si vous synchronisez le dossier racine, tous les sous-dossiers et fichiers se retrouvent dans le même partage de fichiers Azure.

#### <a name="other-best-practices-to-consider"></a>Autres meilleures pratiques à prendre en compte

En dehors de la limite de synchronisation de 30 partages de fichiers Azure par serveur, il convient de prendre en compte l'efficacité de la synchronisation.

En présence de plusieurs partages sur votre serveur se synchronisant avec leur propre partage de fichiers Azure, la synchronisation peut fonctionner en parallèle pour l'ensemble. Le vecteur d’échelle ne correspond pas à la taille de tous les fichiers dans une étendue de synchronisation. Il correspond au nombre d’éléments (fichiers et dossiers) qui doivent être traités.

Un seul partage de fichiers Azure peut contenir jusqu’à 100 Tio.
Azure File Sync prend en charge la synchronisation jusqu’à 100 millions d'éléments par partage de fichiers Azure.

La synchronisation du volume racine ne constitue pas toujours la meilleure réponse. La synchronisation de plusieurs emplacements présente des avantages et permet notamment de conserver un nombre d’éléments plus bas par étendue de synchronisation. En plus d'être importante à des fins de synchronisation, la configuration d’Azure file Sync avec un plus petit nombre d’éléments présente aussi des avantages en termes de restauration côté cloud à partir de sauvegardes, et accélère la récupération d’urgence en cas de perte de serveur et d'approvisionnement d'un nouveau serveur se connectant aux même partages de fichiers Azure.

Combinez les concepts ci-dessus pour déterminer le nombre de partages de fichiers Azure dont vous avez besoin, ainsi que les parties de vos données StorSimple existantes finissant dans ces différents partages.

Créez une liste répertoriant vos réflexions afin de vous y référer lors de l'étape suivante. Il convient ici d'être organisé et de le rester afin de ne perdre aucun détail de votre plan de mappage lors de l'approvisionnement simultané de nombreuses ressources.
