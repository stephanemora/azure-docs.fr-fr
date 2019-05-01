---
title: Notes de mise à disponibilité générale de passerelle de zone de données Azure | Microsoft Docs
description: Décrit les problèmes critiques et résolutions pour la passerelle de zone de données Azure exécutant la version en disponibilité générale.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/26/2019
ms.author: alkohli
ms.openlocfilehash: f4ee3a5bd754335ab1c7f124671e9c37307a6a28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754197"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Notes de publication de la disponibilité générale de passerelle de données boîte Edge/Azure données boîte Azure

## <a name="overview"></a>Présentation

Les notes suivantes identifient les problèmes critiques et les problèmes résolus pour la disponibilité générale (GA) release pour la zone de données Azure Edge et passerelle de zone de données Azure.

Les notes de publication sont régulièrement mises à jour ; les problèmes critiques nécessitant une solution de contournement sont ajoutés au fur et à mesure de leur découverte. Avant de déployer votre passerelle de zone de données boîte Edge/Data, lisez attentivement les informations contenues dans les notes de publication.

La disponibilité générale correspond aux versions du logiciel :

- **Passerelle de zone de données 1903 (1.5.814.447)**
- **Zone de données Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Nouveautés

- **Nouvelles images de disque virtuel** -nouvelle VHDX et VMDK sont désormais disponibles dans le portail Azure. Télécharger ces images pour approvisionner, configurer et déployer de nouveaux appareils de passerelle de zone de données GA. Les appareils Data Box Gateway créés dans les préversions antérieures ne peuvent pas être mis à jour vers cette version. Pour plus d’informations, consultez [Tutorial: Prepare to deploy Azure Data Box Gateway (Preview)](data-box-gateway-deploy-prep.md) (Didacticiel : Préparation du déploiement d’Azure Data Box Gateway (préversion)).
- **La prise en charge NFS** -prise en charge NFS est actuellement en version préliminaire et disponible pour la version 3.0 et v4.1 clients qui accèdent à des appareils Edge de zone de données et de la passerelle de zone de données.
- **La résilience du stockage** -appareil Edge de zone vos données peut résister à la défaillance d’un disque de données avec la fonctionnalité de résilience du stockage. Actuellement, cette fonctionnalité est uniquement disponible en tant que version préliminaire. Vous pouvez activer une résilience du stockage en sélectionnant le **faculté de rétablissement** option dans le **paramètres de stockage** local de l’interface utilisateur web.


## <a name="known-issues-in-ga-release"></a>Problèmes connus dans la version en disponibilité générale

Le tableau suivant fournit un résumé des problèmes connus pour la passerelle de la zone de données mise en production en cours d’exécution.

| Non. | Fonctionnalité | Problème | Solution de contournement/commentaires |
| --- | --- | --- | --- |
| **1.** |Types de fichier | Types de fichiers suivants ne sont pas prises en charge : fichiers, bloquer les fichiers, sockets, canaux, les liens symboliques de caractères.  |La copie de ces fichiers entraîne la création de fichiers de longueur nulle sur le partage NFS. Ces fichiers restent dans un état d’erreur et sont également signalés dans le fichier *error.xml*. <br> Si des liens symboliques existent vers des répertoires, ces derniers ne sont jamais marqués hors connexion. Par conséquent, vous pouvez ne pas voir la croix grise sur les répertoires, qui indique qu’ils sont hors connexion et que tout le contenu associé a été complètement chargé vers Azure. |
| **2.** |Suppression | En raison d’un bogue dans cette version, si un partage NFS est supprimé, le partage peut ne pas être supprimé. L’état du partage indique *Suppression*.  |Cela ne se produit que lorsque le partage utilise un nom de fichier non pris en charge. |
| **3.** |Copier | Échec de la copie des données avec l’erreur :  Impossible de terminer l’opération demandée du fait d’une limitation du système de fichiers.  |Les autres données Stream (ADS) associés de taille de fichier supérieure à 128 Ko n’est pas pris en charge.   |


## <a name="next-steps"></a>Étapes suivantes

- [Préparer le déploiement d’Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- [Préparer le déploiement de zone de données Azure Edge](data-box-edge-deploy-prep.md).
