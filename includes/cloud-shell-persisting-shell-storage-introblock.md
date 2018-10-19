---
author: jluk
ms.service: cloud-shell
ms.topic: persist-storage
ms.date: 9/7/2018
ms.author: juluk
ms.openlocfilehash: 6055b70c7df2704a334b7f14c9365863ddafbd5a
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/07/2018
ms.locfileid: "44164540"
---
# <a name="persist-files-in-azure-cloud-shell"></a>Conserver des fichiers dans Azure Cloud Shell
Cloud Shell utilise le stockage de fichiers Azure pour conserver les fichiers entre les sessions. Lors du premier démarrage, Cloud Shell vous invite à associer un partage de fichiers nouveau ou existant afin de conserver les fichiers entre les sessions.

> [!NOTE]
> Bash et PowerShell utilisent le même partage de fichiers. Un seul partage de fichiers peut être associé à un montage automatique dans Cloud Shell.

## <a name="create-new-storage"></a>Créer un stockage

Lorsque vous utilisez des paramètres de base et sélectionnez uniquement un abonnement, Cloud Shell crée trois ressources pour vous dans la région prise en charge la plus proche de vous :
* Groupe de ressources : `cloud-shell-storage-<region>`
* Compte de stockage : `cs<uniqueGuid>`
* Partage de fichiers : `cs-<user>-<domain>-com-<uniqueGuid>`

![Paramètre d’abonnement](../articles/cloud-shell/media/persisting-shell-storage/basic-storage.png)

Le partage de fichiers est monté comme un `clouddrive` dans votre répertoire `$Home`. Cette opération n’a lieu qu’une seule fois, car le partage de fichiers est automatiquement monté dans les sessions suivantes. 

> [!NOTE]
> Pour la sécurité, chaque utilisateur doit approvisionner son propre compte de stockage.  Pour le contrôle d’accès en fonction du rôle (RBAC), les utilisateurs doivent disposer d’un accès contributeur ou supérieur au niveau du compte de stockage.

Le partage de fichiers contient également une image de 5 Go. Automatiquement créée pour vous, celle-ci conserve les données dans votre répertoire `$Home`. Cela vaut pour Bash et PowerShell.

## <a name="use-existing-resources"></a>Utiliser les ressources existantes

L’option Avancé vous permet d’associer des ressources existantes. Lorsque l’invite de configuration du stockage s’affiche, sélectionnez **Afficher les paramètres avancés** pour visualiser des options supplémentaires. Les options de stockage renseignées filtrent les comptes de stockage localement redondant (LRS), de stockage géoredondant (GRS) et de stockage redondant dans une zone (ZRS). Accédez [ici pour en savoir plus](https://docs.microsoft.com/azure/storage/common/storage-redundancy#choosing-a-replication-option) sur les options de réplication pour les comptes de Stockage Azure.

![Paramètre Groupe de ressources](../articles/cloud-shell/media/persisting-shell-storage/advanced-storage.png)

Lorsque vous sélectionnez une région Cloud Shell, vous devez choisir de monter un compte de stockage de sauvegarde également dans cette région.

### <a name="supported-storage-regions"></a>Régions de stockage prises en charge
Les comptes de stockage Azure associées doivent résider dans la même région que la machine Cloud Shell sur laquelle le montage est effectué. Pour déterminer votre région actuelle, vous pouvez exécuter `env` dans Bash et localiser la variable `ACC_LOCATION`. Les partages de fichiers reçoivent une image de 5 Go créée pour conserver votre répertoire `$Home`.

Les machines Cloud Shell existent dans les régions suivantes :
|Domaine|Région|
|---|---|
|Amérique|USA Est, USA Centre Sud, USA Ouest|
|Europe|Europe Nord, Europe Ouest|
|Asie-Pacifique|Inde Centre, Asie Sud-Est|

## <a name="restrict-resource-creation-with-an-azure-resource-policy"></a>Restreindre la création de ressources avec une stratégie de ressource Azure
Les comptes de stockage que vous créez dans Cloud Shell sont identifiés à l’aide de la balise `ms-resource-usage:azure-cloud-shell`. Si vous souhaitez interdire aux utilisateurs de créer des comptes de stockage par le biais de Cloud Shell, créez une [stratégie de ressource Azure pour les balises](../articles/azure-policy/json-samples.md) déclenchée par cette balise spécifique.
