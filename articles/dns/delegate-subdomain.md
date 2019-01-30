---
title: Déléguer un sous-domaine Azure DNS
description: Découvrez comment déléguer un sous-domaine Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 1/22/2019
ms.author: victorh
ms.openlocfilehash: 87a80703c473245660a850645ca3fef21bbd80f6
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54452715"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Déléguer un sous-domaine Azure DNS

Vous pouvez utiliser le portail Azure pour déléguer un sous-domaine DNS. Par exemple, si vous êtes propriétaire du domaine contoso.com, vous pouvez déléguer un sous-domaine nommé *engineering* à une autre zone qui peut être administrée séparément de la zone contoso.com.

## <a name="prerequisites"></a>Prérequis

Pour déléguer un sous-domaine Azure DNS, vous devez tout d’abord déléguer votre domaine public à Azure DNS. Pour obtenir des instructions sur la façon de configurer vos serveurs de noms pour la délégation, consultez [Déléguer un domaine à Azure DNS](./dns-delegate-domain-azure-dns.md). Une fois votre domaine délégué à votre zone Azure DNS, vous pouvez déléguer votre sous-domaine.

Les exemples de cet article utilisent le domaine contoso.com. Vous devez remplacer votre propre domaine lors de l’application de ces procédures.

## <a name="create-a-zone-for-your-subdomain"></a>Créer une zone pour votre sous-domaine

Tout d’abord, créez la zone pour le sous-domaine **engineering**.

1. Dans le portail Azure, sélectionnez **Créer une ressource**.
2. Dans la zone de recherche, tapez **DNS** et sélectionnez **Zone DNS**.
3. Sélectionnez **Créer**.
4. Dans le volet **Créer une zone DNS**, tapez **engineering.contoso.com** dans la zone de texte **Nom**.
5. Sélectionnez le groupe de ressources pour votre zone. Vous souhaiterez peut-être utiliser le même groupe de ressources que celui de la zone parente, afin de regrouper les ressources similaires.
6. Cliquez sur **Créer**.
7. Une fois le déploiement réussi, accédez à la nouvelle zone.

## <a name="note-the-name-servers"></a>Noter les serveurs de noms

Ensuite, copiez les quatre serveurs de noms pour votre sous-domaine.

1. Dans le volet de la zone **engineering**, notez les quatre serveurs de noms pour la zone. Vous les utiliserez ultérieurement.
2. Créez un enregistrement **A** à des fins de test. Par exemple, créez un enregistrement A **www** et configurez-le avec une adresse IP **10.10.10.10**.

## <a name="create-an-ns-record"></a>Créer un enregistrement NS

Ensuite, créez un enregistrement de serveur de noms (NS) pour la zone **engineering**.

1. Accédez à la zone du domaine parent.
2. Sélectionnez **+ Jeu d’enregistrements**.
3. Dans le volet **Ajouter un jeu d’enregistrements**, tapez **engineering** dans la zone de texte **Nom**.
4. Pour **Type**, sélectionnez **NS**.
5. Sous **Serveur de noms**, entrez les quatre serveurs de noms que vous avez notés précédemment à partir de la zone **engineering**.
6. Cliquez sur **OK**.

## <a name="test-the-delegation"></a>Tester la délégation

Utilisez nslookup pour tester la délégation.

1. Ouvrez une fenêtre PowerShell.
2. À l’invite de commandes, tapez `nslookup www.engineering.<your domain name>.`.
3. Vous devez recevoir une réponse ne faisant pas autorité montrant l’adresse **10.10.10.10**.



## <a name="next-steps"></a>Étapes suivantes

Découvrir comment [Configurer des DNS inversés dans les services hébergés par Azure](dns-reverse-dns-for-azure-services.md).