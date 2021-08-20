---
title: 'Exemple de script : Créer ou modifier une stratégie de sauvegarde des fichiers et des dossiers'
description: Découvrez comment utiliser un script pour créer une nouvelle stratégie ou modifier la stratégie actuelle pour la sauvegarde des fichiers et des dossiers.
ms.topic: sample
ms.date: 06/23/2021
ms.openlocfilehash: 98f6509765a8681e68b0f1179e04f7d610369c74
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112560798"
---
# <a name="powershell-script-to-create-a-new-or-modify-the-current-file-and-folder-backup-policy"></a>Script PowerShell pour créer ou modifier une stratégie de sauvegarde des fichiers et des dossiers

Ce script vous aide à créer ou à modifier une stratégie de sauvegarde des fichiers et des dossiers pour le serveur protégé par l’agent MARS.

## <a name="sample-script"></a>Exemple de script

```azurepowershell
<#

.SYNOPSIS
 Modify file folder policy

.DESCRIPTION
 Modify file folder policy

.ROLE
Administrators

#>
param (
    [Parameter(Mandatory = $true)]
    [string[]]
    $filePath,
    [Parameter(Mandatory = $true)]
    [string[]]
    $daysOfWeek,
    [Parameter(Mandatory = $true)]
    [string[]]
    $timesOfDay,
    [Parameter(Mandatory = $true)]
    [int]
    $weeklyFrequency,

    [Parameter(Mandatory = $false)]
    [int]
    $retentionDays,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionWeeklyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionWeeks,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionMonthlyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionMonths,

    [Parameter(Mandatory = $false)]
    [Boolean]
    $retentionYearlyPolicy,
    [Parameter(Mandatory = $false)]
    [int]
    $retentionYears
)
Set-StrictMode -Version 5.0
$env:PSModulePath = (Get-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Session Manager\Environment' -Name PSModulePath).PSModulePath
Import-Module MSOnlineBackup
$ErrorActionPreference = "Stop"
Try {
    $timesOfDaySchedule = @()
    foreach ($time in $timesOfDay) {
        $timesOfDaySchedule += ([TimeSpan]$time)
    }
    $daysOfWeekSchedule = @()
    foreach ($day in $daysOfWeek) {
        $daysOfWeekSchedule += ([System.DayOfWeek]$day)
    }

    $schedule = New-OBSchedule -DaysOfWeek $daysOfWeekSchedule -TimesOfDay $timesOfDaySchedule -WeeklyFrequency $weeklyFrequency
    if ($daysOfWeekSchedule.Count -eq 7) {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy -and $retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy -and $retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionYearlyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        else {
            $retention = New-OBRetentionPolicy -RetentionDays $retentionDays
        }
    }
    else {
        if ($retentionWeeklyPolicy -and $retentionMonthlyPolicy -and $retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionWeeklyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionWeeklyPolicy -and $retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionYearlyPolicy -and $retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
        elseif ($retentionWeeklyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionWeeklyPolicy:$true -WeekDaysOfWeek $daysOfWeekSchedule -WeekTimesOfDay $timesOfDaySchedule -RetentionWeeks $retentionWeeks
        }
        elseif ($retentionMonthlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionMonthlyPolicy:$true -MonthDaysOfWeek $daysOfWeekSchedule -MonthTimesOfDay $timesOfDaySchedule -RetentionMonths $retentionMonths
        }
        elseif ($retentionYearlyPolicy) {
            $retention = New-OBRetentionPolicy -RetentionYearlyPolicy:$true -YearDaysOfWeek $daysOfWeekSchedule -YearTimesOfDay $timesOfDaySchedule -RetentionYears $retentionYears
        }
    }

    $oldPolicy = Get-OBPolicy
    if ($oldPolicy) {
        $ospec = Get-OBFileSpec $oldPolicy

        $p = Remove-OBFileSpec -FileSpec $ospec -Policy $oldPolicy -Confirm:$false

        $fileSpec = New-OBFileSpec -FileSpec $filePath

        Add-OBFileSpec -Policy $p -FileSpec $fileSpec -Confirm:$false
        Set-OBSchedule -Policy $p -Schedule $schedule -Confirm:$false
        Set-OBRetentionPolicy -Policy $p -RetentionPolicy $retention -Confirm:$false
        Set-OBPolicy -Policy $p -Confirm:$false
        $p
    }
    else {
        $policy = New-OBPolicy
        $fileSpec = New-OBFileSpec -FileSpec $filePath
        Add-OBFileSpec -Policy $policy -FileSpec $fileSpec
        Set-OBSchedule -Policy $policy -Schedule $schedule
        Set-OBRetentionPolicy -Policy $policy -RetentionPolicy $retention
        Set-OBPolicy -Policy $policy -Confirm:$false
    }
}
Catch {
    if ($error[0].ErrorDetails) {
        throw $error[0].ErrorDetails
    }
    throw $error[0]
}

```

## <a name="how-to-execute-the-script"></a>Comment exécuter le script

1. Enregistrez le script ci-dessus sur votre ordinateur avec le nom de votre choix et l’extension .ps1.
1. Exécutez le script en indiquant les paramètres suivants :
   - Planification de la sauvegarde et nombre de jours, semaines, mois ou années pendant lesquels la sauvegarde doit être conservée.
   - – filePath : Fichiers et dossiers à inclure ou à exclure de la sauvegarde.

## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](../backup-client-automation.md) sur l’utilisation de PowerShell pour déployer et gérer des sauvegardes locales à l’aide de l’agent MARS.