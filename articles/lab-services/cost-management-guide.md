---
title: Guide de gestion des coûts pour Azure Lab Services
description: Découvrez les différentes façons de visualiser les coûts des services Lab.
author: rbest
ms.author: rbest
ms.date: 08/16/2020
ms.topic: article
ms.openlocfilehash: 0aaa454df05cd8981b314abe238163caced7864c
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88604598"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestion des coûts pour Azure Lab Services

La gestion des coûts peut être divisée en deux domaines distincts : l’estimation des coûts et l’analyse des coûts.  L’estimation des coûts se produit lors de la configuration du laboratoire pour s’assurer que la structure initiale du laboratoire sera adaptée au budget attendu.  L’analyse des coûts se produit généralement à la fin du mois pour analyser les coûts et déterminer les actions nécessaires pour le mois suivant.

## <a name="estimate-the-lab-costs"></a>Estimer les coûts de laboratoire

Chaque tableau de bord de laboratoire dispose d’une section **Coûts et facturation** qui présente une estimation sommaire du coût que peut représenter le laboratoire pour le mois.  L’estimation des coûts résume les heures d’utilisation avec le nombre maximal d’utilisateurs par le coût horaire estimé.  Pour obtenir l’estimation la plus précise possible, paramétrez le laboratoire, y compris la [planification](how-to-create-schedules.md), et le tableau de bord reflétera le coût estimé.  

Cette estimation peut ne pas intégrer tous les coûts encourus : certaines ressources ne sont pas incluses.  Le coût de préparation du modèle n’est pas pris en compte dans l’estimation des coûts.  Le temps nécessaire à la création du modèle peut varier considérablement. Le coût d’exécution du modèle est le même que le coût global par heure du laboratoire. Tous les coûts de la [Galerie d’images partagées](how-to-use-shared-image-gallery.md) ne sont pas inclus dans le tableau de bord du labo, car une galerie peut être partagée entre plusieurs laboratoires.  Enfin, les heures correspondant au démarrage d’un ordinateur par le créateur du laboratoire sont exclues de cette estimation.

