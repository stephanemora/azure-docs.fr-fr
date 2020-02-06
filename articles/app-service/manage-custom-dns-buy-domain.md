---
title: Acheter un nom de domaine personnalisé
description: Découvrez comment acheter un domaine App Service et comment vous en servir de domaine personnalisé pour votre application Azure App Service.
ms.assetid: 70fb0e6e-8727-4cca-ba82-98a4d21586ff
ms.topic: article
ms.date: 11/24/2017
ms.custom: seodec18
ms.openlocfilehash: afb40d0f3681bc02351e43166fccfaafe7741128
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023410"
---
# <a name="buy-a-custom-domain-name-for-azure-app-service"></a>Acheter un nom de domaine personnalisé pour Azure App Service

Les domaines App Service sont des domaines de niveau supérieur gérés directement dans Azure. Ils facilitent la gestion des domaines personnalisés pour [Azure App Service](overview.md). Ce tutoriel explique comment acheter un domaine App Service et attribuer des noms DNS dans Azure App Service.

Pour la machine virtuelle Azure ou le stockage Azure, consultez [Assign App Service domain to Azure VM or Azure Storage](https://azure.github.io/AppService/2017/07/31/Assign-App-Service-domain-to-Azure-VM-or-Azure-Storage) (Attribuer un domaine App Service à une machine virtuelle Azure ou un stockage Azure). Pour Services cloud, consultez [Configuration d’un nom de domaine personnalisé pour un service cloud Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

## <a name="prerequisites"></a>Conditions préalables requises

Pour suivre ce tutoriel :

* [Créez une application App Service](/azure/app-service/), ou utilisez une application créée pour un autre didacticiel.
* [Supprimez la limite de dépense de votre abonnement](../cost-management-billing/manage/spending-limit.md#remove). Vous ne pouvez pas acheter des domaines App Service avec des crédits d'abonnement gratuits.

## <a name="prepare-the-app"></a>Préparer l’application

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Pour utiliser des domaines personnalisés dans Azure App Service, le [plan App Service](https://azure.microsoft.com/pricing/details/app-service/) de votre application doit correspondre à un niveau payant (**Partagé**, **De base**, **Standard** ou **Premium**). Au cours de cette étape, vous vérifiez que votre application se trouve dans le niveau tarifaire pris en charge.

### <a name="sign-in-to-azure"></a>Connexion à Azure

Ouvrez le [portail Azure](https://portal.azure.com) et connectez-vous avec votre compte Azure.

### <a name="navigate-to-the-app-in-the-azure-portal"></a>Accéder à l’application dans le portail Azure

Dans le menu de gauche, sélectionnez **App Services**, puis le nom de l’application.

![Navigation au sein du portail pour accéder à l’application Azure](./media/app-service-web-tutorial-custom-domain/select-app.png)

La page de gestion de l’application App Service s’affiche.  

### <a name="check-the-pricing-tier"></a>Vérification du niveau tarifaire

Dans la navigation gauche de la page de l’application, faites défiler jusqu’à la section **Paramètres** et sélectionnez **Monter en puissance (plan App Service)** .

![Menu Monter en puissance](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

Le niveau actuel de l’application est encadré d’un rectangle bleu. Vérifiez que l’application ne se trouve pas dans le niveau **F1**. Les DNS personnalisés ne sont pas disponibles dans le niveau **F1**. 

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

Si le plan App Service n’est pas dans le niveau **F1**, fermez la page **Monter en puissance** et passez à [Acheter le domaine](#buy-the-domain).

### <a name="scale-up-the-app-service-plan"></a>Monter en puissance le plan App Service

Sélectionnez un niveau payant (**D1**, **B1**, **B2**, **B3** ou n’importe quel niveau dans la catégorie **Production**). Pour obtenir des options supplémentaires, cliquez sur **Afficher d’autres options**.

Cliquez sur **Appliquer**.

![Vérification du niveau de tarification](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

Lorsque la notification suivante s’affiche, cela signifie que l’opération est terminée.

![Confirmation d’opération de mise à l’échelle](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

## <a name="buy-the-domain"></a>Acheter le domaine

### <a name="pricing-information"></a>Informations sur la tarification
Pour obtenir des informations de tarification sur les domaines Azure App Service, visitez la [page Tarification d’App Service](https://azure.microsoft.com/pricing/details/app-service/windows/) et faites défiler jusqu’au domaine App Service.

### <a name="sign-in-to-azure"></a>Connexion à Azure
Ouvrez le [portail Azure](https://portal.azure.com/) et connectez-vous avec votre compte Azure.

### <a name="launch-buy-domains"></a>Lancer Acheter des domaines
Sous l’onglet **App Services**, cliquez sur le nom de votre application, puis sélectionnez **Paramètres** et **Domaines personnalisés**.
   
![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Sur la page **Domaines personnalisés**, cliquez sur **Acheter un domaine**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

> [!NOTE]
> Si vous ne voyez pas la section **Domaines App Service**, vous devez supprimer la limite de dépenses de votre compte Azure (voir [Conditions préalables](#prerequisites)).
>
>

### <a name="configure-the-domain-purchase"></a>Configurer l’achat de domaine

Sur la page **Domaine App Service**, dans la zone **Recherche de domaine**, tapez le nom de domaine que vous souhaitez acheter et tapez `Enter`. Les domaines disponibles proposés s'affichent juste en dessous de la zone de texte. Sélectionnez un ou plusieurs domaines que vous souhaitez acheter.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

> [!NOTE]
> Les [domaines de niveau supérieur](https://wikipedia.org/wiki/Top-level_domain) suivants sont pris en charge par les domaines App Service : _com_, _net_, _co.uk_, _org_, _nl_, _in_, _biz_, _org.uk_ et _co.in_.
>
>

Cliquez sur **Informations de contact** et remplissez le formulaire d’informations de contact du domaine. Lorsque vous avez terminé, cliquez sur **OK** pour revenir à la page Domaine App Service.

Il est très important de remplir tous les champs obligatoires aussi précisément que possible. L’inexactitude des informations de contact fournies peut empêcher l’achat des domaines.

Sélectionnez ensuite les options souhaitées pour votre domaine. Pour plus de précisions, consultez le tableau suivant :

| Paramètre | Valeur suggérée | Description |
|-|-|-|
|Protection des données personnelles | Activer | Choisissez l'option « Protection des données personnelles », incluse _gratuitement_ dans le prix d'achat. Certains domaines de niveau supérieur sont gérés par des bureaux d'enregistrement qui ne prennent pas en charge la protection des données personnelles. Ils sont répertoriés dans la page **Protection des données personnelles**. |
| Attribuer des noms d’hôte par défaut | **www** et **\@** | Si vous le souhaitez, vous pouvez sélectionner les liaisons de nom d’hôte souhaitées. Lorsque l’opération d’achat de domaine est terminée, votre application est accessible aux noms d’hôtes choisis. Si l’application se trouve derrière [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/), vous ne voyez pas l’option pour attribuer le domaine racine (@), car Traffic Manager ne prend pas en charge les enregistrements A. Vous pouvez apporter des modifications aux attributions de nom d’hôte après l’achat de domaine. |

### <a name="accept-terms-and-purchase"></a>Accepter les mentions et acheter

Cliquez sur **Mentions légales** pour consulter les mentions et les frais, puis cliquez sur **Acheter**.

> [!NOTE]
> Les domaines App Service utilisent GoDaddy pour l’inscription du domaine, et Azure DNS pour l’hébergement. Outre les frais d’inscription de domaine, les frais d’utilisation d’Azure DNS s’appliquent aussi. Pour en savoir plus, consultez la page relative à la [tarification Azure DNS](https://azure.microsoft.com/pricing/details/dns/).
>
>

Retournez sur la page **Domaine App Service**, puis cliquez sur **OK**. Pendant que l’opération, vous voyez apparaître les notifications suivantes :

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-validate.png)

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-purchase-success.png)

### <a name="test-the-hostnames"></a>Test des noms d’hôte

Si vous avez attribué des noms d’hôte par défaut à votre application, vous voyez également une notification de réussite pour chaque nom d’hôte choisi.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

Vous voyez également les noms d’hôtes choisis sur la page **Domaines personnalisés**, dans la section **Noms d’hôte personnalisés**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added.png)

> [!NOTE]
> Si le domaine personnalisé présente l’état **Non sécurisé**, cela signifie qu’il n’est pas encore lié à un certificat SSL. Toutes les requêtes HTTPS d’un navigateur sur votre domaine personnalisé recevront une erreur ou un avertissement, selon le navigateur. Pour configurer une liaison SSL, consultez [Sécuriser un nom DNS personnalisé avec une liaison SSL dans Azure App Service](configure-ssl-bindings.md).
>

Pour tester des noms d’hôte, accédez à ceux qui sont répertoriés dans le navigateur. Dans l’exemple de la capture d’écran précédente, essayez d’accéder à _kontoso.net_ et _www\.kontoso.net_.

## <a name="assign-hostnames-to-app"></a>Attribuer des noms d’hôte à une application

Si vous choisissez de ne pas attribuer un ou plusieurs noms d’hôte par défaut à votre application lors de l’achat, ou si vous devez attribuer un nom d’hôte non répertorié, sachez que vous pouvez attribuer ce nom à tout moment.

Vous pouvez également attribuer des noms d’hôte dans le domaine App Service à n’importe quelle autre application. La procédure varie selon que le domaine App Service et l’application appartiennent ou non au même abonnement.

- Autre abonnement : mapper des enregistrements DNS personnalisés depuis le domaine App Service vers l’application comme un domaine acheté en externe. Pour en savoir plus sur l’ajout de noms DNS personnalisés à un domaine App Service, consultez [Gérer les enregistrements DNS personnalisés](#custom). Pour mapper un domaine acheté en externe à une application, consultez [Mapper un nom DNS personnalisé existant à Azure App Service](app-service-web-tutorial-custom-domain.md). 
- Même abonnement : Utilisez les étapes suivantes.

### <a name="launch-add-hostname"></a>Lancer Ajouter un nom d’hôte
Sur la page **App Services**, sélectionnez le nom de l’application à laquelle vous souhaitez attribuer des noms d’hôte, puis **Paramètres**, et **Domaines personnalisés**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Vérifiez que votre domaine acheté est répertorié dans la section **Domaines App Service**, mais ne le sélectionnez pas. 

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

> [!NOTE]
> Tous les domaines App Service du même abonnement sont affichés sur la page **Domaines personnalisés** de l’application. Si votre domaine est inclus dans l’abonnement de l’application, mais qu’il ne s’affiche pas sur la page **Domaines personnalisés** de l’application, ouvrez à nouveau la page **Domaines personnalisés** ou actualisez la page web. Vérifiez également la cloche de notification en haut du portail Azure pour savoir s’il y a des échecs lors de la création ou de la progression.
>
>

Sélectionnez **Ajouter un nom d’hôte**.

### <a name="configure-hostname"></a>Configurer le nom d’hôte
Dans la boîte de dialogue **Ajouter un nom d’hôte**, entrez le nom de domaine complet du domaine App Service ou de n’importe quel sous-domaine. Par exemple :

- kontoso.net
- www\.kontoso.net
- abc.kontoso.net

Lorsque vous avez terminé, sélectionnez **Valider**. Le type d’enregistrement de nom d’hôte est automatiquement sélectionné pour vous.

Sélectionnez **Ajouter un nom d’hôte**.

Une fois l’opération terminée, vous voyez une notification de réussite concernant le nom d’hôte attribué.  

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-bind-success.png)

### <a name="close-add-hostname"></a>Fermer Ajouter un nom d’hôte
Sur la page **Ajouter un nom d’hôte**, attribuez le nom d’hôte de votre choix à votre application. Lorsque vous avez terminé, fermez la page **Ajouter un nom d’hôte**.

Vous devez maintenant voir le ou les noms d’hôte nouvellement attribués sur la page **Domaines personnalisés** de l’application web.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostnames-added2.png)

### <a name="test-the-hostnames"></a>Test des noms d’hôte

Accédez aux noms d’hôte répertoriés dans le navigateur. Dans l’exemple sur la capture d’écran précédente, essayez d’accéder à _abc.kontoso.net_.

## <a name="renew-the-domain"></a>Renouveler le domaine

Le domaine App Service que vous avez acheté est valable un an à compter de la date d'achat. Par défaut, le domaine est configuré pour se renouveler automatiquement, avec une facturation selon votre mode de paiement, pour l'année suivante. Vous pouvez renouveler manuellement votre nom de domaine.

Si vous souhaitez désactiver le renouvellement automatique ou renouveler manuellement votre domaine, suivez les étapes décrites ici.

Sous l’onglet **App Services**, cliquez sur le nom de votre application, puis sélectionnez **Paramètres** et **Domaines personnalisés**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

Dans la section **Domaines App Service**, sélectionnez le domaine que vous souhaitez configurer.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-select-domain.png)

Dans la barre de navigation à gauche du domaine, sélectionnez **Renouvellement de domaine**. Pour arrêter automatiquement le renouvellement de votre domaine, sélectionnez **Désactivé**, puis **Enregistrer**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-autorenew.png)

Pour renouveler manuellement votre domaine, sélectionnez **Renouveler le domaine**. Cependant, ce bouton n'est actif que [90 jours avant l'expiration du domaine](#when-domain-expires).

Si le renouvellement de votre domaine est réussi, vous recevrez une notification par e-mail dans les 24 heures.

## <a name="when-domain-expires"></a>Lorsque le domaine expire

Azure traite les domaines App Service expirés ou sur le point d’expirer comme suit :

* Si le renouvellement automatique est désactivé : 90 jours avant l’expiration de domaine, une notification par e-mail est envoyée et le bouton **Renouveler le domaine** est activé dans le portail.
* Si le renouvellement automatique est activé : Le jour après la date d’expiration de votre domaine, Azure tente de vous facturer pour le renouvellement du nom de domaine.
* Si une erreur se produit pendant le renouvellement automatique (par exemple, si votre carte enregistrée est arrivée à expiration), ou si le renouvellement automatique est désactivé et que vous autorisez l’expiration du domaine, Azure vous avertit de l’expiration du domaine et réserve votre nom de domaine. Vous pouvez [renouveler manuellement](#renew-the-domain) votre domaine.
* Les 4 et 12e jours après expiration, Azure vous envoie des e-mails de notification supplémentaires. Vous pouvez [renouveler manuellement](#renew-the-domain) votre domaine.
* Le 19e jour après expiration, votre domaine reste réservé, mais fait alors l’objet de frais d’échange. Vous pouvez appeler le support technique pour renouveler votre nom de domaine, soumis aux frais de renouvellement et d’échange applicables.
* Le 25e jour après expiration, Azure met votre domaine aux enchères avec un service de vente aux enchères de noms de domaine du secteur. Vous pouvez appeler le support technique pour renouveler votre nom de domaine, soumis aux frais de renouvellement et d’échange applicables.
* Le 30e jour après expiration, vous n’êtes plus en mesure de récupérer votre domaine.

<a name="custom"></a>

## <a name="manage-custom-dns-records"></a>Gérer des enregistrements DNS personnalisés

Dans Azure, les enregistrements DNS d’un domaine App Service sont gérés à l’aide d’[Azure DNS](https://azure.microsoft.com/services/dns/). Vous pouvez ajouter, supprimer et mettre à jour les enregistrements DNS, comme pour un domaine acheté en externe.

### <a name="open-app-service-domain"></a>Ouvrir le domaine App Service

Dans le portail Azure, dans le menu de gauche, sélectionnez **Tous les services** > **Domaines App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Sélectionnez le domaine à gérer. 

### <a name="access-dns-zone"></a>Accéder à la zone DNS

Dans le menu de gauche du domaine, sélectionnez **Zone DNS**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-dns-zone.png)

Cette action ouvre la page [Zone DNS](../dns/dns-zones-records.md) de votre domaine App Service dans Azure DNS. Pour savoir comment modifier des enregistrements DNS, consultez [Gérer des zones DNS dans le portail Azure](../dns/dns-operations-dnszones-portal.md).

## <a name="cancel-purchase-delete-domain"></a>Annuler l’achat (supprimer le domaine)

Après avoir acheté le domaine App Service, vous disposez de cinq jours pour annuler votre achat et bénéficier d’un remboursement intégral. Au-delà de cinq jours, vous pouvez supprimer le domaine App Service, mais vous ne serez pas remboursé.

### <a name="open-app-service-domain"></a>Ouvrir le domaine App Service

Dans le portail Azure, dans le menu de gauche, sélectionnez **Tous les services** > **Domaines App Service**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-access.png)

Sélectionnez le domaine que vous souhaitez annuler ou supprimer. 

### <a name="delete-hostname-bindings"></a>Supprimer des liaisons de noms d’hôte

Dans le menu de gauche du domaine, sélectionnez **Liaisons de noms d’hôte**. Les liaisons de nom d’hôte de tous les services Azure sont répertoriées ici.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-hostname-bindings.png)

Vous ne pouvez pas supprimer le domaine App Service avant d’avoir supprimé toutes les liaisons de nom d’hôte.

Supprimez chaque liaison de nom d’hôte en sélectionnant **...**  > **Supprimer**. Après avoir supprimé toutes les liaisons, sélectionnez **Enregistrer**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-delete-hostname-bindings.png)

### <a name="cancel-or-delete"></a>Annuler ou supprimer

Dans le menu de gauche du domaine, sélectionnez **Vue d’ensemble**. 

Si la période d’annulation pour domaine acheté n’est pas écoulée, sélectionnez **Annuler l’achat**. Dans le cas contraire, vous verrez le bouton **Supprimer**. Pour supprimer le domaine sans remboursement, sélectionnez **Supprimer**.

![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-cancel.png)

Pour confirmer l'opération, sélectionnez **Oui**.

Une fois l’opération terminée, le domaine n’est plus lié à votre abonnement et est de nouveau disponible à l’achat. 

## <a name="direct-default-url-to-a-custom-directory"></a>Diriger l'URL par défaut vers un répertoire personnalisé

Par défaut, App Service dirige les demandes web au répertoire racine du code de votre application. Pour les diriger vers un sous-répertoire, par exemple `public`, voir [Diriger l'URL par défaut vers un répertoire personnalisé](app-service-web-tutorial-custom-domain.md#virtualdir).
