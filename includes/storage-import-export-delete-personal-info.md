---
title: Fichier Include
description: Fichier Include
services: azure-policy
author: craigshoemaker
ms.service: azure-policy
ms.topic: include
ms.date: 05/18/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: e6a0ded137162328fd446b65ddb4a15fa6f1db88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "67177276"
---
## <a name="deleting-personal-information"></a>Suppression d’informations personnelles

[!INCLUDE [gdpr-intro-sentence.md](gdpr-intro-sentence.md)]

Les informations personnelles sont utilisées par le service Import/Export (via le portail et l’API) au cours des opérations d’importation et d’exportation. Les données suivantes sont utilisées au cours de ces processus :

- Nom du contact
- Numéro de téléphone
- Email
- Adresse postale
- City
- Code postal
- State
- Pays/Province/Région
- ID de lecteur de disque
- Numéro de compte du transporteur
- Numéro de suivi d’expédition

Lors de la création d’un travail d’importation/exportation, les utilisateurs fournissent des informations de contact et une adresse d’expédition. Les informations personnelles peuvent être stockées à deux emplacements : dans la tâche et, éventuellement, dans les paramètres du portail. Les informations personnelles sont stockées dans les paramètres du portail uniquement si vous activez la case à cocher **Enregistrer le transporteur et l’adresse de réexpédition comme valeurs par défaut** dans la section *Informations de réexpédition* du processus d’exportation.

Les informations de contact personnel peuvent être supprimées des manières suivantes :

- Les données enregistrées avec le travail sont supprimées avec le travail. Les utilisateurs peuvent supprimer manuellement des travaux et les travaux terminés sont automatiquement supprimés après 90 jours. Vous pouvez supprimer manuellement les travaux via l’API REST ou le Portail Azure. Pour supprimer le travail dans le Portail Azure, accédez à votre travail d’importation/exportation, puis cliquez sur *Supprimer* dans la barre de commandes. Pour plus d’informations sur la suppression d’un travail d’importation/exportation via l’API REST, consultez [Supprimer un travail d’importation/exportation](../articles/storage/common/storage-import-export-cancelling-and-deleting-jobs.md).

- Les informations de contact enregistrées dans les paramètres du portail peuvent être supprimées en supprimant les paramètres du portail. Vous pouvez supprimer les paramètres du portail en procédant comme suit :
  - Connectez-vous au [portail Azure](https://portal.azure.com).
  - Cliquez sur l’icône *Paramètres*![Icône des paramètres Azure](media/storage-import-export-delete-personal-info/azure-settings-icon.png).
  - Cliquez sur *Exporter tous les paramètres* (pour enregistrer vos paramètres actuels dans un fichier `.json`).
  - Cliquez sur *Supprimer tous les paramètres et les tableaux de bord privés* pour supprimer tous les paramètres y compris les informations de contact enregistrées.

Pour plus d’informations, consultez la Politique de confidentialité Microsoft dans le [Centre de gestion de la confidentialité](https://www.microsoft.com/trustcenter).