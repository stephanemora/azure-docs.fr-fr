---
title: Environnements de test, limitation et limites de service
titleSuffix: Microsoft identity platform
description: Découvrez les limitations et les limites de service à prendre en compte lors du déploiement d’un environnement de test Azure Active Directory et du test d’une application intégrée à la plateforme d’identités Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2021
ms.author: ryanwi
ms.reviewer: arcrowe
ms.openlocfilehash: f0594994593dbb9505ca7fea24a11a5c5bc7a4eb
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262790"
---
# <a name="throttling-and-service-limits-to-consider-for-testing"></a>Limitation et limites de service à prendre en compte pour les tests
En tant que développeur, vous souhaitez tester votre application avant de la mettre en production. Lorsque vous testez des applications protégées par la plateforme d’identités Microsoft, vous devez configurer un environnement Azure Active Directory (Azure AD) et un locataire à utiliser pour le test.  

Les applications qui s’intègrent à la plateforme d’identités Microsoft requièrent la création et la gestion d’objets d’annuaire (comme des inscriptions d’applications, des principaux de service, des groupes et des utilisateurs) dans un locataire Azure AD.  Les paramètres de client de production qui affectent le comportement de votre application doivent être répliqués dans le locataire de test. Remplissez votre locataire de test avec l’accès conditionnel, l’octroi d’autorisations, le mappage de revendications, la durée de vie des jetons et les stratégies d’émission de jeton nécessaires. Votre application peut également utiliser des ressources Azure comme le calcul ou le stockage, qui doivent être ajoutées à l’environnement de test. Votre environnement de test peut nécessiter beaucoup de ressources, en fonction de l’application à tester.

Afin de garantir une utilisation fiable des services par tous les clients, Azure AD et d’autres services limitent le nombre de ressources qui peuvent être créées par client et par locataire. Lorsque vous configurez un environnement de test et déployez des objets d’annuaire et des ressources Azure, vous pouvez atteindre ces limites et quotas de service.

Azure AD, Microsoft Graph et d’autres services Azure limitent également le nombre d’appels simultanés à un service ou limitent la quantité de charge de calcul par client afin d’éviter une utilisation excessive des ressources. Il s’agit d’une pratique connue sous le nom de limitation, et elle garantit que les services Azure peuvent gérer l’utilisation et les demandes entrantes sans interruption de service. La limitation peut se produire au niveau de l’application, du locataire ou de l’ensemble du service. La limitation se produit généralement lorsqu’une application a un grand nombre de requêtes dans ou entre des locataires.  Au moment de l’exécution, votre application peut lire ou mettre à jour les objets d’annuaire Azure AD par le biais de Microsoft Graph dans le cadre de sa logique métier. Par exemple, lire ou définir des attributs utilisateur, mettre à jour le calendrier d’un utilisateur ou envoyer des e-mails pour le compte de l’utilisateur.  Lors de son exécution, votre application peut également déployer, accéder à, mettre à jour et supprimer des ressources Azure. Pendant le test, votre application peut atteindre ces limitations au moment de l’exécution et les limites de service mentionnées précédemment lors du déploiement de ressources ou d’objets d’annuaire.

## <a name="azure-ad-service-limits-relevant-to-testing"></a>Limites de service Azure AD applicables au test
Vous trouverez [ici](../enterprise-users/directory-service-limits-restrictions.md) les contraintes générales relatives à l’utilisation d’Azure AD et les limites du service.  Pour plus d’informations générales sur les abonnements Azure et limites, quotas et contraintes de service, consultez [ceci](../../azure-resource-manager/management/azure-subscription-service-limits.md).

Le tableau suivant répertorie limites du service Azure AD à prendre en compte lors de la configuration d’un environnement de test ou de l’exécution de tests. 

