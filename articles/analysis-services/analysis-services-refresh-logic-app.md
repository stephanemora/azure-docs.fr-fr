---
title: Actualiser avec Logic Apps pour les modèles Azure Analysis Services | Microsoft Docs
description: Découvrez comment coder actualisation asynchrone à l’aide d’Azure Logic Apps.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 6e1ac5dfd1972e406a1bd8dcd26e6aef2c4ea6d1
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919860"
---
# <a name="refresh-with-logic-apps"></a>Actualiser avec Logic Apps

À l’aide de Logic Apps et les appels REST, vous pouvez effectuer les opérations d’actualisation automatique des données sur vos modèles tabulaires Azure Analysis, y compris la synchronisation des réplicas en lecture seule pour la montée de la requête.

Pour en savoir plus sur l’utilisation des API REST avec Azure Analysis Services, consultez [asynchrone actualiser avec l’API REST](analysis-services-async-refresh.md).

## <a name="authentication"></a>Authentification

Tous les appels doivent être authentifiées avec un jeton Azure Active Directory (OAuth 2) valide.  Les exemples de cet article utilisera un Principal de Service (SPN) pour s’authentifier auprès d’Azure Analysis Services. Pour plus d’informations, consultez [créer un principal de service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="design-the-logic-app"></a>Concevoir l’application logique

> [!IMPORTANT]
> Les exemples suivants supposent que le pare-feu Azure Analysis Services est désactivé.  Si le pare-feu est activé, l’adresse IP publique de l’initiateur de la demande doit être dans la liste verte dans le pare-feu Azure Analysis Services. Pour en savoir plus sur les plages d’adresses IP de l’application logique par région, consultez [limites et les informations de configuration pour Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses).

### <a name="prerequisites"></a>Conditions préalables

#### <a name="create-a-service-principal-spn"></a>Créez un Principal de Service (SPN)

Pour en savoir plus sur la création d’un Principal de Service, consultez [créer un principal de service à l’aide du portail Azure](../active-directory/develop/howto-create-service-principal-portal.md).

#### <a name="configure-permissions-in-azure-analysis-services"></a>Configurer des autorisations dans Azure Analysis Services
 
Le principal du Service que vous créez doit avoir des autorisations d’administrateur de serveur sur le serveur. Pour plus d’informations, consultez [Ajouter un principal de service au rôle d’administrateur du serveur](analysis-services-addservprinc-admins.md)

### <a name="configure-the-logic-app"></a>Configurer l’application logique

Dans cet exemple, l’application logique est conçue pour déclencher lorsqu’une requête HTTP est reçue. Cela permet l’utilisation d’outil d’orchestration, tels que Azure Data Factory pour déclencher l’actualisation de modèle Azure Analysis Services.

Une fois que vous avez créé une application logique :

1. Dans le Concepteur d’application logique, choisissez la première action en tant que **quand une demande HTTP est reçue**.

   ![Ajouter les activités HTTP reçu](./media/analysis-services-async-refresh-logic-app/1.png)

Cette étape est rempli avec l’URL HTTP POST, une fois l’application logique est enregistrée.

2. Ajouter une nouvelle étape et recherchez **HTTP**.  

   ![Ajouter les activités HTTP](./media/analysis-services-async-refresh-logic-app/9.png)

   ![Ajouter les activités HTTP](./media/analysis-services-async-refresh-logic-app/10.png)

3. Sélectionnez **HTTP** pour ajouter cette action.

   ![Ajouter les activités HTTP](./media/analysis-services-async-refresh-logic-app/2.png)

Configurer l’activité HTTP comme suit :

|Propriété  |Valeur  |
|---------|---------|
|**Méthode**     |POST         |
|**URI**     | https://*votre région du serveur*/servers/*nom du serveur aas*/models/*le nom de votre base de données*/ <br /> <br /> Par exemple : https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/|
|**En-têtes**     |   Type de contenu, application/json <br /> <br />  ![headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Corps**     |   Pour en savoir plus sur la formation de corps de la demande, consultez [asynchrone actualiser avec l’API REST - POST /refreshes](analysis-services-async-refresh.md#post-refreshes). |
|**Authentification**     |OAuth Active Directory         |
|**Tenant**     |Renseignez votre ID de locataire Azure Active Directory         |
|**Public ciblé**     |https://*.asazure.windows.net         |
|**ID client**     |Entrez votre ID de client de nom de principal du Service         |
|**Type d’informations d’identification**     |Secret         |
|**Secret**     |Entrez votre Secret de nom de principal du Service         |

Exemple :

![Activité HTTP terminée](./media/analysis-services-async-refresh-logic-app/7.png)

À présent tester l’application logique.  Dans le Concepteur d’application logique, cliquez sur **exécuter**.

![Tester l’application logique](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>Utiliser l’application logique avec Azure Data Factory

Une fois l’application logique est enregistrée, passez en revue la **quand une demande HTTP est reçue** activité, puis copiez le **URL HTTP POST** qui est désormais générée.  Il s’agit de l’URL qui peut être utilisé par Azure Data Factory pour effectuer l’appel asynchrone pour déclencher l’application logique.

Voici un exemple activité Azure Data Factory Web qui effectue cette action.

![Activité Web de Data Factory](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Utiliser une application logique autonome

Si vous ne prévoyez pas sur l’utilisation d’un outil d’Orchestration tels que Data Factory pour déclencher l’actualisation de modèle, vous pouvez définir l’application logique à déclencher l’actualisation selon une planification.

À l’aide de l’exemple ci-dessus, supprime la première activité et remplacez-la par une **planification** activité.

![Planifier une activité](./media/analysis-services-async-refresh-logic-app/12.png)

![Planifier une activité](./media/analysis-services-async-refresh-logic-app/13.png)

Cet exemple utilise **périodicité**.

Une fois que l’activité a été ajoutée, configurez l’intervalle et la fréquence, puis ajoutez un nouveau paramètre et choisissez **aux heures indiquées**.

![Planifier une activité](./media/analysis-services-async-refresh-logic-app/16.png)

Sélectionnez les heures souhaitées.

![Planifier une activité](./media/analysis-services-async-refresh-logic-app/15.png)

Enregistrez l’application logique.

## <a name="next-steps"></a>Étapes suivantes

[Exemples](analysis-services-samples.md)  
[API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)
