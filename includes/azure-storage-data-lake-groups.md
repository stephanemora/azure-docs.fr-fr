---
services: storage
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/29/2020
ms.author: normesta
ms.custom: include file
ms.openlocfilehash: 73a8f20122e21c4557289995c15b008401285011
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92131647"
---
Utilisez toujours les [groupes de sécurité Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups) comme principal affecté dans les entrées ACL. Résistez à la possibilité d’attribuer directement des utilisateurs ou des principaux de service individuels. L’utilisation de cette structure vous permettra d’ajouter et de supprimer des utilisateurs ou des principaux de service sans devoir réappliquer les ACL sur la structure de répertoires dans son ensemble. Au lieu de cela, vous pouvez simplement ajouter ou supprimer des utilisateurs et des principaux de service dans le groupe de sécurité Azure AD approprié. 

Il existe plusieurs façons de configurer des groupes. Par exemple, imaginez que vous disposez d’un répertoire nommé **/LogData** qui contient les données de journal générées par votre serveur. Azure Data Factory (ADF) ingère les données dans ce dossier. Des utilisateurs spécifiques de l’équipe d’ingénierie des services chargent les journaux et gèrent les autres utilisateurs de ce dossier, et divers clusters Databricks analysent les journaux à partir de ce dossier. 

Pour activer ces activités, vous pouvez créer un groupe `LogsWriter` et un groupe `LogsReader`. Ensuite, vous pouvez affecter des autorisations comme suit :

- Ajoutez le groupe `LogsWriter` à l’ACL du répertoire **/LogData** avec les autorisations `rwx`.
- Ajoutez le groupe `LogsReader` à l’ACL du répertoire **/LogData** avec les autorisations `r-x`.
- Ajoutez l’objet de principal de service ou l’Identité du service administré (MSI) pour ADF au groupe `LogsWriters`.
- Ajoutez les utilisateurs de l’équipe d’ingénierie des services au groupe `LogsWriter`.
- Ajoutez l’objet de principal de service ou la MSI pour Databricks au groupe `LogsReader`.

Si un utilisateur de l’équipe d’ingénierie des services quitte l’entreprise, vous pouvez simplement le supprimer du groupe `LogsWriter`. Si vous n’avez pas ajouté cet utilisateur à un groupe, mais que vous avez ajouté une entrée ACL dédiée pour cet utilisateur, vous devez supprimer cette entrée ACL du répertoire **/LogData** . Vous devez également supprimer l’entrée de tous les sous-répertoires et fichiers de l’ensemble de la hiérarchie de répertoires du répertoire **/LogData** . 

Pour créer un groupe et ajouter des membres, consultez [Créer un groupe de base et ajouter des membres avec Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).