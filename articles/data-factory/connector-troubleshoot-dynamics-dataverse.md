---
title: Résoudre les problèmes liés aux connecteurs Dynamics 365, Dataverse (Common Data Service) et Dynamics CRM
titleSuffix: Azure Data Factory & Azure Synapse
description: Découvrez comment résoudre les problèmes liés aux connecteurs Dynamics 365, Dataverse (Common Data Service) et Dynamics CRM dans Azure Data Factory et Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/13/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 28aa7fee3ab7cf2bbc8f10d1ba2f5ea54a792cd6
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066664"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>Résoudre les problèmes liés aux connecteurs Dynamics 365, Dataverse (Common Data Service) et Dynamics CRM dans Azure Data Factory et Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Cet article fournit des suggestions pour résoudre les problèmes courants liés aux connecteurs Dynamics 365, Dataverse (Common Data Service) et Dynamics CRM dans Azure Data Factory et Azure Synapse.

## <a name="error-code-dynamicscreateserviceclienterror"></a>Code d’erreur : DynamicsCreateServiceClientError

- **Message** : `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **Cause** : Il s’agit d’un problème temporaire du côté du serveur Dynamics.

- **Recommandation** :  Exécutez à nouveau le pipeline. Si l’échec persiste, essayez de réduire le parallélisme. Si le problème persiste, contactez le support Dynamics.


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>Colonnes manquantes lorsque vous importez un schéma ou prévisualisez des données

- **Symptômes** : Certaines colonnes sont manquantes lorsque vous importez un schéma ou prévisualisez des données. Message d’erreur : `The valid structure information (column name and type) are required for Dynamics source.`

- **Cause** : Ce problème est lié à la conception, car Data Factory et les pipelines Synapse ne peuvent pas afficher les colonnes qui ne contiennent pas de valeurs dans les 10 premiers enregistrements. Assurez-vous que les colonnes que vous avez ajoutées sont au format correct. 

- **Recommandation** : Ajoutez manuellement les colonnes sous l’onglet de mappage.


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>Code d’erreur : DynamicsMissingTargetForMultiTargetLookupField

- **Message** : `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **Cause** : La colonne cible n’existe pas dans la source ou dans le mappage de colonnes.

- **Recommandation** :  
  1. Assurez-vous que la source contient la colonne cible. 
  2. Ajoutez la colonne cible dans le mappage de colonnes. Assurez-vous que la colonne du récepteur est au format *{fieldName}@EntityReference* .


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>Code d’erreur : DynamicsInvalidTargetForMultiTargetLookupField

- **Message** : `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **Cause** : Un nom d’entité incorrect est fourni en tant qu’entité cible d’un champ de recherche à plusieurs cibles.

- **Recommandation** :  Fournissez un nom d’entité valide pour le champ de recherche à plusieurs cibles.


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>Code d’erreur : DynamicsInvalidTypeForMultiTargetLookupField

- **Message** : `The provided target type is not a valid string. Field: '%fieldName;'.`

- **Cause** : La valeur dans la colonne cible n’est pas une chaîne.

- **Recommandation** :  Fournissez une chaîne valide dans la colonne cible de recherche à plusieurs cibles.


## <a name="error-code-dynamicsfailedtorequetserver"></a>Code d’erreur : DynamicsFailedToRequetServer

- **Message** : `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **Cause** : Le serveur Dynamics est instable ou inaccessible ou le réseau rencontre des problèmes.

- **Recommandation** :  Pour plus d’informations, vérifiez la connectivité réseau ou consultez le journal du serveur Dynamics. Pour obtenir de l’aide supplémentaire, contactez le support Dynamics.


