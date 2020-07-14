---
title: Journaux bruts HTTP Azure CDN
description: Cet article décrit les journaux bruts HTTP Azure CDN.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: a2522eba17574246ab99a0d47a42f128d5f61ace
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84888639"
---
# <a name="azure-cdn-http-raw-logs"></a>Journaux bruts HTTP Azure CDN
Les journaux bruts offrent des informations détaillées sur les opérations et erreurs qui sont importantes pour l’audit et la résolution des problèmes. Les journaux bruts diffèrent des journaux d’activité. Les journaux d’activité apportent une visibilité dans les opérations effectuées sur des ressources Azure. Les journaux bruts fournissent un enregistrement des opérations de votre ressource.

> [!IMPORTANT]
> La fonctionnalité des journaux bruts HTTP est disponible pour Azure CDN à partir de Microsoft.

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer. 

## <a name="sign-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuration

Pour configurer les journaux bruts de votre instance Azure CDN à partir du profil Microsoft : 

1. Dans le menu du portail Azure, sélectionnez **Toutes les ressources** >>  **\<your-CDN-profile>** .

2. Sous **Supervision**, sélectionnez **Paramètres de diagnostic**.

3. Sélectionnez **+ Ajouter le paramètre de diagnostic**.

    ![Paramètre de diagnostic CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Les journaux bruts sont disponibles uniquement au niveau du profil, tandis que les journaux agrégés des codes d’état http sont disponibles au niveau du point de terminaison.

4. Sous **Paramètres de diagnostic**, entrez un nom pour le paramètre de diagnostic sous **Nom des paramètres de diagnostic**.

5. Sélectionnez le **journal** et définissez la rétention en jours.

6. Sélectionnez les **Détails de la destination**. Les options de destination sont les suivantes :
    * **Envoyer à Log Analytics**
        * Sélectionnez l’**Abonnement** et l’**Espace de travail Log Analytics**.
    * **Archiver dans un compte de stockage**
        * Sélectionnez l’**Abonnement** et le **Compte de stockage**.
    * **Diffuser vers un hub d’événements**
        * Sélectionnez l’**Abonnement**, l’**Espace de noms du hub d’événements**, le **Nom du hub d’événements (facultatif)** et le **Nom de la stratégie du hub d’événements**.

    ![Paramètre de diagnostic CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Sélectionnez **Enregistrer**.

## <a name="raw-logs-properties"></a>Propriétés des journaux bruts

Azure CDN du service Microsoft fournit actuellement des journaux bruts. Les journaux bruts présentent chaque requête d’API individuellement, chaque entrée appliquant le schéma suivant : 

| Propriété              | Description                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Chaîne de référence unique qui identifie une requête traitée par Front Door, également envoyée en tant qu’en-tête X-Azure-Ref au client. Nécessaire pour pouvoir effectuer une recherche détaillée dans les journaux d’accès pour une requête spécifique. |
| HttpMethod            | Méthode HTTP utilisée par la requête.                                                                                                                                                                     |
| HttpVersion           | Type de la requête ou de la connexion.                                                                                                                                                                   |
| RequestUri            | URI de la requête reçue.                                                                                                                                                                         |
| RequestBytes          | Taille du message de requête HTTP en octets, en-têtes de requête et corps de requête compris.                                                                                                   |
| ResponseBytes         | Octets envoyés en tant que réponse par le serveur back-end.                                                                                                                                                    |
| UserAgent             | Type de navigateur utilisé par le client.                                                                                                                                                               |
| ClientIp              | Adresse IP du client à l’origine de la demande.                                                                                                                                                  |
| TimeTaken             | Durée nécessaire à l’exécution de l’action, en millisecondes.                                                                                                                                            |
| SecurityProtocol      | Version du protocole TLS/SSL utilisée par la requête, ou Null si aucun chiffrement.                                                                                                                           |
| Point de terminaison              | Point de terminaison CDN que l’hôte a configuré sous le profil CDN parent.                                                                                                                                   |
| Backend Host name     | Nom de l’hôte back-end ou origine où les requêtes sont envoyées.                                                                                                                                |
| Sent to origin shield | Si la valeur est true, cela signifie que la requête a été traitée à partir du cache de protection d’origine au lieu du pop de périphérie. La protection d’origine est un cache parent utilisé pour améliorer le taux d’accès au cache.                                       |
| HttpStatusCode        | Code d’état HTTP retourné par le proxy.                                                                                                                                                        |
| HttpStatusDetails     | État résultant de la requête. Vous trouverez la signification de cette valeur de chaîne dans la Table de référence des états.                                                                                              |
| Pop                   | Pop de périphérie, qui a répondu à la demande de l’utilisateur. Les abréviations des POP sont les codes aéroport de leurs Metros respectifs.                                                                                   |
| Cache Status          | Indique si l’objet a été retourné à partir du cache ou s’il est issu de l’origine.                                                                                                             |
> [!IMPORTANT]
> La fonctionnalité des journaux bruts HTTP est disponible automatiquement pour tous les profils créés ou mis à jour après le **25 février 2020**. Pour les profils CDN créés précédemment, vous devez mettre à jour le point de terminaison CDN après la configuration de la journalisation. Par exemple, vous pouvez accéder au filtrage géographique sous les points de terminaison CDN et bloquer tous les pays/régions qui ne sont pas pertinents pour votre charge de travail, puis cliquer sur Enregistrer. 

> [!NOTE]
> Vous pouvez voir les journaux sous votre profil Log Analytics en exécutant une requête. Voici un exemple de requête : AzureDiagnostics | where Category == "AzureCdnAccessLog"

## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez activé les journaux bruts HTTP pour le service CDN Microsoft.

Pour plus d’informations sur Azure CDN et les autres services Azure mentionnés dans cet article, consultez :

* [Analyser](cdn-log-analysis.md) des modèles d’utilisation Azure CDN.

* En savoir plus sur [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Configurer [Log Analytics dans Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
