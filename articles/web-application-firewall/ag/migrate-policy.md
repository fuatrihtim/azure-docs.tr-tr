---
title: Azure Application Gateway için WAF ilkelerini geçirme
description: Azure PowerShell kullanarak Azure Web uygulaması güvenlik duvarı ilkelerini geçirmeyi öğrenin.
services: web-application-firewall
ms.topic: conceptual
author: vhorne
ms.service: web-application-firewall
ms.date: 04/16/2020
ms.author: ant
ms.openlocfilehash: 9b60075eb861fe598a05ba014a7def96bc815d06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/19/2021
ms.locfileid: "97653019"
---
# <a name="migrate-web-application-firewall-policies-using-azure-powershell"></a>Azure PowerShell kullanarak Web uygulaması güvenlik duvarı ilkelerini geçirme

Bu betik, bir WAF yapılandırması veya özel kurallardan yalnızca WAF ilkesindeki bir tam WAF ilkesi arasında geçiş yapmayı kolaylaştırır. Portalda *WAF ilkesine geçiş* söyleyen bir uyarı görebilirsiniz veya geomatch özel kuralları (Önizleme), site başına WAF ILKESI ve URI başına WAF ilkesi (Önizleme) veya bot azaltma RuleSet (Önizleme) gibi yeni WAF özelliklerinin olmasını isteyebilirsiniz. Bu özelliklerden herhangi birini kullanmak için, uygulama ağ geçidiniz ile ilişkili tam bir WAF ilkesi gerekir. 

