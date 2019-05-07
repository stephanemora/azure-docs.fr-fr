---
title: Référentiel sécurisé client pour Microsoft Azure
description: Vue d’ensemble technique de référentiel sécurisé client pour Microsoft Azure, ce qui permet de contrôler des accès au fournisseur de cloud Microsoft peut s’avérer nécessaire accéder aux données client.
author: cabailey
ms.service: security
ms.topic: article
ms.author: cabailey
manager: barbkess
ms.date: 05/07/2019
ms.openlocfilehash: 468e392cd2c45d79cbb24f8d737a6e83fbcd2725
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079269"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Référentiel sécurisé client pour Microsoft Azure

> [!NOTE]
> Pour utiliser cette fonctionnalité, votre organisation doit disposer d’un [plan de support Azure](https://azure.microsoft.com/support/plans/) avec un niveau minimal de **développeur**.

Référentiel sécurisé client pour Microsoft Azure fournit une interface pour les clients vérifier et approuver ou refuser les demandes d’accès aux données client. Il est utilisé dans les cas où un ingénieur Microsoft doit accéder aux données client pendant une demande de support.

Cet article décrit comment les demandes au référentiel sécurisé client sont initiée par suivies et stockées pour les audits et des révisions ultérieures.

Référentiel sécurisé client est désormais à la disposition générale et actuellement activée pour l’accès Bureau à distance aux machines virtuelles.

## <a name="workflow"></a>Workflow

Les étapes suivantes décrivent un flux de travail typique pour une demande au référentiel sécurisé client.

1. Une personne dans votre organisation a un problème avec sa charge de travail Azure.

2. Une fois que cette personne permet de résoudre le problème, mais ne peut pas résoudre le problème, ils ouvrent un ticket de support à partir de la [Azure portal](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Le ticket est affecté à un ingénieur du Support client Azure.

3. Un ingénieur du Support technique Azure examine la demande de service et détermine les étapes suivantes pour résoudre le problème.

4. Si l’ingénieur du support ne peut pas résoudre le problème à l’aide des données de télémétrie et d’outils standard, l’étape suivante consiste à demander des autorisations élevées à l’aide d’un service d’accès juste à temps (JIT). Cette demande peut être à partir de l’ingénieur du support d’origine. Ou bien, il peut être d’un ingénieur différentes, car il est affecté à l’équipe Azure DevOps.

5. Après l’accès à la demande est soumise par l’ingénieur Azure, juste-à-temps évalue la demande en prenant en compte des facteurs tels que :
    - L’étendue de la ressource
    - Si le demandeur est une identité isolée ou à l’aide de l’authentification multifacteur
    - Niveaux d’autorisation
    
    Selon la règle JIT, cette demande peut également inclure une approbation à partir des approbateurs Microsoft interne. Par exemple, l’approbateur peut être le responsable du support client ou le Gestionnaire de DevOps.

6. Lorsque la demande nécessite un accès direct aux données client, une demande au référentiel sécurisé client est lancée. Par exemple, accès Bureau à distance à la machine virtuelle d’un client.
    
    La demande est désormais dans un **client averti** état, en attente d’approbation du client avant d’accorder l’accès.

7. À l’organisation du client, l’utilisateur qui a le [rôle propriétaire](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-rbac-roles) pour Azure abonnement reçoit un message électronique à partir de Microsoft, pour les informer de la demande d’accès en attente. Pour les demandes au référentiel sécurisé client, cette personne est l’approbateur désigné.
    
    Exemple d’e-mail :
    
    ![Azure référentiel sécurisé client - notification par courrier électronique](./media/azure-customer-lockbox/customer-lockbox-email-notification.png)

8. La notification par courrier électronique fournit un lien vers le **au référentiel sécurisé client** panneau dans le portail Azure. En utilisant ce lien, l’approbateur désigné se connecte au portail Azure pour afficher des demandes ayant leur organisation pour le référentiel sécurisé client en attente :
    
    ![Azure référentiel sécurisé client - page d’accueil](./media/azure-customer-lockbox/customer-lockbox-landing-page.png)
    
   La requête reste dans la file d’attente du client pendant quatre jours. Après cette date, la demande d’accès expire automatiquement et aucun accès n’est accordé aux ingénieurs de Microsoft.

9. Pour obtenir les détails de la demande en attente, l’approbateur désigné peut sélectionner la demande de zone de sécurité à partir de **demandes en attente**:
    
    ![Référentiel sécurisé de client Azure - afficher la demande en attente](./media/azure-customer-lockbox/customer-lockbox-pending-requests.png)

10. L’approbateur désigné peut également sélectionner le **ID de demande de SERVICE** pour afficher la demande de ticket de support a été créée par l’utilisateur d’origine. Ces informations fournissent le contexte pour la raison pour laquelle le Support Microsoft est engagé et l’historique du problème signalé. Par exemple : 
    
    ![Référentiel sécurisé de client Azure - afficher la demande de ticket de support](./media/azure-customer-lockbox/customer-lockbox-support-ticket.png)

11. Après avoir examiné la demande, l’approbateur désigné sélectionne **approuver** ou **Deny**:
    
    ![Azure au référentiel sécurisé client - Sélectionnez approuver ou refuser](./media/azure-customer-lockbox/customer-lockbox-approval.png)
    
    À la suite de la sélection :
    - **Approuver**:  L’accès est accordé à l’ingénieur de Microsoft. L’accès est accordé pour une période par défaut de huit heures.
    - **Refuser**: La demande d’accès avec élévation de privilèges par l’ingénieur de Microsoft est rejetée et aucune action supplémentaire n’est nécessaire.

Pour des fins d’audit, les actions effectuées dans ce flux de travail sont enregistrées dans [les journaux de demandes au référentiel sécurisé client](#auditing-logs).

## <a name="auditing-logs"></a>Journaux d’activité d’audit

Journaux de référentiel sécurisé client sont stockés dans les journaux d’activité. Dans le portail Azure, sélectionnez **journaux d’activité** pour afficher les informations d’audit relatives aux demandes de référentiel sécurisé client. Vous pouvez filtrer pour des actions spécifiques, telles que :
- **Refuser la demande de la zone de sécurité**
- **Créer une demande de zone de sécurité**
- **Approuver la demande de la zone de sécurité**
- **Expiration de la demande de la zone de sécurité**

Par exemple :

![Azure référentiel sécurisé client - journaux d’activité](./media/azure-customer-lockbox/customer-lockbox-activitylogs.png)

## <a name="supported-services-and-scenarios"></a>Scénarios et services pris en charge

Les services et les scénarios suivants sont actuellement en général la disponibilité pour le référentiel sécurisé client.

### <a name="remote-desktop-access-to-virtual-machines"></a>Accès Bureau à distance aux machines virtuelles

Référentiel sécurisé client est actuellement activé pour les demandes d’accès Bureau à distance aux machines virtuelles. Les charges de travail suivants sont pris en charge :
- Plateforme en tant que service (PaaS) - version 1
- Infrastructure en tant que service (IaaS) - Windows et Linux (Azure Resource Manager uniquement)
- Machines virtuelles identiques - Windows et Linux

> [!NOTE]
> Les instances IaaS Classic ne sont pas pris en charge par référentiel sécurisé client. Si vous avez des charges de travail exécutées sur des instances IaaS de classique, nous vous recommandons de que migrer les Classic aux modèles de déploiement Resource Manager. Pour obtenir des instructions, consultez [plateforme prise en charge la migration de ressources IaaS classic vers Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md).

#### <a name="detailed-audit-logs"></a>Journaux d’audit détaillés

Pour les scénarios qui impliquent l’accès Bureau à distance, vous pouvez utiliser les journaux des événements Windows pour passer en revue les actions effectuées par l’ingénieur de Microsoft. Envisagez d’utiliser Azure Security Center pour collecter vos journaux des événements et de copier les données dans votre espace de travail pour l’analyse. Pour plus d’informations, consultez [collecte des données dans Azure Security Center](../security-center/security-center-enable-data-collection.md).

## <a name="exclusions"></a>Exclusions

Demandes de référentiel sécurisé client ne sont pas déclenchés dans les scénarios de prise en charge d’ingénierie suivants :

- Un ingénieur Microsoft doit faire une activité qui se situe en dehors des procédures de fonctionnement standard. Par exemple, pour récupérer ou restaurer des services dans les scénarios inattendues ou imprévisibles.

- Un ingénieur Microsoft accède à la plateforme Azure dans le cadre de la résolution des problèmes et par inadvertance a accès aux données client. Par exemple, l’équipe de réseau Azure effectue la résolution des problèmes qui résulte dans une capture de paquets sur un périphérique réseau. Toutefois, si le client chiffré les données alors qu’il était en transit, l’ingénieur ne peut pas lire les données.

## <a name="next-steps"></a>Étapes suivantes

Référentiel sécurisé client est automatiquement disponible pour tous les clients qui ont un [plan de support Azure](https://azure.microsoft.com/support/plans/) avec un niveau minimal de **développeur**.

Lorsque vous avez un plan de support éligibles, aucune action n’est nécessaire pour permettre au référentiel sécurisé client. Demandes de référentiel sécurisé client sont initiées par un ingénieur Microsoft si cette action est nécessaire pour la progression d’un ticket de support est classé à partir d’une personne dans votre organisation.
