---
title: Gestion des prospects du marketplace commercial de Microsoft
description: En savoir plus sur la génération et la réception des prospects de vos offres Microsoft AppSource et Place de marché Azure
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: dsindona
ms.openlocfilehash: 5d1bf859968c7b7a889abe635c917d0da11bf6e1
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837326"
---
# <a name="customer-leads-from-your-commercial-marketplace-offer"></a>Prospects de votre offre de marketplace commercial

Les prospects sont des clients intéressés par vos offres sur [Microsoft AppSource](https://appsource.microsoft.com) ou la [Place de marché Azure](https://azuremarketplace.microsoft.com), ou qui procèdent à leur déploiement. Vous pouvez recevoir des prospects une fois que votre offre est publiée sur le marketplace commercial. Cet article explique les concepts de gestion des prospects suivants :

* Comment votre offre publiée sur le marketplace commercial génère des prospects pour que vous ne manquiez aucune opportunité commerciale. 
* Comment connecter votre système de gestion de la relation client (CRM) à votre offre pour pouvoir gérer vos prospects dans un emplacement central.
* Les données de prospect que nous vous envoyons afin que vous puissiez effectuer le suivi des clients qui vous ont contacté.

## <a name="generate-customer-leads"></a>Générer des prospects

Voici les endroits où les prospects sont générés :

- Un client consent à partager ses informations après avoir sélectionné **Me contacter** sur la Place de marché commerciale. Il s’agit d’un prospect d’*intérêt initial*. Nous partageons avec vous des informations sur ce client qui a manifesté de l’intérêt pour votre produit. Il constitue la partie supérieure de l’entonnoir d’acquisition.

    ![Me contacter dans Dynamics 365](./media/commercial-marketplace-get-customer-leads/dynamics-365-contact-me.png)

- Un client sélectionne **Obtenir maintenant** (ou **Créer** sur le [portail Azure](https://portal.azure.com/)) pour obtenir votre offre. Il s’agit d’un prospect *actif*. Nous partageons avec vous des informations sur ce client qui a commencé à déployer votre produit.

    ![Bouton SQL : Télécharger maintenant](./media/commercial-marketplace-get-customer-leads/sql-get-it-now.png)

    ![Bouton Windows Server : Créer](./media/commercial-marketplace-get-customer-leads/windows-server-create.png)

- Un client sélectionne **Version d’évaluation** ou **Essai gratuit** pour essayer votre offre. Les versions d’évaluation et les essais gratuits constituent pour vous une occasion de partager votre activité instantanément avec des clients potentiels, sans barrières.

    ![Bouton Dynamics 365 : Version d’évaluation](./media/commercial-marketplace-get-customer-leads/dynamics-365-test-drive.png)

    ![Bouton Dynamics 365 : Essai gratuit](./media/commercial-marketplace-get-customer-leads/dynamics-365-free-trial.png)

## <a name="connect-to-your-crm-system"></a>Connexion à votre système CRM

[!INCLUDE [Test drive content](./includes/connect-lead-management.md)]

## <a name="understand-lead-data"></a>Comprendre les données de prospect

Chaque prospect que vous recevez pendant le processus d’acquisition de clients dispose de données dans des champs spécifiques. Le premier champ à rechercher est le champ `LeadSource`, qui suit ce format : **Source-Action** | **Offre**.

**Sources** La valeur de ce champ est renseignée en fonction de la place de marché qui a généré le prospect. Les valeurs possibles sont `"AzureMarketplace"`, `"AzurePortal"` et `"AppSource (SPZA)"`.

**Actions** : La valeur de ce champ est renseignée en fonction de l’action effectuée par le client sur la Place de marché, qui a généré le prospect.

Les valeurs possibles sont les suivantes :

- **« INS »**  : signifie *installation*. Cette action est proposée sur la Place de marché Azure ou dans AppSource lorsqu’un client acquiert votre produit.
- **« PLT »**  : désigne un *essai gratuit mené par un partenaire*. Cette action est proposée dans AppSource lorsqu’un client sélectionne l’option **Me contacter**.
- **« DNC »**  : signifie *Ne pas contacter*. Cette action apparaît dans AppSource lorsqu’un partenaire qui a été répertorié sur la page de votre application reçoit une demande de contact. Nous partageons une notification indiquant que ce client a été répertorié sur votre application, mais qu’il n’est pas nécessaire de le contacter.
- **« Create »**  : cette action apparaît uniquement sur le portail Azure et est générée lorsqu’un client achète votre offre sur son compte.
- **« StartTestDrive »**  : cette action est proposée uniquement pour l’option **Version d’évaluation** et elle est générée quand un client démarre sa version d’évaluation.

**Offres** : Vous pouvez avoir plusieurs offres sur la Place de marché commerciale. La valeur de ce champ est renseignée en fonction de l’offre qui a généré le prospect. L’ID de serveur de publication et l’ID d’offre sont tous deux envoyés dans ce champ et sont des valeurs que vous avez fournies lors de la publication de l’offre sur la Place de marché.

Les exemples suivants présentent des valeurs au format `publisherid.offerid` attendu : 

- `checkpoint.check-point-r77-10sg-byol`
- `bitnami.openedxcypress`
- `docusign.3701c77e-1cfa-4c56-91e6-3ed0b622145`

## <a name="customer-information"></a>Informations client

Les informations du client sont envoyées dans plusieurs champs. L’exemple suivant présente les informations client qui sont contenues dans un prospect :

- FirstName : John
- LastName : Smith
- Adresse e-mail : jsmith\@microsoft.com
- Téléphone : 1234567890
- Pays : US
- Société : Microsoft
- Titre : CTO

>[!NOTE]
>Les données de l’exemple précédent ne sont pas toujours toutes disponibles pour chaque prospect. Comme vous recevez des prospects de plusieurs étapes, comme mentionné dans la section « Générer des prospects », la meilleure façon de les traiter consiste à dédupliquer les enregistrements et à personnaliser les suivis. De cette manière, chaque client reçoit un message approprié et vous créez une relation unique.

## <a name="best-practices-for-lead-management"></a>Meilleures pratiques de gestion des prospects

- **Processus** : Définissez un processus de vente clair, avec des jalons, des analyses et des responsabilités claires dans l’équipe.
- **Qualification** : Définissez les conditions préalables qui indiquent si un prospect était pleinement qualifié. Assurez-vous que les représentants commerciaux ou marketing qualifient les prospects avec soins avant de les incorporer dans le processus de vente.
- **Suivi** : N’oubliez pas d’effectuer un suivi. Une transaction nécessite en général entre 5 et 12 appels de suivi.
- **Soins** : Soignez vos prospects pour bénéficier d’une plus grande marge de profits.

## <a name="common-questions-about-lead-management"></a>Questions courantes sur la gestion des prospects

### <a name="where-can-i-get-help-in-setting-up-my-lead-destination"></a>Où puis-je obtenir de l’aide pour configurer la destination des prospects ?

Suivez les étapes de la section [Connexion à votre système CRM](#connect-to-your-crm-system) ou soumettez un ticket de support via [Aide et support de l’Espace partenaires](https://partner.microsoft.com/support/v2/?stage=1). Sélectionnez ensuite **Création d’offre** > **Votre type d’offre** > **Configuration de la gestion des prospects**.

### <a name="am-i-required-to-configure-a-lead-destination-in-order-to-publish-an-offer-in-the-commercial-marketplace"></a>Dois-je obligatoirement configurer une destination de prospect pour publier une offre sur la place de marché commerciale ?

La réponse dépend du type d’offre que vous publiez. Les offres de logiciel en tant que service (SaaS) et d’engagement client Dynamics 365 utilisent **Me contacter** pour lister toutes les offres Dynamics 365 pour la finance et les opérations, toutes les offres Dynamics 365 Business Central et toutes les offres de services de conseil. Par conséquent, elles nécessitent une connexion à une destination de prospect. Si votre type d’offre ne figure pas dans cette liste, une connexion à une destination de prospect n’est pas nécessaire. Nous vous recommandons de configurer une destination de prospect pour ne pas manquer d’opportunités commerciales.

### <a name="how-can-i-find-the-test-lead"></a>Comment trouver le prospect test ?

Recherchez `"MSFT_TEST"` dans votre destination de prospect. Voici un exemple de prospect test de Microsoft :

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

### <a name="i-have-a-live-offer-but-why-am-i-not-seeing-any-leads"></a>J’ai une offre en ligne, mais je ne vois aucun prospect. Pourquoi ?

Assurez-vous que votre connexion à la destination du prospect est valide. Nous vous enverrons un prospect test une fois que vous aurez sélectionné **Publier** dans votre offre, dans l’Espace partenaires. Si vous voyez le prospect test, la connexion est valide. Vous pouvez également tester votre connexion de prospect en essayant d’acquérir la préversion de l’offre au cours de l’étape de préversion. Sélectionnez **Obtenir maintenant**, **Me contacter** ou **Essai gratuit** dans la liste sur la Place de marché commerciale.

De plus, vérifiez de rechercher les bonnes données. Le contenu de la section [Comprendre les données de prospect](#understand-lead-data) de cet article décrit les données de prospect que nous envoyons à votre destination de prospect.

### <a name="i-configured-azure-blob-storage-as-my-lead-destination-but-why-dont-i-see-the-lead"></a>J’ai configuré le stockage Blob Azure comme destination de prospect. Pourquoi le prospect n’est-il pas visible ?

Le stockage Blob Azure n’est plus pris en charge comme destination de prospect et vous risquez de manquer les prospects générés par votre offre. Basculez vers l’une des autres options de [destination des prospects](./commercial-marketplace-get-customer-leads.md). 

### <a name="i-received-an-email-from-the-commercial-marketplace-but-why-cant-i-find-the-lead-in-my-crm"></a>J’ai reçu un e-mail de la Place de marché commerciale, mais pourquoi le prospect est-il introuvable dans mon système CRM ?

Il est possible que le domaine de messagerie de l’utilisateur final soit .edu. Pour des raisons de confidentialité, nous ne transmettons pas d’informations personnelles en provenance du domaine .edu. Soumettez un ticket de support via [Aide et support de l’Espace partenaires](https://partner.microsoft.com/support/v2/?stage=1).

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-can-i-view-the-leads"></a>J’ai configuré une table Azure comme destination de prospect. Comment afficher les prospects ?

Vous pouvez accéder aux données de prospect stockées dans la table Azure à partir du portail Azure. Vous pouvez également télécharger et installer gratuitement l’[Explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour afficher les données de table de votre compte de stockage Azure.

### <a name="i-configured-an-azure-table-as-my-lead-destination-can-i-get-notified-whenever-a-new-commercial-marketplace-lead-is-sent"></a>J’ai configuré une table Azure comme destination de prospect. Puis-je recevoir une notification chaque fois qu’un nouveau prospect est envoyé par la Place de marché commerciale ?

Oui. Suivez les instructions figurant dans [Configurer la gestion des prospects à l’aide d’une table Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) pour configurer un flux Microsoft Flow qui envoie un e-mail si un prospect est ajouté à la table Azure.

### <a name="i-configured-salesforce-as-my-lead-destination-but-why-cant-i-find-the-leads"></a>J’ai configuré Salesforce comme destination de prospect, mais pourquoi les prospects sont-ils introuvables ?

Vérifiez si le formulaire Web-to-Lead est un champ obligatoire avec liste déroulante. Si c’est le cas, faites-en un champ de texte non obligatoire.

### <a name="there-was-an-issue-with-my-lead-destination-and-i-missed-some-leads-can-i-have-them-sent-to-me-in-an-email"></a>Un problème s’est produit au niveau de ma destination des prospects, ce qui m’a fait manquer des prospects. Puis-je me les faire envoyer par e-mail ?

En raison des stratégies relatives aux informations personnelles, nous ne pouvons pas partager les informations sur les prospects via un e-mail non sécurisé.

### <a name="i-configured-an-azure-table-as-my-lead-destination-how-much-will-it-cost"></a>J’ai configuré une table Azure comme destination de prospect. Combien cela va-t-il coûter ?

Les données de génération de prospects sont peu volumineuses. Elles représentent moins de 1 Go pour presque tous les serveurs de publication. Le coût dépend du nombre de prospects reçus. Par exemple, si 1 000 prospects sont reçus en un mois, le coût est d’environ 50 cents. Pour plus d’informations sur la tarification du stockage, consultez [Vue d’ensemble de la tarification de Stockage Azure](https://azure.microsoft.com/pricing/details/storage/).

Si vous n’avez pas trouvé de réponse à votre question, contactez le Support Microsoft via [Aide et support de l’Espace partenaires](https://aka.ms/marketplacepublishersupport). Sélectionnez ensuite **Création d’offre** > **Votre type d’offre** > **Configuration de la gestion des prospects**.

### <a name="im-receiving-email-notifications-when-new-customer-leads-are-received-how-can-i-configure-someone-else-to-receive-these-emails"></a>Je reçois des notifications par e-mail lors de la réception de nouveaux prospects. Comment puis-je configurer un autre destinataire pour ces e-mails ?

Accédez à votre offre dans l’Espace partenaires, puis accédez à la page **Configuration de l’offre** > **Gestion des prospects** > **Modifier**. Mettez à jour les adresses e-mail dans le champ **E-mail du contact**.

## <a name="next-steps"></a>Étapes suivantes

Une fois les paramètres techniques configurés, incorporez ces prospects à votre stratégie commerciale et marketing, ainsi qu’à vos processus opérationnels actuels. Nous cherchons à mieux comprendre votre processus de vente global. C’est pourquoi nous tenons à travailler en étroite collaboration avec vous pour vous apporter des prospects de qualité et des données en quantité suffisante pour garantir votre réussite. Nous apprécions vos commentaires sur la façon dont nous pouvons optimiser et améliorer les prospects que nous vous envoyons, accompagnés de toutes les données nécessaires pour assurer la réussite de ces clients. Faites-nous savoir si vous souhaitez nous [communiquer vos commentaires](mailto:AzureMarketOnboard@microsoft.com) et suggestions pour permettre à votre équipe commerciale de mieux exploiter les prospects de la Place de marché commerciale.