Yeni bir WAF ilkesi oluşturma hakkında daha fazla bilgi için bkz. [Application Gateway Için Web uygulaması güvenlik duvarı Ilkeleri oluşturma](create-waf-policy-ag.md). Geçirme hakkında daha fazla bilgi için bkz. [WAF Ilkesine geçme](create-waf-policy-ag.md#migrate-to-waf-policy).

## <a name="to-migrate-to-waf-policy-using-the-migration-script"></a>Geçiş betiği kullanılarak WAF ilkesine geçiş yapmak için

Geçiş betiğini çalıştırmak için aşağıdaki adımları kullanın: 

1. Aşağıdaki Cloud Shell penceresini açın veya Portal içinden bir tane açın.
2. Betiği Cloud Shell penceresine kopyalayın ve çalıştırın.
3. Betik, abonelik KIMLIĞI, kaynak grubu adı, WAF yapılandırmasının ilişkilendirildiği Application Gateway adı ve oluşturulacak yeni WAF ilkesinin adını sorar. Bu girdileri girdikten sonra betik çalışır ve yeni WAF ilkenizi oluşturur
4. Yeni WAF ilkesini uygulama ağ geçidiniz ile ilişkilendirin. Portalda WAF ilkesine gidin ve **Ilişkili uygulama ağ geçitleri** sekmesini seçin. **Application Gateway ilişkilendir** ' i seçin ve ardından WAF ilkesi ile ilişkilendirilecek Application Gateway seçin.

> [!NOTE]
> Aşağıdaki koşullar mevcutsa betik bir geçişi tamamlamaz:
> - Kuralın tamamı devre dışı bırakıldı. Bir geçişi gerçekleştirmek için RuleGroup 'un tamamının devre dışı bırakılmadığından emin olun.
> - *Eşittir any* işleci olan bir dışlama girişi. Bir geçişi gerçekleştirmek için, *eşittir any* işlecinin hariç tutma girişlerinin mevcut olmadığından emin olun.
>
> Daha fazla bilgi için, betikteki *ValidateInput* işlevine bakın.

```azurepowershell-interactive
<#PSScriptInfo
.DESCRIPTION
Will be used to migrate to the application-gateway to a top level waf policy experience.

.VERSION 1.0

.GUID b6fedd43-ebd0-41ed-9847-4f1c1c43be22

.AUTHOR Venkat.Krishnan

.PARAMETER subscriptionId 
Subscription Id of where the resources are present.
.PARAMETER resourceGroupName
Resource-group where the resources are present.
.PARAMETER applicationGatewayName
Application-Gateway name
.PARAMETER wafPolicyName
Name of the web application firewall policy

.EXAMPLE
./migrateToWafPolicy.ps1 -subscriptionId  <your-subscription-id> -applicationGatewayName <your-appgw-name> -resourceGroupName <your-resource-group-name> -wafPolicyName <new-waf-policy-name>
#>

param(
    [Parameter(Mandatory=$true)]
    [string] $subscriptionId,
    [Parameter(Mandatory=$true)]
    [string] $resourceGroupName,
    [Parameter(Mandatory=$true)]
    [string] $applicationGatewayName,
    [Parameter(Mandatory=$true)]
    [string] $wafPolicyName
)

function ValidateInput ($appgwName, $resourceGroupName) {
    # Obtain the application-gateway
    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName
    if (-not $appgw) {
        Write-Error "ApplicationGateway: $applicationGatewayName is not present in ResourceGroup: $resourceGroupName"
        return $false
    }

    # Check if already have a global firewall policy
    if ($appgw.FirewallPolicy) {
        $fp = Get-AzResource -ResourceId $appgw.FirewallPolicy.Id
        if ($fp.PolicySettings) {
            Write-Error "ApplicationGateway: $applicationGatewayName already has a global firewall policy: $fp.Name. Please use portal for changing the policy."
            return $false
        }
    }

    if ($appgw.WebApplicationFirewallConfiguration) {
        # Throw an error, since ruleGroup disabled case can't be migrated now.
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                if ($disabled.Rules.Count -eq 0) {
                    $ruleGroupName = $disabled.RuleGroupName
                    Write-Error "The ruleGroup '$ruleGroupName' is disabled. Currently we can't migrate to a firewall policy when an entire ruleGroup is disabled. This feature will be delivered shortly. To continue, kindly ensure the entire rulegroups are not disabled. "
                    return $false
                }
            }
        }

        # Throw an error when exclusion entry with 'EqualsAny' operator is present
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($null -ne $excl.MatchVariable -and $null -eq $excl.SelectorMatchOperator -and $null -eq $excl.Selector) {
                    Write-Error " You have an exclusion entry(s) with the 'Equals any' operator. Currently we can't migrate to a firewall policy with 'Equals Any' operator. This feature will be delivered shortly. To continue, kindly ensure exclusion entries with 'Equals Any' operator is not present. "
                    return $false
                }
            }
        }
    }

    if ($appgw.Sku.Name -ne "WAF_v2" -or $appgw.Sku.Tier -ne "WAF_v2") {
        Write-Error " Cannot associate a firewall policy to application gateway :$applicationGatewayName since the Sku is not on WAF_v2"
        return $false
    }

    return $true
}

function Login() {
    $context = Get-AzContext
    if ($null -eq $context -or $null -eq $context.Account) {
        Login-AzAccount
    }
}

function createNewTopLevelWafPolicy ($subscriptionId, $resourceGroupName, $applicationGatewayName, $wafPolicyName) {
    Select-AzSubscription -Subscription $subscriptionId
    $retVal = ValidateInput -appgwName $applicationGatewayName -resourceGroupName $resourceGroupName
    if (!$retVal) {
        return
    }

    $appgw = Get-AzApplicationGateway -Name $applicationGatewayName -ResourceGroupName $resourceGroupName

    # Get the managedRule and PolicySettings
    $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule
    $policySetting = New-AzApplicationGatewayFirewallPolicySetting
    if ($appgw.WebApplicationFirewallConfiguration) {
        $ruleGroupOverrides = [System.Collections.ArrayList]@()
        if ($appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
            foreach ($disabled in $appgw.WebApplicationFirewallConfiguration.DisabledRuleGroups) {
                $rules = [System.Collections.ArrayList]@()
                if ($disabled.Rules.Count -gt 0) {
                    foreach ($rule in $disabled.Rules) {
                        $ruleOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleOverride -RuleId $rule
                        $_ = $rules.Add($ruleOverride)
                    }
                }
                
                $ruleGroupOverride = New-AzApplicationGatewayFirewallPolicyManagedRuleGroupOverride -RuleGroupName $disabled.RuleGroupName -Rule $rules
                $_ = $ruleGroupOverrides.Add($ruleGroupOverride)
            }
        }

        $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion 
        if ($ruleGroupOverrides.Count -ne 0) {
            $managedRuleSet = New-AzApplicationGatewayFirewallPolicyManagedRuleSet -RuleSetType $appgw.WebApplicationFirewallConfiguration.RuleSetType -RuleSetVersion $appgw.WebApplicationFirewallConfiguration.RuleSetVersion -RuleGroupOverride $ruleGroupOverrides
        }
    
        $exclusions = [System.Collections.ArrayList]@()  
        if ($appgw.WebApplicationFirewallConfiguration.Exclusions) {
            foreach ($excl in $appgw.WebApplicationFirewallConfiguration.Exclusions) {
                if ($excl.MatchVariable -and $excl.SelectorMatchOperator -and $excl.Selector) {
                    $exclusionEntry = New-AzApplicationGatewayFirewallPolicyExclusion -MatchVariable  $excl.MatchVariable -SelectorMatchOperator $excl.SelectorMatchOperator -Selector $excl.Selector
                    $_ = $exclusions.Add($exclusionEntry)
                }
            }
        }
    
        $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet
        $exclCount = $exclusions.Count
        if ($exclCount -ne 0) {
            $managedRule = New-AzApplicationGatewayFirewallPolicyManagedRule -ManagedRuleSet $managedRuleSet -Exclusion $exclusions
        }

        
        $policySetting = New-AzApplicationGatewayFirewallPolicySetting -MaxFileUploadInMb $appgw.WebApplicationFirewallConfiguration.FileUploadLimitInMb -MaxRequestBodySizeInKb $appgw.WebApplicationFirewallConfiguration.MaxRequestBodySizeInKb -Mode Detection -State Disabled
        if ($appgw.WebApplicationFirewallConfiguration.FirewallMode -eq "Prevention") {
            $policySetting.Mode = "Prevention"
        }

        if ($appgw.WebApplicationFirewallConfiguration.Enabled) {
            $policySetting.State = "Enabled"
        }

        $policySetting.RequestBodyCheck = $appgw.WebApplicationFirewallConfiguration.RequestBodyCheck;
    }

    if ($appgw.FirewallPolicy) {
        $customRulePolicyId = $appgw.FirewallPolicy.Id
        $rg = Get-AzResourceGroup -Id $customRulePolicyId
        $crPolicyName = $customRulePolicyId.Substring($customRulePolicyId.LastIndexOf("/") + 1)
        $customRulePolicy = Get-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $crPolicyName
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -ResourceGroupName $rg.ResourceGroupName -Name $wafPolicyName -CustomRule $customRulePolicy.CustomRules -ManagedRule $managedRule -PolicySetting $policySetting -Location $appgw.Location
    } else { 
        $wafPolicy = New-AzApplicationGatewayFirewallPolicy -Name $wafPolicyName -ResourceGroupName $resourceGroupName -PolicySetting $policySetting -ManagedRule $managedRule -Location $appgw.Location
    }

    if (!$wafPolicy) {
        return
    }

    $appgw.FirewallPolicy = $wafPolicy
    $appgw = Set-AzApplicationGateway -ApplicationGateway $appgw
    Write-Host " firewallPolicy: $wafPolicyName has been created/updated successfully and applied to applicationGateway: $applicationGatewayName!"
    return $wafPolicy
}

function Main() {
    Login
    $policy = createNewTopLevelWafPolicy -subscriptionId $subscriptionId -resourceGroupName $resourceGroupName -applicationGatewayName $applicationGatewayName -wafPolicyName $wafPolicyName
    return $policy
}

Main
```
## <a name="next-steps"></a>Sonraki adımlar

[Web uygulaması güvenlik duvarı 'nın ilgili kural grupları ve kuralları](application-gateway-crs-rulegroups-rules.md)hakkında daha fazla bilgi edinin.
