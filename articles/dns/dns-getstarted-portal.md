---
title: 'Démarrage rapide : Créer un enregistrement et une zone DNS à l’aide du portail Azure'
description: Utilisez ce guide de démarrage rapide pour découvrir comment créer un enregistrement et une zone DNS dans Azure DNS. Il s’agit d’un guide pas à pas pour la création et la gestion de votre première zone et de votre premier enregistrement DNS à l’aide du portail Azure.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: quickstart
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 0acb5bf18c078d8b7eb6a5c14a61fcef622f9f2d
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/06/2018
ms.locfileid: "48831125"
---
# <a name="quickstart-configure-azure-dns-for-name-resolution-using-the-azure-portal"></a>Démarrage rapide : Configurer Azure DNS pour la résolution de nom à l’aide du portail Azure

 Vous pouvez configurer Azure DNS pour résoudre les noms d’hôtes dans votre domaine public. Par exemple, si vous avez acheté le nom de domaine contoso.com à partir d’un bureau d’enregistrement de nom de domaine, vous pouvez configurer Azure DNS pour héberger le domaine contoso.com et résoudre www.contoso.com sur l’adresse IP de votre serveur web ou application web.

Dans ce démarrage rapide, vous allez créer un domaine de test, puis un enregistrement d’adresse nommé « www » pour une résolution sur l’adresse IP 10.10.10.10.

Il est important de savoir que tous les noms et toutes les adresses IP utilisés dans ce démarrage rapide sont des exemples et ne sont pas destinés à représenter un scénario réel. Toutefois, le cas échéant, des scénarios réels sont également décrits.

<!---
You can also perform these steps using [Azure PowerShell](dns-getstarted-powershell.md) or the cross-platform [Azure CLI](dns-getstarted-cli.md).
--->

Une zone DNS permet d’héberger les entrées DNS d’un domaine particulier. Pour commencer à héberger votre domaine dans le DNS Azure, vous devez créer une zone DNS pour ce nom de domaine. Chaque entrée DNS (ou enregistrement) de votre domaine est ensuite créée à l’intérieur de cette zone DNS. Les étapes suivantes montrent comment effectuer cette opération.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
2. En haut à gauche, cliquez sur **+ Créer une ressource**, sur **Mise en réseau**, puis sur **Zone DNS** pour ouvrir la page **Créer une zone DNS**.

    ![Zone DNS](./media/dns-getstarted-portal/openzone650.png)

4. Sur la page **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :


   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|contoso.xyz|Dans cet exemple, le nom de la zone DNS peut être n’importe quelle valeur, tant qu’elle n’est pas déjà configurée sur les serveurs Azure DNS. Une valeur réelle est un domaine que vous avez acheté auprès d’un bureau d’enregistrement de nom de domaine.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez un abonnement pour y créer la zone DNS.|
   |**Groupe de ressources**|**Créer :** dns-test|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. |
   |**Lieu**|USA Est||

La création de la zone peut prendre plusieurs minutes.

## <a name="create-a-dns-record"></a>Créer un enregistrement DNS

Créez maintenant un nouvel enregistrement d’adresse (enregistrement « A »). Les enregistrements « A » sont utilisés pour résoudre un nom d’hôte sur une adresse IPv4.

1. Allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur la zone DNS **contoso.xyz** sur la page Toutes les ressources. Si l’abonnement sélectionné comporte déjà plusieurs ressources, vous pouvez saisir **contoso.xyz** dans la case **Filtrer par nom…** pour accéder facilement à la zone DNS.

1. En haut de la page **Zone DNS**, sélectionnez **+ Jeu d’enregistrements** pour ouvrir la page **Ajouter un jeu d’enregistrements**.

1. Sur la page **Ajouter un jeu d’enregistrements**, saisissez les valeurs suivantes, puis cliquez sur **OK**. Dans cet exemple, vous créez un enregistrement « A ».

   |**Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|www|Nom de l’enregistrement. Il s’agit du nom à utiliser pour l’hôte que vous souhaitez résoudre sur une adresse IP.|
   |**Type**|A| Type d’enregistrement DNS à créer. Les enregistrements « A » sont les plus courants, mais il existe des autres types d’enregistrements pour les serveurs de messagerie (MX), les adresses IPv6 (AAAA), etc. |
   |**TTL**|1|Durée de vie de la requête DNS. Spécifie la durée pendant laquelle les clients et serveurs DNS peuvent mettre en cache une réponse.|
   |**Unité de durée de vie**|hours|Mesure de temps pour la durée de vie.|
   |**Adresse IP**|10.10.10.10| Cette valeur est l’adresse IP correspondant à la résolution de l’enregistrement « A ». Il s’agit simplement une valeur de test pour ce démarrage rapide. Pour obtenir un exemple réel, vous devez entrer l’adresse IP publique de votre serveur web.|


Étant donné, que dans ce guide de démarrage rapide, vous n’achetez pas vraiment de nom de domaine réel, il n’est pas nécessaire de configurer Azure DNS comme serveur de noms auprès de votre bureau d’enregistrement de nom de domaine. Mais dans un scénario réel, vous souhaiterez que tous les internautes puissent résoudre votre nom d’hôte pour se connecter à votre application ou serveur web. Pour plus d’informations sur ce scénario réel, consultez [Délégation de domaine à Azure DNS](dns-delegate-domain-azure-dns.md).


## <a name="test-the-name-resolution"></a>Tester la résolution de nom

Maintenant que vous disposez d’une zone de test, avec un enregistrement « A » de test, vous pouvez tester la résolution de nom avec un outil appelé *nslookup*. 

1. Vous devez tout d’abord noter les serveurs de noms Azure DNS à utiliser avec nslookup. 

   Les serveurs de noms de votre zone sont répertoriés sur la page **Vue d’ensemble** de la zone DNS. Copiez le nom d’un des serveurs de noms :

   ![zone](./media/dns-getstarted-portal/viewzonens500.png)

2. Maintenant, ouvrez une invite de commandes et exécutez la commande suivante :

   ```
   nslookup <host name> <name server>
   
   For example:

   nslookup www.contoso.xyz ns1-08.azure-dns.com
   ```

Vous devez obtenir des résultats semblables à ceux-ci :

![nslookup](media/dns-getstarted-portal/nslookup.PNG)

Cela vérifie que la résolution de nom fonctionne correctement. www.contoso.xyz se résout en 10.10.10.10, tout comme vous l’avez configuré !

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources **dns-test** pour supprimer les ressources créées dans ce guide de démarrage rapide. Pour cela, cliquez sur le groupe de ressources **dns-test**, puis sur **Supprimer le groupe de ressources**.


## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)