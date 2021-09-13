---
title: 'Azure AD Connect : Documentation de référence concernant ADSyncTools PowerShell | Microsoft Docs'
description: Ce document fournit des informations de référence sur le module ADSyncTools.psm1 PowerShell.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 768b037573d1114141ca9722feb6a9ecd510dd5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122524536"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect :  Documentation de référence concernant ADSyncTools PowerShell
La documentation suivante fournit des informations de référence sur le module ADSyncTools.psm1 PowerShell inclus avec Azure AD Connect.

## <a name="install-the-adsynctools-powershell-module"></a>Installer le module PowerShell ADSyncTools
Pour installer le module PowerShell ADSyncTools, effectuez les étapes suivantes :

1.  Ouvrez Windows PowerShell avec des privilèges d’administrateur.
2.  Tapez ou collez le code suivant : 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Appuyez sur Entrée.
4.  Pour vérifier que le module a été installé, entrez ou copiez-collez ceci :
    ```powershell
    Get-module AdSyncTools
    ```
5.  Vous devez maintenant voir des informations sur le module.


## <a name="clear-adsynctoolsmsdsconsistencyguid"></a>Clear-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Effacer le mS-DS-ConsistencyGuid d’un objet Active Directory
### SYNTAX
```
Clear-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Efface la valeur de mS-DS-ConsistencyGuid pour l’objet Active Directory cible.
Prend en charge les objets Active Directory dans les forêts multidomaines.

### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>EXEMPLE 3
```
'User1@Contoso.com' | Clear-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objet cible dans Active Directory pour effacer mS-DS-ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="connect-adsynctoolssqldatabase"></a>Connect-ADSyncToolsSqlDatabase
### SYNOPSIS
Se connecter à une base de données SQL à des fins de test
### SYNTAX
```
Connect-ADSyncToolsSqlDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>]
 [[-Port] <String>] [[-UserName] <String>] [[-Password] <String>] [<CommonParameters>]
```
### DESCRIPTION
Fonctions et utilitaires SQL pour les diagnostics
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Database 'ADSync'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Instance 'INTANCE01' -Database 'ADSync'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nom du serveur SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-instance"></a>-Instance
Nom de l'instance SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-database"></a>-Database
Nom de la base de données SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Port SQL Server (par exemple,
49823)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-username"></a>-UserName
Nom d’utilisateur des informations de connexion SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-password"></a>-Mot de passe
Mot de passe des informations de connexion SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsaaddistinguishedname"></a>ConvertFrom-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Convertir le DistinguishedName du connecteur Azure AD en ImmutableId
### SYNTAX
```
ConvertFrom-ADSyncToolsAadDistinguishedName [-DistinguishedName] <String> [<CommonParameters>]
```
### DESCRIPTION
Accepte un DistinguishedName de connecteur Azure AD comme CN={514635484D4B376E38307176645973555049486139513D3D} et le convertit dans la valeur ImmutableID base64 correspondante, par exemple QF5HMK7n80qvdYsUPIHa9Q==
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
ConvertFrom-ADSyncToolsAadDistinguishedName 'CN={514635484D4B376E38307176645973555049486139513D3D}'
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName de l’espace du connecteur Azure AD
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertfrom-adsynctoolsimmutableid"></a>ConvertFrom-ADSyncToolsImmutableID
### SYNOPSIS
Convertir l’ImmutableId base64 (SourceAnchor) en valeur GUID
### SYNTAX
```
ConvertFrom-ADSyncToolsImmutableID [-Value] <String> [<CommonParameters>]
```
### DESCRIPTION
Convertit la valeur de l’ImmutableID à partir d’une chaîne Base64 et retourne une valeur GUID. Si la chaîne base64 ne peut pas être convertie en GUID, retourne un tableau d’octets.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
ConvertFrom-ADSyncToolsImmutableID 'iGhmiAEBERG7uxI0VniQqw=='
```
#### <a name="example-2"></a>EXEMPLE 2
```
'iGhmiAEBERG7uxI0VniQqw==' | ConvertFrom-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
ImmutableId au format Base64
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsaaddistinguishedname"></a>ConvertTo-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Convertir l’ImmutableId en DistinguishedName du connecteur Azure AD
### SYNTAX
```
ConvertTo-ADSyncToolsAadDistinguishedName [-ImmutableId] <String> [<CommonParameters>]
```
### DESCRIPTION
Accepte un ImmutableId (SourceAnchor) comme QF5HMK7n80qvdYsUPIHa9Q== et le convertit dans la valeur DistinguishedName correspondante du connecteur Azure AD, par exemple CN={514635484D4B376E38307176645973555049486139513D3D}
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
ConvertTo-ADSyncToolsAadDistinguishedName 'QF5HMK7n80qvdYsUPIHa9Q=='
```
### PARAMETERS
#### <a name="-immutableid"></a>-ImmutableId
ImmutableId (SourceAnchor)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolscloudanchor"></a>ConvertTo-ADSyncToolsCloudAnchor
### SYNOPSIS
Convertir un Anchor Base64 en CloudAnchor
### SYNTAX
```
ConvertTo-ADSyncToolsCloudAnchor [-Anchor] <String> [<CommonParameters>]
```
### DESCRIPTION
Accepte un Anchor Base64 comme VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA== et le convertit dans la valeur CloudAnchor correspondante, par exemple User_abc12345-1234-abcd-9876-ab0123456789
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
ConvertTo-ADSyncToolsCloudAnchor "VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA=="
```
#### <a name="example-2"></a>EXEMPLE 2
```
"VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==" | ConvertTo-ADSyncToolsCloudAnchor
```
### PARAMETERS
#### <a name="-anchor"></a>-Anchor
Anchor Base64
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="convertto-adsynctoolsimmutableid"></a>ConvertTo-ADSyncToolsImmutableID
### SYNOPSIS
Convertir le GUID (ObjectGUID / ms-Ds-Consistency-Guid) en chaîne Base64
### SYNTAX
```
ConvertTo-ADSyncToolsImmutableID [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Convertit la valeur d’un GUID, d’une chaîne de GUID ou d’un tableau d’octets en une chaîne Base64
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
ConvertTo-ADSyncToolsImmutableID '88888888-0101-3333-cccc-1234567890cd'
```
#### <a name="example-2"></a>EXEMPLE 2
```
'88888888-0101-3333-cccc-1234567890cd' | ConvertTo-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
GUID, chaîne GUID ou tableau d’octets
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsaaddisconnectors"></a>Export-ADSyncToolsAadDisconnectors
### SYNOPSIS
Exporter les objets déconnecteurs Azure AD
### SYNTAX
```
Export-ADSyncToolsAadDisconnectors [[-SyncObjectType] <Object>] [<CommonParameters>]
```
### DESCRIPTION
Exécute l’outil CSExport pour exporter tous les déconnecteurs au format XML, puis prend cette sortie XML et la convertit en fichier CSV avec : UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Export-ADSyncToolsDisconnectors -SyncObjectType 'PublicFolder'
```
Exporte vers un fichier CSV tous les objets déconnecteurs PublicFolder
#### <a name="example-2"></a>EXEMPLE 2
```
Export-ADSyncToolsDisconnectors
```
Exporte vers un fichier CSV tous les objets déconnecteurs
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
ObjectType à inclure dans la sortie
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
Utilisez l’argument ObjectType au cas où vous souhaiteriez exporter des déconnecteurs pour un seul type d’objet
### OUTPUTS
Exporte dans un fichier CSV des objets déconnecteurs comprenant : 

UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId and CloudAnchor

## <a name="export-adsynctoolshybridaadjoinreport"></a>Export-ADSyncToolsHybridAadJoinReport
### SYNOPSIS
Génère un rapport sur les certificats stockés dans les objets d’ordinateur Active Directory, en particulier les certificats émis par la fonctionnalité de jonction Azure AD Hybride.
### SYNTAX
#### <a name="singleobject"></a>SingleObject
```
Export-ADSyncToolsHybridAadJoinReport [-DN] <String> [[-Filename] <String>] [<CommonParameters>]
```
#### <a name="multipleobjects"></a>MultipleObjects
```
Export-ADSyncToolsHybridAadJoinReport [-OU] <String> [[-Filename] <String>] [<CommonParameters>]
```
### DESCRIPTION
Cet outil vérifie tous les certificats présents dans la propriété UserCertificate d’un objet d’ordinateur dans AD et, pour chaque certificat non expiré présent, valide si le certificat a été émis pour la fonctionnalité de jonction Azure AD Hybride (autrement dit, le nom d’objet correspondant à CN={ObjectGUID}).
Avant la version 1.4, Azure AD Connect synchronisait dans Azure AD tout ordinateur qui contenait au moins un certificat. Désormais, avec la version 1.4 et versions ultérieures, le moteur ADSync peut identifier les certificats de jonction Azure AD Hybride et « filtrer dans le cloud » (exclure) l’objet d’ordinateur pour ne pas le synchroniser avec Azure AD, sauf s’il existe un certificat de jonction Azure AD Hybride valide.
Les objets Azure AD déjà synchronisés dans AD, mais sans certificat de jonction Azure AD Hybride valide, sont supprimés d’Azure AD (CloudFiltered=TRUE) par Azure AD Connect.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -DN 'CN=Computer1,OU=SYNC,DC=Fabrikam,DC=com'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -OU 'OU=SYNC,DC=Fabrikam,DC=com' -Filename "MyHybridAzureADjoinReport.csv" -Verbose
```
### PARAMETERS
#### <a name="-dn"></a>-DN
DistinguishedName de l’ordinateur
```yaml
Type: String
Parameter Sets: SingleObject
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-ou"></a>-OU
OrganizationalUnit AD
```yaml
Type: String
Parameter Sets: MultipleObjects
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filename"></a>-Filename
Nom du fichier CSV de sortie (facultatif)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Pour plus d’informations : [Comprendre Azure AD Connect 1.4.xx.x et la disparition des appareils](/troubleshoot/azure/active-directory/reference-connect-device-disappearance).

## <a name="export-adsynctoolsobjects"></a>Export-ADSyncToolsObjects
### SYNOPSIS
Exporter des objets Azure AD Connect vers des fichiers XML
### SYNTAX
#### <a name="objectid"></a>ObjectId
```
Export-ADSyncToolsObjects [-ObjectId] <Object> [-Source] <Object> [-ExportSerialized] [<CommonParameters>]
```
#### <a name="distinguishedname"></a>DistinguishedName
```
Export-ADSyncToolsObjects [-DistinguishedName] <Object> [-ConnectorName] <Object> [-ExportSerialized]
 [<CommonParameters>]
