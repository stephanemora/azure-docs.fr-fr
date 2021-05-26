---
title: Customer Lockbox pour Microsoft Azure
description: Vue d’ensemble technique de Customer Lockbox pour Microsoft Azure, qui permet de contrôler les accès de fournisseur cloud lorsque Microsoft doit accéder aux données client.
author: TerryLanfear
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 05/12/2021
ms.openlocfilehash: 2d115fb0c4cd068c8b7cdc36a0a17d3e5a0827c9
ms.sourcegitcommit: 1ee13b62c094a550961498b7a52d0d9f0ae6d9c0
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 05/12/2021
ms.locfileid: "109839578"
---
# <a name="customer-lockbox-for-microsoft-azure"></a>Customer Lockbox pour Microsoft Azure

> [!NOTE]
> Pour utiliser cette fonctionnalité, votre organisation doit disposer d’un [plan de support Azure](https://azure.microsoft.com/support/plans/) avec un niveau minimal de **développeur**.

La plupart des opérations, le support et le dépannage effectués par le personnel et les sous-traitants de Microsoft ne nécessitent pas l’accès aux données client. Dans les rares cas où un tel accès est requis, Customer Lockbox pour Microsoft Azure fournit une interface dans laquelle les clients peuvent vérifier et approuver/refuser les demandes d’accès aux données client. Elle est utilisée dans les cas où un ingénieur Microsoft a besoin d’accéder aux données client, que ce soit en réponse à un ticket de support initié par le client ou à un problème identifié par Microsoft.

Cet article décrit comment activer Customer Lockbox et comment les demandes de Lockbox sont initiées, suivies et stockées en vue d’audits et de révisions ultérieurs.

<a name='supported-services-and-scenarios-in-general-availability'></a><a name='supported-services-and-scenarios-in-preview'></a>
## <a name="supported-services-and-scenarios"></a>Services et scénarios pris en charge

### <a name="general-availability"></a>Disponibilité générale
Les services suivants sont généralement disponibles pour Customer Lockbox :

- Gestion des API Azure
- Azure App Service
- Recherche cognitive Azure
- Azure Cognitive Services
- Azure Container Registry
- Azure Database pour MySQL
- Azure Databricks
- Azure Data Box
- Explorateur de données Azure
- Azure Data Factory
- Azure Database pour PostgreSQL
- Azure Functions
- Azure HDInsight
- Azure Kubernetes Service
- Azure Monitor
- Stockage Azure
- Azure SQL Database
- Transfert d'abonnement Azure
- Azure Synapse Analytics
- Machines virtuelles dans Azure (couvrant l’accès au Bureau à distance, l’accès aux images mémoire et les disques managés)

### <a name="public-preview"></a>Version préliminaire publique
Les services suivants sont en préversion pour Customer Lockbox :

- Azure Machine Learning
- Azure Batch

## <a name="enable-customer-lockbox"></a>Activer Customer Lockbox

Vous pouvez désormais activer Customer Lockbox à partir du [module d’administration](https://aka.ms/customerlockbox/administration) dans le panneau Customer Lockbox.  

> [!NOTE]
> Pour activer Customer Lockbox, le [rôle d’administrateur général](../../active-directory/roles/manage-roles-portal.md) doit être attribué au compte d’utilisateur.

## <a name="workflow"></a>Workflow

Les étapes suivantes décrivent un workflow classique pour une demande Customer Lockbox.

1. Une personne dans votre organisation a un problème avec sa charge de travail Azure.

2. Une fois que cette personne a identifié le problème, sans pour autant le résoudre, elle ouvre un ticket de support sur le [portail Azure](https://ms.portal.azure.com/signin/index/?feature.settingsportalinstance=mpac). Le ticket est affecté à un ingénieur du support client Azure.

3. Un ingénieur du support Azure examine la demande de service et détermine les étapes suivantes pour résoudre le problème.

4. Si l’ingénieur du support ne parvient pas à résoudre le problème à l’aide des données générées par le service et des outils standard, l’étape suivante consiste à demander des autorisations élevées à l’aide d’un service d’accès juste à temps (JAT). Cette demande peut provenir de l’ingénieur du support technique d’origine ou d’un autre ingénieur, car le problème est réaffecté à l’équipe Azure DevOps.

5. Une fois la demande d’accès soumise par l’ingénieur Azure, le service juste-à-temps évalue la demande en tenant compte des facteurs suivants :
    - Étendue de la ressource
    - Si l’auteur de la demande est une identité isolée ou utilise l’authentification multifacteur
    - Niveaux d’autorisation

    Selon la règle JIT, cette demande peut également inclure une approbation des approbateurs Microsoft internes. Par exemple, l’approbateur peut être le responsable du support client ou le responsable DevOps.

6. Lorsque la demande nécessite un accès direct aux données client, une demande Customer Lockbox est lancée. Par exemple, pour un accès Bureau à distance à la machine virtuelle d’un client.

    La demande a désormais l’état **Client averti** : elle attend l’approbation du client avant que l’accès soit accordé.

7. Dans l’organisation du client, l’utilisateur qui a le [rôle de propriétaire](../../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) pour l’abonnement Azure reçoit un e-mail de la part de Microsoft l’informant de la demande d’accès en attente. Pour les demandes Customer Lockbox, cette personne est l’approbateur désigné.

    Exemple d’e-mail :

    ![Azure Customer Lockbox - Notification par courrier électronique](./media/customer-lockbox-overview/customer-lockbox-email-notification.png)

8. La notification par e-mail contient un lien vers le panneau **Customer Lockbox** dans le module Administration. Via ce lien, l’approbateur désigné se connecte au portail Azure pour afficher des demandes en attente de l’organisation pour Customer Lockbox :

    ![Azure Customer Lockbox - Page d’accueil](./media/customer-lockbox-overview/customer-lockbox-landing-page.png)

   La demande reste dans la file d’attente du client pendant quatre jours. Passé ce délai, la demande d’accès expire automatiquement et aucun accès n’est accordé aux ingénieurs Microsoft.

9. Pour obtenir les détails de la demande en attente, l’approbateur désigné peut sélectionner la demande Customer Lockbox sous les **demandes en attente** :

    ![Azure Customer Lockbox - Afficher la demande en attente](./media/customer-lockbox-overview/customer-lockbox-pending-requests.png)

10. L’approbateur désigné peut également sélectionner l’**ID DE DEMANDE DE SERVICE** pour afficher la demande de ticket de support créée par l’utilisateur d’origine. Ces informations permettent de comprendre pourquoi le support Microsoft a été sollicité et de connaître l’historique du problème signalé. Par exemple :

    ![Azure Customer Lockbox - Afficher la demande du ticket de support](./media/customer-lockbox-overview/customer-lockbox-support-ticket.png)

11. Après avoir examiné la demande, l’approbateur désigné sélectionne **Approuver** ou **Refuser** :

    ![Azure Customer Lockbox - Sélectionner Approuver ou Refuser](./media/customer-lockbox-overview/customer-lockbox-approval.png)

    Selon la sélection :
    - **Approuver** :  l’accès est accordé à l’ingénieur Microsoft. L’accès est accordé pour une durée par défaut de huit heures.
    - **Refuser** : la demande d’accès avec élévation de privilèges de l’ingénieur Microsoft est rejetée, et aucune action supplémentaire n’est nécessaire.

À des fins d’audit, les actions effectuées dans ce workflow sont enregistrées dans les [journaux de demandes Customer Lockbox](#auditing-logs).

## <a name="auditing-logs"></a>Journaux d’activité d’audit

Les journaux Customer Lockbox sont stockés dans les journaux d’activité. Dans le portail Azure, sélectionnez **Journaux d’activité** pour afficher les informations d’audit relatives aux demandes Customer Lockbox. Vous pouvez filtrer selon des actions spécifiques :
- **Refuser une demande Lockbox**
- **Créer une demande Lockbox**
- **Approuver une demande Lockbox**
- **Expiration d’une demande Lockbox**

Par exemple :

![Azure Customer Lockbox - Journaux d’activité](./media/customer-lockbox-overview/customer-lockbox-activitylogs.png)

## <a name="customer-lockbox-integration-with-azure-security-benchmark"></a>Intégration de Customer Lockbox au benchmark de sécurité Azure

Nous avons introduit un nouveau contrôle de base de référence ([3.13](../benchmarks/security-control-identity-access-control.md#313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios)) dans le benchmark de sécurité Azure qui couvre la mise en application de Customer Lockbox. Les clients peuvent désormais tirer parti du point de référence afin de réviser les conditions d’application de Customer Lockbox pour un service.

## <a name="exclusions"></a>Exclusions

Les demandes de Customer Lockbox ne sont pas déclenchées dans les scénarios de support d’ingénierie suivants :

- Scénarios d’urgence qui ne répondent pas aux procédures de fonctionnement standard. Par exemple, si une panne de service majeure nécessite une attention immédiate pour récupérer ou restaurer des services dans un scénario inattendu ou imprévisible. Ces événements d’urgence sont rares et, dans la plupart des cas, ne nécessitent aucun accès aux données client pour être résolus.
- Un ingénieur Microsoft accède à la plateforme Azure dans le cadre de la résolution des problèmes et est exposé par inadvertance aux données client. Par exemple, lors de la résolution des problèmes, l’équipe de réseau Azure capture des paquets sur un périphérique réseau. Il est rare que ces scénarios exigent l’accès à des quantités significatives de données client. Les clients peuvent renforcer la protection de leurs données grâce à l’utilisation du chiffrement de données en transit et au repos.

Les demandes de Customer Lockbox ne sont pas non plus déclenchées par des demandes juridiques externes pour les données. Pour plus d’informations, consultez la discussion sur les [Demandes gouvernementales de données](https://www.microsoft.com/trust-center/) dans le centre de gestion de la confidentialité Microsoft.

## <a name="next-steps"></a>Étapes suivantes

Customer Lockbox est disponible pour tous les clients qui disposent d’un [plan de support Azure](https://azure.microsoft.com/support/plans/) avec un niveau minimal de **Développeur**. Vous pouvez activer Customer Lockbox à partir du [module Administration](https://aka.ms/customerlockbox/administration) dans le panneau Customer Lockbox.

Des demandes de Customer Lockbox sont initiées par un ingénieur Microsoft si cette action est nécessaire pour faire progresser un cas de support.
