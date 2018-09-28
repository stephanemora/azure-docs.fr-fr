---
title: 'Tutoriel : Créer un enregistrement d’alias Azure DNS pour référencer un enregistrement de ressource dans la zone.'
description: Ce tutoriel vous montre comment configurer un enregistrement d’alias Azure DNS pour référencer un enregistrement de ressource dans la zone.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 09c1768602fede51d7ff2b23f48278a1d0b0cd2a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990839"
---
# <a name="tutorial-create-an-alias-record-to-refer-to-a-zone-resource-record"></a>Tutoriel : Créer un enregistrement d’alias pour référencer un enregistrement de ressource de zone

Les enregistrements d’alias peuvent référencer d’autres jeux d’enregistrements du même type. Par exemple, un jeu d’enregistrements CNAME DNS peut être un alias pour un autre jeu d’enregistrements CNAME du même type. Ceci est pratique si vous voulez que certains jeux d’enregistrements se comportent comme des alias et d’autres comme des non-alias.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Créer un enregistrement d’alias pour un enregistrement de ressource dans la zone
> * Tester l’enregistrement d’alias


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="prerequisites"></a>Prérequis
Vous devez disposer d’un nom de domaine disponible, que vous pouvez héberger dans Azure DNS pour le test. Vous devez disposer d’un contrôle total de ce domaine, notamment de la possibilité de définir les enregistrements de serveur de noms pour le domaine.

Pour obtenir des instructions pour héberger votre domaine dans Azure DNS, consultez [Tutoriel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="create-an-alias-record"></a>Créer un enregistrement d’alias

Créez un enregistrement d’alias qui pointe vers un enregistrement de ressource dans la zone.

### <a name="create-the-target-resource-record"></a>Créer l’enregistrement de ressource cible
1. Cliquez sur votre zone Azure DNS pour ouvrir la zone.
2. Cliquez sur **Jeu d’enregistrements**.
3. Dans la zone de texte **Nom**, entrez **server**.
4. Pour le **Type**, sélectionnez **A**.
5. Dans la zone de texte **ADRESSE IP**, tapez **10.10.10.10**.
6. Cliquez sur **OK**.

### <a name="create-the-alias-record"></a>Créer l’enregistrement d’alias
1. Cliquez sur votre zone Azure DNS pour ouvrir la zone.
2. Cliquez sur **Jeu d’enregistrements**.
3. Dans la zone de texte **Nom**, entrez **test**.
4. Pour le **Type**, sélectionnez **A**.
5. Cliquez sur **Oui** dans la case à cocher **Alias du jeu d’enregistrements**, puis sélectionnez l’option **Jeu d’enregistrements de zone**.
6. Pour le **Jeu d’enregistrements de zone**, sélectionnez l’enregistrement **server**.
7. Cliquez sur **OK**.

## <a name="test-the-alias-record"></a>Tester l’enregistrement d’alias

1. Lancer votre outil nslookup préféré. Une option est d’accéder à [https://network-tools.com/nslook](https://network-tools.com/nslook).
2. Définissez le type de requête pour les enregistrements A et recherchez **test.\<votre nom de domaine\>**. Vous devez obtenir **10.10.10.10** comme réponse.
3. Dans le portail Azure, changez l’enregistrement A **server** en **10.11.11.11**.
4. Attendez quelques minutes, puis utilisez à nouveau nslookup pour l’enregistrement **test**.
5. Vous devez obtenir **10.11.11.11** comme réponse.

## <a name="clean-up-resources"></a>Supprimer les ressources

Quand vous n’en avez plus besoin, vous pouvez supprimer les enregistrements de ressources **server** et **test** dans votre zone.


## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez créé un enregistrement d’alias pour référencer un enregistrement de ressource dans la zone. Pour en savoir plus sur Azure DNS et les applications web, passez au tutoriel sur les applications web.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)
