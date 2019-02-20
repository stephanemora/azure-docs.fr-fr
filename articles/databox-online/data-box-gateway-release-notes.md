---
title: Notes de publication de préversion d’Azure Data Box Gateway | Microsoft Docs
description: Décrit les problèmes majeurs existants et les résolutions pour Azure Data Box Gateway exécutant la préversion.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: 0265de5b224e62d188fe6e3b9322d5c2e3f77fa1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883132"
---
# <a name="azure-data-box-gateway-preview-release-notes"></a>Notes de publication de préversion d’Azure Data Box Gateway

## <a name="overview"></a>Vue d’ensemble

Les notes de publication suivantes identifient les problèmes majeurs existants et les problèmes résolus dans la préversion de Microsoft Azure Data Box Gateway.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Lisez attentivement les informations contenues dans les notes de publication avant de déployer votre Data Box Gateway.

La préversion correspond à la version du logiciel **Data Box Gateway préversion 2.0**.

## <a name="issues-fixed-in-preview-release"></a>Problèmes résolus dans la préversion

Le tableau suivant récapitule les problèmes corrigés dans cette version.

| Non. | Problème |
| --- | --- |
| **1.** | Dans cette version, lorsqu’un fichier chargé par un autre outil (AzCopy) est actualisé puis mis à jour d’une manière qui augmente/étend la taille du fichier, l’erreur suivante est observée : *Erreur 400 : InvalidBlobOrBlock (L’objet blob spécifié ou le contenu du bloc n’est pas valide.)*|
| **2.** |En raison d’un bogue dans cette version, vous pouvez voir des instances du code d’erreur 110 dans *error.xml* avec des noms d’éléments non reconnaissables. | 
| **3.** |En raison d’un bogue dans cette version, vous pouvez voir des instances du code d’erreur 2003 lors du chargement de fichiers spécifiques. | 
| **4.** |Dans cette version, vous ne pouvez actualiser qu’un seul partage à la fois. | 


## <a name="known-issues-in-preview-release"></a>Problèmes connus dans la préversion

Le tableau suivant résume les problèmes connus pour Data Box Gateway exécutant la préversion.

| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
| --- | --- | --- | --- |
| **1.** |Mises à jour |Les appareils Data Box Gateway créés dans les préversions antérieures ne peuvent pas être mis à jour vers cette version. |Téléchargez les images de disque virtuel de la nouvelle version, puis configurez et déployez de nouveaux appareils. Pour plus d’informations, consultez [Tutorial: Prepare to deploy Azure Data Box Gateway (Preview)](data-box-gateway-deploy-prep.md) (Didacticiel : Préparation du déploiement d’Azure Data Box Gateway (préversion)). |
| **2.** |Disque de données configuré |Une fois que vous avez provisionné un disque de données d’une certaine taille spécifiée et créé le Data Box Gateway correspondant, vous ne devez pas réduire ce disque de données. Toute tentative de réduction du disque entraîne la perte de toutes les données locales sur l’appareil. | |
| **3.** |Renommer |Le changement de nom d’objets n’est pas pris en charge. |Contactez le Support Microsoft si cette fonctionnalité est essentielle pour votre flux de travail. |
| **4.** |Copier| Si un fichier en lecture seule est copié sur l’appareil, la propriété de lecture seule n’est pas conservée. | |
| **5.** |Types de fichier | Les types de fichiers Linux suivants ne sont pas pris en charge : fichiers de caractères, fichiers de blocs, sockets, pipes et liens symboliques.  |La copie de ces fichiers entraîne la création de fichiers de longueur nulle sur le partage NFS. Ces fichiers restent dans un état d’erreur et sont également signalés dans le fichier *error.xml*. |
| **6.** |Suppression | En raison d’un bogue dans cette version, si un partage NFS est supprimé, le partage peut ne pas être supprimé. L’état du partage indique *Suppression*.  |Cela ne se produit que lorsque le partage utilise un nom de fichier non pris en charge. |
| **7.** |Actualiser | Les autorisations et les listes de contrôle d’accès (ACL) ne sont pas conservées lors d’une opération d’actualisation.  | |
| **8.** |Copier | Échec de la copie des données avec l’erreur :  Impossible de terminer l’opération demandée du fait d’une limitation du système de fichiers.  |Cette erreur se produit lorsque le flux de données alternatif (ADS) associé au fichier dépasse 128 Ko (limite maximale pour ReFS).  |
| **9.** |Liens symboliques |Les liens symboliques ne sont pas pris en charge.  |Si des liens symboliques existent vers des répertoires, ces derniers ne sont jamais marqués hors connexion. Par conséquent, vous pouvez ne pas voir la croix grise sur les répertoires, qui indique qu’ils sont hors connexion et que tout le contenu associé a été complètement chargé vers Azure. |
| **10.** |Partages |Actualiser un conteneur existant avec des objets blob de pages vers un partage d'objets blob de blocs (ou inversement) entraîne des échecs de chargement sur la modification de fichier.  |Ce comportement est observé lors des étapes suivantes : <li> Créer un partage d'objets blob de blocs sur l'appareil. </li><li> Associer le partage avec un conteneur cloud existant présentant des objets blob de pages.</li><li>Actualiser ce partage. </li><li>Modifier plusieurs fichiers actualisés déjà stockés en tant qu'objets blob de pages dans le cloud.</li> Des échecs de chargement sont observés. |
| **11.** |Aide en ligne |Les liens d’aide dans le portail Azure peuvent ne pas diriger vers la documentation.|Les liens d’aide fonctionneront dans la version en disponibilité générale. |

## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md).


