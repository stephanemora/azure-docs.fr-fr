---
title: Guide de gestion des coûts pour Azure Lab Services
description: Découvrez les différentes façons de visualiser les coûts des services Lab.
author: rbest
ms.author: rbest
ms.date: 06/26/2020
ms.topic: article
ms.openlocfilehash: fbbaf4a3646260fc09467e214b82fd0213415635
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445302"
---
# <a name="cost-management-for-azure-lab-services"></a>Gestion des coûts pour Azure Lab Services

La gestion des coûts peut être divisée en deux domaines distincts : l’estimation des coûts et l’analyse des coûts.  L’estimation des coûts se produit lors de la configuration du laboratoire pour s’assurer que la structure initiale du laboratoire sera adaptée au budget attendu.  L’analyse des coûts se produit généralement à la fin du mois pour analyser les coûts et déterminer les actions nécessaires pour le mois suivant.

## <a name="estimating-the-lab-costs"></a>Estimation des coûts de laboratoire

Chaque tableau de bord de laboratoire dispose d’une section **Coûts et facturation** qui présente une estimation sommaire du coût que peut représenter le laboratoire pour le mois.  L’estimation des coûts résume les heures d’utilisation avec le nombre maximal d’utilisateurs par le coût horaire estimé.  Pour obtenir l’estimation la plus précise possible, paramétrez le laboratoire, y compris la [planification](how-to-create-schedules.md), et le tableau de bord reflétera le coût estimé.  

Cette estimation peut ne pas intégrer tous les coûts encourus : certaines ressources ne sont pas incluses.  Le coût de préparation du modèle n’est pas pris en compte dans l’estimation des coûts.  Le temps nécessaire à la création du modèle peut varier considérablement. Le coût d’exécution du modèle est le même que le coût global par heure du laboratoire. Tous les coûts de la [Galerie d’images partagées](how-to-use-shared-image-gallery.md) ne sont pas inclus dans le tableau de bord du labo, car une galerie peut être partagée entre plusieurs laboratoires.  Enfin, les heures correspondant au démarrage d’un ordinateur par le créateur du laboratoire sont exclues de cette estimation.

> [!div class="mx-imgBorder"]
> ![Estimation des coûts du tableau de bord](./media/cost-management-guide/dashboard-cost-estimation.png)

## <a name="analyzing-previous-months-usage"></a>Analyser l’utilisation des mois précédents

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

## <a name="understanding-the-usage"></a>Compréhension de l’utilisation

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

### <a name="auto-shutdown-on-disconnect"></a>Arrêt automatique lors de la déconnexion

Lors de la création du laboratoire, le propriétaire du laboratoire peut définir les machines virtuelles du laboratoire pour qu’elles [s’arrêtent lorsque la connexion RDP à la machine virtuelle est interrompue](how-to-enable-shutdown-disconnect.md).  Ce paramètre permet d’éviter le scénario dans lequel l’étudiant se déconnecte mais oublie d’arrêter la machine virtuelle.

### <a name="quota-vs-scheduled-time"></a>Quota de temps ou heure planifiée

Comprendre la différence entre [quota de temps](classroom-labs-concepts.md#quota) et [heure planifiée](classroom-labs-concepts.md#schedules) permettra au propriétaire du laboratoire de le configurer pour qu’il réponde mieux aux besoins du professeur et des étudiants.  L’heure planifiée est une heure fixe à laquelle toutes les machines virtuelles des étudiants ont été démarrées et sont disponibles pour la connexion.  La planification est couramment utilisée pour les situations où tous les étudiants ont leur propre machine virtuelle et suivent les instructions du professeur à une heure définie de la journée, comme les heures de classe.  L’inconvénient est que toutes les machines virtuelles des étudiants sont démarrées et qu’elles engendrent des coûts, même si un étudiant ne s’y connecte pas.  Le quota de temps correspond au temps alloué à chaque étudiant pour l’utiliser à sa discrétion et sert souvent aux sessions d’étude autonome. Les machines virtuelles ne sont pas démarrées tant que l’étudiant ne les démarre pas.  

Un laboratoire peut utiliser le quota de temps, l’heure planifiée ou une combinaison des deux. Si une classe n’a pas besoin de l’heure planifiée, utilisez uniquement le quota de temps pour une utilisation optimale des machines virtuelles.

### <a name="scheduled-event---stop-only"></a>Événement planifié - arrêt uniquement

Dans la planification, vous pouvez ajouter un type d’événement arrêt uniquement, qui arrêtera tous les ordinateurs à un moment donné.  Certains propriétaires de laboratoires ont défini un événement d’arrêt uniquement tous les jours à minuit pour réduire le coût et l’utilisation du quota lorsqu’un étudiant oublie d’éteindre la machine virtuelle qu’il utilise.  L’inconvénient de ce type d’événement est que toutes les machines virtuelles seront arrêtées même si l’étudiant en utilise une.

### <a name="other-costs-related-to-labs"></a>Autres coûts liés aux laboratoires 

Il existe des coûts qui ne sont pas intégrés aux services Lab, mais qui peuvent être liés à un service Lab.  Une galerie d’images partagées peut être connectée à des laboratoires, mais ne s’affiche pas sous les coûts des services Lab et engendre des coûts.  Pour réduire les coûts globaux, vous devez supprimer toutes les images inutilisées de la galerie, car elles représentent un coût de stockage hérité.  Les laboratoires peuvent avoir des connexions à d’autres ressources Azure par un réseau virtuel (VNet) lors de la suppression d’un laboratoire. vous devez donc supprimer le réseau virtuel et les autres ressources.

## <a name="conclusion"></a>Conclusion

Nous espérons que les informations ci-dessus vous donneront une meilleure compréhension des coûts d’utilisation et de l’utilisation des outils pour réduire les coûts excessifs.
