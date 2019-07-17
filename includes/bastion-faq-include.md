---
title: Fichier Include
description: Fichier Include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 06/17/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 29ab9b3c33aae6005510c34b207c7f87714149e5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/05/2019
ms.locfileid: "67608167"
---
### <a name="preview"></a>Comment participer à la préversion publique ?

Vous devez vous intégrer pour pouvoir participer à la préversion publique. Suivez les étapes décrites dans [cet article](../articles/bastion/bastion-create-host-portal.md) pour créer une ressource Azure Bastion. Actuellement, quand vous accédez à ce service et l’utilisez, vous devez utiliser le [portail Azure en préversion](https://aka.ms/BastionHost) à la place du portail Azure standard.

### <a name="regions"></a>Quelles sont les régions disponibles pendant la préversion ?

Vous pouvez déployer et utiliser la ressource Bastion dans l’une de ces régions en préversion par le biais du [lien vers le portail Azure en préversion](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="portal"></a>Je ne parviens pas à trouver la ressource Bastion dans le portail Azure. Que dois-je faire ?

Veillez à utiliser le [lien vers le portail Azure en préversion](https://aka.ms/BastionHost), et non le portail Azure standard.

### <a name="publicip"></a>Ai-je besoin d’une adresse IP publique sur ma machine virtuelle ?

Vous n’avez PAS besoin d’une adresse IP publique sur la machine virtuelle Azure à laquelle vous êtes connecté avec le service Azure Bastion. Le service Bastion va ouvrir la session/connexion RDP/SSH à votre machine virtuelle sur l’adresse IP privée de celle-ci, au sein de votre réseau virtuel.

### <a name="rdpssh"></a>Ai-je besoin d’un client RDP ou SSH ?

Vous n’avez pas besoin d’un client RDP ou SSH pour accéder par RDP/SSH à votre machine virtuelle Azure dans votre portail Azure. Utilisez le [lien vers le portail Azure en préversion](https://aka.ms/BastionHost) pour accéder à la préversion du portail. Cela vous permet d’obtenir un accès RDP/SSH à votre machine virtuelle directement dans le navigateur.

### <a name="agent"></a>Ai-je besoin d’un agent exécuté sur la machine virtuelle Azure ?

Vous n’avez pas besoin d’installer un agent ou un logiciel sur votre navigateur ou sur votre machine virtuelle Azure. Le service Bastion est sans agent et ne nécessite aucun logiciel supplémentaire pour RDP/SSH.

### <a name="browsers"></a>Quels sont les navigateurs pris en charge ?

Pendant la préversion publique, utilisez le navigateur Microsoft Edge ou Google Chrome sur Windows. Pour Apple Mac, utilisez le navigateur Google Chrome. Microsoft Edge Chromium est également pris en charge sur Windows et Mac, respectivement.

### <a name="roles"></a>Certains rôles sont-ils obligatoires pour accéder à une machine virtuelle ?

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="previewbill"></a>Tarifs : serai-je facturé pour ma participation à la préversion ?

Vous serez uniquement facturé partiellement pendant la préversion publique. Toutefois, aucun contrat de niveau de service n’est associé à votre déploiement. Pour plus d’informations, consultez la [page relative aux prix appliqués](https://aka.ms/BastionHostPricing).

### <a name="previewbill"></a>Pourquoi le message d’erreur « Votre session a expiré » s’affiche avant le démarrage de la session Bastion ?

Une session ne doit être lancée qu’à partir du portail Azure. Connectez-vous au portail Azure, puis redémarrez votre session. Si vous accédez à l’URL directement à partir d’une autre session de navigateur ou d’un autre onglet, cette erreur est normale. Cela permet de sécuriser votre session, en évitant qu’elle soit accessible en dehors du portail Azure.
