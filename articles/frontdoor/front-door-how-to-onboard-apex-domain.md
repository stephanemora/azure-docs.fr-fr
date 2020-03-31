---
title: Intégrer un domaine racine ou apex à un Front Door existant – Portail Azure
description: Découvrez comment intégrer un domaine racine ou apex à un Front Door existant à l’aide du portail Azure.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: bb1042e15d4366923174996388eeb2fb99aef429
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74184610"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Intégrer un domaine racine ou apex à votre Front Door
Azure Front Door utilise des enregistrements CNAME pour valider la propriété du domaine en vue de l’intégration de domaines personnalisés. En outre, Front Door n’expose pas l’adresse IP de serveur frontal associée à votre profil Front Door. Vous ne pouvez donc pas mapper votre domaine apex à une adresse IP si l’objectif est de l’intégrer à Azure Front Door.

Le protocole DNS empêche l'attribution d'enregistrements CNAME à l'extrémité de la zone. Par exemple, si votre domaine est `contoso.com`, vous pouvez créer des enregistrements CNAME pour `somelabel.contoso.com` ; mais vous ne pouvez pas en créer pour `contoso.com` lui-même. Cette restriction pose un problème aux propriétaires d’applications qui disposent d’applications à charge équilibrée derrière Azure Front Door. Dans la mesure où l’utilisation d’un profil Front Door nécessite la création d’un enregistrement CNAME, il est impossible de pointer vers le profil Front Door à partir de l’apex de zone.

Ce problème est résolu en utilisant des enregistrements d’alias sur Azure DNS. Contrairement aux enregistrements CNAME, les enregistrements d’alias sont créés à l’apex de zone, et les propriétaires d’applications peuvent l’utiliser pour pointer leur enregistrement d’apex de zone vers un profil Front Door doté de points de terminaison publics. Les propriétaires d’applications peuvent pointer vers le même profil Front Door que celui utilisé pour tout autre domaine de leur zone DNS. Par exemple, `contoso.com` et `www.contoso.com` peuvent pointer vers le même profil Front Door. 

Le mappage de votre domaine racine ou apex à votre profil Front Door nécessite essentiellement une mise à plat de CNAME ou une recherche DNS, un mécanisme dans lequel le DNS fournisseur résout de manière récursive l’entrée CNAME jusqu’à ce qu’il obtienne une adresse IP. Cette fonctionnalité est prise en charge par Azure DNS pour les points de terminaison Front Door. 

> [!NOTE]
> D’autres fournisseurs de DNS prennent également en charge la mise à plat de CNAME ou la recherche de DNS. Azure Front Door recommande toutefois d’utiliser Azure DNS pour ses clients en vue de l’hébergement de leurs domaines.

Vous pouvez utiliser le portail Azure pour intégrer un domaine apex à votre Front Door et activer le protocole HTTPS sur celui-ci en l’associant à un certificat pour point de terminaison SSL. Les domaines apex sont également appelés des domaines racine ou nus.

Dans cet article, vous apprendrez comment :

> [!div class="checklist"]
> * Créer un enregistrement d’alias qui pointe vers votre profil Front Door
> * Ajouter le domaine racine au Front Door
> * Configurer HTTPS sur le domaine racine

> [!NOTE]
> Ce didacticiel nécessite que vous disposiez déjà d’un profil Front Door. Consultez d’autres didacticiels tels que [Démarrage rapide : Créer un Front Door](./quickstart-create-front-door.md) ou [Créer un Front Door avec redirection HTTP ou HTTPS](./front-door-how-to-redirect-https.md) pour commencer.

## <a name="create-an-alias-record-for-zone-apex"></a>Créer un enregistrement d’alias d’apex de zone

1. Ouvrez la configuration **Azure DNS** du domaine à intégrer.
2. Créez ou modifiez l’enregistrement d’apex de zone.
3. Sélectionnez le **type** d’enregistrement _A_, puis sélectionnez _Oui_ pour **Jeu d’enregistrements d’alias**. **Type d’alias** doit être défini sur _Ressource Azure_.
4. Choisissez l’abonnement Azure hébergeant votre profil Front Door, puis sélectionnez la ressource Front Door dans la liste déroulante **Ressource Azure**.
5. Cliquez sur **OK** pour envoyer les modifications.

    ![Enregistrement d’alias d’apex de zone](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. L’étape ci-dessus crée un enregistrement d’apex de zone qui pointe vers votre ressource Front Door, ainsi qu’un mappage d’enregistrement CNAME « afdverify » (exemple : `afdverify.contosonews.com`) vers `afdverify.<name>.azurefd.net` qui sera utilisé pour l’intégration du domaine à votre profil Front Door.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Intégrer le domaine personnalisé à votre Front Door

1. Dans l’onglet du concepteur de Front Door, cliquez sur l’icône « + » dans la section des hôtes frontend pour ajouter un nouveau domaine personnalisé.
2. Entrez le nom de domaine racine ou apex dans le champ du nom d’hôte personnalisé, par exemple `contosonews.com`.
3. Une fois le mappage CNAME du domaine à votre Front Door validé, cliquez sur **Ajouter** pour ajouter le domaine personnalisé.
4. Cliquez sur **Enregistrer** pour envoyer les modifications.

![Menu Domaines personnalisés](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>Activer HTTPS sur votre domaine personnalisé

1. Cliquez sur le domaine personnalisé qui a été ajouté puis, sous la section **HTTPS sur un domaine personnalisé**, modifiez l’état sur **Activé**.
2. Sélectionnez le **type de gestion des certificats** _« Utiliser mon propre certificat »_ .

> [!WARNING]
> Le type de gestion des certificats managés par Front Door n’est actuellement pas pris en charge pour les domaines apex ou racine. La seule option d’activation HTTPS sur un domaine apex ou racine disponible pour Front Door consiste à utiliser votre propre certificat SSL personnalisé hébergé sur Azure Key Vault.

3. Vérifiez que vous avez configuré les autorisations Front Door appropriées pour accéder à votre coffre de clés, comme indiqué dans l’interface utilisateur, avant de passer à l’étape suivante.
4. Choisissez un **compte Key Vault** dans votre abonnement actuel, puis sélectionnez le **Secret** et la **Version du secret** appropriés à mapper au bon certificat.
5. Cliquez sur **Mettre à jour** pour enregistrer la sélection, puis cliquez sur **Enregistrer**.
6. Après quelques minutes, cliquez sur **Actualiser**, puis cliquez une nouvelle fois sur le domaine personnalisé pour voir la progression de l’approvisionnement de certificats. 

> [!WARNING]
> Vérifiez que vous avez créé des règles d’acheminement appropriées pour votre domaine apex ou ajouté le domaine aux règles d’acheminement existantes.

## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment [créer une porte d’entrée](quickstart-create-front-door.md).
- Découvrez [comment fonctionne Front Door](front-door-routing-architecture.md).