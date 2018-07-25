---
title: Présentation des licences basées sur le groupe dans Azure AD | Microsoft Docs
description: Description des licences basées sur les groupes Azure Active Directory, de leur fonctionnement et des bonnes pratiques
services: active-directory
keywords: Gestion des licences Azure AD
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: fundamentals
ms.topic: overview
ms.workload: identity
ms.date: 06/13/2018
ms.author: lizross
ms.reviewer: krbain
ms.custom: it-pro
ms.openlocfilehash: 5f5aab6a8514636e8be09deb773748080871b9d4
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005229"
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Principes de base des licences basées sur les groupes dans Azure Active Directory

Les services cloud payants Microsoft, comme Office 365, Enterprise Mobility + Security, Dynamics 365 et d’autres produits similaires, requièrent des licences. Ces licences sont affectées à chaque utilisateur qui a besoin d’accéder à ces services. Pour gérer les licences, les administrateurs utilisent l’un des portails de gestion (Office ou Azure) et des applets de commande PowerShell. Azure Active Directory (Azure AD) est l’infrastructure de base qui prend en charge la gestion de tous les services cloud Microsoft. Azure AD stocke des informations sur les états d’affectation de licence pour les utilisateurs.

Jusqu’à présent, les licences ne pouvaient être affectées qu’au niveau de chaque utilisateur, ce qui pouvait rendre difficile la gestion à grande échelle. Par exemple, pour ajouter ou supprimer des licences utilisateur en raison d’évolutions de l’organisation, par exemple l’arrivée ou le départ d’utilisateurs dans l’organisation ou dans un service, l’administrateur devait souvent écrire un script PowerShell complexe. Ce script permet d’effectuer des appels individuels au service cloud.

Pour relever ces défis, Azure AD inclut maintenant une gestion des licences par groupe. Vous pouvez affecter une ou plusieurs licences de produits à un groupe. Azure AD permet de garantir que les licences sont affectées à tous les membres du groupe. Tous les nouveaux membres qui rejoignent le groupe se voient affecter les licences appropriées. Lorsqu’ils quittent le groupe, ces licences sont supprimées. Ceci élimine toute nécessité d’automatiser la gestion des licences avec PowerShell pour refléter les évolutions de la structure de l’organisation et des services utilisateur par utilisateur.

>[!Note]
>La gestion des licences par groupe est une fonctionnalité en préversion publique de Azure Active Directory (Azure AD), disponible avec un plan de licence Azure AD payante. Pour plus d’informations sur les préversions, consultez [Conditions d’utilisation supplémentaires pour les préversions de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="features"></a>Caractéristiques

Voici les principales fonctionnalités de la gestion des licences par groupe :

- Les licences peuvent être affectées à n’importe quel groupe de sécurité dans Azure AD. Les groupes de sécurité peuvent être synchronisés en local à l’aide d’Azure AD Connect. Vous pouvez également créer des groupes de sécurité directement dans Azure AD (ils sont également appelé groupes cloud purs) ou automatiquement avec la fonctionnalité de groupe dynamique d’Azure AD.

- Lorsqu’une licence de produit est affectée à un groupe, l’administrateur peut désactiver un ou plusieurs plans de services dans le produit. En règle générale, c’est le cas lorsque l’organisation n’est pas encore prête à utiliser un service inclus dans un produit. Par exemple, l’administrateur peut affecter Office 365 à un service, mais désactiver temporairement le service Yammer.

- Tous les services de cloud computing Microsoft nécessitant des licences au niveau des utilisateurs sont pris en charge. Cela comprend tous les produits Office 365, Enterprise Mobility + Security et Dynamics 365.

- Les licences basées sur les groupes ne sont pour le moment disponibles que sur le [Portail Azure](https://portal.azure.com). Si vous utilisez principalement d’autres portails de gestion pour la gestion des utilisateurs et groupes, tels que le portail Office 365, vous pouvez continuer à le faire. Vous devez toutefois utiliser le portail Azure pour gérer les licences au niveau du groupe.

- Azure AD gère automatiquement les modifications de licences qui résultent de modifications de l’appartenance aux groupes. En règle générale, les modifications de licence sont effectives quelques minutes après une modification d’appartenance.

- Un utilisateur peut être membre de plusieurs groupes dans lesquels des stratégies de licences sont spécifiées. Un utilisateur peut également disposer de licences affectées directement, en dehors de groupes. L’état utilisateur qui en résulte est une combinaison de toutes les licences de produits et services affectées.

- Dans certains cas, des licences ne peuvent pas être affectées à un utilisateur. Par exemple, les licences disponibles dans le client peuvent ne pas être suffisantes, ou des services conflictuels peuvent avoir été affectés simultanément. Les administrateurs ont accès aux informations sur les utilisateurs pour lesquels Azure AD n’a pas pu traiter entièrement les licences de groupes. Ils peuvent prendre des mesures correctives en fonction de ces informations.

- Dans la préversion publique, un abonnement d’évaluation ou payant pour les éditions Azure AD De base ou Premium est obligatoire dans le locataire pour utiliser la gestion des licences par groupe.

## <a name="your-feedback-is-welcome"></a>Vos commentaires sont les bienvenus.

Si vous avez des commentaires ou des demandes de fonctionnalités, partagez-les avec nous via le [forum d’administrateur Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=162510).

## <a name="next-steps"></a>Étapes suivantes

Pour plus d’informations sur d’autres scénarios de gestion des licences par le biais des licences basées sur les groupes, consultez :

* [Affectation de licences à un groupe dans Azure Active Directory](../users-groups-roles/licensing-groups-assign.md)
* [Identification et résolution des problèmes de licence pour un groupe dans Azure Active Directory](../users-groups-roles/licensing-groups-resolve-problems.md)
* [Migration des utilisateurs individuels sous licence vers les licences basées sur les groupes dans Azure Active Directory](../users-groups-roles/licensing-groups-migrate-users.md)
* [Autres scénarios de licences basées sur les groupes Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)
