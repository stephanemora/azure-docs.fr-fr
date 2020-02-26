---
title: Intégration de la réécriture du mot de passe locale avec la réinitialisation de mot de passe en libre-service Azure AD - Azure Active Directory
description: Obtenir les mots de passe réécrits au niveau de l’infrastructure AD sur site
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa66299753ab11dcad280361cb5fb6f0c31ef242
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368161"
---
# <a name="what-is-password-writeback"></a>Qu’est-ce que la réécriture du mot de passe ?

Le fait d’avoir un utilitaire de réinitialisation de mot de passe basé sur le cloud est très utile, mais la plupart des entreprises disposent toujours d’un annuaire local contenant leurs utilisateurs. Comment Microsoft gère-t-il la synchronisation d’Active Directory (AD) sur site avec les modifications de mot de passe dans le cloud ? La réécriture du mot de passe est une fonctionnalité activée avec [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) qui permet aux modifications de mot de passe dans le cloud d’être réécrites sur un annuaire local existant en temps réel.

La réécriture du mot de passe est prise en charge dans les environnements qui utilisent :

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synchronisation de hachage de mot de passe](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Authentification directe](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> La réécriture du mot de passe ne fonctionnera plus pour les clients qui utilisent les versions d’Azure AD Connect 1.0.8641.0 et antérieures quand [Azure Access Control Service (ACS) sera mis hors service le 7 novembre 2018](../azuread-dev/active-directory-acs-migration.md). Les versions d’Azure AD Connect 1.0.8641.0 et antérieures cesseront alors d’autoriser la réécriture du mot de passe, car elles dépendent d’ACS pour cette fonctionnalité.
>
> Pour éviter une interruption de service, effectuez une mise à niveau depuis une version antérieure d’Azure AD Connect vers une version plus récente ; consultez l’article [Azure AD Connect : Effectuer une mise à niveau vers la dernière version](../hybrid/how-to-upgrade-previous-version.md).
>

La réécriture du mot de passe permet :

* **L’application de stratégies de mot de passe Active Directory locales** : lorsqu’un utilisateur réinitialise son mot de passe, il est vérifié pour assurer qu’il répond à votre stratégie Active Directory locale avant d’être validé dans l’annuaire. Ainsi, nous vérifions l’historique, la complexité, l’âge, les filtres de mot de passe et toute autre restriction de mot de passe éventuellement définie dans l’annuaire Active Directory local.
* **Le retour d’informations immédiat** :  L’écriture différée de mot de passe est une opération synchrone. Vos utilisateurs sont informés immédiatement si leur mot de passe ne respecte pas la stratégie définie ou s’il n’a pas pu être réinitialisé ou modifié pour une raison quelconque.
* **La prise en charge de la modification des mots de passe à partir du panneau d’accès et d’Office 365** : lorsque des utilisateurs fédérés ou synchronisés par mot de passe haché modifient leurs mots de passe (ceux-ci ayant ou non expiré), ces mots de passe sont réécrits dans votre environnement Active Directory local.
* **La prise en charge de la réécriture du mot de passe lorsqu’un administrateur le réinitialise depuis le portail Azure** : chaque fois qu’un administrateur réinitialise le mot de passe d’un utilisateur dans le [portail Azure](https://portal.azure.com), dès lors que cet utilisateur est fédéré ou qu’il dispose de la synchronisation de mot de passe haché, le mot de passe est réécrit en local. Actuellement, cette fonctionnalité n’est pas prise en charge dans le portail d’administration Office.
* **L’absence de règles de pare-feu entrantes** : la réécriture de mot de passe utilise un relais Microsoft Azure Service Bus comme canal de communication sous-jacent. Toutes les communications sont sortantes sur le port 443.

> [!NOTE]
> Les comptes d’administrateur qui existent dans des groupes protégés de votre annuaire AD local peuvent bénéficier de la réécriture du mot de passe. Les administrateurs peuvent changer leur mot de passe dans le cloud, mais ne peuvent pas utiliser la réinitialisation de mot de passe pour réinitialiser un mot de passe oublié. Pour plus d’informations sur les groupes protégés, consultez la page [Comptes et groupes protégés dans Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/appendix-c--protected-accounts-and-groups-in-active-directory).

## <a name="licensing-requirements-for-password-writeback"></a>Conditions de licence pour la réécriture du mot de passe

**Réinitialisation/modification/déverrouillage de mot de passe libre-service avec écriture différée locale est une fonctionnalité Premium d’Azure AD**. Pour plus d’informations sur les licences, consultez la [page des tarifs Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Pour que vous puissiez utiliser la réécriture du mot de passe, il faut que l’une des licences suivantes soit attribué sur votre locataire :

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 ou A3
* Enterprise Mobility + Security E5 ou A5
* Microsoft 365 E3 ou A3
* Microsoft 365 E5 ou A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Les plans de licences Office 365 édition autonome *ne prennent pas en charge « les réinitialisation/modification/déverrouillage de mot de passe libre-service avec réécriture locale »* et nécessitent l’un des plans précédents pour que cette fonctionnalité soit opérationnelle.

## <a name="how-password-writeback-works"></a>Fonctionnement de la réécriture du mot de passe

Lorsqu’un utilisateur fédéré ou disposant de la synchronisation du hachage de mot de passe réinitialise ou modifie son mot de passe dans le cloud, les événements suivants ont lieu :

1. Vérification du type de mot de passe de l’utilisateur. Si le mot de passe de l’utilisateur est géré localement :
   * Vérification du bon fonctionnement du service de réécriture. Dans le cas, l’utilisateur peut continuer.
   * Si le service de réécriture n’est pas disponible, l’utilisateur est informé que son mot de passe ne peut pas être réinitialisé pour l’instant.
1. Ensuite, l’utilisateur transite par les portails d’authentification appropriés et atteint la page de **réinitialisation du mot de passe**.
1. L’utilisateur sélectionne un nouveau mot de passe et le confirme.
1. Lorsqu’il clique sur **Envoyer**, le mot de passe en texte brut est chiffré avec une clé de contenu créée durant le processus de configuration de réécriture.
1. Le mot de passe chiffré est inclus dans une charge utile qui est envoyée via un canal HTTPS à votre relais Service Bus spécifique à l’abonné (configuré pour vous lors du processus d’installation de la réécriture). Ce relais est protégé par un mot de passe généré de manière aléatoire, connu uniquement de votre installation locale.
1. Une fois que le message a atteint le Service Bus, le point de terminaison de réinitialisation de mot de passe sort automatiquement du mode veille et découvre qu’une demande de réinitialisation est en attente.
1. Ce service recherche alors l’utilisateur concerné à l’aide de l’attribut d’ancrage de cloud. Pour que cette recherche aboutisse :

   * L’objet utilisateur doit exister dans l’espace connecteur Active Directory.
   * L’objet utilisateur doit être lié à l’objet métaverse (MV) correspondant.
   * L’objet utilisateur doit être lié à l’objet connecteur Azure Active Directory correspondant.
   * Le lien de l’objet connecteur Active Directory au métaverse doit avoir pour règle de synchronisation `Microsoft.InfromADUserAccountEnabled.xxx` sur le lien.
   
   Lorsque l’appel provient du cloud, le moteur de synchronisation utilise l’attribut **cloudAnchor** pour rechercher l’objet CS (Connector Space) Azure Active Directory. Il suit ensuite le lien vers l’objet MV, puis le lien vers l’objet Active Directory. Étant donné qu’il peut exister plusieurs objets Active Directory (plusieurs forêts) pour le même utilisateur, le moteur de synchronisation s’appuie sur le lien `Microsoft.InfromADUserAccountEnabled.xxx` pour choisir celui qui convient.

1. Une fois le compte d’utilisateur trouvé, Une tentative de réinitialiser le mot de passe directement dans la forêt Active Directory appropriée est effectuée.
1. Si l’opération de définition du mot de passe réussit, l’utilisateur reçoit le message que son mot de passe a été modifié.
   > [!NOTE]
   > Si le hachage de mot de passe de l’utilisateur est synchronisé à Azure AD à l’aide de la synchronisation de hachage de mot de passe, il se peut que la stratégie de mot de passe locale soit plus faible que la stratégie de mot de passe cloud. Dans ce cas, la stratégie locale est appliquée. Cela garantit que votre stratégie locale est appliquée dans le cloud, que vous utilisiez ou non la synchronisation ou la fédération de hachage de mot de passe pour fournir une authentification unique.

1. Si l’opération de définition du mot de passe échoue, un message d’erreur invite l’utilisateur à réessayer. L’opération peut échouer pour les raisons suivantes :
    * Le service était arrêté.
    * Le mot de passe sélectionné ne répondait pas aux stratégies de l’organisation.
    * Nous n’avons pas pu trouver l’utilisateur dans l’annuaire Active Directory local.

      Les messages d’erreur fournissent des conseils aux utilisateurs afin qu’ils tentent de résoudre leur problème sans intervention de l’administrateur.

## <a name="password-writeback-security"></a>Sécurité de réécriture du mot de passe

La réécriture du mot de passe est un service hautement sécurisé. Pour garantir la protection de vos informations, un modèle de sécurité à quatre niveaux est activé, comme décrit ci-dessous :

* **Relais de Service Bus propre au client**
   * Lorsque vous configurez le service, un relais Service Bus spécifique au client est défini et protégé par un mot de passe fort généré de manière aléatoire, auquel Microsoft n’a jamais accès.
* **Clé de chiffrement de mot de passe forte et verrouillée**
   * Une fois le relais Service Bus créé, une paire de clés symétriques fortes est créée, permettant de chiffrer le mot de passe lorsqu’il arrive sur le réseau. Cette clé réside uniquement dans le magasin de secrets de votre entreprise dans le cloud, lequel est fortement verrouillé et audité, comme n’importe quel mot de passe de l’annuaire.
* **Norme TLS (Transport Layer Security)**
   1. Lorsqu’une opération de réinitialisation ou de modification de mot de passe a lieu dans le cloud, le mot de passe en clair est chiffré avec votre clé publique.
   1. Le mot de passe chiffré est inséré dans un message HTTPS envoyé à votre relais Service Bus via un canal chiffré, à l’aide de certificats SSL Microsoft.
   1. Une fois le message arrivé dans Service Bus, votre agent local sort du mode veille et s’authentifie auprès de Service Bus en utilisant le mot de passe fort précédemment généré.
   1. L’agent local récupère le message chiffré et le déchiffre à l’aide de la clé privée.
   1. L’agent local tente ensuite de définir le mot de passe via l’API SetPassword AD DS. C’est cette étape qui permet d’appliquer votre stratégie de mot de passe Active Directory local (complexité, âge, historique et filtres) dans le cloud.
* **Stratégies d’expiration de message**
   * Si étant donné que votre service local est arrêté, le message reste dans Service Bus, il expire et est supprimé après quelques minutes. Le délai d’expiration et la suppression du message renforcent ainsi la sécurité.

### <a name="password-writeback-encryption-details"></a>Informations détaillées sur le chiffrement de la réécriture du mot de passe

Lorsqu’un utilisateur soumet une réinitialisation de mot de passe, cette demande transite via plusieurs étapes de chiffrement avant son arrivée dans votre environnement local. Ces étapes de chiffrement garantissent une sécurité et une fiabilité maximales. En voici le détail :

* **Étape 1 : Chiffrement du mot de passe avec la clé RSA de 2 048 bits**. Lorsqu’un utilisateur envoie un mot de passe en vue de sa réécriture en local, celui-ci est préalablement chiffré avec une clé RSA de 2 048 bits.
* **Étape 2 : Chiffrement de niveau package avec AES-GCM**. La méthode AES-GCM permet de chiffrer la totalité du package, avec le mot de passe et les métadonnées nécessaires. Ce chiffrement empêche quiconque ayant un accès direct au canal ServiceBus sous-jacent de visualiser ou compromettre le contenu.
* **Étape 3 : Toutes les communications passent par une connexion TLS/SSL**. Toutes les communications avec ServiceBus transitent par un canal SSL/TLS. Ce chiffrement protège le contenu contre les tiers non autorisés.
* **Renouvellement automatique des clés tous les 6 mois**. Tous les 6 mois ou chaque fois que la réécriture du mot de passe est désactivée puis réactivée sur Azure AD Connect, nous renouvelons automatiquement toutes les clés afin d’assurer la sécurité et la protection maximales du service.

### <a name="password-writeback-bandwidth-usage"></a>Utilisation de la bande passante de la réécriture du mot de passe

L’écriture différée des mots de passe est un service à bande passante faible qui renvoie les demandes à l’agent local, uniquement dans les circonstances suivantes :

* Deux messages sont envoyés lors de l’activation ou de la désactivation de la fonctionnalité via Azure AD Connect.
* Un message est envoyé une fois toutes les cinq minutes comme une pulsation du service tant que le service est en cours d’exécution.
* Deux messages sont envoyés à chaque fois qu’un nouveau mot de passe est proposé :
   * Le premier message est une requête visant à effectuer l’opération.
   * Le deuxième message contient le résultat de l’opération et est envoyé dans les circonstances suivantes :
      * Chaque fois qu’un nouveau mot de passe est soumis pendant une réinitialisation de mot de passe libre-service par l’utilisateur.
      * Chaque fois qu’un nouveau mot de passe est soumis pendant une opération de modification de mot de passe par l’utilisateur.
      * Chaque fois qu’un nouveau mot de passe est soumis au cours d’une réinitialisation des mots de passe des utilisateurs réalisée par un administrateur (depuis les portails d’administration Azure uniquement).

#### <a name="message-size-and-bandwidth-considerations"></a>Considérations relatives à la taille des messages et à la bande passante

La taille de chaque message décrit précédemment est généralement inférieure à 1 Ko. Même sous des charges extrêmes, le service d’écriture différée des mots de passe lui-même utilise quelques kilobits par seconde de bande passante. Étant donné que chaque message est envoyé en temps réel, uniquement lorsqu’une opération de mise à jour du mot de passe le requiert, et étant donné que la taille du message est très limitée, l’utilisation de la bande passante de la fonctionnalité d’écriture différée est effectivement trop petite pour produire un impact mesurable.

## <a name="supported-writeback-operations"></a>Opérations de réécriture prises en charge

Les mots de passe sont réécrits dans les situations suivantes :

* **Opérations de l’utilisateur final prises en charge**
   * Toute modification volontaire en libre-service du mot de passe de l’utilisateur final
   * Toute modification forcée en libre-service du mot de passe de l’utilisateur final (par exemple, expiration du mot de passe)
   * Toute réinitialisation du mot de passe en libre-service de l’utilisateur final émanant du [portail de réinitialisation des mots de passe](https://passwordreset.microsoftonline.com)
* **Opérations de l’administrateur prises en charge**
   * Toute modification volontaire en libre-service du mot de passe de l’administrateur
   * Toute modification forcée du mot de passe en libre-service de l’administrateur (par exemple, expiration du mot de passe)
   * Toute réinitialisation du mot de passe en libre-service de l’administrateur émanant du [portail de réinitialisation du mot de passe](https://passwordreset.microsoftonline.com)
   * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [portail Azure](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Opérations de réécriture non prises en charge

Les mots de passe ne sont *pas* réécrits dans les situations suivantes :

* **Opérations de l’utilisateur final non prises en charge**
   * Tout utilisateur final réinitialisant son mot de passe à l’aide de PowerShell (version 1 ou version 2) ou l’API Microsoft Graph
* **Opérations de l’administrateur non prises en charge**
   * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur à l’aide de PowerShell (version 1 ou version 2) ou l’API Microsoft Graph
   * Toute réinitialisation du mot de passe de l’utilisateur final réalisée par l’administrateur depuis le [Centre d’administration Microsoft 365](https://admin.microsoft.com)

> [!WARNING]
> L’utilisation de la case à cocher « L’utilisateur doit changer de mot de passe à la prochaine ouverture de session » dans les outils d’administration d’Active Directory locaux, comme Utilisateurs et ordinateurs Active Directory ou le Centre d’administration Active Directory, est prise en charge comme fonctionnalité en préversion d’Azure AD Connect. Pour plus d’informations, consultez l’article [Implémenter la synchronisation de hachage du mot de passe avec la synchronisation Azure AD Connect](../hybrid/how-to-connect-password-hash-synchronization.md).

## <a name="next-steps"></a>Étapes suivantes

Activer la réécriture du mot de passe à l’aide du tutoriel : [Activation de la réécriture du mot de passe](tutorial-enable-writeback.md)
