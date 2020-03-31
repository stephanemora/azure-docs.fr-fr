---
title: Déploiement de l’agent Azure File Sync
description: Déploiement de l’agent Azure File Sync Bloc de texte commun, partagé entre plusieurs documents sur la migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 694becc49667204ef2071a140bb6330285089039
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209276"
---
Dans le cadre de cette section, vous allez installer l’agent Azure File Sync sur votre serveur Windows Server.
Le [guide de déploiement](../articles/storage/files/storage-sync-files-deployment-guide.md) explique que vous devez désactiver la **sécurité renforcée d’Internet Explorer**. La sécurité renforcée d’Internet Explorer est une mesure de sécurité qui n’est pas applicable avec Azure File Sync. Sa désactivation vous permet de vous authentifier auprès d’Azure sans aucun problème.

Ouvrez PowerShell et installez les modules PowerShell nécessaires à l’aide des commandes suivantes. Veillez à installer le module complet et le fournisseur NuGet quand vous y êtes invité :

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Si vous rencontrez des problèmes pour accéder à Internet à partir de votre serveur, vous devez les résoudre dès à présent. Azure File Sync peut utiliser n’importe quelle connexion réseau à Internet disponible.
L’exigence d’un serveur proxy pour accéder à Internet est également prise en charge. Vous pouvez configurer un proxy au niveau des machines maintenant ou spécifier un proxy qui sera utilisé uniquement par la synchronisation des fichiers pendant l’installation de l’agent.

Si ceci implique l’ouverture de vos pare-feux pour ce serveur, il peut s’agir pour vous d’une approche acceptable. À la fin de l’installation du serveur, une fois l’inscription du serveur effectuée, un rapport de connectivité réseau s’affiche. Ce rapport indique les URL de point de terminaison exactes dans Azure, avec lesquelles la synchronisation de fichiers doit communiquer, pour la région que vous avez sélectionnée. Le rapport indique également la raison pour laquelle la communication est nécessaire. Vous pouvez utiliser le rapport pour verrouiller les pare-feux autour de ce serveur pour des URL spécifiques.

Vous pouvez également suivre une approche plus classique qui consiste à ne pas ouvrir complètement les pare-feux et, plutôt, à forcer le serveur à communiquer avec des espaces de noms DNS de niveau supérieur. L’article [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md) fournit une documentation et des informations supplémentaires. Appliquez vos bonnes pratiques relatives aux réseaux.

À la fin de l’Assistant d’*installation* du serveur, un Assistant d’*inscription* du serveur apparaît.
Inscrivez le serveur auprès de votre ressource de service de synchronisation de stockage Azure déployée précédemment.

Le guide de déploiement décrit ces étapes plus en détail et traite notamment des modules PowerShell ci-dessus, que vous devez commencer par installer : [Installer l’agent Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md).

Vous devez utiliser l’agent le plus récent. Celui-ci peut être téléchargé à partir du Centre de téléchargement Microsoft : [Azure File Sync - agent](https://aka.ms/AFS/agent "Téléchargement de l’agent Azure File Sync").

Une fois l’installation et l’inscription du serveur correctement effectuées, vous pouvez vérifier si vous avez correctement réalisé cette étape : accédez à la ressource de service de synchronisation de stockage dans le portail Azure, puis sélectionnez « Serveurs inscrits » dans le menu de gauche. Vous serveur doit déjà figurer dans la liste.
