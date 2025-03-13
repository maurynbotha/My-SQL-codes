# My-SQL-codes

SELECT DISTINCT RIGHT(CONCAT('0000000000', r.MerchantNumber), 12) AS MerchantNumber,
    --r.CompanyName AS MerchantName,
    --r.AccountName,
    r.AccountNumber,
   -- r.MobileNumber,
    --r.EmailAddress,
    r.CreatedAt AS DateOnboarded,
    rb.Name as BusinessDescription,
    s.Name AS Account_Officer,
    COALESCE(mo.`sector/directorate`, se.Name) AS `Sector`,
    COALESCE(mo.division, g.Name) AS `Group`,
    COALESCE(mo.region, reg.Name) AS `Region`,
    COALESCE(CAST(b.Code AS VARCHAR(255)), CAST(mo.Branch_code AS VARCHAR(255))) AS BranchCode,
    COALESCE(mo.Branch_Name, b.Name) AS BranchName,
    c.Name AS ChannelName,
    COALESCE(j.State, st.Name) AS State,
    ob.name AS BankName,
    s.DsaCode,
    s.DSALead,
    s.DsaName,
    CASE
        WHEN r.Referral = '' THEN CONCAT(
               COALESCE(c.Name, ''),
                 '-',
                s.Name,
                 '-',
               s.AgentorAffiliateCode,
                '  ',
               COALESCE(g.Name, '-'),
                '  ',
               COALESCE(se.Name, '-')
               )
          ELSE r.Referral
    END AS Referral
FROM onboarding.registrations r
LEFT JOIN medusa.terminals t 
ON RIGHT(CONCAT('0000000000', t.merchantid), 12) = RIGHT(CONCAT('0000000000', r.MerchantNumber), 12)
LEFT JOIN dwh.jira_merchant_pos_onboard j
ON RIGHT(CONCAT('0000000000', j.Hydrogen_MID), 12) = RIGHT(CONCAT('0000000000', r.MerchantNumber), 12)
LEFT JOIN onboarding.sources s ON s.RegistrationId = r.Id
LEFT JOIN onboarding.channels c ON c.Id = s.Channel
LEFT JOIN onboarding.groups g ON g.Id = s.Group
LEFT JOIN onboarding.sectors se ON se.Id = s.Sector
LEFT JOIN onboarding.states st ON st.Id = r.City
LEFT JOIN onboarding.banks ob on r.BankName = ob.Id
LEFT JOIN onboarding.Regions reg ON reg.Id = s.Region
LEFT JOIN onboarding.merchanttbusinesss rb on rb.id = r.BusinessCategory
LEFT JOIN onboarding.accessbankbranches b ON b.id = s.BranchCode
left JOIN onboarding.all_access_terminals_uploaded mo 
ON upper(cast(t.terminalid as string)) = upper(cast(mo.terminalid as string))
--RIGHT(CONCAT('0000000000', r.MerchantNumber), 12) = RIGHT(CONCAT('0000000000', mo.MerchantNumber), 12)
where r.companyName not in ('HYDROGEN PAYMENT SERVICES COMPANY LIMITED',
'HYDROGEN PAYMENT SERVICES COMPANY LIMITED',
'Hydrogen payment services limited',
'HYDROGEN PAYMENT SERVICES COMPANY LIMITED',
'Hydrogen Payment Service Company Limited',
'HYDROGEN PAYMENT SERVICES COMPANY LIMITED (HYDROGEN PERSEUS)',
'HYDROGEN PAYMENT SERVICES COMPANY LIMITED'
)
