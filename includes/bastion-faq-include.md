---
title: Fichier Include
description: Fichier Include
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: include
ms.date: 10/15/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d31e30991056cc891e63347a2c88e7fc4caeab28
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/05/2019
ms.locfileid: "74875453"
---
### <a name="regions"></a>Quelles régions sont disponibles ?

[!INCLUDE [region](bastion-regions-include.md)]

### <a name="publicip"></a>Ai-je besoin d’une adresse IP publique sur ma machine virtuelle ?

Vous n’avez PAS besoin d’une adresse IP publique sur la machine virtuelle Azure à laquelle vous êtes connecté avec le service Azure Bastion. Le service Bastion va ouvrir la session/connexion RDP/SSH à votre machine virtuelle sur l’adresse IP privée de celle-ci, au sein de votre réseau virtuel.

### <a name="rdpssh"></a>Ai-je besoin d’un client RDP ou SSH ?

Vous n’avez pas besoin d’un client RDP ou SSH pour accéder par RDP/SSH à votre machine virtuelle Azure dans votre portail Azure. Utilisez le [portail Azure](https://portal.azure.com) pour obtenir un accès RDP/SSH à votre machine virtuelle directement dans le navigateur.

### <a name="agent"></a>Ai-je besoin d’un agent exécuté sur la machine virtuelle Azure ?

Vous n’avez pas besoin d’installer un agent ou un logiciel sur votre navigateur ou sur votre machine virtuelle Azure. Le service Bastion est sans agent et ne nécessite aucun logiciel supplémentaire pour RDP/SSH.

### <a name="browsers"></a>Quels sont les navigateurs pris en charge ?

Utilisez le navigateur Microsoft Edge ou Google Chrome sous Windows. Pour Apple Mac, utilisez le navigateur Google Chrome. Microsoft Edge Chromium est également pris en charge sur Windows et Mac, respectivement.

### <a name="roles"></a>Certains rôles sont-ils obligatoires pour accéder à une machine virtuelle ?

Pour établir une connexion, les rôles suivants sont nécessaires :

* Rôle de lecteur sur la machine virtuelle
* Rôle de lecteur sur la carte réseau avec adresse IP privée de la machine virtuelle
* Rôle de lecteur sur la ressource Azure Bastion

### <a name="pricingpage"></a>Quel est la tarification ?

Pour plus d’informations, consultez la [page relative aux prix appliqués](https://aka.ms/BastionHostPricing).

### <a name="session"></a>Pourquoi le message d’erreur « Votre session a expiré » s’affiche avant le démarrage de la session Bastion ?

Une session ne doit être lancée qu’à partir du portail Azure. Connectez-vous au portail Azure, puis redémarrez votre session. Si vous accédez à l’URL directement à partir d’une autre session de navigateur ou d’un autre onglet, cette erreur est normale. Cela permet de sécuriser votre session, en évitant qu’elle soit accessible en dehors du portail Azure.

### <a name="keyboard"></a>Quelles sont les dispositions de clavier prises en charge pendant la session à distance Bastion ?

Azure Bastion prend actuellement en charge la disposition de clavier en-US-QWERTY dans la machine virtuelle.  La prise en charge d’autres paramètres régionaux pour la disposition de clavier est en cours de développement.

### <a name="udr"></a>Le routage défini par l’utilisateur (UDR) est-il pris en charge sur un sous-réseau Azure Bastion ?

Non. Le routage UDR n’est pas pris en charge sur un sous-réseau Azure Bastion.
Pour les scénarios qui incluent Azure Bastion et Pare-feu Azure/Appliance virtuelle réseau (NVA) dans le même réseau virtuel, vous n’avez pas besoin de forcer le trafic d’un sous-réseau Azure Bastion vers le Pare-feu Azure, car la communication entre Azure Bastion et vos machines virtuelles est privée. Pour plus d’informations, consultez [Accessing VMs behind Azure Firewall with Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="filetransfer"></a>Le transfert de fichiers est-il pris en charge avec une session RDP Azure Bastion ?

Nous déployons tous les efforts nécessaires pour ajouter de nouvelles fonctionnalités. Pour le moment, le transfert de fichiers n’est pas pris en charge, mais il fait partie de notre feuille de route. N’hésitez pas à nous faire part de vos commentaires sur les nouvelles fonctionnalités dans la [page des commentaires Azure Bastion](https://feedback.azure.com/forums/217313-networking?category_id=367303).
