---
title: 'Didacticiel : héberger votre domaine et votre sous-domaine dans Azure DNS'
description: Ce didacticiel vous montre comment configurer Azure DNS pour héberger vos zones DNS.
services: dns
author: vhorne
manager: jeconnoc
ms.service: dns
ms.topic: tutorial
ms.date: 6/13/2018
ms.author: victorh
ms.openlocfilehash: 44f5bf9a28d56e85bae1d50136c50868ec96eb4e
ms.sourcegitcommit: 30221e77dd199ffe0f2e86f6e762df5a32cdbe5f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/23/2018
ms.locfileid: "39205439"
---
# <a name="tutorial-host-your-domain-in-azure-dns"></a>Didacticiel : héberger votre domaine dans Azure DNS

Vous pouvez utiliser Azure DNS pour héberger vos domaines DNS et gérer vos enregistrements DNS. En hébergeant vos domaines dans Azure, vous pouvez gérer vos enregistrements DNS avec les mêmes informations d’identification, les mêmes API, les mêmes outils et la même facturation que vos autres services Azure. 

Par exemple, supposons que vous achetez le domaine contoso.net à partir d’un registre de noms de domaine et que vous créez une zone avec le nom contoso.net dans Azure DNS. En tant que propriétaire du domaine, votre registre vous permet de configurer les enregistrements de serveur de noms (NS) pour votre domaine. Le registre stocke ces enregistrements NS dans la zone parent .net. Les utilisateurs internet du monde entier sont ensuite redirigés vers votre domaine dans la zone Azure DNS lorsqu’ils tentent de résoudre des enregistrements DNS dans contoso.net.


Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]
> * Création d’une zone DNS
> * Récupérer une liste des serveurs de noms
> * Déléguer le domaine
> * Vérifier que la délégation fonctionne


Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

## <a name="create-a-dns-zone"></a>Création d’une zone DNS

1. Connectez-vous au portail Azure.
1. Dans le coin supérieur gauche, sélectionnez **Créer une ressource** > **Mise en réseau** > **Zone DNS** pour ouvrir la page **Créer une zone DNS**.

   ![Zone DNS](./media/dns-delegate-domain-azure-dns/openzone650.png)

1. Sur la page **Créer une zone DNS**, entrez les valeurs suivantes, puis cliquez sur **Créer** :

   | **Paramètre** | **Valeur** | **Détails** |
   |---|---|---|
   |**Name**|[votre nom de domaine] |Le nom de domaine que vous avez acheté. Ce didacticiel utilise contoso.net comme exemple.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la zone.|
   |**Groupe de ressources**|**Créer :** contosoRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. |
   |**Lieu**|Est des États-Unis||

> [!NOTE]
> L’emplacement du groupe de ressources n’a aucun impact sur la zone DNS. L’emplacement de la zone DNS est toujours « global » et n’est pas affiché.

## <a name="retrieve-name-servers"></a>Récupérer les serveurs de noms

Avant de pouvoir déléguer votre zone DNS à Azure DNS, vous devez d’abord connaître les serveurs de noms correspondant à votre zone. Azure DNS alloue des serveurs de noms à partir d’un pool chaque fois qu’une zone est créée.

1. Une fois la zone DNS créée, accédez au panneau **Favoris** du portail Azure et sélectionnez **Toutes les ressources**. Sur la page **Toutes les ressources**, sélectionnez votre zone DNS. Si l’abonnement que vous avez sélectionné comporte déjà plusieurs ressources, vous pouvez saisir votre nom de domaine dans la case **Filtrer par nom** pour accéder facilement à la passerelle d’application. 

1. Récupérez les serveurs de noms à partir de la page de la zone DNS. Dans cet exemple, la zone contoso.net a été attribuée aux serveurs de noms*ns1-01.azure-dns.com*, *ns2-01.azure-dns.net*, *ns3-01.azure-dns.org*, et *ns4-01.azure-dns.info* :

   ![Liste des serveurs de noms](./media/dns-delegate-domain-azure-dns/viewzonens500.png)

Azure DNS crée automatiquement des enregistrements NS faisant autorité dans votre zone pour les serveurs de noms attribués.


## <a name="delegate-the-domain"></a>Déléguer le domaine

Maintenant que la zone DNS est créée et que vous disposez des serveurs de noms, vous devez mettre à jour le domaine parent avec les serveurs de noms Azure DNS. Chaque bureau d’enregistrement a ses propres outils de gestion DNS pour modifier les enregistrements de serveur de noms pour un domaine. Dans la page de gestion des DNS du registre, modifiez les enregistrements NS et remplacez-les par ceux créés par les serveurs de noms Azure DNS.

Lors de la délégation d’un domaine à Azure DNS, vous devez utiliser les serveurs de noms fournis par Azure DNS. Nous vous recommandons d’utiliser les quatre serveurs de noms, quel que soit le nom de votre domaine. La délégation de domaine ne requiert pas que le serveur de noms utilise le même domaine de premier niveau que votre domaine.

Les délégations utilisant les serveurs de noms dans votre propre zone (parfois appelés *serveurs de noms de redirection vers un microsite*) ne sont actuellement pas prises en charge dans Azure DNS.

## <a name="verify-that-the-delegation-is-working"></a>Vérifier que la délégation fonctionne

Une fois la délégation effectuée, vous pouvez vérifier qu’elle fonctionne à l’aide d’un outil tel que *nslookup* pour interroger l’enregistrement Start of Authority (SOA) de votre zone. L’enregistrement SOA est créé automatiquement en même temps que la zone. Vous devrez peut-être patienter 10 minutes ou plus après la délégation effectuée avant de pouvoir vérifier correctement qu’elle fonctionne. La propagation des modifications dans le système DNS peut prendre du temps.

Il est inutile de spécifier les serveurs de noms Azure DNS. Si la délégation est correctement configurée, le processus de résolution DNS normal détecte automatiquement les serveurs de noms.

À partir d’une invite de commandes, tapez une commande nslookup semblable à la suivante :

```
nslookup -type=SOA contoso.net
```

Voici un exemple de réponse à partir de la commande précédente :

```
Server: ns1-04.azure-dns.com
Address: 208.76.47.4

contoso.net
primary name server = ns1-04.azure-dns.com
responsible mail addr = msnhst.microsoft.com
serial = 1
refresh = 900 (15 mins)
retry = 300 (5 mins)
expire = 604800 (7 days)
default TTL = 300 (5 mins)
```

## <a name="clean-up-resources"></a>Supprimer les ressources

Vous pouvez conserver le groupe de ressources **contosoRG** si vous avez l’intention d’effectuer le didacticiel suivant. Sinon, supprimez le groupe de ressources **contosoRG** pour supprimer les ressources créées dans ce didacticiel. Pour cela, cliquez sur le groupe de ressources **contosoRG**, puis sur **Supprimer le groupe de ressources**. 

## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez créé une zone DNS pour votre domaine et l’avez délégué à Azure DNS. Pour en savoir plus sur Azure DNS et les applications web, passez au tutoriel sur les applications web.

> [!div class="nextstepaction"]
> [Créer des enregistrements DNS pour une application web dans un domaine personnalisé](./dns-web-sites-custom-domain.md)