## <a name="error-code-dynamicsfailedtoconnect"></a>Code d’erreur : DynamicsFailedToConnect 
 
 - **Message** : `Failed to connect to Dynamics: %message;` 
 
 - **Causes et recommandations** : Différentes causes peuvent être à l’origine de cette erreur. Consultez la liste ci-dessous pour obtenir une analyse des causes possibles et des recommandations associées.

    | Analyse de la cause                                               | Recommandation                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Vous voyez `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` ou `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'` si votre cas d’usage remplit les **trois** conditions suivantes : <li>Vous vous connectez à Dynamics 365, Common Data Service ou Dynamics CRM.</li><li>Vous utilisez l’authentification Office 365.</li><li>Votre locataire et votre utilisateur sont configurés dans Azure Active Directory pour [l’accès conditionnel](../active-directory/conditional-access/overview.md) et/ou Multifactor Authentication est requis (voir ce [lien](/powerapps/developer/data-platform/authenticate-office365-deprecation) vers la documentation de Dataverse).</li>  Dans ces circonstances, la connexion aboutissait avant le 08/06/2021. À partir du 09/06/2021, la connexion commencera à échouer en raison de la dépréciation du service de découverte régional (voir ce [lien](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)).| Si votre locataire et votre utilisateur sont configurés dans Azure Active Directory pour [l’accès conditionnel](../active-directory/conditional-access/overview.md) et/ou que Multifactor Authentication est requis, vous devez utiliser « Azure AD service-principal » pour l’authentification après le 08/06/2021. Pour obtenir des instructions détaillées, référez-vous à ce [lien](./connector-dynamics-crm-office-365.md#prerequisites).|
    |Si vous voyez `Office 365 auth with OAuth failed` dans le message d’erreur, cela signifie que votre serveur peut avoir des configurations non compatibles avec OAuth.| <li>Contactez l’équipe du support technique de Dynamics avec le message d’erreur détaillé pour obtenir de l’aide.</li><li>Utilisez l’authentification du principal de service. Pour cela, vous pouvez vous référer à cet article : [Exemple : Dynamics en ligne à l’aide du principal de service Azure AD et de l’authentification du certificat](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication).</li>
    |Si vous voyez `Unable to retrieve authentication parameters from the serviceUri` dans le message d’erreur, cela signifie que vous entrez une URL de service Dynamics ou un proxy/pare-feu incorrect pour intercepter le trafic. |<li>Assurez-vous que vous avez placé l’URI de service correct dans le service lié.</li><li>Si vous utilisez le runtime d’intégration auto-hébergé, assurez-vous que le pare-feu/proxy n’intercepte pas les demandes adressées au serveur Dynamics.</li> |
    |Si vous voyez `An unsecured or incorrectly secured fault was received from the other party` dans le message d’erreur, cela signifie que des réponses inattendues ont été obtenues du côté serveur.  | <li>Vérifiez que votre nom d’utilisateur et votre mot de passe sont corrects si vous utilisez l’authentification Office 365. </li><li> Vérifiez que vous avez entré le bon URI de service.</li><li>Si vous utilisez l’URL CRM régionale (l’URL comporte un chiffre après « crm »), veillez à utiliser le bon identifiant régional.</li><li>Contactez l’équipe du support technique de Dynamics pour obtenir de l’aide.</li>|
    |Si vous voyez `No Organizations Found` dans le message d’erreur, cela signifie que le nom de votre organisation est incorrect ou que vous avez utilisé un identificateur de région CRM erroné dans l’URL du service.|<li>Vérifiez que vous avez entré le bon URI de service.</li><li>Si vous utilisez l’URL CRM régionale (l’URL comporte un chiffre après « crm »), veillez à utiliser le bon identifiant régional.</li><li>Contactez l’équipe du support technique de Dynamics pour obtenir de l’aide.</li>|
    | Si vous voyez `401 Unauthorized` et un message d’erreur relatif à AAD, cela signifie qu’il y a un problème avec le principal du service. |Suivez les instructions du message d’erreur pour corriger le problème du principal de service. |
   |Pour les autres erreurs, le problème se trouve généralement côté serveur. |Utilisez [XrmToolBox](https://www.xrmtoolbox.com/) pour établir la connexion. Si l’erreur persiste, contactez l’équipe du support technique de Dynamics pour obtenir de l’aide. |

## <a name="error-code-dynamicsoperationfailed"></a>Code d’erreur : DynamicsOperationFailed 
 
- **Message** : `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **Cause** : L’opération a échoué côté serveur. 

- **Recommandation** : Extrayez le code d’erreur de l’opération Dynamics à partir du message d’erreur : `Dynamics operation failed with error code: {code}` et reportez-vous à l’article [Codes d’erreur de service web](/powerapps/developer/data-platform/org-service/web-service-error-codes) pour obtenir des informations plus détaillées. Vous pouvez contacter l’équipe du support technique de Dynamics si nécessaire. 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>Code d’erreur : DynamicsInvalidFetchXml 
  
- **Message** : `The Fetch Xml query specified is invalid.` 

- **Cause** : Il existe une erreur dans le fichier XML récupéré (fetch).  

- **Recommandation** : Corrigez l’erreur dans le fichier XML récupéré (fetch). 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>Code d’erreur : DynamicsMissingKeyColumns 
 
- **Message** : `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **Cause** : Les données sources ne contiennent pas la colonne clé pour l’entité récepteur. 

- **Recommandation** : Confirmez que les colonnes clés se trouvent dans les données sources ou mappez une colonne source à la colonne clé de l’entité récepteur. 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>Code d’erreur : DynamicsPrimaryKeyMustBeGuid 
 
- **Message** : `The primary key attribute '%attribute;' must be of type guid.` 
 
- **Cause** : Le type de la colonne de clé primaire n’est pas « GUID ». 
 
- **Recommandation** : Assurez-vous que la colonne de clé primaire dans les données sources est de type « GUID ». 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>Code d’erreur : DynamicsAlternateKeyNotFound 
 
- **Message** : `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **Cause** : La clé de remplacement fournie n’existe pas, ce qui peut être dû à des noms de clé incorrects ou à des autorisations insuffisantes. 
 
- **Recommandation** : <br/> 
    - Corrigez les fautes de frappe dans le nom de la clé.<br/> 
    - Assurez-vous que vous disposez des autorisations suffisantes sur l’entité. 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>Code d’erreur : DynamicsInvalidSchemaDefinition 
 
- **Message** : `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **Cause** : Les colonnes de récepteur dans le mappage de colonne n’ont pas la propriété « type ». 
 
- **Recommandation** : Vous pouvez ajouter la propriété « type »à ces colonnes dans le mappage de colonnes à l’aide de l’éditeur JSON sur le portail. 

## <a name="the-copy-activity-from-the-dynamics-365-reads-more-rows-than-the-actual-number"></a>L’activité de copie de Dynamics 365 lit plus de lignes que le nombre réel

- **Symptômes** : l’activité de copie de Dynamics 365 lit plus de lignes que le nombre réel.

- **Cause** : le serveur Dynamics 365 indique toujours plus d’enregistrements disponibles. 

- **Recommandation** : utilisez **XrmToolBox** pour tester le FetchXML avec la pagination. **XrmToolBox** avec certains outils installés peut obtenir le nombre d’enregistrements. Pour plus d'informations, consultez [XrmToolBox](https://www.xrmtoolbox.com/).

## <a name="cannot-access-virtual-columns-from-dynamics-sources-in-the-copy-activity"></a>Impossible d’accéder aux colonnes virtuelles à partir de sources Dynamics dans l’activité de copie

- **Symptômes** : impossible d’accéder aux colonnes virtuelles à partir de sources Dynamics dans l’activité de copie.

- **Cause** : la colonne virtuelle n’est pas prise en charge pour le moment. 

- **Recommandation** : pour la valeur Groupe d’options, suivez les options ci-dessous pour l’obtenir :
  - Vous pouvez obtenir le code de type d’objet en vous référant à la [façon de rechercher le code de type d’objet pour une entité](https://powerobjects.com/tips-and-tricks/find-object-type-code-entity/) et au [blog Dynamics 365](https://dynamicscrmdotblog.wordpress.com/).
  - Vous pouvez lier l’entité StringMap à votre entité cible et obtenir les valeurs associées.

## <a name="the-parallel-copy-in-a-dynamics-crm-data-store"></a>Copie parallèle dans une banque de données Dynamics CRM

- **Symptômes** : vous ne savez pas s’il est possible de configurer la copie parallèle dans une banque de données Dynamics CRM et vous ne connaissez pas la plage de valeurs qui peuvent être définies dans la section « Degré de parallélisme de la copie ».

- **Recommandation** : la copie parallèle contrôle le parallélisme et la section « Degré de parallélisme de la copie » peut être définie sur une valeur différente de zéro. Un grand nombre peut entraîner une limitation du côté du serveur Dynamics, ce qui peut réduire le débit, mais la limitation est maintenant gérée avec le kit de développement logiciel (SDK) public.

  :::image type="content" source="./media/connector-troubleshoot-guide/degree-of-copy-parallelism-section.png" alt-text="Diagramme de la section Degré de parallélisme de la copie.":::

## <a name="dynamics-type-conversion"></a>Conversion de type dynamique

- **Symptômes** : vous essayez de convertir le GUID en une chaîne dans la source Dynamics, mais vous rencontrez une erreur.

- **Cause** : lorsque Dynamics est utilisé en tant que source, la conversion de type n’est pas prise en charge.

- **Recommandation** : activez la mise en lots et réessayez.

## <a name="next-steps"></a>Étapes suivantes

Si vous avez besoin d’une aide supplémentaire, essayez les ressources suivantes :

- [Guide de résolution des problèmes de connecteur](connector-troubleshoot-guide.md)
- [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Demandes de fonctionnalités Data Factory](/answers/topics/azure-data-factory.html)
- [Vidéos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Page Microsoft Q&A](/answers/topics/azure-data-factory.html)
- [Forum Stack Overflow pour Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Informations Twitter sur Data Factory](https://twitter.com/hashtag/DataFactory)
