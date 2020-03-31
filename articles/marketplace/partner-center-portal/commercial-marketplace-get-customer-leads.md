---
title: Configurer des prospects | Place de marché Azure
description: Configurer des prospects dans une place de marché commerciale.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: dsindona
ms.openlocfilehash: ce576d9825819770486197a6c39425adf6ac3208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275898"
---
# <a name="customer-leads-from-your-marketplace-offer"></a>Prospects de votre offre de place de marché

Les prospects sont des clients qui sont intéressés par vos produits sur la [Place de marché Azure](https://azuremarketplace.microsoft.com) ou sur [d’AppSource](https://appsource.microsoft.com) ou qui procèdent à ce déploiement. Vous recevrez des prospects une fois que votre offre sera publiée sur la place de marché. Cet article explique ce qui suit :

* Votre offre de place de marché génère des prospects, pour que vous ne manquiez aucune opportunité commerciale. 
* Connecter votre CRM à votre offre, afin de pouvoir gérer vos prospects dans un seul emplacement centralisé.
* Comprendre les données de prospect que nous vous envoyons, afin de pouvoir suivre les clients qui vous ont contacté.

## <a name="generate-customer-leads"></a>Générer des prospects

Voici les endroits où les prospects sont générés :

1. Lorsque le client consent à partager ses informations après avoir sélectionné « Me contacter » sur la place de marché. Ce prospect est un prospect **d’intérêt initial** ; nous partageons des informations sur un client qui a manifesté de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.

      ![Me contacter dans Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

2. Quand un client sélectionne « Obtenir maintenant » ou « Créer » (sur le [Portail Azure](https://portal.azure.com/)) pour obtenir votre offre, il devient un **prospect actif** ; nous partageons les informations sur un client qui a commencé à déployer votre produit.

    ![Obtenir maintenant dans SQL](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Windows Server Create](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

3. Le client commence un « test drive » ou un « essai gratuit » de votre offre. Les essais et les versions d’évaluation constituent pour vous une opportunité accélérée de partager votre activité instantanément avec des clients potentiels sans barrières à l’entrée.

    ![Version d’évaluation Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Version d’évaluation Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connexion à votre système CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Comprendre les données de prospect

Chaque prospect que vous recevez pendant le processus d’acquisition de clients dispose de données dans des champs spécifiques. Le premier champ à rechercher est le champ `LeadSource`, qui suit ce format : **Source-Action** | **Offre**.

**Sources** La valeur de ce champ est renseignée en fonction de la place de marché qui a généré le prospect. Les valeurs possibles sont `"AzureMarketplace"`, `"AzurePortal"` et `"AppSource (SPZA)"`.

**Actions** : La valeur de ce champ est renseignée en fonction de l’action effectuée par le client sur la place de marché, ce qui a généré le prospect. 

Les valeurs possibles sont les suivantes :

- « INS » : installation. Cette action figure sur la Place de marché Azure ou dans AppSource lorsqu’un client achète votre produit.
- « PLT » : désigne une évaluation conduite par un partenaire. Cette action apparaît dans AppSource lorsqu’un client utilise l’option Me contacter.
- « DNC » : ne pas contacter. Cette action apparaît dans AppSource lorsqu’un partenaire qui a été répertorié sur la page de votre application reçoit une demande de contact. Nous vous informons que ce client a été répertorié sur votre application, mais qu’il n’est pas nécessaire de le contacter.
- « Create » : cette action apparaît uniquement dans le portail Azure et est générée lorsqu’un client achète votre offre sur son compte.
- « StartTestDrive » : cette action est destinée uniquement aux versions d’évaluation ; elle est générée quand un client démarre sa version d’évaluation.

**Offres** : Vous pouvez avoir plusieurs offres sur la place de marché. La valeur de ce champ est renseignée en fonction de l’offre qui a généré le prospect. L’ID d’éditeur et l’ID d’offre sont tous deux envoyés dans ce champ et sont des valeurs que vous avez fournies lors de la publication de l’offre sur la place de marché.

Les exemples suivants présentent des exemples de valeurs au format `publisherid.offerid`attendu : 

1. `checkpoint.check-point-r77-10sg-byol`
1. `bitnami.openedxcypress`
1. `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-info"></a>Informations client

Les informations du client sont envoyées dans plusieurs champs. L’exemple suivant présente les informations client qui sont contenues dans un prospect.

- FirstName : John
- LastName : Smith
- Adresse e-mail : jsmith\@microsoft.com
- Téléphone : 1234567890
- Pays : US
- Société : Microsoft
- Titre : CTO

>[!Note]
>Les données de l’exemple précédent ne sont pas toujours toutes disponibles pour chaque prospect. Comme vous recevez des prospects lors de plusieurs étapes comment mentionné dans la section Prospects, la meilleure façon de les traiter consiste à dédupliquer les informations et à personnaliser le suivi. De cette manière, chaque client reçoit un message approprié, et vous créez une relation unique.

## <a name="best-practices-for-lead-management"></a>Meilleures pratiques de gestion des prospects

1. *Processus* : définissez un processus de vente clair, avec des jalons, des indicateurs de performance clés et des responsabilités claires dans l’équipe.
2. *Qualification* : définissez les conditions préalables qui indiquent si un prospect a été totalement qualifié. Assurez-vous que les représentants commerciaux ou marketing qualifient les prospects attentivement avant de les faire passer dans le processus de vente.
3. *Suivi* : n’oubliez pas d’effectuer un suivi ; une transaction demande en général entre 5 et 12 appels de suivi.
4. *Soignez vos prospects* : soignez vos prospects afin de bénéficier d’une marge plus élevée.

## <a name="leads-frequently-asked-questions"></a>Forum aux questions sur les prospects

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Où puis-je obtenir de l’aide pour configurer la destination des prospects ?

Vous trouverez la documentation [ici](#connect-to-your-crm-system). Vous pouvez sinon envoyer une demande de support sur aka.ms/marketplacepublishersupport, puis sélectionner **« Création d’offre »** → **Votre type d’offre** → **« Configuration de la gestion des prospects »** .

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-on-the-marketplace"></a>Dois-je obligatoirement configurer une destination des prospects pour publier une offre sur la Place de marché ?

La réponse dépend du type d’offre que vous publiez. Les offres SaaS et Dynamics 365 for Customer Engagement sont répertoriées comme « Me contacter », toutes les offres Dynamics 365 for Operations, toutes les offres Dynamics 365 Business Central et toutes les offres de service de conseil nécessitent une connexion à une destination de prospect. Si votre type d’offre ne figure pas dans la liste, cela signifie qu’il n’est pas obligatoire. Toutefois, il est recommandé de configurer la destination des prospects pour ne manquer aucune opportunité commerciale.

### <a name="how-can-i-find-the-test-lead"></a>Comment trouver le prospect test ?

Recherchez `"MSFT_TEST"` dans votre destination des prospects. Voici un exemple de prospect test de Microsoft :

```
company = MSFT_TEST_636573304831318844
country = US
description = MSFT_TEST_636573304831318844
email = MSFT_TEST_636573304831318844@test.com
encoding = UTF-8
encoding = UTF-8
first_name = MSFT_TEST_636573304831318844
last_name = MSFT_TEST_636573304831318844
lead_source = MSFT_TEST_636573304831318844-MSFT_TEST_636573304831318844|<Offer Name>
oid = 00Do0000000ZHog
phone = 1234567890
title = MSFT_TEST_636573304831318844
```

### <a name="i-have-a-live-offer-but-im-not-seeing-any-leads"></a>J’ai une offre en ligne, mais je ne vois aucun prospect.

Assurez-vous que votre connexion à la destination du prospect est valide. Nous vous enverrons un prospect test après avoir appuyé sur Publier dans votre offre dans l’Espace partenaires. Si vous voyez le prospect test, la connexion est valide. Vous pouvez également tester votre connexion de prospect en essayant d’acquérir la préversion de l’offre au cours de l’étape de préversion : cliquez sur « Obtenir maintenant », « Me contacter » ou « Essai gratuit » sur le référencement sur la place de marché.

En outre, assurez-vous que vous recherchez les bonnes données. La section [Comprendre les données de prospect](#understand-lead-data) de ce document décrit les données liées au prospect que nous envoyons à la destination de vos prospects.

### <a name="i-have-configured-azure-blob-as-my-lead-destination-why-dont-i-see-the-lead"></a>J’ai configuré Blob Azure comme destination des prospects. Pourquoi est-ce que je ne vois pas de prospects ?

La destination des prospects d’objets blob Azure n’est plus prise en charge et vous risquez donc de manquer les prospects générés par votre offre. Basculez vers l’une des autres options de [destination des prospects](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-marketplace-why-cant-i-find-the-lead-in-my-crm"></a>J’ai reçu un e-mail de la Place de marché. Pourquoi est-ce que je ne trouve pas les prospects dans mon système CRM ?

Il est possible que le domaine de messagerie de l’utilisateur final soit .edu. Pour des raisons de confidentialité, nous ne transmettons pas de données personnelles en provenance du domaine .edu. Soumettez un ticket de support via aka.ms/marketplacepublishersupport.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>J’ai configuré Table Azure/Blob Azure comme destination des prospects. Pourquoi est ce que je ne vois pas les prospects ?

Vous pouvez accéder aux données sur les prospects stockées dans la table Azure sur le Portail Azure ou bien télécharger et installer gratuitement [l’Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour afficher les données de table de votre compte de stockage Azure.

### <a name="i-have-configured-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-lead-is-sent-by-marketplace"></a>J’ai configuré Table Azure comme destination des prospects. Puis-je recevoir une notification chaque fois qu’un nouveau prospect est envoyé par la Place de marché ?

Oui, suivez les instructions pour configurer un Microsoft Flow qui envoie un e-mail si un prospect est ajouté à la table Azure dans la documentation [ici](./commercial-marketplace-lead-management-instructions-azure-table.md).

### <a name="i-have-configured-salesforce-as-my-lead-destination-why-cant-i-find-the-leads"></a>J’ai configuré Salesforce comme destination des prospects. Pourquoi est-ce que je ne trouve pas les prospects  ?

Vérifiez si le formulaire « web to lead » est un champ obligatoire avec liste déroulante. Si c’est le cas, faites-en un champ de texte non obligatoire.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Un problème s’est produit au niveau de ma destination des prospects, ce qui m’a fait manquer des prospects. Puis-je me les faire envoyer par e-mail ?

En raison des politiques relatives aux informations d’identification personnelle (PII), nous ne pouvons pas partager les informations sur les prospects via une messagerie non sécurisée.

### <a name="i-have-configured-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>J’ai configuré Table Azure comme destination des prospects. Quel en sera le coût ?

Le volume de données de génération de prospects est faible (< 1 Go pour pratiquement tous les éditeurs). Le coût dépend du nombre de prospects reçus. Pour 1 000 prospects reçus dans un mois, le coût est d’environ 50 cents. Pour plus d’informations sur la tarification du stockage, consultez [Tarification de Storage](https://azure.microsoft.com/pricing/details/storage/).

Si vous n’avez pas trouvé de réponse à votre question, contactez le support sur aka.ms/marketplacepublishersupport, puis sélectionnez **« Création d’offre »** → **Votre type d’offre** → **« Configuration de la gestion des prospects »** . 

## <a name="next-steps"></a>Étapes suivantes

Une fois les paramètres techniques configurés, vous devez incorporer ces prospects à votre stratégie commerciale et marketing, ainsi qu’à vos processus opérationnels actuels. Nous cherchons à mieux comprendre votre processus de vente global. C’est pourquoi nous tenons à travailler en étroite collaboration avec vous pour vous apporter des prospects de qualité et des données en quantité suffisante pour garantir votre réussite. Nous apprécions vos commentaires sur la façon dont nous pouvons optimiser et améliorer les prospects que nous vous envoyons, accompagnés de toutes les données nécessaires pour assurer la réussite de ces clients. Faites-nous savoir si vous souhaitez nous [communiquer des commentaires](mailto:AzureMarketOnboard@microsoft.com) et des suggestions pour permettre à votre équipe de vente de mieux exploiter les prospects de la Place de marché.