| Category         | Limite   |
|-------------------|----------------|
| Locataires | Un même utilisateur peut créer un maximum de 200 annuaires.|
| Ressources | <ul><li>L’édition gratuite d’Azure Active Directory permet de créer par défaut 50 000 ressources Azure AD au maximum dans un même locataire. Si vous avez au moins un domaine vérifié, le quota de service Azure AD par défaut est étendu à 300 000 ressources Azure AD. Le quota du service Azure AD pour les organisations créées par inscription en libre-service reste à 50 000 ressources Azure AD même après que vous avez effectué une prise de contrôle de l’administration interne et que l’organisation est convertie en locataire managé avec au moins un domaine vérifié. Cette limite de service n’est pas liée à la limite de niveau tarifaire de 500 000 ressources sur la page de tarification Azure AD. Pour aller au-delà du quota par défaut, vous devez contacter le Support Microsoft.</li><li>Un utilisateur non-administrateur peut créer jusqu’à 250 ressources Azure AD. Les ressources actives et les ressources supprimées qui peuvent être restaurées sont comptabilisées dans ce quota. Seules les ressources Azure AD supprimées il y a moins de 30 jours peuvent être restaurées. Les ressources Azure AD supprimées qui ne peuvent plus être restaurées comptent pour un quart dans ce quota pendant 30 jours. Si vos développeurs sont susceptibles de dépasser régulièrement ce quota dans le cadre de leurs tâches habituelles, vous pouvez créer et attribuer un rôle personnalisé avec l’autorisation de créer un nombre illimité d’inscriptions d’applications.</li></ul>|
| Applications| <ul><li>Un utilisateur, un groupe ou un principal de service peut avoir un maximum de 1 500 attributions de rôles d’application.</li><li>Un utilisateur peut avoir au maximum 48 applications pour lesquelles les informations d’identification de nom d’utilisateur et de mot de passe sont configurées.</li></ul>|
| Manifeste d’application| Il est possible d’ajouter au maximum 1 200 entrées dans le manifeste de l’application. |
| Groupes |  <ul><li>Un utilisateur non administrateur peut créer jusqu’à 250 groupes dans une organisation Azure AD. Tout administrateur Azure AD qui peut gérer des groupes dans l’organisation peut également créer un nombre illimité de groupes (jusqu’au nombre maximal d’objets Azure AD). Si vous attribuez à un utilisateur un rôle pour supprimer la limite, attribuez-lui un rôle intégré avec moins de privilèges, comme celui d’administrateur d’utilisateurs ou d’administrateur de groupes.</li><li>Une organisation Azure AD peut contenir jusqu’à 5000 groupes dynamiques.</li><li>Un maximum de 300 groupes assignables à un rôle peuvent être créés dans une seule organisation (locataire) Azure AD.</li><li>Il n’y a pas de limite au nombre de ressources Azure AD qui peuvent faire partie d’un même groupe.</li><li>Un utilisateur peut être membre de n’importe quel nombre de groupes.</li></ul>|
| Rôles et autorisations Azure AD | <ul><li>Vous pouvez créer un maximum de 30 rôles personnalisés Azure AD dans une organisation Azure AD.</li><li>Un maximum de 100 attributions de rôles personnalisées Azure AD pour un seul principal au niveau de l’étendue du locataire.</li><li>Un maximum de 100 attributions de rôles intégrées Azure AD pour un principal unique dans une étendue non locataire (telle qu’une unité administrative ou un objet Azure AD). Il n’existe aucune limite pour les attributions de rôles intégrées Azure AD au niveau de l’étendue du locataire.</li></ul>|


## <a name="throttling-limits-relevant-to-testing"></a>Limites applicables au test

Les limites de Microsoft Graph globales suivantes s’appliquent :

| Type de demande | Par application sur tous les locataires |
|-------------------|----------------|
| Type de demande | Par application sur tous les locataires |
| Quelconque | 2000 demandes par seconde| 

Le tableau suivant répertorie les limites d’Azure AD à prendre en compte lors de l’exécution de tests. La limitation est basée sur un algorithme de compartiment de jetons, qui fonctionne en ajoutant les coûts individuels des requêtes. La somme des coûts des requêtes est ensuite comparée aux limites prédéfinies. Seules les requêtes dépassant les limites sont restreintes. Pour plus d’informations sur les coûts de requête, consultez [Limites du service d’identité et d’accès](/graph/throttling#pattern). Vous trouverez d’autres limites spécifiques au service sur Microsoft Graph [ici](/graph/throttling#service-specific-limits).

| Type de limite | Quota d’unités de ressource | Quota d’écriture |
|-------------------|----------------|----------------|
| Paire application + locataire | S : 3500, M : 5000, L : 8000 par 10 secondes | 3000 par 2 minutes et 30 secondes |
| application | 150 000 par 20 secondes | 70 000 par 5 minutes |
| tenant | Non applicable | 18 000 par 5 minutes |

La limite des paires application + locataire varie en fonction du nombre d’utilisateurs pour lesquels les requêtes de locataire sont exécutées. Les tailles des locataires sont définies comme suit : S - moins de 50 utilisateurs, M - entre 50 et 500 utilisateurs, et L - au-delà de 500 utilisateurs.

## <a name="what-happens-when-a-throttling-limit-is-exceeded"></a>Que se passe-t-il lorsqu’une limite est dépassée ? 
Le comportement de limitation peut dépendre du type et du nombre de demandes. Par exemple, si vous avez un volume élevé de demandes, tous les types de demandes sont limités. Les seuils de limite varient en fonction du type de demande. Par conséquent, vous pouvez vous trouver dans une situation où les écritures sont limitées mais les lectures sont toujours autorisées.

Lorsque vous dépassez une limite, vous recevez le code d’état HTTP `429 Too many requests` et votre requête échoue. La réponse inclut la valeur d’en-tête `Retry-After` qui spécifie le nombre de secondes pendant lesquelles votre application doit attendre (ou rester en veille) avant d’envoyer la requête suivante. Relancez la requête.  Si vous envoyez une requête avant l’écoulement du temps d’attente avant une nouvelle tentative, votre requête n’est pas traitée et une autre valeur de nouvelle tentative est renvoyée. Si la demande échoue à nouveau avec un code d'erreur 429, vous êtes toujours limité. Continuez à utiliser le délai recommandé de `Retry-After` et réessayez la requête jusqu'à ce qu'elle aboutisse.

## <a name="next-steps"></a>Étapes suivantes
Découvrez comment [configurer un environnement de test](test-setup-environment.md).