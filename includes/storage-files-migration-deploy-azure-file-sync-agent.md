---
title: Fichier include
description: Fichier include
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: 544bbaa358f0ca158c757524406ffd046ad43af7
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/08/2021
ms.locfileid: "109645224"
---
Dans le cadre de cette section, vous allez installer l’agent Azure File Sync sur votre instance Windows Server.

Le [Guide de déploiement](../articles/storage/file-sync/file-sync-deployment-guide.md) explique que vous devez désactiver la **configuration de sécurité renforcée d’Internet Explorer**. Cette mesure de sécurité n’est pas applicable avec Azure File Sync. La désactivation de cette option vous permet de vous authentifier auprès d’Azure sans aucun problème.

Ouvrez PowerShell. Installez les modules PowerShell nécessaires à l’aide des commandes suivantes. Veillez à installer le module complet et le fournisseur NuGet quand vous y êtes invité.

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

Si vous rencontrez des problèmes pour accéder à Internet à partir de votre serveur, vous devez les résoudre dès à présent. Azure File Sync peut utiliser n’importe quelle connexion réseau à Internet disponible. L’exigence d’un serveur proxy pour accéder à Internet est également prise en charge. Vous pouvez configurer un proxy au niveau des machines dès maintenant ou spécifier un proxy que la fonctionnalité Azure File Sync sera la seule à utiliser lors de l’installation de l’agent.

Si la configuration d’un proxy implique l’ouverture de vos pare-feu pour le serveur, cette approche est peut-être acceptable pour vous. À la fin de l’installation du serveur, une fois l’inscription du serveur effectuée, un rapport de connectivité réseau indique les URL de point de terminaison exactes dans Azure avec lesquelles Azure File Sync doit communiquer pour la région que vous avez sélectionnée. Le rapport indique également la raison pour laquelle la communication est nécessaire. Vous pouvez utiliser le rapport pour verrouiller les pare-feu autour du serveur sur des URL spécifiques.

Vous pouvez également adopter une approche plus conservatrice dans laquelle vous n’ouvrez pas les pare-feu en grand. Vous pouvez à la place limiter le serveur pour qu’il communique avec des espaces de noms DNS de niveau supérieur. Pour plus d’informations, consultez [Paramètres de proxy et de pare-feu d’Azure File Sync](../articles/storage/file-sync/file-sync-firewall-and-proxy.md). Appliquez vos bonnes pratiques relatives aux réseaux.

À la fin de l’Assistant Installation du serveur, un Assistant Inscription du serveur s’affiche. Inscrivez le serveur auprès de la ressource Azure de votre service de synchronisation du stockage déployée précédemment.

Le guide de déploiement décrit ces étapes plus en détail et traite notamment des modules PowerShell que vous devez installer en premier : [Installation de l’agent Azure File Sync](../articles/storage/file-sync/file-sync-deployment-guide.md).

Utilisez l’agent le plus récent. Vous pouvez aussi le télécharger à partir du Centre de téléchargement Microsoft : [Agent Azure File Sync](https://aka.ms/AFS/agent "Téléchargement de l’agent Azure File Sync").

Une fois l’installation et l’inscription du serveur terminées, vous pouvez confirmer que vous avez correctement accompli cette étape. Accédez à la ressource du service de synchronisation de stockage dans le portail Azure. Dans le menu de gauche, accédez à **Serveurs inscrits**. Votre serveur y est répertorié.
