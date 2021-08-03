---
title: Mise à jour d’Azure HDInsight Ubuntu 18.04
description: En savoir plus sur les modifications apportées au système d’exploitation Azure HDInsight Ubuntu 18.04.
ms.service: hdinsight
ms.topic: conceptual
ms.author: yanacai
author: yanancai
ms.date: 05/25/2021
ms.openlocfilehash: 61d801d7091d2a619ff6a8b6436f386c125ca4be
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111414895"
---
# <a name="hdinsight-ubuntu-1804-os-update"></a>Mise à jour du système d’exploitation HDInsight Ubuntu 18.04

Cet article fournit plus de détails sur la mise à jour du système d’exploitation HDInsight Ubuntu 18.04 et les éventuelles modifications nécessaires.

## <a name="update-overview"></a>Vue d’ensemble de la mise à jour

HDInsight a commencé à déployer la nouvelle image de cluster HDInsight 4.0 s’exécutant sur Ubuntu 18.04 en mai 2021. Une fois disponibles, les clusters HDInsight 4.0 nouvellement créés s’exécuteront par défaut sur Ubuntu 18.04. Les clusters existants sur Ubuntu 16.04 s’exécuteront en l’état avec une prise en charge complète.

HDInsight 3.6 continuera de s’exécuter sur Ubuntu 16.04. Il atteindra la fin du support standard d’ici le 30 juin 2021 et passera au support De base à compter du 1er juillet 2021. Pour plus d’informations sur les dates et les options de prise en charge, consultez [Versions d’Azure HDInsight](./hdinsight-component-versioning.md). Ubuntu 18.04 ne sera pas pris en charge pour HDInsight 3.6. Si vous souhaitez utiliser Ubuntu 18.04, vous devez migrer vos clusters vers HDInsight 4.0.

Si vous souhaitez déplacer des clusters existants vers Ubuntu 18.04, supprimez et recréez vos clusters. Planifiez la création ou la recréation de votre cluster. 

## <a name="script-actions-changes"></a>Modifications d’actions de script

Des actions de script HDInsight sont utilisées pour installer des composants supplémentaires et modifier des paramètres de configuration. Une action de script est un script Bash qui s’exécute sur les nœuds dans un cluster HDInsight.

Il se peut que vous deviez apporter des modifications pour vos actions de script.

**Lorsque vous récupérez vos packages là où vous en avez besoin, modifiez chaque instance de `xenial` en `bionic` :**

Par exemple :
- Mettez à jour `http://packages.treasuredata.com/3/ubuntu/xenial/ xenial contrib` vers `http://packages.treasuredata.com/3/ubuntu/bionic/ bionic contrib`.
- Mettez à jour `http://azure.archive.ubuntu.com/ubuntu/ xenial main restricted` vers `http://azure.archive.ubuntu.com/ubuntu/ bionic main restricted`.

**Certaines versions de package ne sont pas présentes pour bionic :** 

Par exemple, [Node.js version 4. x](https://deb.nodesource.com/node_4.x/dists/) ne figure pas dans le référentiel bionic. [Node.js version 12.x](https://deb.nodesource.com/node_12.x/dists/bionic/) y figure.

Les scripts qui installent des versions antérieures qui ne sont pas présentes pour bionic doivent être mis à jour vers des versions ultérieures.

**/etc/rc.local does not exist by default dans la version 18.04 :**

Certains scripts utilisent `/etc/rc.local` pour les démarrages de service, mais il n’existe pas par défaut dans Ubuntu 18.04. Il doit être converti en une unité système appropriée. 

**Les packages de système d’exploitation de base ont été mis à jour :**

Si vos scripts dépendent d’un package de version plus ancien dans Ubuntu 16.04, il se peut que cela ne fonctionne pas. Connectez-vous via SSH à votre nœud de cluster, puis exécutez `dpkg --list` sur votre nœud de cluster pour afficher les détails de tous les packages installés.
 
**En général, Ubuntu 18.04 a des règles plus strictes que la version 16.04.**

## <a name="custom-applications"></a>Applications personnalisées
Certaines [applications tierces](./hdinsight-apps-install-applications.md) peuvent être installées sur le cluster HDInsight. Il se peut que ces applications ne fonctionnent pas bien avec Ubuntu 18.04. Afin de réduire le risque de changements cassants, HDInsight ne déploie pas la nouvelle image pour des abonnements sur lesquels des applications personnalisées ont été installées depuis le 25 février 2021. Si vous souhaitez essayer la nouvelle image avec vos abonnements de test, ouvrez un ticket de support pour activer votre abonnement.

## <a name="edge-nodes"></a>Nœuds de périmètre
Avec la nouvelle image, le système d’exploitation pour les nœuds de périphérie du cluster est également mis à jour vers Ubuntu 18.04. Vos clients existants doivent être testés avec Ubuntu 18.04. Afin de réduire le risque de changements cassants, HDInsight ne déploie pas la nouvelle image pour des abonnements qui ont utilisé des nœuds de périphérie depuis le 25 février 2021. Si vous souhaitez essayer la nouvelle image avec vos abonnements de test, ouvrez un ticket de support pour activer votre abonnement.

## <a name="references"></a>Références
 - [Notes de publication d’Ubuntu 18.04 LTS](https://wiki.ubuntu.com/BionicBeaver/ReleaseNotes/)





