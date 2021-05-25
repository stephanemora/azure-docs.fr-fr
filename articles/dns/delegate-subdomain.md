---
title: Déléguer un sous-domaine - Azure DNS
description: Avec ce parcours d’apprentissage, commencez à déléguer un sous-domaine Azure DNS.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 05/03/2021
ms.author: rohink
ms.openlocfilehash: 11c9fd2e453db37a5aa985bcc53acdabf4a42aaf
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763304"
---
# <a name="delegate-an-azure-dns-subdomain"></a>Déléguer un sous-domaine Azure DNS

Vous pouvez utiliser le portail Azure pour déléguer un sous-domaine DNS. Par exemple, si vous êtes propriétaire du domaine contoso.com, vous pouvez déléguer un sous-domaine nommé *engineering* à une autre zone que vous pouvez administrer séparément de la zone contoso.com.

Si vous préférez, vous pouvez également déléguer un sous-domaine à l’aide d’[Azure PowerShell](delegate-subdomain-ps.md).

## <a name="prerequisites"></a>Prérequis

Pour déléguer un sous-domaine Azure DNS, vous devez tout d’abord déléguer votre domaine public à Azure DNS. Pour obtenir des instructions sur la façon de configurer vos serveurs de noms pour la délégation, consultez [Déléguer un domaine à Azure DNS](./dns-delegate-domain-azure-dns.md). Une fois votre domaine délégué à votre zone Azure DNS, vous pouvez déléguer votre sous-domaine.

> [!NOTE]
> Contoso.com est utilisé comme exemple tout au long de cet article. Changez votre nom de votre domaine par contoso.com.

## <a name="create-a-zone-for-your-subdomain"></a>Créer une zone pour votre sous-domaine

Tout d’abord, créez la zone pour le sous-domaine **engineering**.

1. Dans le portail Azure, sélectionnez **+ Créer une ressource**.

1. Recherchez **Zone DNS**, puis sélectionnez **Créer**.

1. Dans la page **Créer une zone DNS**, sélectionnez le groupe de ressources pour votre zone. Vous pouvez utiliser le même groupe de ressources que celui de la zone parente afin de regrouper les ressources similaires.

1.  Entrez `engineering.contoso.com` comme **nom du projet**, puis sélectionnez **Créer**.

1. Une fois le déploiement réussi, accédez à la nouvelle zone.

## <a name="note-the-name-servers"></a>Noter les serveurs de noms

Ensuite, notez les quatre serveurs de noms pour le sous-domaine engineering.

Dans la page de présentation de la zone **engineering**, notez les quatre serveurs de noms pour la zone. Vous aurez besoin de ces serveurs de noms ultérieurement.

## <a name="create-a-test-record"></a>Créer un enregistrement test

Créez un enregistrement **A** à des fins de test. Par exemple, créez un enregistrement A **www** et configurez-le avec une adresse IP **10.10.10.10**.

## <a name="create-an-ns-record"></a>Créer un enregistrement NS

Ensuite, créez un enregistrement de serveur de noms (NS) pour la zone **engineering**.

1. Accédez à la zone du domaine parent.

1. Sélectionnez **+ Jeu d’enregistrements** en haut de la page de présentation.

1. Dans la page **Ajouter un jeu d’enregistrements**, saisissez **engineering** dans la zone de texte **Nom**.

1. Pour **Type**, sélectionnez **NS**.

1. Sous **Serveur de noms**, entrez les quatre serveurs de noms que vous avez notés précédemment à partir de la zone **engineering**.

1. Sélectionnez **OK** pour sauvegarder l’enregistrement.

## <a name="test-the-delegation"></a>Tester la délégation

Utilisez nslookup pour tester la délégation.

1. Ouvrez une fenêtre PowerShell.

1. À l’invite de commandes, tapez `nslookup www.engineering.contoso.com.`.

1. Vous devez recevoir une réponse ne faisant pas autorité montrant l’adresse **10.10.10.10**.

## <a name="next-steps"></a>Étapes suivantes

Découvrir comment [Configurer des DNS inversés dans les services hébergés par Azure](dns-reverse-dns-for-azure-services.md).