```
### DESCRIPTION
Exporte les objets ADSync internes à partir du métaverse, ainsi que les objets connectés associés à partir des espaces de connecteur
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Export-ADSyncToolsObjects -ObjectId '9D220D58-0700-E911-80C8-000D3A3614C0' -Source Metaverse
```
#### <a name="example-2"></a>EXEMPLE 2
```
Export-ADSyncToolsObjects -ObjectId '9e220d58-0700-e911-80c8-000d3a3614c0' -Source ConnectorSpace
```
#### <a name="example-3"></a>EXEMPLE 3
```
Export-ADSyncToolsObjects -DistinguishedName 'CN=User1,OU=ADSync,DC=Contoso,DC=com' -ConnectorName 'Contoso.com'
```
### PARAMETERS
#### <a name="-objectid"></a>-ObjectId
ObjectId est l’identificateur unique de l’objet dans l’espace de connecteur ou le métaverse correspondant
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-source"></a>-Source
Source est la table dans laquelle l’objet réside (ConnectorSpace ou Metaverse)
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName est l’identificateur de l’objet dans l’espace de connecteur correspondant
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-connectorname"></a>-ConnectorName
ConnectorName est le nom de l’espace de connecteur où l’objet réside
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-exportserialized"></a>-ExportSerialized
ExportSerialized exporte des fichiers XML supplémentaires
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolsrunhistory"></a>Export-ADSyncToolsRunHistory
### SYNOPSIS
Exporter l’historique des exécutions Azure AD Connect
### SYNTAX
```
Export-ADSyncToolsRunHistory [-TargetName] <String> [<CommonParameters>]
```
### DESCRIPTION
Fonction permettant d’exporter les résultats des exécutions de profils et des exécutions d’étapes Azure AD Connect, au format CSV et au format XML respectivement.
Le fichier CSV des exécutions de profils qui en résulte peut être importé dans une feuille de calcul, et le fichier XML des exécutions d’étapes peut être importé avec Import-Clixml
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Export-ADSyncToolsRunHistory -TargetName MyADSyncHistory
```
### PARAMETERS
#### <a name="-targetname"></a>-TargetName
Nom du fichier de sortie
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="export-adsynctoolssourceanchorreport"></a>Export-ADSyncToolsSourceAnchorReport
### SYNOPSIS
Exporter le rapport ms-DS-Consistency-GUID
### SYNTAX
```
Export-ADSyncToolsSourceAnchorReport [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```
### DESCRIPTION
Génère un rapport ms-ds-Consistency-Guid basé sur un fichier CSV importé à partir d’Import-ADSyncToolsSourceAnchor
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsSourceAnchorReport -Output ".\AllSyncUsers-Report"
```
#### <a name="example-2"></a>EXEMPLE 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-alternativeloginid"></a>-AlternativeLoginId
Utiliser un ID de connexion alternatif (adresse e-mail)
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Nom du fichier de sortie pour les fichiers CSV et LOG
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsaadobject"></a>Get-ADSyncToolsAadObject
### SYNOPSIS
Obtenir des objets synchronisés pour un SyncObjectType donné
### SYNTAX
```
Get-ADSyncToolsAadObject [-SyncObjectType] <Object> [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Lit tous les objets synchronisés pour une classe d’objets donnée (SyncObjectType) dans Azure AD.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsAadObject -SyncObjectType 'publicFolder' -Credential $(Get-Credential)
```
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
Type d'objet
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Informations d’identification de l’administrateur général Azure AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

### OUTPUTS

Cette applet de commande retourne les propriétés « Shadow » qui sont synchronisées par le client de synchronisation, et qui peuvent être différentes de la valeur stockée dans la propriété correspondante Azure AD. Par exemple, l’UPN d’un utilisateur synchronisé avec un suffixe de domaine non vérifié (« user@nonverified.domain ») verra son suffixe UPN dans Azure AD converti en domaine par défaut du locataire (« user@tenantname.onmicrosoft.com »). Dans ce cas, Get-ADSyncToolsAadObject retourne la valeur « Shadow » de « user@nonverified.domain », et non la valeur « user@tenantname.onmicrosoft.com » présente dans Azure AD.

## <a name="get-adsynctoolsmsdsconsistencyguid"></a>Get-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Obtenir le mS-DS-ConsistencyGuid d’un objet Active Directory
### SYNTAX
```
Get-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Retourne la valeur de l’attribut mS-DS-ConsistencyGuid de l’objet Active Directory cible au format GUID. Prend en charge les objets Active Directory dans les forêts multidomaines.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>EXEMPLE 3
```
'User1@Contoso.com' | Get-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objet cible à récupérer dans Active Directory
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory
### SYNOPSIS
Obtenir l’historique des exécutions Azure AD Connect
### SYNTAX
```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Fonction qui retourne l’historique des exécutions Azure AD Connect au format XML
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>EXEMPLE 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Nombre de jours dans le passé pour lesquels collecter l’historique (par défaut = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolsrunhistorylegacywmi"></a>Get-ADSyncToolsRunHistoryLegacyWmi
### SYNOPSIS
Obtenir l’historique d’exécution Azure AD Connect des versions antérieures d’Azure AD Connect (WMI)
### SYNTAX
```
Get-ADSyncToolsRunHistoryLegacyWmi [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Fonction qui retourne l’historique des exécutions Azure AD Connect au format XML
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>EXEMPLE 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
Nombre de jours dans le passé pour lesquels collecter l’historique (par défaut = 1)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="get-adsynctoolssqlbrowserinstances"></a>Get-ADSyncToolsSqlBrowserInstances
### SYNOPSIS
Obtenir des instances SQL Server à partir du service SQL Browser
### SYNTAX
```
Get-ADSyncToolsSqlBrowserInstances [[-Server] <String>]
```
### DESCRIPTION
Fonctions et utilitaires SQL pour les diagnostics
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsSqlBrowserInstances -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nom du serveur SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="get-adsynctoolstenantazureenvironment"></a>Get-ADSyncToolsTenantAzureEnvironment
### SYNOPSIS
Fonction d’assistance pour obtenir l’environnement Azure auquel appartient l’utilisateur.
### SYNTAX
```
Get-ADSyncToolsTenantAzureEnvironment [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
Cette fonction appellera le point de terminaison de découverte OAuth pour obtenir CloudInstance et tenant_region_scope dans le but de déterminer l’environnement Azure.
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration

### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsTenantAzureEnvironment -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Objet d’informations d’identification PowerShell de l’utilisateur :

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
Objet d’informations d’identification PowerShell de l’utilisateur

### OUTPUTS
Environnement Azure (chaîne)
## <a name="get-adsynctoolstls12"></a>Get-ADSyncToolsTls12
### SYNOPSIS
Obtient les paramètres Client\Server TLS 1.2 pour .NET Framework
### SYNTAX
```
Get-ADSyncToolsTls12 [<CommonParameters>]
```
### DESCRIPTION
Lit les informations du registre concernant TLS 1.2 pour .NET Framework :

| Chemin d’accès                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Activé                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Activé                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Get-ADSyncToolsTls12
```
### PARAMETERS
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Pour plus d’informations : [Application de TLS 1.2 pour Azure AD Connect](reference-connect-tls-enforcement.md).

## <a name="import-adsynctoolsobjects"></a>Import-ADSyncToolsObjects
### SYNOPSIS
Importer un objet Azure AD Connect à partir d’un fichier XML
### SYNTAX
```
Import-ADSyncToolsObjects [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Importe un objet ADSync interne à partir d’un fichier XML qui a été exporté à l’aide de Export-ADSyncToolsObjects
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Import-ADSyncToolsObjects -Path .\20210224-003104_81275a23-0168-eb11-80de-00155d188c11_MV.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Chemin du fichier XML à importer
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolsrunhistory"></a>Import-ADSyncToolsRunHistory
### SYNOPSIS
Importer l’historique des exécutions Azure AD Connect
### SYNTAX
```
Import-ADSyncToolsRunHistory [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Fonction permettant d’importer les résultats des exécutions d’étapes Azure AD Connect à partir du XML créé à l’aide de Export-ADSyncToolsRunHistory
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Export-ADSyncToolsRunHistory -Path .\RunHistory-RunStep.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
Chemin du fichier XML à importer
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="import-adsynctoolssourceanchor"></a>Import-ADSyncToolsSourceAnchor
### SYNOPSIS
Importer ImmutableID à partir d’Azure AD
### SYNTAX
```
Import-ADSyncToolsSourceAnchor [-Output] <String> [-IncludeSyncUsersFromRecycleBin] [<CommonParameters>]
```
### DESCRIPTION
Génère un fichier avec tous les utilisateurs Azure AD synchronisés contenant la valeur ImmutableID au format GUID. Exigences : Module MSOnline PowerShell
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Import-ADSyncToolsSourceAnchor -OutputFile '.\AllSyncUsers.csv'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-output"></a>-Output
Fichier CSV de sortie
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Obtenir des utilisateurs synchronisés de la Corbeille Azure AD
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="invoke-adsynctoolssqlquery"></a>Invoke-ADSyncToolsSqlQuery
### SYNOPSIS
Appeler une requête SQL dans une base de données à des fins de test
### SYNTAX
```
Invoke-ADSyncToolsSqlQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```
### DESCRIPTION
Fonctions et utilitaires SQL pour les diagnostics
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823 | Invoke-ADSyncToolsSqlQuery
```
#### <a name="example-2"></a>EXEMPLE 2
```
$sqlConn = New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823
```
Invoke-ADSyncToolsSqlQuery -SqlConnection $sqlConn -Query 'SELECT *, database_id FROM sys.databases'
### PARAMETERS
#### <a name="-sqlconnection"></a>-SqlConnection
Connexion SQL
```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-query"></a>-Query
Requête SQL
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: SELECT name, database_id FROM sys.databases
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).

## <a name="remove-adsynctoolsaadobject"></a>Remove-ADSyncToolsAadObject
### SYNOPSIS
Supprimer l’objet synchronisé orphelin d’Azure AD
### SYNTAX
#### <a name="csvinput"></a>CsvInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-InputCsvFilename] <Object> [-WhatIf] [-Confirm]
 [<CommonParameters>]
```
#### <a name="objectinput"></a>ObjectInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-SourceAnchor] <Object> [-SyncObjectType] <Object>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Supprime d’Azure AD un ou plusieurs objets synchronisés en fonction de SourceAnchor et ObjecType par lots de 10 objets. Le fichier CSV peut être généré à l’aide d’Export-ADSyncToolsAadDisconnectors
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Remove-ADSyncToolsAadObject -InputCsvFilename .\DeleteObjects.csv -Credential (Get-Credential)
```
#### <a name="example-2"></a>EXEMPLE 2
```
Remove-ADSyncToolsAadObject -SourceAnchor '2epFRNMCPUqhysJL3SWL1A==' -SyncObjectType 'publicFolder' -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Informations d’identification de l’administrateur général Azure AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-inputcsvfilename"></a>-InputCsvFilename
Fichier d’entrée CSV
```yaml
Type: Object
Parameter Sets: CsvInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-sourceanchor"></a>SourceAnchor
SourceAnchor de l’objet
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-syncobjecttype"></a>-SyncObjectType
Type d'objet
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
Montre ce qui se passe en cas d’exécution de l’applet de commande.
L’applet de commande n’est pas exécutée.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
Vous demande une confirmation avant d’exécuter l’applet de commande.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### INPUTS
InputCsvFilename doit pointer vers un fichier CSV contenant au moins 2 colonnes : SourceAnchor, SyncObjectType
### OUTPUTS
Affiche les résultats de l’opération ExportDeletions. EXCLUSION DE RESPONSABILITÉ : Hormis les objets User qui disposent d’une Corbeille, tous les autres types d’objets qui sont supprimés avec cette fonction ne pourront pas être récupérés.
## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates
### SYNOPSIS
Script pour supprimer les certificats arrivés à expiration de l’attribut UserCertificate
### SYNTAX
```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```
### DESCRIPTION
Ce script prend tous les objets d’une unité d’organisation cible dans votre domaine Active Directory, filtrés selon la classe d’objet (utilisateur/ordinateur), et supprime tous les certificats arrivés à expiration présents dans l’attribut UserCertificate.
Par défaut (en mode BackupOnly), il sauvegarde uniquement les certificats arrivés à expiration dans un fichier et n’apporte aucune modification dans Active Directory.
Si vous utilisez -BackupOnly $false, tous les certificats expirés présents dans l’attribut UserCertificate de ces objets seront supprimés d’Active Directory après avoir été copiés dans le fichier.
Chaque certificat est sauvegardé sous un nom de fichier distinct : ObjectClass_ObjectGUID_CertThumprint.cer. Le script crée également un fichier journal au format CSV affichant tous les utilisateurs avec des certificats valides ou expirés, ainsi que l’action réelle effectuée (Ignoré/Exporté/Supprimé).
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -utilisateur ObjectClass
#### <a name="example-2"></a>EXEMPLE 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ordinateur ObjectClass -$false BackupOnly
### PARAMETERS
#### <a name="-targetou"></a>-TargetOU
Unité d’organisation cible pour rechercher des objets AD
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-backuponly"></a>-BackupOnly
BackupOnly ne supprime aucun certificat dans AD
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-objectclass"></a>-ObjectClass
Filtre Classe d’objet
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState
### SYNOPSIS
Réparer l’état de mise à niveau automatique d’Azure AD Connect
### SYNTAX
```
Repair-ADSyncToolsAutoUpgradeState
```
### DESCRIPTION
Résout un problème lié à la mise à niveau automatique datant de la build 1.1.524 (2017), qui désactive la vérification en ligne des nouvelles versions lorsque la mise à niveau automatique est activée.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Repair-ADSyncToolsAutoUpgradeState
```
## <a name="resolve-adsynctoolssqlhostaddress"></a>Resolve-ADSyncToolsSqlHostAddress
### SYNOPSIS
Résoudre un nom de serveur SQL
### SYNTAX
```
Resolve-ADSyncToolsSqlHostAddress [-Server] <String> [<CommonParameters>]
```
### DESCRIPTION
Fonctions et utilitaires SQL pour les diagnostics
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Resolve-ADSyncToolsSqlHostAddress -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nom du serveur SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="search-adsynctoolsadobject"></a>Search-ADSyncToolsADobject
### SYNOPSIS
Recherche un objet Active Directory dans la forêt Active Directory à l’aide de son UserPrincipalName, sAMAccountName ou DistinguishedName
### SYNTAX
```
Search-ADSyncToolsADobject [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
Prend en charge les requêtes multidomaines et retourne toutes les propriétés nécessaires, y compris mS-DS-ConsistencyGuid.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Search-ADSyncToolsADobject 'CN=user1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Search-ADSyncToolsADobject -Identity "user1@Contoso.com"
```
#### <a name="example-3"></a>EXEMPLE 3
```
Get-ADUser 'CN=user1,OU=Sync,DC=Contoso,DC=com' | Search-ADSyncToolsADobject
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Utilisateur cible dans AD pour définir ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolsmsdsconsistencyguid"></a>Set-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Définir le mS-DS-ConsistencyGuid d’un objet Active Directory
### SYNTAX
```
Set-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
Définit une valeur dans l’attribut mS-DS-ConsistencyGuid pour l’utilisateur Active Directory cible
Prend en charge les objets Active Directory dans les forêts multidomaines.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value '88666888-0101-1111-bbbb-1234567890ab'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-3"></a>EXEMPLE 3
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' '8d6c6818-018c-4f11-9bb8-1b04e2caa1b6'
```
#### <a name="example-4"></a>EXEMPLE 4
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-5"></a>EXEMPLE 5
```
'88666888-0101-1111-bbbb-1234567890ab' | Set-ADSyncToolsMsDsConsistencyGuid -Identity User1
```
#### <a name="example-6"></a>EXEMPLE 6
```
'GGhsjYwBEU+buBsE4sqhtg==' | Set-ADSyncToolsMsDsConsistencyGuid User1
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
Objet cible dans AD pour lequel définir mS-DS-ConsistencyGuid
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-value"></a>-Value
Valeur à définir (ImmutableId, tableau d’octets, GUID, chaîne GUID ou chaîne Base64)
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="set-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
Définit les paramètres Client\Server TLS 1.2 pour le .NET Framework
### SYNTAX
```
Set-ADSyncToolsTls12 [[-Enabled] <Boolean>] [<CommonParameters>]
```
### DESCRIPTION
Définit les entrées de Registre dans le but d’activer ou de désactiver TLS 1.2 pour le .NET Framework :

| Chemin d’accès                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | Activé                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | Activé                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

L’exécution de l’applet de commande sans aucun paramètre active TLS 1.2 pour .NET Framework

### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Set-ADSyncToolsTls12
```
#### <a name="example-2"></a>EXEMPLE 2
```
Set-ADSyncToolsTls12 -Enabled $true
```
### PARAMETERS
#### <a name="-enabled"></a>-Enabled
TLS 1.2 activé
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: True
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
### RELATED LINKS
Pour plus d’informations : [Application de TLS 1.2 pour Azure AD Connect](reference-connect-tls-enforcement.md).

## <a name="test-adsynctoolssqlnetworkport"></a>Test-ADSyncToolsSqlNetworkPort
### SYNOPSIS
Tester le port réseau SQL Server
### SYNTAX
```
Test-ADSyncToolsSqlNetworkPort [[-Server] <String>] [[-Port] <String>]
```
### DESCRIPTION
Fonctions et utilitaires SQL pour les diagnostics
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01'
```
#### <a name="example-2"></a>EXEMPLE 2
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01' -Port 1433
```
### PARAMETERS
#### <a name="-server"></a>-Server
Nom du serveur SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Port SQL Server
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport
### SYNOPSIS
Crée un fichier de trace à partir d’une étape d’importation Active Directory
### SYNTAX
#### <a name="adconnectorxml"></a>ADConnectorXML
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADConnectorXML] <String> [<CommonParameters>]
```
#### <a name="adwatermarkinput"></a>ADwatermarkInput
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADwatermark] <String> [<CommonParameters>]
```
### DESCRIPTION
Effectue le suivi de toutes les requêtes LDAP d’une importation Active Directory à partir d’un point de contrôle de limites Active Directory donné (également appelé « cookie de partition »). Crée un fichier de trace « .\ADimportTrace_yyyyMMddHHmmss.log » dans le dossier actif.
Pour utiliser -ADConnectorXML, accédez à Synchronization Service Manager, cliquez avec le bouton droit sur votre connecteur AD, puis sélectionnez « Exporter le connecteur... ».
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Trace Active Directory Import for user objects by providing an AD Connector XML file
```
Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Filter '(&(objectClass=user))' -ADConnectorXML .\ADConnector.xml
#### <a name="example-2"></a>EXEMPLE 2
```
Trace Active Directory Import for all objects by providing the Active Directory watermark (cookie) and AD Connector credential
```
$creds = Get-Credential Trace-ADSyncToolsADImport -DC 'DC1.contoso.com' -RootDN 'DC=Contoso,DC=com' -Credential $creds -ADwatermark "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
### PARAMETERS
#### <a name="-dc"></a>-DC
Contrôleur de domaine cible
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-rootdn"></a>-RootDN
Nom de domaine de la racine de forêt
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
Type des objets AD devant faire l’objet d’un suivi.
Utiliser '(&(objectClass=*))' pour tous les types d’objets
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Fournir les informations d’identification pour envoyer une requête LDAP à AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-ssl"></a>-SSL
Connexion SSL
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adconnectorxml"></a>-ADConnectorXML
Fichier XML d’exportation du connecteur Active Directory : cliquez avec le bouton droit sur le connecteur AD, puis sélectionnez « Exporter le connecteur... ».
```yaml
Type: String
Parameter Sets: ADConnectorXML
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adwatermark"></a>-ADwatermark
Entrez manuellement le filigrane, au lieu d’employer un fichier XML, par exemple, $ADwatermark = "TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)"
```yaml
Type: String
Parameter Sets: ADwatermarkInput
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery
### SYNOPSIS
Suivi des requêtes LDAP
### SYNTAX
```
Trace-ADSyncToolsLdapQuery [-RootDN] <String> [-Credential] <PSCredential> [[-Server] <String>]
 [[-Port] <Int32>] [-Filter <String>] [<CommonParameters>]
```
### DESCRIPTION
{{ Renseigner la description }}
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Trace-ADSyncToolsLdapQuery -RootDN "DC=Contoso,DC=com" -Credential $Credential
```
### PARAMETERS
#### <a name="-rootdn"></a>-RootDN
DistinguishedName de la forêt ou du domaine
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Informations d’identification AD
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-server"></a>-Server
Nom du contrôleur de domaine (facultatif)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
Port du contrôleur de domaine (par défaut : 389)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: 389
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
Filtre LDAP (par défaut : objectClass=*)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).
## <a name="update-adsynctoolssourceanchor"></a>Update-ADSyncToolsSourceAnchor
### SYNOPSIS
Met à jour des utilisateurs avec la nouvelle valeur ConsistencyGuid (ImmutableId)
### SYNTAX
```
Update-ADSyncToolsSourceAnchor [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String> [-Action] <String>
 [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
Met à jour des utilisateurs avec la nouvelle valeur ConsistencyGuid (ImmutableId) provenant du rapport ConsistencyGuid. Cette fonction prend en charge le commutateur WhatIf. Remarque : Le rapport ConsistencyGuid doit être importé sous forme de fichier délimité par des tabulations.
### EXAMPLES
#### <a name="example-1"></a>EXEMPLE 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2 -WhatIf
```
#### <a name="example-2"></a>EXEMPLE 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-action"></a>-Action
Action
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
Nom du fichier de sortie pour les fichiers LOG
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
Montre ce qui se passe en cas d’exécution de l’applet de commande.
L’applet de commande n’est pas exécutée.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
Vous demande une confirmation avant d’exécuter l’applet de commande.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
Cette applet de commande prend en charge les paramètres courants : -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction et -WarningVariable. Pour plus d’informations, consultez [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters).