---
title: Récupération d’un compte de stockage supprimé
description: Découvrez comment récupérer un compte de stockage supprimé.
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252630"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Récupération d’un compte de stockage supprimé

Stockage Azure offre une résilience des données via des réplicas automatisés, mais n’empêche pas les utilisateurs ou le code de l’application d’endommager les données, que ce soit par mégarde ou par malveillance. La conservation de l’intégrité des données pendant des instances d’application ou des erreurs d’utilisateurs nécessite des techniques plus avancées, comme la copie des données vers un emplacement de stockage secondaire avec un journal d’audit.

Le tableau suivant fournit une vue d’ensemble de l’étendue de la récupération d’un compte de stockage en fonction de la stratégie de réplication.

| |LRS|ZRS|GRS|RA-GRS|
|---|---|---|---|---|
|Compte de stockage Azure Resource Manager|Oui|Oui|Oui|Oui|
|Compte de stockage classique|Oui|Oui|Oui|Oui|

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
