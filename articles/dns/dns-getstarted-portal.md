---
title: 'Démarrage rapide : Créer un enregistrement et une zone DNS à l’aide du portail Azure'
description: Utilisez ce guide de démarrage rapide pas à pas pour apprendre à créer un enregistrement et une zone Azure DNS à l’aide du portail Azure.
services: dns
author: vhorne
ms.service: dns
ms.topic: quickstart
ms.date: 12/4/2018
ms.author: victorh
ms.openlocfilehash: 9929662f1fe4612e51c82248f64e3191f7fdb223
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52955199"
---
# <a name="quickstart-configure-azure-dns-for-name-resolution-by-using-the-portal"></a>Démarrage rapide : Configurer Azure DNS pour la résolution de noms à l’aide du portail

Vous pouvez configurer Azure DNS pour résoudre les noms d’hôtes dans votre domaine public. Par exemple, si vous avez acheté le nom de domaine *contoso.com* auprès d’un bureau d’enregistrement de noms de domaine, vous pouvez configurer Azure DNS pour héberger le domaine *contoso.com* et résoudre *www.contoso.com* en l’adresse IP de votre serveur web ou application web.

Dans ce guide de démarrage rapide, vous allez créer un domaine test, puis un enregistrement d’adresse pour résoudre *www* en l’adresse *IP 10.10.10.10*.

>[!IMPORTANT]
>Tous les noms et adresses IP cités dans ce guide de démarrage rapide sont des exemples qui ne représentent pas des scénarios réels. Le guide de démarrage rapide aborde également les implications du monde réel, le cas échéant.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Pour toutes les étapes du portail, connectez-vous au [portail Azure](https://portal.azure.com).

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

Une zone DNS contient les entrées DNS d’un domaine. Pour commencer à héberger votre domaine dans Azure DNS, vous devez créer une zone DNS pour ce nom de domaine. 

**Pour créer la zone DNS**

1. En haut à gauche, sélectionnez **Créer une ressource**, **Mise en réseau**, puis **Zone DNS**.
   
1. Dans la page **Créer une zone DNS**, tapez ou sélectionnez les valeurs suivantes :
   
   - **Nom** : Pour l’exemple de ce guide de démarrage rapide, tapez *contoso.xyz*. Vous pouvez affecter comme nom de zone DNS toute valeur qui n’est pas encore configurée sur les serveurs Azure DNS. Une valeur réelle est un domaine que vous avez acheté auprès d’un bureau d’enregistrement de nom de domaine.
   - **Groupe de ressources** : Sélectionnez **Créer**, entrez *dns-test*, puis sélectionnez **OK**. Le nom du groupe de ressources doit être unique au sein de l’abonnement Azure. 
   
1. Sélectionnez **Créer**.

   ![Zone DNS](./media/dns-getstarted-portal/openzone650.png)
   
La création de la zone peut prendre plusieurs minutes.

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Vous créez des entrées ou enregistrements DNS pour votre domaine à l’intérieur de la zone DNS. Créer un nouvel enregistrement d’adresse ou enregistrement « A » pour résoudre un nom d’hôte en adresse IPv4.

**Pour créer un enregistrement « A »**

1. Dans le portail Azure, sous **Toutes les ressources**, ouvrez la zone DNS **contoso.xyz** dans le groupe de ressources **dns-test**. Vous pouvez entrer *contoso.xyz* dans la zone **Filtrer par nom** pour la trouver plus facilement.

1. En haut de la page **Zone DNS**, sélectionnez **+ Jeu d’enregistrements**.

1. Dans la page **Ajouter un jeu d’enregistrements**, tapez ou sélectionnez les valeurs suivantes :

   - **Nom** : Tapez *www*. Le nom d’enregistrement est le nom d’hôte que vous souhaitez résoudre en l’adresse IP spécifiée.
   - **Type** : Sélectionnez **A**. Les enregistrements « A » sont les plus courants, mais il existe d’autres types d’enregistrements pour les serveurs de messagerie (« MX »), les adresses IPv6 (« AAAA »), et ainsi de suite. 
   - **DURÉE DE VIE** : Tapez *1*. La *durée de vie* (TTL) de la requête DNS spécifie la durée pendant laquelle les clients et serveurs DNS peuvent mettre en cache une réponse.
   - **Unité de durée de vie** : Sélectionnez **Heures**. Il s’agit de l’unité de temps pour la valeur **TTL**. 
   - **Adresse IP** : Pour l’exemple de ce guide de démarrage rapide, tapez *10.10.10.10*. Cette valeur est l’adresse IP en laquelle est résolu le nom d’enregistrement. Dans votre scénario réel, vous devez entrer l’adresse IP publique de votre serveur web.

Dans la mesure où ce guide de démarrage rapide n’utilise pas un domaine réel, il n’est pas nécessaire de configurer les serveurs de noms Azure DNS auprès d’un bureau d’enregistrement de noms de domaine. Avec un domaine réel, vous souhaiterez que tous les internautes puissent résoudre le nom d’hôte pour se connecter à votre serveur web ou à votre application. Vous consulterez votre bureau d’enregistrement de noms de domaine afin de remplacer les enregistrements de serveur par les serveurs de noms Azure DNS. Pour plus d’informations, consultez [Tutoriel : Héberger votre domaine dans Azure DNS](dns-delegate-domain-azure-dns.md#delegate-the-domain).

## <a name="test-the-name-resolution"></a>Tester la résolution de nom

Maintenant que vous disposez d’une zone DNS test avec un enregistrement « A » test, vous pouvez tester la résolution de noms avec un outil appelé *nslookup*. 

**Pour tester la résolution de noms DNS**

1. Dans le portail Azure, sous **Toutes les ressources**, ouvrez la zone DNS **contoso.xyz** dans le groupe de ressources **dns-test**. Vous pouvez entrer *contoso.xyz* dans la zone **Filtrer par nom** pour la trouver plus facilement.

1. Copiez l’un des noms de serveurs de noms à partir de la liste de serveurs de noms dans la page **Vue d’ensemble**. 
   
   ![zone](./media/dns-getstarted-portal/viewzonens500.png)
   
   >[!NOTE]
   >Dans un scénario réel, vous copiez les quatre noms de serveurs de noms, y compris les points finaux, et vous les utilisez pour les nouveaux noms de serveurs de noms Azure DNS dans votre bureau d’enregistrement de noms de domaine. Pour plus d’informations, voir [Délégation de domaine à Azure DNS](dns-delegate-domain-azure-dns.md).
   
1. Ouvrez une invite de commandes et exécutez la commande suivante :

   ```
   nslookup <host name> <name server name>
   ```
   
   Par exemple : 
   
   ```
   nslookup www.contoso.xyz ns1-08.azure-dns.com.
   ```
   
   Un écran similaire à celui-ci doit s’afficher :
   
   ![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Le nom d’hôte **www.contoso.xyz** est résolu en **10.10.10.10**, tout comme vous l’avez configuré. Ce résultat confirme que la résolution de noms fonctionne correctement. 

## <a name="clean-up-resources"></a>Supprimer des ressources

Quand vous n’avez plus besoin des ressources créées dans ce guide de démarrage rapide, supprimez-les en effaçant le groupe de ressources **dns-test**. Ouvrez le groupe de ressources **dns-test**, puis sélectionnez **Supprimer le groupe de ressources**.

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)