> [!div class="mx-imgBorder"]
> ![Estimation des coûts du tableau de bord](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyze-previous-months-usage"></a>Analyser l’utilisation des mois précédents

L’analyse des coûts permet d’examiner l’utilisation des mois précédents pour déterminer les ajustements à apporter au laboratoire.  La répartition des coûts par le passé est disponible dans l’[Analyse des coûts d’abonnement](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis).  Dans le Portail Azure, vous pouvez saisir « Abonnements » dans le champ de recherche en haut, puis sélectionner l’option Abonnements.  

> [!div class="mx-imgBorder"]
> ![Recherche d’un abonnement](./media/cost-management-guide/subscription-search.png)

Sélectionnez l’abonnement spécifique à examiner.

> [!div class="mx-imgBorder"]
> ![Sélection d'un abonnement](./media/cost-management-guide/subscription-select.png)

 Sélectionnez « Cost Analysis » dans le volet gauche sous **Cost Management**.

> [!div class="mx-imgBorder"]
> ![Analyse des coûts d’abonnement](./media/cost-management-guide/subscription-cost-analysis.png)

Ce tableau de bord permet une analyse détaillée des coûts, y compris l’export vers différents types de fichiers selon une planification.  Cost Management propose de nombreuses fonctionnalités pour plus d’informations, consultez [Présentation de la facturation de Cost Management](https://docs.microsoft.com/azure/cost-management-billing/cost-management-billing-overview)

Filtrage par type de ressource : `microsoft.labservices/labaccounts` affiche uniquement le coût associé aux services Lab.

## <a name="understand-the-usage"></a>Comprendre l’utilisation

Voici un exemple d’analyse des coûts.

> [!div class="mx-imgBorder"]
> ![Analyse des coûts d’abonnement](./media/cost-management-guide/cost-analysis.png)

Il y a six colonnes par défaut : Ressource, type de ressource, emplacement, nom de groupe de ressources, balises, coût.  La colonne Ressource contient les informations sur le compte de labo, le nom du labo et la machine virtuelle.  Les lignes avec le compte de labo/nom du labo/par défaut correspondent au coût du laboratoire, qui peut être consulté sur les deuxième et troisième lignes.  Les machines virtuelles utilisées auront un coût sous le compte du labo/nom du labo/nom par défaut/nom de la machine virtuelle.  Dans cet exemple, en ajoutant la première ligne à la deuxième, commençant toutes deux par « aaalab/dockerlab », vous obtenez le coût total du laboratoire « dockerlab » dans le compte de laboratoire « aaalab ».

Pour obtenir des informations sur la Galerie d’images partagées, remplacez le type de ressource par `Microsoft.Compute/Galleries`, ce qui vous donnera le coût global de la Galerie d’images.  Les galeries d’images partagées peuvent ne pas apparaître dans les coûts en fonction de l’emplacement de stockage de la galerie.

> [!NOTE]
> La galerie Shared Image Gallery n’est pas connectée au compte de laboratoire.  Cela signifie que plusieurs laboratoires peuvent utiliser la même image.

## <a name="separating-costs"></a>Séparation des coûts

Certaines universités ont utilisé le compte de labo et le groupe de ressources pour séparer les différentes classes.  Chaque classe aura son propre compte de labo et son propre groupe de ressources. Dans le volet analyse des coûts, ajoutez un filtre basé sur le nom du groupe de ressources avec le nom de groupe de ressources correspondant à la classe et seuls les coûts de cette classe seront visibles.  Cela permet une délimitation plus claire entre les différentes classes lors de la visualisation des coûts.  La fonctionnalité [d’export planifié](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) de l’analyse des coûts permet de télécharger les coûts de chaque classe dans des fichiers distincts.

## <a name="managing-costs"></a>Gestion des coûts

Selon le type de classe, il existe des moyens de gérer les coûts pour réduire ceux correspondant au fonctionnement des machines virtuelles sans qu’un étudiant les utilise.

### <a name="maximize-cost-control-with-auto-shutdown-settings"></a>Optimiser le contrôle des coûts avec les paramètres d’arrêt automatique

Les fonctionnalités de contrôle des coûts par arrêt automatique vous permettent de prévenir de manière proactive le gaspillage des heures d’utilisation des machines virtuelles dans les labos. La combinaison des trois fonctionnalités suivantes d’arrêt et de déconnexion automatiques permet d’intercepter la plupart des cas où les utilisateurs laissent accidentellement leurs machines virtuelles en marche :

> [!div class="mx-imgBorder"]
> ![Analyse des coûts d’abonnement](./media/cost-management-guide/auto-shutdown-disconnect.png)

Ces paramètres peuvent être configurés au niveau du compte labo ainsi qu’au niveau du labo. Si les paramètres sont activés au niveau du compte labo, ils sont appliqués à tous les labos au sein du compte labo. Pour tous les nouveaux comptes labo, ces paramètres sont activés par défaut. 

#### <a name="details-about-auto-shutdown-settings"></a>Détails sur les paramètres d’arrêt automatique

* Déconnecter automatiquement les utilisateurs des machines virtuelles que le système d’exploitation juge inactives (Windows uniquement)

    > [!NOTE]
    > Ce paramètre est disponible uniquement pour les machines virtuelles Windows.

    Lorsque le paramètre est activé, l’utilisateur est déconnecté de toutes les machines du labo lorsque le système d’exploitation Windows considère que la session est inactive (y compris les modèles de machines virtuelles). La [définition de l’inactivité du système d’exploitation Windows](https://docs.microsoft.com/windows/win32/taskschd/task-idle-conditions#detecting-the-idle-state) utilise deux critères : 

    * Absence de l’utilisateur : aucune entrée au clavier ou à la souris.
    * Absence de consommation des ressources : tous les processeurs et tous les disques sont restés inactifs pendant un certain pourcentage de temps.

    Les utilisateurs verront un message similaire à celui-ci dans la machine virtuelle avant d’être déconnectés : 

    > [!div class="mx-imgBorder"]
    > ![Analyse des coûts d’abonnement](./media/cost-management-guide/idle-timer-expired.png)
    
    La machine virtuelle est toujours en cours d’exécution lorsque l’utilisateur est déconnecté. Si l’utilisateur se reconnecte à la machine virtuelle en se connectant, les fenêtres ou les fichiers qui étaient ouverts ou le travail non sauvegardé avant la déconnexion seront toujours là. Dans cet état, parce que la machine virtuelle fonctionne, elle est toujours comptabilisée comme active et accumule les coûts. 
    
    Pour arrêter automatiquement les machines virtuelles Windows inactives qui sont déconnectées, utilisez la combinaison des paramètres **Déconnecter les utilisateurs quand les machines virtuelles sont inactives** et **Arrêter les machines virtuelles quand les utilisateurs se déconnectent**.

    Par exemple, si vous configurez les paramètres comme suit :
    
    * Déconnecter les utilisateurs quand les machines virtuelles sont inactives : 15 minutes après la détection de l’état d’inactivité.
    * Arrêter les machines virtuelles quand les utilisateurs se déconnectent : 5 minutes après la déconnexion de l’utilisateur.
    
    Les machines virtuelles Windows s’arrêtent automatiquement 20 minutes après que l’utilisateur cesse de les utiliser. 
    
    > [!div class="mx-imgBorder"]
    > ![Analyse des coûts d’abonnement](./media/cost-management-guide/vm-idle-diagram.png)
* Arrêter automatiquement les machines virtuelles quand les utilisateurs se déconnectent (Windows et Linux)
    
    Ce paramètre prend en charge les machines virtuelles Windows et Linux. Lorsque ce paramètre est activé, l’arrêt automatique se produit dans les cas suivants :
    
    * Pour Windows, la connexion Bureau à distance (RDP) est déconnectée.
    * Pour Linux, la connexion SSH est déconnectée.
    
    > [!NOTE]
    > Seules [des distributions et versions spécifiques de Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/diagnostics-linux#supported-linux-distributions) sont prises en charge.
    
    Vous pouvez spécifier la durée pendant laquelle les machines virtuelles doivent attendre que l’utilisateur se reconnecte avant de s’arrêter automatiquement. 
* Arrêter automatiquement les machines virtuelles démarrées, mais auxquelles les utilisateurs ne se connectent pas
     
    Dans un labo, un utilisateur peut démarrer une machine virtuelle sans jamais s’y connecter. Par exemple :
    
    * Une planification dans le labo permet de démarrer toutes les machines virtuelles pour une session de cours, mais certains étudiants ne se présentent pas et ne se connectent pas à leurs machines.  
    * Un utilisateur démarre une machine virtuelle, mais oublie de se connecter. 
    
    Le paramètre « Arrêter les machines virtuelles quand les utilisateurs ne se connectent pas » détecte ces cas et arrête automatiquement les machines virtuelles.  
    
Pour plus d’informations sur la configuration et l’activation de l’arrêt automatique des machines virtuelles lors de la déconnexion, consultez les articles suivants :

* [Configurer l’arrêt automatique des machines virtuelles pour un compte labo](how-to-configure-lab-accounts.md)
* [Configurer l’arrêt automatique des machines virtuelles pour un labo](how-to-enable-shutdown-disconnect.md)

### <a name="quota-vs-scheduled-time"></a>Quota de temps ou heure planifiée

Comprendre la différence entre [quota de temps](classroom-labs-concepts.md#quota) et [heure planifiée](classroom-labs-concepts.md#schedules) permettra au propriétaire du laboratoire de le configurer pour qu’il réponde mieux aux besoins du professeur et des étudiants.  L’heure planifiée est une heure fixe à laquelle toutes les machines virtuelles des étudiants ont été démarrées et sont disponibles pour la connexion.  La planification est couramment utilisée pour les situations où tous les étudiants ont leur propre machine virtuelle et suivent les instructions du professeur à une heure définie de la journée, comme les heures de classe.  L’inconvénient est que toutes les machines virtuelles des étudiants sont démarrées et qu’elles engendrent des coûts, même si un étudiant ne s’y connecte pas.  Le quota de temps correspond au temps alloué à chaque étudiant pour l’utiliser à sa discrétion et sert souvent aux sessions d’étude autonome. Les machines virtuelles ne sont pas démarrées tant que l’étudiant ne les démarre pas.  

Un laboratoire peut utiliser le quota de temps, l’heure planifiée ou une combinaison des deux. Si une classe n’a pas besoin de l’heure planifiée, utilisez uniquement le quota de temps pour une utilisation optimale des machines virtuelles.

### <a name="scheduled-event---stop-only"></a>Événement planifié - arrêt uniquement

Dans la planification, vous pouvez ajouter un type d’événement arrêt uniquement, qui arrêtera tous les ordinateurs à un moment donné.  Certains propriétaires de laboratoires ont défini un événement d’arrêt uniquement tous les jours à minuit pour réduire le coût et l’utilisation du quota lorsqu’un étudiant oublie d’éteindre la machine virtuelle qu’il utilise.  L’inconvénient de ce type d’événement est que toutes les machines virtuelles seront arrêtées même si l’étudiant en utilise une.

### <a name="other-costs-related-to-labs"></a>Autres coûts liés aux laboratoires 

Il existe des coûts qui ne sont pas intégrés aux services Lab, mais qui peuvent être liés à un service Lab.  Une galerie d’images partagées peut être connectée à des laboratoires, mais ne s’affiche pas sous les coûts des services Lab et engendre des coûts.  Pour réduire les coûts globaux, vous devez supprimer toutes les images inutilisées de la galerie, car elles représentent un coût de stockage hérité.  Les laboratoires peuvent avoir des connexions à d’autres ressources Azure par un réseau virtuel (VNet) lors de la suppression d’un laboratoire. vous devez donc supprimer le réseau virtuel et les autres ressources.

## <a name="conclusion"></a>Conclusion

Nous espérons que les informations ci-dessus vous donneront une meilleure compréhension des coûts d’utilisation et de l’utilisation des outils pour réduire les coûts excessifs.
