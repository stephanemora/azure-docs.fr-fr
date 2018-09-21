---
title: Réviser les accès à une application des membres d’un groupe ou des utilisateurs avec Azure AD| Microsoft Docs
description: Découvrez comment réviser les accès des membres d’un groupe ou des utilisateurs ayant accès à une application dans Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance
ms.date: 05/02/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.openlocfilehash: 72b560aa742fb509b55fe5e27cb87b9a224bce7e
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/14/2018
ms.locfileid: "45606879"
---
# <a name="complete-an-access-review-of-members-of-a-group-or-users-access-to-an-application-in-azure-ad"></a>Effectuer une révision d’accès des membres d’un groupe ou des accès des utilisateurs à une application dans Azure AD

Les administrateurs peuvent utiliser Azure Active Directory (Azure AD) pour [créer une révision d’accès](create-access-review.md) des membres du groupe ou des utilisateurs affectés à une application. Azure AD envoie automatiquement aux réviseurs un e-mail les invitant à réviser l’accès. Si un utilisateur n’a pas reçu d’e-mail, vous pouvez lui envoyer les instructions décrites dans l’article [Effectuer votre propre révision d’accès](perform-access-review.md). (Sachez que les invités à qui a été attribué le rôle de réviseur mais qui n’ont pas accepté l’invitation ne recevront pas d’e-mails des révisions d’accès, car il est nécessaire d’avoir accepté l’invitation pour pouvoir commencer la révision.) Si la période de révision d’accès est terminée ou que l’administrateur a arrêté la révision d’accès, suivez les étapes décrites dans cet article pour voir et appliquer les résultats.

## <a name="view-an-access-review-in-the-azure-portal"></a>Afficher une révision d’accès dans le portail Azure

1. Accédez à la [page des révisions d’accès](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/), sélectionnez **Programmes**, puis choisissez le programme qui contient le contrôle de révision d’accès.

2. Cliquez sur **Gérer** et sélectionnez le contrôle de révision d’accès. S’il existe de nombreux contrôles dans le programme, vous pouvez appliquer un filtre pour n’afficher que les contrôles d’un type spécifique et les trier selon leur état. Vous pouvez également effectuer une recherche en saisissant le nom du contrôle de révision d’accès ou le nom complet de son auteur. 

## <a name="stop-a-review-that-hasnt-finished"></a>Arrêter une révision qui n’est pas terminée

Si la révision n’a pas encore atteint sa date de fin planifiée, un administrateur peut l’arrêter plus tôt en sélectionnant **Arrêter**. Une fois la révision arrêtée, il n’est plus possible de vérifier l’accès des utilisateurs. Vous ne pouvez pas redémarrer une révision ayant été arrêtée.

## <a name="apply-the-changes"></a>Appliquer les modifications 

Dès qu’une révision d’accès est terminée, soit parce qu’elle a atteint la date de fin, soit parce qu’un administrateur l’a arrêtée manuellement, si l’application automatique n’était pas configurée pour la révision, vous pouvez sélectionner **Appliquer** pour appliquer manuellement les modifications. Le résultat de la révision est implémenté via une mise à jour du groupe ou de l’application. Si l’accès d’un utilisateur a été refusé lors de la révision, lorsque l’administrateur sélectionne cette option, Azure AD supprime l’appartenance de cet utilisateur ou son assignation à l’application. 

Dès qu’une révision d’accès est terminée, si l’application automatique était configurée, l’état de la révision passe de Terminé à Appliqué après des états intermédiaires. Les éventuels utilisateurs dont l’accès est refusé devraient perdre leur appartenance au groupe de ressources ou leur affectation d’applications au bout de quelques minutes.

Lorsque l’application automatique est configurée, l’option **Appliquer** n’aura pas d’effet sur un groupe provenant d’un répertoire local ou un groupe dynamique. Si vous souhaitez modifier un groupe qui provient d’un répertoire local, téléchargez les résultats et appliquez ces modifications à la représentation du groupe dans ce répertoire.

## <a name="download-the-results-of-the-review"></a>Télécharger les résultats de la révision

Pour récupérer les résultats de la révision, sélectionnez **Approbations**, puis cliquez sur **Télécharger**. Le fichier CSV généré est consultable dans Excel ou dans d’autres programmes qui permettent d’ouvrir des fichiers CSV encodés UTF-8.

## <a name="optional-delete-a-review"></a>Facultatif : supprimer une révision
Si la révision ne vous intéresse plus, vous pouvez la supprimer. Sélectionnez **Supprimer** pour supprimer la révision d’Azure AD.

> [!IMPORTANT]
> Il n’y a pas d’avertissement avant la suppression. Par conséquent, assurez-vous de bien vouloir supprimer cette révision.
> 
> 

## <a name="next-steps"></a>Étapes suivantes

- [Manage user access with Azure AD access reviews](manage-user-access-with-access-reviews.md) (Gérer l’accès des utilisateurs avec les révisions d’accès Azure AD)
- [Manage guest access with Azure AD access reviews](manage-guest-access-with-access-reviews.md) (Gérer l’accès des invités avec les révisions d’accès Azure AD)
- [Manage programs and controls for Azure AD access reviews](manage-programs-controls.md) (Gérer les programmes et les contrôles pour la révision d’accès Azure AD)
- [Create an access review for members of a group or access to an application](create-access-review.md) (Créer une révision de l’accès des membres d’un groupe ou de l’accès à une application)
- [Create an access review of users in an Azure AD administrative role](../privileged-identity-management/pim-how-to-start-security-review.md) (Créer une révision d’accès pour les utilisateurs dans un rôle administratif d’Azure AD)
