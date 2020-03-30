---
title: Surveillance et gestion de session Azure Bastion | Microsoft Docs
description: Cet article explique comment sélectionner une session en cours et forcer sa déconnexion ou la supprimer.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: f56a724174b81966642b2870360014597436eade
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76981087"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Surveillance et gestion de session Azure Bastion

Une fois que le service Bastion approvisionné et déployé dans votre réseau virtuel, vous pouvez l’utiliser pour vous connecter sans problème à toute machine virtuelle dans ce réseau virtuel. Quand des utilisateurs se connectent à des charges de travail, Azure bastion permet de surveiller les sessions à distance et de prendre des mesures de gestion rapides. La surveillance de session Azure Bastion vous permet de vois quels utilisateurs sont connectés à quelles machines virtuelles. Elle indique l’adresse IP à partir de laquelle l’utilisateur est connecté, la durée pendant laquelle il a été connecté et le moment auquel il s’est connecté. L’expérience de gestion de session vous permet de sélectionner une session en cours ainsi que forcer sa déconnexion ou la supprimer afin d’en déconnecter l’utilisateur.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Surveiller des sessions à distance

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre ressource Azure Bastion, puis sélectionnez **Sessions** dans la page Azure Bastion.

   ![sessions](./media/session-monitoring/sessions.png)
2. Dans la page **Sessions**, vous pouvez voir les sessions à distance en cours sur le côté droit.

   ![afficher une session](./media/session-monitoring/view-session.png)
3. Sélectionnez **Actualiser** pour afficher la liste à jour des sessions à distance. Lorsque vous sélectionnez Actualiser, Azure bastion récupère les informations de surveillance les plus récentes et les actualise dans le portail.

   ![actualiser](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Activez le port 4443 pour le trafic entrant à partir du Gestionnaire de passerelle pour que la surveillance de session fonctionne.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Forcer la déconnexion d’une session à distance en cours ou la supprimer

Vous pouvez sélectionner un ensemble de sessions et forcer leur déconnexion. Les étapes suivantes montrent comment supprimer des sessions à distance :

1. Accédez à votre ressource Azure Bastion et sélectionnez **Sessions** dans la page Azure Bastion.

   ![naviguer](./media/session-monitoring/navigate.png)
2. Une fois l’option Sessions sélectionnée, la liste des sessions à distance s’affiche.

   ![afficher la liste des sessions](./media/session-monitoring/list.png)
3. Sélectionnez une session à distance et les trois ellipses situées à droite de la ligne de la session, puis choisissez **Supprimer**.

   ![delete](./media/session-monitoring/delete.png)
4. Lorsque vous sélectionnez Supprimer, la session à distance est déconnectée et l’utilisateur voit s’afficher un message « Vous avez été déconnecté » dans la session à distance.

   ![déconnecter](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Étapes suivantes

Lisez les [questions fréquentes sur Bastion](bastion-faq.md).