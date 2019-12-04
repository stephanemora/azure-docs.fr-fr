---
title: Utilisation d’un partage de fichiers Azure avec Stockage Azure | Microsoft Docs
description: Découvrez comment utiliser un partage de fichiers Azure avec Windows et Windows Server.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: e1e581134b4e3821659c43bb7f55003239594bd9
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74234450"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Récupération d’un compte de stockage supprimé

Stockage Azure offre une résilience des données via des réplicas automatisés, mais n’empêche pas les utilisateurs ou le code de l’application d’endommager les données, que ce soit par mégarde ou par malveillance. La conservation de l’intégrité des données pendant des instances d’application ou des erreurs d’utilisateurs nécessite des techniques plus avancées, comme la copie des données vers un emplacement de stockage secondaire avec un journal d’audit.

Le tableau suivant fournit une vue d’ensemble de l’étendue de la récupération d’un compte de stockage en fonction de la stratégie de réplication.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Compte de stockage Azure Resource Manager|OUI|OUI|OUI|OUI|
|Compte de stockage classique|OUI|OUI|OUI|OUI|

Réunissez les informations suivantes et remplissez une demande de support auprès du Support Microsoft :

* Nom du compte de stockage
* Date de suppression
* Région du compte de stockage
* Comment le compte de stockage a-t-il été supprimé ?
* Quelle méthode a supprimé le compte de stockage ? (Portail, PowerShell, etc.)

Points importants

* Cela peut généralement prendre jusqu’à 15 jours à compter de la suppression pour que le service de stockage puisse effectuer des nettoyages de la mémoire, de sorte que la récupération des comptes de stockage ne peut pas être réalisée au moyen d’un SLA.
* Support Microsoft tentera de récupérer le conteneur/compte de son mieux et ne peut garantir la récupération.

> [!NOTE]
> La récupération peut échouer si le compte a été recréé. Si vous avez déjà recréé le compte, vous devez d’abord le supprimer avant de pouvoir effectuer la récupération.
