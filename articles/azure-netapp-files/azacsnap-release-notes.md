---
title: Notes de publication pour l’outil Azure Application Consistent Snapshot pour Azure NetApp Files | Microsoft Docs
description: Fournit des notes de publication pour l’outil Azure Application Consistent Snapshot que vous pouvez utiliser avec Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 269ef95835e5284806e7c1eaa76980e635c3d1dd
ms.sourcegitcommit: ad921e1cde8fb973f39c31d0b3f7f3c77495600f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/25/2021
ms.locfileid: "107946544"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Notes de publication pour l'outil Azure Application Consistent Snapshot

Cette page répertorie les principales modifications apportées à AzAcSnap pour fournir de nouvelles fonctionnalités ou résoudre les défauts.

## <a name="april-2021"></a>Avril 2021

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (Build : 20210421.6349) - Version GA (21 avril 2021)

AzAcSnap v5.0 (Build : 20210421.6349) a été mis à la disposition générale. Cette build a bénéficié des correctifs et améliorations suivants :

- Le délai de nouvelle tentative hdbsql (pour attendre une réponse de SAP HANA) est automatiquement défini sur la moitié du paramètre « savePointAbortWaitSeconds » pour éviter les situations de concurrence.  Le paramètre « savePointAbortWaitSeconds » peut être modifié directement dans le fichier de configuration JSON et doit être au minimum de 600 secondes.

Téléchargez la [version la plus récente](https://aka.ms/azacsnapdownload) du programme d’installation et consultez la [procédure de démarrage](azacsnap-get-started.md).

## <a name="march-2021"></a>Mars 2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview (Build:20210318.30771)

AzAcSnap v5.0 Preview (Build:20210318.30771) a été publié avec les améliorations et correctifs suivants :

- Suppression de la nécessité d’ajouter l’utilisateur AZACSNAP dans les bases de données des locataires SAP HANA ; voir la section [Activer la communication avec SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana).
- Correctif pour autoriser une [restauration](azacsnap-cmd-ref-restore.md) avec des volumes configurés avec la QoS manuelle.
- Ajout du contrôle mutex pour limiter les connexions SSH pour Azure Large Instance.
- Correction du programme d’installation pour gérer les noms de chemin d’accès avec des espaces et d’autres problèmes connexes.
- En vue de la prise en charge d’autres serveurs de base de données, le paramètre facultatif « --hanasid » a été remplacé par « --dbsid ».

## <a name="next-steps"></a>Étapes suivantes

- [Bien démarrer avec l’outil Azure Application Consistent Snapshot](azacsnap-get-started.md)
