---
title: Déployer l’agent Azure File Sync
description: Déploiement de l’agent Azure File Sync Bloc de texte commun, partagé entre plusieurs documents de migration.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 66388f14949b4f398de18c9162ca453e7fb3cbe1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143611"
---
Dans le cadre de cette section, vous allez installer l’agent Azure File Sync sur votre instance Windows Server.

Le [Guide de déploiement](../articles/storage/files/storage-sync-files-deployment-guide.md) montre que vous devez désactiver la **configuration de sécurité renforcée d’Internet Explorer**. Cette mesure de sécurité n’est pas applicable avec Azure File Sync. La désactivation de cette option vous permet de vous authentifier auprès d’Azure sans aucun problème.

Ouvrez PowerShell et installez les modules PowerShell nécessaires à l’aide des commandes suivantes. Veillez à installer le module complet et le fournisseur NuGet quand vous y êtes invité :

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Si vous rencontrez des problèmes pour accéder à Internet à partir de votre serveur, vous devez les résoudre dès à présent. Azure File Sync peut utiliser n’importe quelle connexion réseau à Internet disponible. L’exigence d’un serveur proxy pour accéder à Internet est également prise en charge. Vous pouvez configurer un proxy au niveau des machines dès maintenant ou spécifier un proxy que la fonctionnalité Azure File Sync sera la seule à utiliser lors de l’installation de l’agent.

Si la configuration d’un proxy implique l’ouverture de vos pare-feux pour ce serveur, cette approche est peut-être acceptable pour vous. À la fin de l’installation du serveur, une fois l’inscription du serveur effectuée, un rapport de connectivité réseau indique les URL de point de terminaison exactes dans Azure avec lesquelles Azure File Sync doit communiquer pour la région que vous avez sélectionnée. Le rapport indique également la raison pour laquelle la communication est nécessaire. Vous pouvez utiliser le rapport pour verrouiller les pare-feux autour de ce serveur sur des URL spécifiques.

Vous pouvez également adopter une approche plus prudente dans laquelle vous n’ouvrez pas les pare-feu en grand, mais limitez le serveur à la communication avec des espaces de noms DNS de niveau supérieur. Pour plus d’informations, consultez [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/files/storage-sync-files-firewall-and-proxy.md). Appliquez vos bonnes pratiques relatives aux réseaux.

À la fin de l’Assistant *Installation* du serveur, un Assistant *Inscription* du serveur s’affiche. Inscrivez le serveur auprès de la ressource Azure de votre service de synchronisation du stockage déployée précédemment.

Le guide de déploiement décrit ces étapes plus en détail et traite notamment des modules PowerShell que vous devez installer en premier : [Installation de l’agent Azure File Sync](../articles/storage/files/storage-sync-files-deployment-guide.md).

Utilisez l’agent le plus récent. Vous pouvez aussi le télécharger à partir du Centre de téléchargement Microsoft : [Agent Azure File Sync](https://aka.ms/AFS/agent "Téléchargement de l’agent Azure File Sync").

Une fois l’installation et l’inscription du serveur terminées, vous pouvez vérifier si vous avez correctement accompli cette étape. Accédez à la ressource de service de synchronisation du stockage dans le portail Azure, puis à **Serveurs inscrits** dans le menu de gauche. Votre serveur y est répertorié.
