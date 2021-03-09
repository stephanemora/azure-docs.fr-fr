---
title: Afficher l’historique des journaux des tâches web
description: Affichez l’historique des journaux des travaux ayant échoué et réussi.
author: ggailey777
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 4b4e421458438aecc7c08e397f1fc919a8cc2225
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101743180"
---
# <a name="view-webjob-history-in-the-azure-portal"></a>Afficher l’historique des tâches web dans le portail Azure

Affichez l’historique des journaux des travaux ayant échoué et réussi.

1. Choisissez la tâche web dont vous souhaitez afficher l’historique, puis sélectionnez le bouton **Journaux d’activité**.

    ![Bouton Journaux d’activité](./media/web-sites-create-web-jobs/wjbladelogslink.png)

1. À la page **Détails de la tâche web**, sélectionnez une heure pour afficher les détails d’une exécution.

    ![Détails de la tâche web](./media/web-sites-create-web-jobs/webjobdetails.png)

1. À la page **WebJob Run Details**, sélectionnez **Activer/désactiver la sortie** pour afficher le texte du contenu du journal.

    ![Détails d'exécution de la tâche WebJob](./media/web-sites-create-web-jobs/webjobrundetails.png)

    Pour afficher le texte de sortie dans une nouvelle fenêtre de navigateur, sélectionnez **télécharger** . Pour télécharger le texte, cliquez avec le bouton droit sur **télécharger** et utilisez les options de votre navigateur pour enregistrer le contenu du fichier.

1. Sélectionnez le lien de navigation **WebJobs** en haut de la page pour accéder à une liste de tâches web.

    ![Barre de navigation de la tâche web](./media/web-sites-create-web-jobs/breadcrumb.png)

    ![Liste des tâches Web dans le tableau de bord d’historique](./media/web-sites-create-web-jobs/webjobslist.png)

## <a name="next-steps"></a>Étapes suivantes

* Utiliser le [SDK WebJobs](https://github.com/Azure/azure-webjobs-sdk/wiki) pour simplifier de nombreuses tâches de programmation

* Apprendre à [développer et déployer des tâches web avec Visual Studio](webjobs-dotnet-deploy-vs.md)