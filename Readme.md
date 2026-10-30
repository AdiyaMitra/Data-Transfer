Use PAC_Policy

BEGIN TRY
-- to run comment out the goto line below
--     goto Stop -- while developing to prevent accidental run
BEGIN TRANSACTION 


declare @XmlData xml; 
declare @HistoryId Bigint;
declare @Policynumber  nvarchar(50);


declare @WrittenPremium decimal(19, 4);
declare @Change decimal(19, 4);
declare @TotalPremium decimal(19, 4);
declare @GrossPremium decimal(19, 4);
declare @PartTimeCredit decimal(19, 4);
declare @LCredit decimal(19, 4);
declare @Credit decimal(19, 4);
declare @RiskManagementCredit decimal(19, 4);

 
declare @StateTax decimal(19,4);

declare @EffectiveDate date;
declare @ExpirationDate date;
declare @TotalSurcharges int;
declare @ClaimsPremium decimal(19,4);
--declare @TotalSurcharges decimal(19,4);
declare @TodaysDate date;


Set @TodaysDate=GETDATE();
Set @XmlData=null; 
Set @HistoryId=720764; 
Set @Policynumber=3222566;

Set @TotalPremium =2040.00;
Set @Change =2040.00;
Set @WrittenPremium =2040.00;



-- Create Temp table with required XmlData
Create table #TempTable (PolicyNum nvarchar(50), HistId bigint, XmlData text) 
INSERT INTO #TempTable (PolicyNum, HistId, XmlData) 
              SELECT Policynumber,HistoryId,XmlData 
              FROM History Where HistoryId =@HistoryId;

 --Update Value in Temp Table
  Select @XmlData=Cast(XmlData as XML) from #TempTable where HistId =@HistoryId;
 
   
  
  SET @XmlData.modify('replace value of (/session/data/TotalPremium/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/Premium/text())[1] with sql:variable("@TotalPremium")')

  
   SET @XmlData.modify('replace value of (/session/data/policy/line/Premium/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/RiskCoverages/coverage[1]/DisplayRMBR/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/RiskCoverages/coverage[1]/Premium/text())[1] with sql:variable("@TotalPremium")')

  SET @XmlData.modify('replace value of (/session/data/TotalPurePremium/text())[1] with sql:variable("@TotalPremium")')

  SET @XmlData.modify('replace value of (/session/data/policy/PurePremium/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/CancelledPremiumForTail/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/PurePremium/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPremiums/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPurePremiums/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/Premium/text())[1] with sql:variable("@TotalPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/PurePremium/text())[1] with sql:variable("@TotalPremium")')

  SET @XmlData.modify('replace value of (/session/data/TotalPurePremium/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/ManualPremiumChange/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/LineTotalPurePremium/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/PurePremium/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/PurePremiumChange/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPremiums/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPurePremiums/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/change/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/PurePremium/change/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/RiskCoverages/coverage[1]/change/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/TotalPremium/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/PremiumChange/text())[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/LineTotalPremium/@change)[1] with sql:variable("@Change")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/change/text())[1] with sql:variable("@Change")')



  SET @XmlData.modify('replace value of (/session/data/TotalPremium/@written)[1] with sql:variable("@WrittenPremium")')  
SET @XmlData.modify('replace value of (/session/data/policy/PremiumWritten/text())[1] with sql:variable("@WrittenPremium")')
SET @XmlData.modify('replace value of (/session/data/policy/ManualPremiumWritten/text())[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/LineTotalPremium/@written)[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/LineTotalPurePremium/@written)[1] with sql:variable("@WrittenPremium")')  
SET @XmlData.modify('replace value of (/session/data/policy/PurePremium/@written)[1] with sql:variable("@WrittenPremium")')
SET @XmlData.modify('replace value of (/session/data/policy/line/written/text())[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/PurePremiumWritten/text())[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPremiums/@written)[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/RiskTotalPurePremiums/@written)[1] with sql:variable("@WrittenPremium")')
  SET @XmlData.modify('replace value of (/session/data/policy/line/risk/written/text())[1] with sql:variable("@WrittenPremium")')
   SET @XmlData.modify('replace value of (/session/data/policy/line/risk/RiskCoverages/coverage[1]/written/text())[1] with sql:variable("@WrittenPremium")')




  
  
                              Update #TempTable set XmlData=Cast(@XmlData as varchar(Max)) where HistId =@HistoryId;

-- Update into History table

                              UPDATE History SET History.XMLData = #TempTable.XmlData FROM History,#TempTable WHERE History.HistoryId = #TempTable.HistId 
                              UPDATE History SET History.WrittenPremium=@WrittenPremium , ChangeDate=@TodaysDate, History.ChangePremium=@Change  FROM History,#TempTable WHERE History.HistoryId = #TempTable.HistId

                              DROP TABLE #TempTable 

COMMIT

Stop:

END TRY
BEGIN CATCH

       select ERROR_MESSAGE() as ErrorMessage

    IF @@TRANCOUNT > 0
        ROLLBACK
END CATCH



SELECT
    HistoryId,
    CAST(XmlData AS XML).value(
        '(/session/data/TotalPremium/text())[1]',
        'decimal(19,4)'
    ) AS TotalPremium,

    CAST(XmlData AS XML).value(
        '(/session/data/policy/Premium/text())[1]',
        'decimal(19,4)'
    ) AS PolicyPremium,

    CAST(XmlData AS XML).value(
        '(/session/data/policy/PremiumWritten/text())[1]',
        'decimal(19,4)'
    ) AS PremiumWritten
FROM History
WHERE HistoryId = 720764;



SELECT
    HistoryId,
    PolicyNumber,
    CAST(XmlData AS XML)
FROM History
WHERE HistoryId = 720764;​‌


This stored procedure is making half of the changes in this XML from 3294.00 to 2040.00​‌

<session id="42611" clientId="08DCF97D6C9D7004">
  <properties context="EarnedPremiumLetter,Assistant" id="pFCAEF096340E4795A6C90BA612BB2A18" dateModified="2026-02-24" manuscript="Carrier_Tail_Rules_1_0_0" caption="TransACT  Tail  Rules  (1.0.0)" engineVersion="2.0.0" cultureCode="en-US" currencyCode="USD" cultureName="United States [english]">
    <userName>OMSNICPDC\efigueroa</userName>
  </properties>
  <data id="d3012E2DB3172475C83B224FA727BC73A">
    <AutoRenewFlag>0</AutoRenewFlag>
    <account id="a6CC579A076EA4B148011AE3801EB5539">
      <ForeignAddress>0</ForeignAddress>
      <NameType>Individual</NameType>
      <person id="p210A193C991444E89039D76002898544">
        <LastName>08DCF97D6C9D7004_LN</LastName>
        <FirstName>Jack</FirstName>
        <MiddleName />
        <Prefix>DR</Prefix>
        <Suffix>DDS</Suffix>
        <SSN />
        <USCitizen>1</USCitizen>
      </person>
      <Gender>M</Gender>
      <DateOfBirth>1972-10-24</DateOfBirth>
      <address id="a2AE1E68295B8447E8ECDE2973A23AE78">
        <Address1 prior="Address1-08DCF97D6C9D7004" id="A2138B20233BB435E84D083B5E7745664">Address1-08DCF97D6C9D7004</Address1>
        <Address2 prior="Address2-08DCF97D6C9D7004" id="AA67D575846FC46BA9D4F310CE3A259C1" />
        <City prior="" id="C2DAF7E0B4CB742A1A33F14919224758E">Williamsville</City>
        <State prior="" id="SCED38EFFD9D14A33B08080D9AA89165F">NY</State>
        <ZipCode prior="" id="Z9A2370E77C1A49E98971D9EDF281FBE7">14221</ZipCode>
        <ZipCodeExtension prior="" id="ZEA71CBF9F5E94468ACB04421A8F4560E" />
        <DisplayState prior="" id="D29126AC1932E4009B58C84C7B8C08258">NY</DisplayState>
        <DisplayZipCodeAndExtension prior="" id="D008ED4F5EE964F0DB2B795059F5E99C4">14221</DisplayZipCodeAndExtension>
        <AddressFormat>US</AddressFormat>
        <Type>Physical</Type>
        <IsPhysicalAddress>1</IsPhysicalAddress>
        <RuralAddressIndicator>0</RuralAddressIndicator>
      </address>
      <County />
      <ForeignAddress7 />
      <Address3 prior="Address3-08DCF97D6C9D7004" id="A23A32ECD8F964BD58FBCBE9205C9459C" />
      <Address4 prior="Address4-08DCF97D6C9D7004" id="AB30B9F2999814BABBE4BEFE55741A935" />
      <MailingAttention prior="" id="M91B744D25AC3478E9194BB16477034E5" />
      <Email>08DCF97D6C9D7004@123.abc</Email>
      <PrimaryPhone>7163331822</PrimaryPhone>
      <PartyRecordID>08DCF97D6C9D7004</PartyRecordID>
      <PartyEducationCount>1</PartyEducationCount>
      <CustomerID>1086177</CustomerID>
      <BillingAddress1 prior="BILADDR1-08DCF97D6C9D7004" id="BE430479B1D394293BE23E1BF08FDB739">BILADDR1-08DCF97D6C9D7004</BillingAddress1>
      <BillingAttention prior="" id="B7D299F7DEC9443DEB46FB24414BC35C8" />
      <BillingAddress2 prior="BILADDR2-08DCF97D6C9D7004" id="B54204AA8F7B1445888EC3382945A407A" />
      <BillingAddress3 prior="BILADDR3-08DCF97D6C9D7004" id="BE2730D2A3E474502BEC68C2050F8B7BA" />
      <BillingAddress4 prior="BILADDR4-08DCF97D6C9D7004" id="B053E141560964C90A0342CC319C18458" />
      <BillingCity prior="" id="BFA48EBF3EA6D4982A793F17546FCA284">Williamsville</BillingCity>
      <BillingZipCode prior="" id="BCEDBDCF0107A4186B76B292B821B4C3C">14221</BillingZipCode>
      <BillingState prior="" id="B8F3437E2D7714B15AE8B4FD4F212C985">NY</BillingState>
      <BillingZipCodeExtention prior="" id="B5D2E977DA8B2433C9FF693807E9EFAEF" />
      <SuffixCode>08</SuffixCode>
      <ForeignAddress1 />
      <ForeignAddress2 />
      <ForeignAddress3 />
      <ForeignAddress4 />
      <ForeignAddress5 />
      <ForeignAddress6 />
      <LocationDetailEditMode>Edit</LocationDetailEditMode>
      <location id="l6DD8C579BC6149B7BD9FECA82DA1F50B">
        <AddressFormat>US</AddressFormat>
        <City>Buffalo</City>
        <State>NY</State>
        <County>Erie</County>
        <ZipCode>14221</ZipCode>
        <ZipCodePlaceHolder>14221</ZipCodePlaceHolder>
        <Description>Concierge Dental Group</Description>
        <Address1>LOCADDR1-08DCF97D6C9D7004</Address1>
        <Address2>LOCADDR2-08DCF97D6C9D7004</Address2>
        <Address3>LOCADDR3-08DCF97D6C9D7004</Address3>
        <Address4>LOCADDR4-08DCF97D6C9D7004</Address4>
        <TaxJurisdiction />
        <Territory>NY003</Territory>
        <NoOfHoursWorked>35</NoOfHoursWorked>
        <FIPS>0</FIPS>
        <PercentOfBusiness>0</PercentOfBusiness>
      </location>
      <Name>Jack  08DCF97D6C9D7004_LN DDS</Name>
      <NameFullWithMiddleInitial>Jack  08DCF97D6C9D7004_LN DDS</NameFullWithMiddleInitial>
      <HighestRatedTerritory>003</HighestRatedTerritory>
      <HighestRatingTerritoryFortress prior="" id="H7308120505D242D29E293E109B7B8678">003</HighestRatingTerritoryFortress>
      <AccountInputDisplayConcatenatedAttentionAndFullAddress>159 Crown Royal Drive
Williamsville NY 14221
 
 
 
 </AccountInputDisplayConcatenatedAttentionAndFullAddress>
      <DisplayBillingZipCodeExtention prior="" id="D74EFFB89E1BE473E86DB54E145DEBCAB">14221</DisplayBillingZipCodeExtention>
      <BillingAddressDisplayConcatenatedAttentionAndFullAddress>159 Crown Royal Drive
Williamsville NY 14221
 
 
 
 </BillingAddressDisplayConcatenatedAttentionAndFullAddress>
      <AccountPrivateVIPFlag>0</AccountPrivateVIPFlag>
      <MailAddHeader>Mailing Address</MailAddHeader>
      <BillAddHeader>Billing Address</BillAddHeader>
      <accountProcessing id="a3079AD8482D843ADAF5CA01DCD90EAA4" />
      <InsuredName prior="Jack  08DCF97D6C9D7004_LN DDS" id="IBAECAFE1886B4874ACC01C621ED7202D">Jack  08DCF97D6C9D7004_LN DDS</InsuredName>
      <EntityName />
      <response id="r9833604652FE4433A58EDF0164587DA0">
        <OldName />
      </response>
    </account>
    <policy id="pAF47FE4903BB4A7C8EF6F6D872B77720">
      <policyProcessing id="p8B4C48902A6E457997EC9CC4052E1F8E">
        <CurrentPolicyNumberIdentifier>3222566</CurrentPolicyNumberIdentifier>
        <ReferralRuleIsBindable>0</ReferralRuleIsBindable>
        <Value>1</Value>
        <StoreReferralAnswers>0</StoreReferralAnswers>
      </policyProcessing>
      <UnderwritingCompany>Fortress</UnderwritingCompany>
      <ConvertedPolicyIndicator>0</ConvertedPolicyIndicator>
      <IsConvertedPolicy>0</IsConvertedPolicy>
      <RekeyPolicyIndicator>0</RekeyPolicyIndicator>
      <IsPolicyVisited>0</IsPolicyVisited>
      <EffectiveDate>2025-11-07</EffectiveDate>
      <InsuredSince>2024-11-07</InsuredSince>
      <Term>12</Term>
      <OnLineQuote>0</OnLineQuote>
      <ProductType>PLPrimary</ProductType>
      <Status>Cancelled</Status>
      <line description="MedicalProfessionalLiability" id="lFA5CABEA790548FBB965FBD3B0568752" active="0" markForDelete="1">
        <Type>MedicalProfessionalLiability</Type>
        <underwritingquestions id="uD70ABDB239D54BD985441A497617A677">
          <ParticipantDrugAlcoholProgram>0</ParticipantDrugAlcoholProgram>
          <PatientAlcoholProgramIndividual>0</PatientAlcoholProgramIndividual>
          <Radiologist>0</Radiologist>
          <RadiologyEquipment>0</RadiologyEquipment>
          <RadiologyEquipmentOtherPatients>0</RadiologyEquipmentOtherPatients>
          <EmployeeAestheticianOMSNICCover>0</EmployeeAestheticianOMSNICCover>
          <EntityFundVentureCapital>0</EntityFundVentureCapital>
          <EntityGovtContract>0</EntityGovtContract>
          <IitinerantSurgery>0</IitinerantSurgery>
          <IllnessImpairment>0</IllnessImpairment>
          <IllnessImpairmentIndividual>0</IllnessImpairmentIndividual>
          <InvestigationFraud>0</InvestigationFraud>
          <CTImaging>0</CTImaging>
          <CTImagingEquimentOralSurgery>0</CTImagingEquimentOralSurgery>
          <CTImagingOtherPatients>0</CTImagingOtherPatients>
          <CriminalOffense>0</CriminalOffense>
          <CriminalOffenseVehicle>0</CriminalOffenseVehicle>
          <InjectableNeurotoxins>0</InjectableNeurotoxins>
          <Blepharoplasty prior="" id="B85CEABAC552D4EDDA58198F7C4561C07">0</Blepharoplasty>
          <HairTransplants prior="" id="H36BFDC80027F4848A10616E1DA78833B">0</HairTransplants>
          <Otoplasty prior="" id="OE98E9701D70144EDB3FD41B1ED7AE147">0</Otoplasty>
          <Rhinoplasty prior="" id="R2B9A1D3919BE4E69AE9FCB80152EDE1D">0</Rhinoplasty>
          <Rhytidectomy prior="" id="R16E2F8CC791448DCB6710B4D668E5D2E">0</Rhytidectomy>
          <PracticeLocationCoveredByAnotherPLPolicy>0</PracticeLocationCoveredByAnotherPLPolicy>
          <LocationTypeTitle />
          <DentalOffice>1</DentalOffice>
          <GovernmentOffice>0</GovernmentOffice>
          <SurgiCenter>0</SurgiCenter>
          <CorrectionalFacility>0</CorrectionalFacility>
          <DentalServiceOrganizationOrManagementOrganization>0</DentalServiceOrganizationOrManagementOrganization>
          <DentalLaboratory>0</DentalLaboratory>
          <MobileDentalUnit>0</MobileDentalUnit>
          <NursingHome>0</NursingHome>
          <Hospital>0</Hospital>
          <Other1>0</Other1>
          <MarketSegmentTitle />
          <PrivateInsurance>1</PrivateInsurance>
          <Medicare>0</Medicare>
          <Medicaid>0</Medicaid>
          <Other>0</Other>
          <OtherRevenueExplanation />
          <CriminalOffenseExplanation />
          <ParticipantDrugAlcoholProgramExplanation />
          <IllnessImpairmentExplanation />
          <InvestigationFraudExplanation />
          <AlcoholDependency />
          <uwhospitals id="uB728EFAF63EC4F00B761196CFA3EE50E">
            <uwhospital id="u8DC58CD2A00740BA984ACA81A96042C8">
              <NavigateToPartyCheck>0</NavigateToPartyCheck>
              <Privilege />
            </uwhospital>
          </uwhospitals>
          <corporaterelationships id="c5BCB4E9500904C5C9E9B4C83D7724A99">
            <corporaterelationship id="c315CC892ACF54F848BE5AC77282C1415">
              <NameOfPractice>Name_Of_Practice</NameOfPractice>
              <FromDate>2023-10-01</FromDate>
              <ToDate>2024-08-01</ToDate>
            </corporaterelationship>
            <corporaterelationship id="cD0CAE4AFC54541EDAF3C53B5B6F502E9">
              <NameOfPractice>Name_Of_Practice</NameOfPractice>
              <FromDate>2024-11-07</FromDate>
              <ToDate />
            </corporaterelationship>
          </corporaterelationships>
        </underwritingquestions>
        <ClassDetermination id="C1B189A6D59434E1FADCFA284BC4ED25E">
          <Class prior="" id="C9D5FF358D6724DF8A78C6D32CA608230">ClassI</Class>
          <GeneralDentistry>1</GeneralDentistry>
          <Endodontics>0</Endodontics>
          <PediatricDentistry>0</PediatricDentistry>
          <Periodontics>0</Periodontics>
          <Orthodontics>0</Orthodontics>
          <DentalPublicHealth>0</DentalPublicHealth>
          <Prosthodontics>0</Prosthodontics>
          <OMPathology>0</OMPathology>
          <OMRadiology prior="" id="OA385A6089231429498F49483F29171DC">0</OMRadiology>
          <DentalAnesthesiology prior="" id="DDAB8CFD540674EF8B42002EE2611C873">0</DentalAnesthesiology>
          <OMSurgery prior="" id="O413B6FA10C48472A8D7866BC2836D6C9">0</OMSurgery>
          <NumberOfImplants>50</NumberOfImplants>
          <NumberOfExtractions>0</NumberOfExtractions>
          <Administer prior="" id="A8E442BF924B245E3B8A7A871E7523840">1</Administer>
          <MultiDoseSedation>1</MultiDoseSedation>
          <MinimalSedation>0</MinimalSedation>
          <ModerateSedation>0</ModerateSedation>
          <DeepSedation prior="" id="D0959A20BD57047058A282F6133338D00">0</DeepSedation>
          <OtherPatients prior="" id="O56F252BE874840E08B6F1C9A1844A84E">0</OtherPatients>
          <OtherAdminister>0</OtherAdminister>
          <CRNA>0</CRNA>
          <DentalAnesthesiologist>0</DentalAnesthesiologist>
          <MedicalAnesthesiologist>0</MedicalAnesthesiologist>
          <MedicalAnesthesiologistOther>0</MedicalAnesthesiologistOther>
          <Blepharoplasty>0</Blepharoplasty>
          <Rhytidectomy>0</Rhytidectomy>
          <Otoplasty>0</Otoplasty>
          <HairTransplants>0</HairTransplants>
          <Rhinoplasty>0</Rhinoplasty>
          <SpecialtiesAddedList>General Dentistry</SpecialtiesAddedList>
          <CosmeticProcedures />
        </ClassDetermination>
        <risk id="r2AA28516542B444AB8F01E52FBA348F1">
          <Practice>Solo</Practice>
          <ForiegnTravelers>0</ForiegnTravelers>
          <StateMembership>1</StateMembership>
          <StateAbbrForMembershipInfo>NY</StateAbbrForMembershipInfo>
          <InsuredRate>0</InsuredRate>
          <NonInsuredRate>0</NonInsuredRate>
          <PrimaryRisk>1</PrimaryRisk>
          <SetCountCountAffiliateGreaterThan2>0</SetCountCountAffiliateGreaterThan2>
          <CountCountAffiliateGreaterThan2>0</CountCountAffiliateGreaterThan2>
          <CountAffiliateGreaterThan2>0</CountAffiliateGreaterThan2>
          <RiskCoverages id="R56841E85DAD44183B3A936B21F28B999">
            <coverage id="c1C179DE591684E2A9D93A01D1DA78D2A">
              <Type>ProfessionalLiability</Type>
              <Limit prior="" id="L592D4593BE134916A40210D424B1E7EE">1000000/3000000</Limit>
              <Selected>1</Selected>
              <PrimaryCoverage>1</PrimaryCoverage>
              <Caption>Professional Liability</Caption>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>PL</CoverageCode>
              <FacultyDiscount>0</FacultyDiscount>
              <FacultyDiscountPremium>0</FacultyDiscountPremium>
              <ClaimsMadeRating id="C5A5056DEDBAF47DD836AC34516F9F7C2">
                <EffectiveDate>2001-07-03</EffectiveDate>
                <ExpirationDate>2002-07-03</ExpirationDate>
              </ClaimsMadeRating>
              <ClaimsMadeRating id="C0A9D5A88DCB64BD88B75AC8A9EAA95FC">
                <EffectiveDate>2002-07-03</EffectiveDate>
                <ExpirationDate>2003-07-03</ExpirationDate>
              </ClaimsMadeRating>
              <ClaimsMadeRating id="CBD06EB31FB45447CB928EE48074E79EA">
                <EffectiveDate>2003-07-03</EffectiveDate>
                <ExpirationDate>2004-07-03</ExpirationDate>
              </ClaimsMadeRating>
              <ClaimsMadeRating id="C3866D0A211144DA09E819D3D760E70B8">
                <EffectiveDate>2004-07-03</EffectiveDate>
                <ExpirationDate>2005-07-03</ExpirationDate>
              </ClaimsMadeRating>
              <ClaimsMadeRating id="C49C338ABA1CE49ABA2C63F0E5049ABD3">
                <EffectiveDate>2005-07-03</EffectiveDate>
                <ExpirationDate>2026-07-03</ExpirationDate>
              </ClaimsMadeRating>
              <GrossPremiumAdjustment id="G6D249D1AF75A4431B216A6C90B725E73">
                <Premium>0</Premium>
                <Selected>0</Selected>
                <PriorSelected>0</PriorSelected>
                <Indicator>0</Indicator>
                <priorTerm>0</priorTerm>
                <prior>0</prior>
                <cancelPrem>0</cancelPrem>
                <change>0</change>
                <offset>0</offset>
                <onset>0</onset>
                <written>0</written>
              </GrossPremiumAdjustment>
              <SavedSuspensionFlag>0</SavedSuspensionFlag>
              <SavedMilitarySuspensionFlag>0</SavedMilitarySuspensionFlag>
              <LastMilitarySuspensionEffectiveDate />
              <SavedMilitaryPartTimeFlag>0</SavedMilitaryPartTimeFlag>
              <LastSuspensionEffectiveDate>2025-05-01</LastSuspensionEffectiveDate>
              <Indicator>1</Indicator>
              <TailClaimsMadeYearFactor>365</TailClaimsMadeYearFactor>
              <ClaimFreeCreditPremium>0</ClaimFreeCreditPremium>
              <PriorLimit>1000000/3000000</PriorLimit>
              <TerritoryRating id="T83BA60AACE834EEB9CC9CD739EA05EB8">
                <LocationID>l6DD8C579BC6149B7BD9FECA82DA1F50B</LocationID>
                <Indicator>1</Indicator>
                <Premium>2361.222</Premium>
                <written />
                <priorTerm>0</priorTerm>
                <prior>0</prior>
                <cancelPrem>0</cancelPrem>
                <change>0</change>
                <RatingTablesManuScriptID />
              </TerritoryRating>
              <Deductible prior="" id="DF6BD63B20D394AD6A1F1DD2E15E35EF1" />
              <BillingInterface id="BDCC96E01DF50418283DAB8105D399492" />
              <GrossPremium>2361</GrossPremium>
              <ConsultationCreditPremium>0</ConsultationCreditPremium>
              <MilitaryPartTimeCreditPremium>0</MilitaryPartTimeCreditPremium>
              <PartTimeExclusionCreditPremium>0</PartTimeExclusionCreditPremium>
              <PartTimePractice>0</PartTimePractice>
              <RestrictedPartTimeCreditPremium>0</RestrictedPartTimeCreditPremium>
              <VolunteerPartTimeCreditPremium>0</VolunteerPartTimeCreditPremium>
              <MembershipCreditPremium>0</MembershipCreditPremium>
              <AGDMembershipCreditPremium>0</AGDMembershipCreditPremium>
              <AssociationMembershipCreditPremium>0</AssociationMembershipCreditPremium>
              <ClaimsExperienceCreditPremium>0</ClaimsExperienceCreditPremium>
              <ClaimsExperienceSurchargePremium>0</ClaimsExperienceSurchargePremium>
              <DentalBoardExamCreditPremium>0</DentalBoardExamCreditPremium>
              <DentalFacultyCreditPremium>0</DentalFacultyCreditPremium>
              <ExperienceDebitSurchargePremium>0</ExperienceDebitSurchargePremium>
              <LossFreeCreditFortressPremium>0</LossFreeCreditFortressPremium>
              <LossFreeDebit>0</LossFreeDebit>
              <LossFreeDiscountPremium>0</LossFreeDiscountPremium>
              <MultiDentistGroupCreditPremium>0</MultiDentistGroupCreditPremium>
              <NewDentistCreditPremium>0</NewDentistCreditPremium>
              <NewBusinessClaimsFreeCreditPremium>0</NewBusinessClaimsFreeCreditPremium>
              <NewToPracticeCreditPremium>0</NewToPracticeCreditPremium>
              <ResidentRiskManagementCreditPremium>0</ResidentRiskManagementCreditPremium>
              <RiskManagementCreditPremium>-236</RiskManagementCreditPremium>
              <ScheduleCreditPremium>0</ScheduleCreditPremium>
              <ScheduleDebitPremium>0</ScheduleDebitPremium>
              <SurchargePremium>0</SurchargePremium>
              <WaiverOfConsentToSettleDiscountPremium>0</WaiverOfConsentToSettleDiscountPremium>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <Premium>3294</Premium>
              <change>3294</change>
              <offset>0</offset>
              <onset>3294</onset>
              <written>3294</written>
              <DeathCreditPremium>0</DeathCreditPremium>
              <DisabilityCreditPremium>0</DisabilityCreditPremium>
              <RetirementCreditPremium>0</RetirementCreditPremium>
              <TailPremiumDebit>0</TailPremiumDebit>
              <TailPremiumCredit>0</TailPremiumCredit>
              <TotalTailCredits>0</TotalTailCredits>
              <DisplayRMBR>3294</DisplayRMBR>
              <ClaimsMadeYearFactor>1</ClaimsMadeYearFactor>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <PerIncidentDeductibleStartCalc2019>2</PerIncidentDeductibleStartCalc2019>
              <CurrentSuspensionID>sA5080A576A2448F8AB17C5D691AC0D8C</CurrentSuspensionID>
              <suspensions id="s19FB0CD34E7A428C964AE5A09049326A" closed="1">
                <SuspEffectiveDate>2025-05-01</SuspEffectiveDate>
                <SuspExpireDate>2025-11-07</SuspExpireDate>
                <SuspEffectiveTermExpireDate>2026-11-07</SuspEffectiveTermExpireDate>
                <SuspEffectiveTermStartDate>2025-11-07</SuspEffectiveTermStartDate>
              </suspensions>
              <suspensions id="s4297697DFE134F73B6937EE825810E34" closed="1">
                <SuspEffectiveDate>2025-05-01</SuspEffectiveDate>
                <SuspEffectiveTermStartDate>2026-11-07</SuspEffectiveTermStartDate>
                <SuspExpireDate>2025-11-07</SuspExpireDate>
                <SuspEffectiveTermExpireDate>2026-11-07</SuspEffectiveTermExpireDate>
              </suspensions>
              <suspensionsormilitarysuspensionsforforms id="s423BFA4D63F24ED58115B54AB57D701F">
                <EffectiveDate>2025-05-01</EffectiveDate>
                <ExpireDate>2025-11-07</ExpireDate>
                <TermCount>2</TermCount>
                <IsSuspensionOrMilitarySuspension>SuspensionOfInsurance</IsSuspensionOrMilitarySuspension>
              </suspensionsormilitarysuspensionsforforms>
            </coverage>
            <coverage id="c68D9C3E71EEA4883894D39824FD13406">
              <Type>DeptProfessionalRegulation</Type>
              <Caption>DPR</Caption>
              <Indicator>1</Indicator>
              <Limit>25000/25000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-limit</LimitType>
              <CoverageCode>DPR</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <Selected>1</Selected>
              <Deductible />
              <BillingInterface id="BC4427603F20F41A19C7A98236D502EB3" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c88D4A68C5BBA41FE95771E5C27F01771">
              <Type>DentalBoardExam</Type>
              <Caption>Dental Board Exam</Caption>
              <Limit>$1,000,000/$3,000,000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>BOARDEXAM</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="B371C753C919C496AA9AD9C6135BFA853" />
              <Indicator>0</Indicator>
              <Premium>0</Premium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c17FB47C695B2446CB074DBAC27F96B54">
              <Type>DirectorsAndOfficers</Type>
              <Caption>D&amp;O</Caption>
              <Indicator>0</Indicator>
              <Limit>$25,000/$75,000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>DO</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="BC9D720FC041D44B58AA8C5D2ADA7F217" />
              <Premium>0</Premium>
              <ManualPremium>0</ManualPremium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c0D524C6D6EF44A3B9C2DC1A504617B31">
              <Type>DentalProfessor</Type>
              <Caption>Dental Professor </Caption>
              <Limit>1000000/3000000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>PRFDEN</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="B2409F7717FC4483597F0F7A34A9F5194" />
              <Indicator>0</Indicator>
              <Premium>0</Premium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cE6E62D7880334347A1A0CCFBCABF3FFA">
              <Type>DentalResident</Type>
              <Caption>Dental Resident</Caption>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <Indicator>0</Indicator>
              <Limit>1000000/3000000</Limit>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>RSDEN</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="BEF17CC3460B04D4B8835C8D3BEB660FD" />
              <Premium>0</Premium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cDC381D4C564344C4A7F748B756204B75">
              <Type>OMSResident</Type>
              <Caption>OMS Resident</Caption>
              <Limit>1000000/3000000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>RSOMS</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="B6B2F8E48AC324B28AD79AD5C30C742C1" />
              <Indicator>0</Indicator>
              <Premium>0</Premium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="c56C0CD19EC704B9CB5171FE83A891639">
              <Type>RetiredVolunteer</Type>
              <Caption>Retired Volunteer</Caption>
              <Limit>$1,000,000/$3,000,000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>PLR</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="BDD4B9503D1BA4948BD3397C8FAF49938" />
              <Indicator>0</Indicator>
              <Premium>0</Premium>
              <GrossPremium>0</GrossPremium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible />
            </coverage>
            <coverage id="cCEB73CF118794D0AA4CFDEAC6E1D7E61">
              <Type>StandAloneOBCT</Type>
              <Caption>Stand Alone OBCT </Caption>
              <Indicator>0</Indicator>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>SAOBCT</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <Limit>1000000/3000000</Limit>
              <PriorLimit>1000000/3000000</PriorLimit>
              <BillingInterface id="B72B8DCB951BE458EBB9ABD0B2B558FB3" />
              <Premium>0</Premium>
              <written />
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <change>0</change>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="c6F5D2281BF78457EBFCED591FF1A5061">
              <Type>SurgiCenter</Type>
              <Caption>Surgi-Center</Caption>
              <Indicator>0</Indicator>
              <Limit>1000000/3000000</Limit>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>SURG</CoverageCode>
              <RetroDate>2001-07-03</RetroDate>
              <PriorLimit>1000000/3000000</PriorLimit>
              <BillingInterface id="B3FD282E242AF44BB9C5F3B60D83D29BD" />
              <Premium>0</Premium>
              <written />
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <change>0</change>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="c024F8A7B7F27437DA2003B82CD737946">
              <Type>DepAndTrialAttendance</Type>
              <Caption>DTA</Caption>
              <Limit>5000/5000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-limit</LimitType>
              <CoverageCode>DTA</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <Selected>1</Selected>
              <BillingInterface id="BE56C8AACB021414B88B2B30AB1F987B9" />
              <Indicator>1</Indicator>
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c2311FF250CB14193B42E275C6FAF9A3B">
              <Type>FirstAid</Type>
              <Caption>First Aid</Caption>
              <Indicator>0</Indicator>
              <Limit />
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-Limit</LimitType>
              <CoverageCode>FIRSTAID</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <Selected>1</Selected>
              <BillingInterface id="BA232E55DF1A24807A6E52776EDC5F9BB" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c3FAB087C3497411D850A14DDAA6C3161">
              <Type>FacialCosmetic</Type>
              <Caption>Facial Cosmetic</Caption>
              <Indicator>0</Indicator>
              <Selected prior="" id="SC18710B2B26349EBA3A7284FE7D580AC">0</Selected>
              <LimitType>Shared PL</LimitType>
              <RetroDate>2001-07-03</RetroDate>
              <CoverageCode>FAC</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B04DAE15330614922814A2584CB301F1B" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <DeathCredit>0</DeathCredit>
              <DisabilityCredit>0</DisabilityCredit>
              <RetirementCredit>0</RetirementCredit>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <GrossPremium>0</GrossPremium>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible />
            </coverage>
            <coverage id="c313EC205C8B34C538FEF493455D74611">
              <Type>EmploymentPracticesLiability</Type>
              <Caption>EPL</Caption>
              <Indicator>0</Indicator>
              <Limit>25000/25000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-Limit</LimitType>
              <CoverageCode>EPL</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="BEF9F4663298B494F900F518268A50D55" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <Selected>0</Selected>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="c6D2CAB3826E64255BCDEEFF6F649D9A4">
              <Type>FullBodyLipo</Type>
              <Caption>Full-Body Lipo</Caption>
              <Selected>0</Selected>
              <Indicator>0</Indicator>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>LIPO</CoverageCode>
              <TailClaimsMadeYearFactor>1</TailClaimsMadeYearFactor>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B8403592B9DE445A489C7AAA84B6A208C" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <DeathCreditPremium>0</DeathCreditPremium>
              <DisabilityCreditPremium>0</DisabilityCreditPremium>
              <RetirementCreditPremium>0</RetirementCreditPremium>
              <LossFreeDiscountPremium>0</LossFreeDiscountPremium>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <NewBusinessClaimsFreeCreditPremium>0</NewBusinessClaimsFreeCreditPremium>
              <NewToPracticeCreditPremium>0</NewToPracticeCreditPremium>
              <ResidentRiskManagementCreditPremium>0</ResidentRiskManagementCreditPremium>
              <RiskManagementCreditPremium>0</RiskManagementCreditPremium>
              <SuspensionCredit>0</SuspensionCredit>
              <ClaimsExperienceSurchargePremium>0</ClaimsExperienceSurchargePremium>
              <GrossPremiumOMSNIC>0</GrossPremiumOMSNIC>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="c7397DB5D255E491ABC1AFADBF5900CA1">
              <Type>Aesthetician</Type>
              <Caption>Aesthetician</Caption>
              <Indicator>0</Indicator>
              <RetroDate>2001-07-03</RetroDate>
              <Selected>0</Selected>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>AE</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B172764F7AFF74DADB90136D5773809DE" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cA48DBFBD69834766BFCDDA376342E113">
              <Type>DataDefense</Type>
              <Caption>Data Defense</Caption>
              <Limit>$100,000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <Selected>0</Selected>
              <LimitType>CSL</LimitType>
              <CoverageCode>EMD</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <PreviousSelected>0</PreviousSelected>
              <PreviousLimit>$100,000</PreviousLimit>
              <BillingInterface id="B0994FD35C03C4B7E9C92533856637ECC" />
              <Indicator>0</Indicator>
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <BreachResponseAndDefense>0</BreachResponseAndDefense>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c7B26E7F6A60842E687EE2B290F1EC3A1">
              <Type>Tail</Type>
              <Caption>Tail</Caption>
              <RetroDate>2001-07-03</RetroDate>
              <BillingInterface id="B912CAC5E343A4933A78623E15F2B9A01" />
            </coverage>
            <coverage id="c17980F2CCD7A40A38C2D81325C76F30A">
              <Type>FraudAndAbuse</Type>
              <Caption>Fraud and Abuse</Caption>
              <Indicator>0</Indicator>
              <Limit>$25,000/$25,000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-Limit</LimitType>
              <CoverageCode>FRAUD</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B270A48097E7C479E8FB690885ED6CFE4" />
              <Selected>0</Selected>
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cA70546E5436342F1BE87454346464BB5">
              <Type>WrongfulActsDefense</Type>
              <Caption>Wrongful Acts Defense</Caption>
              <Indicator>0</Indicator>
              <Limit>$25,000/$25,000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Sub-limit</LimitType>
              <CoverageCode>WA</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="BF0E744CDBE0045B1A9F8C4E321496482" />
              <Selected>0</Selected>
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cF8486A4FA93D4F64960FA6592C0B4820">
              <Type>OBCT</Type>
              <Caption>OBCT</Caption>
              <Selected prior="0" id="SC49C854CB2ED4862A8EA05DDB4E88AE8">1</Selected>
              <Indicator>0</Indicator>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>Shared PL</LimitType>
              <CoverageCode>OBCT</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <Limit>1000000/3000000</Limit>
              <PriorLimit>1000000/3000000</PriorLimit>
              <BillingInterface id="B63CC9ED5A2C440BDBE532D294993777A" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
              <DeathCredit>0</DeathCredit>
              <DisabilityCredit>0</DisabilityCredit>
              <RetirementCredit>0</RetirementCredit>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
              <Deductible>0</Deductible>
            </coverage>
            <coverage id="cA5A4E26C18DF4C679041570A9D15F582">
              <Type>NYExcess</Type>
              <Caption>NY Excess</Caption>
              <Selected>0</Selected>
              <Indicator>0</Indicator>
              <RetroDate>2001-07-03</RetroDate>
              <LimitType>PL Excess</LimitType>
              <CoverageCode>XNY</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="BBAC81B121935475A8D4B1193D8837BE8" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c764F9E00367148D2AF780043CAB66EBF">
              <Type>OneDay</Type>
              <Indicator>0</Indicator>
              <Selected>0</Selected>
              <Caption>One Day</Caption>
              <LimitType>N/A</LimitType>
              <CoverageCode>ONEDAY</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B78257519DBE84C0ABD94AE1174F06904" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <coverage id="c1D02B970684C47F48525017462AD7946">
              <Type>LocumTenens</Type>
              <Indicator>0</Indicator>
              <Selected>0</Selected>
              <Caption>Locum Tenens</Caption>
              <LimitType>N/A</LimitType>
              <CoverageCode>LOCTEN</CoverageCode>
              <PrimaryCoverage>0</PrimaryCoverage>
              <BillingInterface id="B021AFB190BE04D298E6DB91832D45399" />
              <Premium>0</Premium>
              <change>0</change>
              <offset>0</offset>
              <onset>0</onset>
              <written>0</written>
              <priorTerm>0</priorTerm>
              <prior>0</prior>
              <cancelPrem>0</cancelPrem>
            </coverage>
            <AffliateAddEditMode>Edit</AffliateAddEditMode>
            <coverage id="c3DE378D4008F48C8BD28B3FAFF6D23A3">
              <Type>CorporateProfessionalLiability</Type>
              <Caption>Corporate Professional Liability</Caption>
              <Limit>1000000/3000000</Limit>
              <RetroDate>2001-07-03</RetroDate>
              <Selected>0</Selected>
              <PrimaryCoverage>0</PrimaryCoverage>
              <CoverageCode>CPL</CoverageCode>
              <BillingInterface id="B05817AFD9AA646C6898D8D0490C33BA6" />
            </coverage>
            <IncreasedLimitsFactorForAffilate>1</IncreasedLimitsFactorForAffilate>
            <CountofAffiliateWith0Permium>0</CountofAffiliateWith0Permium>
          </RiskCoverages>
          <HoldGraduationYrGreaterEqualToCurrentYr>0</HoldGraduationYrGreaterEqualToCurrentYr>
          <HoldGraduationYrGreaterLessThanCurrentYr1>0</HoldGraduationYrGreaterLessThanCurrentYr1>
          <HoldGraduationYrGreaterLessThanCurrentYr2>0</HoldGraduationYrGreaterLessThanCurrentYr2>
          <ProfessionalOrganizations />
          <ADA>1</ADA>
          <AGD>0</AGD>
          <Other>1</Other>
          <NameOfAssociation>8th District</NameOfAssociation>
          <CourseData id="C5A81915766034EAFA314E7E14E125015">
            <Course id="CC4250709740446C8BFBBF85083C3C135">
              <Company>Fortress</Company>
              <CourseDate>2014-03-03</CourseDate>
              <CourseName>Fundamental Of Insurance I</CourseName>
              <Location>1 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.01</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>1</CourseID>
            </Course>
            <Course id="C4E06A3E9F6844FEBB0A6B8D4842B65FA">
              <Company>Fortress</Company>
              <CourseDate>2014-03-04</CourseDate>
              <CourseName>Fundamental Of Insurance 2</CourseName>
              <Location>2 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.02</Discount>
              <SeminarType>3rd Party</SeminarType>
              <CourseType>3rd Party</CourseType>
              <CourseID>2</CourseID>
            </Course>
            <Course id="C3962D56427574F2FB2FEEA6B95A86EB5">
              <Company>Fortress</Company>
              <CourseDate>2014-03-05</CourseDate>
              <CourseName>Fundamental Of Insurance 3</CourseName>
              <Location>3 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.03</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>3</CourseID>
            </Course>
            <Course id="C93DF9C2A6C6442FC848386C5A55DA6E2">
              <Company>OMSNIC</Company>
              <CourseDate>2014-03-06</CourseDate>
              <CourseName>Fundamental Of Insurance 4</CourseName>
              <Location>4 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-17T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.04</Discount>
              <SeminarType>On-Line</SeminarType>
              <CourseType>On-Line</CourseType>
              <CourseID>4</CourseID>
            </Course>
            <Course id="CDA0882F5F83744F5B7FDC36688476746">
              <Company>OMSNIC</Company>
              <CourseDate>2014-03-07</CourseDate>
              <CourseName>Fundamental Of Insurance 5</CourseName>
              <Location>5 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-17T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.05</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>5</CourseID>
            </Course>
            <Course id="C87D52AD48F044690A2305BFE5CCE7230">
              <Company>OMSNIC</Company>
              <CourseDate>2014-03-08</CourseDate>
              <CourseName>Fundamental Of Insurance 6</CourseName>
              <Location>6 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-18T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.06</Discount>
              <SeminarType>On-Line</SeminarType>
              <CourseType>On-Line</CourseType>
              <CourseID>6</CourseID>
            </Course>
            <Course id="CD1C87A6DCB0D4537B4C04E663A9A53E8">
              <Company>Fortress</Company>
              <CourseDate>2014-03-09</CourseDate>
              <CourseName>Fundamental Of Insurance 7</CourseName>
              <Location>7 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.07</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>7</CourseID>
            </Course>
            <Course id="C965CFF785A5340E2A6A3B20EFC5BA5AF">
              <Company>Fortress</Company>
              <CourseDate>2014-03-10</CourseDate>
              <CourseName>Fundamental Of Insurance 8</CourseName>
              <Location>8 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.08</Discount>
              <SeminarType>3rd Party</SeminarType>
              <CourseType>3rd Party</CourseType>
              <CourseID>8</CourseID>
            </Course>
            <Course id="CE4BB93692C1F4A9881744B25729527FB">
              <Company>Fortress</Company>
              <CourseDate>2014-03-11</CourseDate>
              <CourseName>Fundamental Of Insurance 9</CourseName>
              <Location>9 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.09</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>9</CourseID>
            </Course>
            <Course id="C4956D09D8BFA447A86D74532157412C2">
              <Company>Fortress</Company>
              <CourseDate>2014-03-12</CourseDate>
              <CourseName>Fundamental Of Insurance 10</CourseName>
              <Location>10 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.1</Discount>
              <SeminarType>On-Line</SeminarType>
              <CourseType>On-Line</CourseType>
              <CourseID>10</CourseID>
            </Course>
            <Course id="CF349063F85A54272AFEA96735A9E9060">
              <Company>Fortress</Company>
              <CourseDate>2014-03-13</CourseDate>
              <CourseName>Fundamental Of Insurance 11</CourseName>
              <Location>11 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.11</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>11</CourseID>
            </Course>
            <Course id="CBBED90A5B0F842959C698BC89EACCDF2">
              <Company>OMSNIC</Company>
              <CourseDate>2014-03-14</CourseDate>
              <CourseName>Fundamental Of Insurance 12</CourseName>
              <Location>12 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.12</Discount>
              <SeminarType>On-Line</SeminarType>
              <CourseType>On-Line</CourseType>
              <CourseID>12</CourseID>
            </Course>
            <Course id="C65FF187868E34D8D8A02475EAB006559">
              <Company>OMSNIC</Company>
              <CourseDate>2014-03-15</CourseDate>
              <CourseName>Fundamental Of Insurance 13</CourseName>
              <Location>13 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>1</ResidentRMCourse>
              <Discount>0.13</Discount>
              <SeminarType>Internal</SeminarType>
              <CourseType>Internal</CourseType>
              <CourseID>13</CourseID>
            </Course>
            <Course id="C9FDB9E5BFDBC4763B2267BDD085DC5DA">
              <Company>OMSNIC</Company>
              <CourseDate>2014-11-03</CourseDate>
              <CourseName>Fundamental Of Insurance 14</CourseName>
              <Location>14 Location</Location>
              <LengthOfCredit>0</LengthOfCredit>
              <CourseExpirationDate>2016-03-07T12:00:00.000</CourseExpirationDate>
              <ResidentRMCourse>0</ResidentRMCourse>
              <Discount>0.14</Discount>
              <SeminarType>On-Line</SeminarType>
              <CourseType>On-Line</CourseType>
              <CourseID>14</CourseID>
            </Course>
          </CourseData>
          <Responses id="R6B1C9FD3E78F444DBD42FDEB15B70C4C" />
          <PurePremium id="PAB7F5BE9651E453888C4521ABA3A3085">3294<change>3294</change></PurePremium>
          <TaxesSurcharges written="0" id="T3248E49EBED2499FB72C5BB411108335" change="0">0</TaxesSurcharges>
          <written>3294</written>
          <Premium>3294</Premium>
          <change>3294</change>
          <Educations id="ED9C8C05B38DF4F0E8A6E4733DFC2CF36">
            <education id="e0ACAA097EEF04D07BC55F6A314DB76C5">
              <Institution>UniversityatBuffalo</Institution>
              <TrainingType>DentalSchool</TrainingType>
              <YearOfGraduation>2000</YearOfGraduation>
              <Degree />
              <NavigateToPartyCheck>0</NavigateToPartyCheck>
              <IsFortressOrOMSNIC>Fortress</IsFortressOrOMSNIC>
            </education>
          </Educations>
          <PartyObj.getPartyRs status="success" id="P9D8D716A809041B3B6F4997580B044D0">
            <PartyRecord xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" id="P790A52476E4E440EA98BDBDA7280D18C">
              <PartyRecordPartyId>08DCF97D6C9D7004</PartyRecordPartyId>
              <Party id="P779A0BE2F5384628B07870170FFFDF2B">
                <PartyId>08DCF97D6C9D7004</PartyId>
                <PartyOFACCheckNotRequired>U</PartyOFACCheckNotRequired>
                <PartyExternalId />
                <ACHPartyExternalId />
                <ExternalPartyNameId />
                <PartyNumber>54485</PartyNumber>
                <PartyName>08DCF97D6C9D7004_LN</PartyName>
                <PartyFirstName>Jack</PartyFirstName>
                <PartyMiddleName />
                <PartyNamePrefixCode />
                <PartyNameSuffixCode>08</PartyNameSuffixCode>
                <PartyNameEffectiveDate>2024-10-31</PartyNameEffectiveDate>
                <PartyNameExpirationDate>9999-12-31</PartyNameExpirationDate>
                <PartyNameComments />
                <PartyTypeCode>P</PartyTypeCode>
                <PartySubTypeCode xsi:nil="true" id="P0C730DE070EA43B2A3EA34F01BF88FB1" />
                <PartyGenderCode>M</PartyGenderCode>
                <PartyIsCompanyPersonnel>0</PartyIsCompanyPersonnel>
                <PartyBirthDate>1972-10-24</PartyBirthDate>
                <PartyDeathDate />
                <PartyComments />
                <PartyNumberOfDependents>0</PartyNumberOfDependents>
                <PartyProfession />
                <PreferredContactMethod />
                <PartyMaritalStatusCode />
                <PartyTaxId />
                <PartyNationality xsi:nil="true" id="PE1EE7CF5C55A47B2BE91884D61D69519" />
                <PartyPartyOwner xsi:nil="true" id="PE0B744A6553142A4B049B60280D3E453" />
                <PartyPreferredLanguageCode>EN</PartyPreferredLanguageCode>
                <PartyCorrespondenceName>08DCF97D6C9D7004_LN</PartyCorrespondenceName>
                <PartyLegacyName />
                <PartyCountryCode>USA</PartyCountryCode>
                <PartyNameCountryCode>USA</PartyNameCountryCode>
                <PartyLockingTS>f229973da68d4cf8b032124600ad3e96:364</PartyLockingTS>
                <PartyExtendedData id="PF445A103F1824202A74E833224CDF532">
                  <USCitizen>1</USCitizen>
                  <OMSNICPartyID>10549392</OMSNICPartyID>
                  <PreferredContactMethod>Email</PreferredContactMethod>
                  <Title />
                  <Paperless>0</Paperless>
                  <Conversion>0</Conversion>
                  <Other>0</Other>
                  <StateAssnof>0</StateAssnof>
                  <Educations id="ED9C8C05B38DF4F0E8A6E4733DFC2CF36">
                    <education id="e0ACAA097EEF04D07BC55F6A314DB76C5">
                      <Institution>UniversityatBuffalo</Institution>
                      <TrainingType>DentalSchool</TrainingType>
                      <YearOfGraduation>2000</YearOfGraduation>
                      <Degree />
                    </education>
                  </Educations>
                  <uwhospitals id="uB728EFAF63EC4F00B761196CFA3EE50E" />
                  <corporaterelationships id="c5BCB4E9500904C5C9E9B4C83D7724A99" />
                  <specialties id="s1784D272404549F98B3AC5467CDBD5C8" />
                  <LossHistories id="LB6BB9B67425042DB892849ABB09A8938" />
                  <RiskManagements id="RD926C5D2BEDF49E6963DD36E3B2C3B12" />
                  <DBAs id="DDDB19D6D5DEF46F0AE1BFC10CE853638" />
                  <PreferredContact id="PCEC2F4293C4E4A5BB42DF1ED5BC3F9C0">
                    <Method>Email</Method>
                    <Value />
                    <MethodType />
                  </PreferredContact>
                  <GreenCardId />
                  <PassportId />
                  <VisaId />
                  <WorkPermitId />
                  <FullName>Jack</FullName>
                  <Ethnicity />
                  <Occupation />
                  <BusinessType />
                  <OriginationDate />
                  <PreferredContactPerson />
                  <MaidenName />
                  <PartyPreferredContact />
                  <AdvisenID />
                  <DUNS />
                  <VIPFlag />
                  <Suffix />
                  <PartyEducationCount>1</PartyEducationCount>
                  <PartyRecordID />
                  <VIPCheckbox />
                  <HospitalPrivilegeCount />
                  <CorporateRelationshipCount />
                  <SpecialtyCount />
                  <LossHistoryCount />
                  <RiskManagementCount />
                  <DBACount />
                  <CustomerID>1086177</CustomerID>
                  <PriorCarrier />
                  <OtherDetail />
                  <ADA />
                  <AGD />
                  <StateAssnStateList />
                  <AGDCaption />
                  <ADACaption />
                  <OtherCaption />
                  <StateCaption />
                  <NewSuf />
                </PartyExtendedData>
              </Party>
              <PartyEmails id="PB27232BBEE6E4B8B9E12B11B00521964">
                <PartyEmail id="P2250E2FBA26F4BF5A5FE1895E4971A7C">
                  <PartyEmailId>08DCF97D6CBC992D</PartyEmailId>
                  <EmailTypeCode>B</EmailTypeCode>
                  <EmailAddress>08DCF97D6C9D7004@123.abc</EmailAddress>
                  <ExternalPartyEmailID />
                  <PartyEmailLockingTS>f229973da68d4cf8b032124600ad3e96:360</PartyEmailLockingTS>
                  <EmailExtendedData />
                  <PrimaryIndicator>1</PrimaryIndicator>
                  <EmailComments />
                  <EmailEffectiveDate>2024-10-31</EmailEffectiveDate>
                  <EmailExpirationDate>9999-12-31</EmailExpirationDate>
                  <IsExpired>0</IsExpired>
                </PartyEmail>
              </PartyEmails>
              <PartyOtherNames />
              <ResponseInfo id="RA88A40121E434353960A660267FD4CE4">
                <ReturnCode>0</ReturnCode>
                <NotificationResponse />
              </ResponseInfo>
              <PartyAccess id="P0545C47F68C24A4182B6D94EC6726A89">
                <UpdateAccess>1</UpdateAccess>
              </PartyAccess>
              <PartyInvolvements id="PB9E6CD71220B46368AC87D3D2148B8C6">
                <PartyInvolvement id="PADE758D69DA34D0893D0DB12D44B370B">
                  <PartyInvolvementId>539147</PartyInvolvementId>
                  <ObjectTypeCode>POL</ObjectTypeCode>
                  <ObjectId>42611</ObjectId>
                  <PolicyId>42611</PolicyId>
                  <RoleCode>I</RoleCode>
                  <EffectiveDate>2024-11-07</EffectiveDate>
                  <ObjectReference>Q3222566</ObjectReference>
                  <ExpirationDate />
                  <PartyInvolvementLockingTS>AAAAAAJT/gk=</PartyInvolvementLockingTS>
                  <ObjectStatus>FullQuote</ObjectStatus>
                </PartyInvolvement>
              </PartyInvolvements>
              <AuthorizedInterest id="AD753091CB81E41ACA87A83EEF29FE6D1">
                <Name>Internal Users</Name>
                <EntityID>1</EntityID>
              </AuthorizedInterest>
            </PartyRecord>
          </PartyObj.getPartyRs>
          <PartyLicenses id="PE3236ACD44FB4BBAB20626A03CF0CD6B">
            <PartyLicense id="P5D26BDF05E534A4D810FC83939C48FEA">
              <PartyLicenseId>08DCF97DAEDF177F</PartyLicenseId>
              <LicenseTypeCode>D</LicenseTypeCode>
              <LicenseStateCode>NY</LicenseStateCode>
              <LicenseNumber>049306</LicenseNumber>
              <LicenseStatusCode>O</LicenseStatusCode>
              <LicenseEffectiveDate />
              <LicenseExpirationDate />
              <LicenseCountryIssued />
              <PartyLicenseLockingTS>f229973da68d4cf8b032124600ad3e96:367</PartyLicenseLockingTS>
              <LicenseExtendedData id="LBD243EBFB0A34DD99EF8F234E01E1884">
                <LicenseExtendedData id="L5DEC13ED8B6F42509F97FF95950FBF97">
                  <LicenseCity />
                  <LicenseCounty />
                </LicenseExtendedData>
              </LicenseExtendedData>
              <NavigateToPartyCheck>0</NavigateToPartyCheck>
            </PartyLicense>
          </PartyLicenses>
          <riskManagement id="r5A7BC48E25E842B69B74F21411D2A3D0">
            <CourseDate>2025-03-27</CourseDate>
            <IsAdded>0</IsAdded>
            <CreditAddedInTermCount>1</CreditAddedInTermCount>
            <CourseName>14</CourseName>
            <CourseLocation>Online</CourseLocation>
            <Credit>0.1</Credit>
            <CourseLocationManualEntry />
            <PolicyEffectiveDate>2025-11-07</PolicyEffectiveDate>
            <RiskCourseEffectiveDate>2025-11-07</RiskCourseEffectiveDate>
            <LengthOfCredit>3</LengthOfCredit>
            <ExpirationDate>2028-11-07</ExpirationDate>
            <OverwriteLengthOfCredit>0</OverwriteLengthOfCredit>
            <IsResidentRiskManagement>0</IsResidentRiskManagement>
            <DisplayCourseLocation>Online</DisplayCourseLocation>
          </riskManagement>
          <AAOMSMembershipIDNumberChanged>0</AAOMSMembershipIDNumberChanged>
          <RatingTablesManuScriptID />
        </risk>
        <Losses id="L1FDD9B83CF9944B9BE0955F582A925F1">
          <PreviousLossCount>0</PreviousLossCount>
          <LossFreeDate>2001-07-03</LossFreeDate>
          <LossRatio>0</LossRatio>
          <PolicyID />
        </Losses>
        <LossFreeDate>2001-07-03</LossFreeDate>
        <OldLossFreeDate>2001-07-03</OldLossFreeDate>
        <LossFreeRate>0</LossFreeRate>
        <OldLossFreeRate />
        <LineCoveragesPreviousCountOfHistoricalSuspensionEndorsement_2009Form>0</LineCoveragesPreviousCountOfHistoricalSuspensionEndorsement_2009Form>
        <SPPCoverage>0</SPPCoverage>
        <AllLossIncidents>0</AllLossIncidents>
        <CoveragePackage>Recommended</CoveragePackage>
        <ExtendedReportingEndorsment>0</ExtendedReportingEndorsment>
        <CappingFactorCalculated>1</CappingFactorCalculated>
        <RiskTotalPurePremiums change="3294" id="R5F4B14B53A294AAFBD84C8F82FBF65C1" written="3294">3294</RiskTotalPurePremiums>
        <RiskTotalTaxesSurcharges written="0" id="R6620254D65C540A8A73CFD4625FF9531" change="0">0</RiskTotalTaxesSurcharges>
        <RiskTotalPremiums written="3294" id="R96EF9AEC6F454FDAA80945A0CBB0C86E" change="3294">3294</RiskTotalPremiums>
        <PurePremiumWritten>3294</PurePremiumWritten>
        <PurePremium>3294</PurePremium>
        <PurePremiumChange>3294</PurePremiumChange>
        <TaxesSurchargesWritten>0</TaxesSurchargesWritten>
        <TaxesSurcharges>0</TaxesSurcharges>
        <TaxesSurchargesChange>0</TaxesSurchargesChange>
        <written>3294</written>
        <Premium>3294</Premium>
        <change>3294</change>
        <CancelledPremium>2125</CancelledPremium>
        <CancelledPremiumForTail>3294</CancelledPremiumForTail>
        <CommissionPlans id="CA585AFE850B5457387330AA6A5CAE4AC">
          <Keys id="K34B2AF53880E488C8FB2DD065F09253B">
            <LineOfBusinessCode>MPL</LineOfBusinessCode>
            <MasterCompanyCode>02</MasterCompanyCode>
            <PlanActivationDate>2025-11-07</PlanActivationDate>
            <PlanExpirationDate>2026-11-07</PlanExpirationDate>
            <PolicyInceptionDate>2025-11-07</PolicyInceptionDate>
            <ProductCode>PLPrimary</ProductCode>
            <StateCode>NY</StateCode>
            <UserKey1 />
            <UserKey2 />
            <UserKey3 />
            <UserKey4 />
            <UserKey5 />
          </Keys>
        </CommissionPlans>
        <TailFactor>1.55</TailFactor>
        <RetirementCredit>0</RetirementCredit>
        <DisabilityCredit>0</DisabilityCredit>
        <DeathCredit>0</DeathCredit>
        <TailDiscount>0</TailDiscount>
        <TailPremiumDebit>0</TailPremiumDebit>
        <TailPremiumCredit>0</TailPremiumCredit>
        <LimitedVicariousLiabilityPremium>0</LimitedVicariousLiabilityPremium>
        <MilitarySuspensionCreditForLVL>0</MilitarySuspensionCreditForLVL>
        <SuspensionOfInsuranceCreditForLVL>0</SuspensionOfInsuranceCreditForLVL>
        <TotalManuscriptCoveragePremiumITR>0</TotalManuscriptCoveragePremiumITR>
        <TotalManuscriptCoveragePremium>0</TotalManuscriptCoveragePremium>
        <EndorsementTotal>0</EndorsementTotal>
        <CommissionPlanId>247</CommissionPlanId>
        <TransactionCommissionType />
        <TransactionCommissionValue>0</TransactionCommissionValue>
        <BillingLOB>LIN1</BillingLOB>
        <LineCommissionOutputCommissionSharePlanId />
        <TailFactorForForms>1.55</TailFactorForForms>
        <TailPremiumForForms>5106</TailPremiumForForms>
        <FormsControlManuscriptID>Fortress_MedicalProfessionalLiability_FormsControl</FormsControlManuscriptID>
        <MultiStateFormsManuScriptID>Fortress_MedicalProfessionalLiability_Forms_Multistate</MultiStateFormsManuScriptID>
        <AKFormsManuScriptID />
        <ALFormsManuScriptID />
        <ARFormsManuScriptID />
        <AZFormsManuScriptID />
        <CAFormsManuScriptID />
        <COFormsManuScriptID />
        <CTFormsManuScriptID />
        <DCFormsManuScriptID />
        <DEFormsManuScriptID />
        <FLFormsManuScriptID />
        <GAFormsManuScriptID />
        <HIFormsManuScriptID />
        <IAFormsManuScriptID />
        <IDFormsManuScriptID />
        <ILFormsManuScriptID />
        <INFormsManuScriptID />
        <KSFormsManuScriptID />
        <KYFormsManuScriptID />
        <LAFormsManuScriptID />
        <MAFormsManuScriptID />
        <MDFormsManuScriptID />
        <MEFormsManuScriptID />
        <MIFormsManuScriptID />
        <MNFormsManuScriptID />
        <MOFormsManuScriptID />
        <MSFormsManuScriptID />
        <MTFormsManuScriptID />
        <NCFormsManuScriptID />
        <NDFormsManuScriptID />
        <NEFormsManuScriptID />
        <NHFormsManuScriptID />
        <NJFormsManuScriptID />
        <NMFormsManuScriptID />
        <NVFormsManuScriptID />
        <NYFormsManuScriptID>Fortress_MedicalProfessionalLiability_Forms_NY</NYFormsManuScriptID>
        <OHFormsManuScriptID />
        <OKFormsManuScriptID />
        <ORFormsManuScriptID />
        <PAFormsManuScriptID />
        <RIFormsManuScriptID />
        <SCFormsManuScriptID />
        <SDFormsManuScriptID />
        <TNFormsManuScriptID />
        <TXFormsManuScriptID />
        <UTFormsManuScriptID />
        <VAFormsManuScriptID />
        <VTFormsManuScriptID />
        <WAFormsManuScriptID />
        <WIFormsManuScriptID />
        <WVFormsManuScriptID />
        <WYFormsManuScriptID />
        <LineCoveragesIsFormShow_1046Form>0</LineCoveragesIsFormShow_1046Form>
        <DisplayLocationforRiskCourseFD1050>Online</DisplayLocationforRiskCourseFD1050>
      </line>
      <coverage />
      <QuoteOrgin>AgencyQuote</QuoteOrgin>
      <SetConvertedPolicyIndicator>0</SetConvertedPolicyIndicator>
      <SetRekeyPolicyIndicator>0</SetRekeyPolicyIndicator>
      <QuoteNumber>Q3222566</QuoteNumber>
      <ExpirationDate>2026-11-07</ExpirationDate>
      <CurrentTermExpirationDate>2026-11-07</CurrentTermExpirationDate>
      <OMSNICPartyID>10549392</OMSNICPartyID>
      <AgencyIDPriorTransaction>10041</AgencyIDPriorTransaction>
      <discounts id="dA0A2A4AD9A334DB5987B5F8228CE96AC">
        <WaiverofConsenttoSettlePercent>0.0025</WaiverofConsenttoSettlePercent>
        <AssociationMembershipCredit>0</AssociationMembershipCredit>
        <MembershipCredit>0</MembershipCredit>
        <Indicator id="I1D050DE96257438E8E0B5C8316631A3A">
          <Type>NewDentist</Type>
          <bValue>0</bValue>
          <NewDentistOverride>0</NewDentistOverride>
        </Indicator>
        <NewDentistStartDate>2024-11-07</NewDentistStartDate>
        <FacultyDiscount>0</FacultyDiscount>
        <SuspensionOfInsurance prior="0" id="SDD6C5533A7424C968C7424344C911C50">0</SuspensionOfInsurance>
        <MilitarySuspension prior="0" id="ME24D9F380CED49E3AFE71F3727B99007">0</MilitarySuspension>
        <MilitaryPartTime prior="" id="M4C91CCEF55234918A62BFAD1A1F94526">0</MilitaryPartTime>
        <SuspensionEndDateForPages>2025-11-07</SuspensionEndDateForPages>
        <MilitarySuspensionEndDateForPages />
        <RiskManagementCredit prior="0" id="RB4E88FC6CF654E43866B56930C26C71A">1</RiskManagementCredit>
        <RiskManagementCreditPercent>0.1</RiskManagementCreditPercent>
        <NewDentistPercent>0</NewDentistPercent>
        <Indicator id="IB25833809BFB43B3BD5131C850ADF6B2">
          <Type>NewToPractice</Type>
          <bValue>0</bValue>
        </Indicator>
        <MembershipCreditPercent>0</MembershipCreditPercent>
        <MultiDentistGroupPercent>0</MultiDentistGroupPercent>
        <NewBusinessClaimFreePercent>0</NewBusinessClaimFreePercent>
        <ConsultationCreditPercent>0.75</ConsultationCreditPercent>
        <DentalBoardExamDiscount>25</DentalBoardExamDiscount>
        <DentalFacultyCreditPercent>0.5</DentalFacultyCreditPercent>
        <LossFreeCredit>0</LossFreeCredit>
        <LossFreeCreditPercent>0</LossFreeCreditPercent>
        <ClaimsFreeCreditMSPercent>10</ClaimsFreeCreditMSPercent>
        <NewToPracticeStartDate>2024-11-07</NewToPracticeStartDate>
        <EditableSuspensionStartDate>2024-10-31</EditableSuspensionStartDate>
        <LossFreeDebitPercent>0</LossFreeDebitPercent>
        <FacultyDiscountPercent>0.1</FacultyDiscountPercent>
        <BreachResponseAndDefensePercent>0.15</BreachResponseAndDefensePercent>
        <SuspensionOrMilitarySuspensionStartDateForForms>2025-05-01</SuspensionOrMilitarySuspensionStartDateForForms>
        <SuspensionOrMilitarySuspensionEndDateForForms>2025-11-07</SuspensionOrMilitarySuspensionEndDateForForms>
        <ExecuteSuspensionOrMilitarySuspensionForForms />
        <NewDentist id="NCA8E30C4FBBC4FD681F110EA91CFC97E">
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
        </NewDentist>
        <NewDentist id="NCBC1585C3CFB46DBB42B1813D668CC37">
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ExpirationDate>2026-11-07</ExpirationDate>
        </NewDentist>
        <NewDentist id="N3236C64E017C47BF9128EA33D890F43E">
          <EffectiveDate>2026-11-07</EffectiveDate>
          <ExpirationDate>2027-11-07</ExpirationDate>
        </NewDentist>
        <discountssuspension id="d8CD9227480084063BFCFA22AAE2E86FD" />
        <ClaimsFreeCreditNYPercent>0</ClaimsFreeCreditNYPercent>
        <ClaimsFreeCreditMS>0</ClaimsFreeCreditMS>
        <ConsultationCredit>0</ConsultationCredit>
        <PartTimePractice prior="" id="PEFBB7DFFC2ED4362A76A6BD26A9B9054">0</PartTimePractice>
        <PartTimeExclusion prior="" id="P869947478FB7416D945CAC5846DB9F25">0</PartTimeExclusion>
        <RestrictedPartTime>0</RestrictedPartTime>
        <VolunteerPartTime>0</VolunteerPartTime>
        <ClaimsFreeCreditNY>0</ClaimsFreeCreditNY>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
        <change>0</change>
        <SetRetirementPercent>0</SetRetirementPercent>
        <SetRetirement>0</SetRetirement>
        <PreviousNewDentistStartdt>2024-11-07</PreviousNewDentistStartdt>
        <PreviousPartTimePracticeCredit>0</PreviousPartTimePracticeCredit>
        <AGDMembershipCredit>0</AGDMembershipCredit>
        <ResidentRiskManagementCredit prior="0" id="RA5507035A1A14151B52C9A40ABF649B1">0</ResidentRiskManagementCredit>
        <NewDentistEndDate>2027-11-07</NewDentistEndDate>
        <ClaimsExperienceCredit>0</ClaimsExperienceCredit>
        <DentalBoardExam>0</DentalBoardExam>
        <DentalFacultyCredit>0</DentalFacultyCredit>
        <LossFreeDebit>0</LossFreeDebit>
        <MultiDentistGroup>0</MultiDentistGroup>
        <NewBusinessClaimFree>0</NewBusinessClaimFree>
        <ScheduleCredit>0</ScheduleCredit>
        <WaiverofConsenttoSettle>0</WaiverofConsenttoSettle>
        <Death>0</Death>
        <Disability>0</Disability>
        <Retirement>0</Retirement>
        <RetirementPercent>0</RetirementPercent>
        <PremiumCredit>0</PremiumCredit>
        <PremiumCreditAmount>0</PremiumCreditAmount>
        <DisabilityPercent>0</DisabilityPercent>
        <DeathPercent>0</DeathPercent>
        <MilitaryPartTimePercent>0</MilitaryPartTimePercent>
        <PartTimePracticePercent>0</PartTimePracticePercent>
        <ScheduleCreditDE>0</ScheduleCreditDE>
        <ScheduleCreditMS>0</ScheduleCreditMS>
        <ScheduleCreditPercentMS>0</ScheduleCreditPercentMS>
        <BreachResponseAndDefense>0</BreachResponseAndDefense>
        <RestrictedPartTimePercent>0</RestrictedPartTimePercent>
        <NewToPracticePercent>0</NewToPracticePercent>
        <PartTimeExclusionPercent>0</PartTimeExclusionPercent>
        <VolunteerPartTimePercent>0</VolunteerPartTimePercent>
        <SuspensionOfInsurancePercent>0.85</SuspensionOfInsurancePercent>
        <MilitarySuspensionPercent>1</MilitarySuspensionPercent>
        <ScheduleCreditPercentOther>0</ScheduleCreditPercentOther>
        <AGDMembershipPercent>10</AGDMembershipPercent>
        <written />
        <SuspensionStartDateForPages>2025-05-01</SuspensionStartDateForPages>
      </discounts>
      <IssueState prior="NY" id="I920B413022E143549E70C61ABBC97F56">NY</IssueState>
      <CoverageType>ClaimsMade</CoverageType>
      <RetroDate>2001-07-03</RetroDate>
      <ApplyTailFactorIndicator>1</ApplyTailFactorIndicator>
      <PolicyType />
      <OMSNICPolicyType>Individual</OMSNICPolicyType>
      <surchargesandtaxes id="s7B996D57EA8C46EA8C5007273C3874B7">
        <ClaimsExperienceSurcharge>0</ClaimsExperienceSurcharge>
        <ClaimsExperienceSurchargePercentage>0</ClaimsExperienceSurchargePercentage>
        <ExperienceDebitSurchargePercentage>0</ExperienceDebitSurchargePercentage>
        <SurchargePercentageFortress>0</SurchargePercentageFortress>
        <SurchargePercentageOMSNIC>0</SurchargePercentageOMSNIC>
        <ClaimsExperienceSurchargeDebitPercent>0</ClaimsExperienceSurchargeDebitPercent>
        <citytax id="c94D3953D1E5147AE8BDB0A6B46E88EA5">
          <Premium>0</Premium>
          <change>0</change>
          <offset>0</offset>
          <onset>0</onset>
          <written>0</written>
          <priorTerm>0</priorTerm>
          <prior>0</prior>
          <cancelPrem>0</cancelPrem>
        </citytax>
        <countytax id="c6EA7AFDED42C439CA3556B8FA339601C">
          <Premium>0</Premium>
          <change>0</change>
          <offset>0</offset>
          <onset>0</onset>
          <written>0</written>
          <priorTerm>0</priorTerm>
          <prior>0</prior>
          <cancelPrem>0</cancelPrem>
        </countytax>
        <statetax id="sBCE5411C7B0748A0B3977EBD4A6296D1">
          <Premium>0</Premium>
          <change>0</change>
          <offset>0</offset>
          <onset>0</onset>
          <written>0</written>
          <priorTerm>0</priorTerm>
          <prior>0</prior>
          <cancelPrem>0</cancelPrem>
        </statetax>
        <ExperienceDebitSurcharge>0</ExperienceDebitSurcharge>
        <ScheduleDebit>0</ScheduleDebit>
        <ScheduleDebitPercentage>0</ScheduleDebitPercentage>
        <SurchargeFortress prior="" id="SAAEB4054A7F54332867F54978D70BADE">0</SurchargeFortress>
        <TotalTaxPremium written="0" id="T847B496994304F5F9A0CD6E56A5D2C2F" change="0">0</TotalTaxPremium>
        <PremiumDebit>0</PremiumDebit>
        <PremiumDebitAmount>0</PremiumDebitAmount>
        <SurchargeOMSNIC>0</SurchargeOMSNIC>
        <CityTax>0</CityTax>
        <CountyTax>0</CountyTax>
        <ExperienceDebitSurchargeCaption>Experience Debit Surcharge</ExperienceDebitSurchargeCaption>
        <written />
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
        <change>0</change>
      </surchargesandtaxes>
      <Indicators id="I9CDCD807F0C447A4A5D3EA0CC2D9CB71">
        <Type>IsAuditable</Type>
        <bValue>0</bValue>
      </Indicators>
      <MoveToAgencyIndicator>0</MoveToAgencyIndicator>
      <LossFreeCreditPrior>0</LossFreeCreditPrior>
      <SeOOSESuspEffective />
      <TermCount>3</TermCount>
      <CancelledBy>Insurer</CancelledBy>
      <RenewalEffectiveDate />
      <CashWithTransaction>1</CashWithTransaction>
      <PaymentMethodCode>CK</PaymentMethodCode>
      <CardNumber />
      <PaymentReference>1133</PaymentReference>
      <Amount>1</Amount>
      <AssignedUnderwriter prior="10304" id="A024A07ED392A4CC5BE12AF6416134799">10304</AssignedUnderwriter>
      <CurrentTermEffectiveDate>2025-11-07</CurrentTermEffectiveDate>
      <PriorCoverageType>ClaimsMade</PriorCoverageType>
      <CancellationDate />
      <SpecialProgramCheckbox>0</SpecialProgramCheckbox>
      <PriorCarrier>MLMIC</PriorCarrier>
      <StartDate>2024-11-07</StartDate>
      <ContinuousProfessionalLiabilityInsurance>1</ContinuousProfessionalLiabilityInsurance>
      <District>District8</District>
      <coverage id="c4725DBA38F284E1EA874D4DEED3408CD">
        <Type>Tail</Type>
        <EstimatedCancelDate>2025-11-07</EstimatedCancelDate>
        <Indicator>0</Indicator>
        <Selected>0</Selected>
        <Caption>Tail</Caption>
        <LimitType>N/A</LimitType>
        <CoverageCode>TAIL</CoverageCode>
        <IncludedCoverage>0</IncludedCoverage>
        <gettailstatus id="g1E901E46C1B04832A2F2BD1234428582">
          <rows id="r63985DAB60254B9589A31379F75074C0">
            <row rowID="1" id="r11DFC219AEC84F25BBE4928D76354625">
              <PremiumBeforeCancel>2125</PremiumBeforeCancel>
              <MilitarySuspensionCredit>0</MilitarySuspensionCredit>
              <SuspensionCredit>0</SuspensionCredit>
            </row>
          </rows>
        </gettailstatus>
        <junk>62</junk>
        <QuotePremium>3294</QuotePremium>
        <QuoteTaxes>0</QuoteTaxes>
        <TailDeath>0</TailDeath>
        <TailDisability>0</TailDisability>
        <TailRetirement>0</TailRetirement>
        <TailPremiumCredit>0</TailPremiumCredit>
        <TailPremiumDebit>0</TailPremiumDebit>
        <PremiumStandard>3294</PremiumStandard>
        <Premium>3294</Premium>
        <QuotePremiumForTail>3294</QuotePremiumForTail>
        <PremiumStandardForForms>3294</PremiumStandardForForms>
      </coverage>
      <IssueStateChange />
      <AccountStatus>  (Cancelled:  Tail-Pending)</AccountStatus>
      <BillClass>OMS2</BillClass>
      <AccountSetupSelection>DB1</AccountSetupSelection>
      <IsAgentVisitedFortPricingPage>1</IsAgentVisitedFortPricingPage>
      <InceptionDate>2024-11-07</InceptionDate>
      <TailExpiration>2079-06-06</TailExpiration>
      <PolicyOutputTailQuoteEffecitiveDate>2025-11-07</PolicyOutputTailQuoteEffecitiveDate>
      <DelimitedTailSelected>0</DelimitedTailSelected>
      <TailQuoteCancellationDate>2025-11-07</TailQuoteCancellationDate>
      <PolicyOutputTailCommittedEffectiveDate>2012-01-01</PolicyOutputTailCommittedEffectiveDate>
      <TailQuoteNonRenewDate />
      <WasRekeyPolicyIndicator>0</WasRekeyPolicyIndicator>
      <SetWasRekeyPolicyIndicator>0</SetWasRekeyPolicyIndicator>
      <TailStartDateForRetro>2025-11-07</TailStartDateForRetro>
      <TotalAGDMembershipCredit id="TBE9F88898468462EB06FCD5AAEBF3182">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalAGDMembershipCredit>
      <TotalAssociationMembershipCredit id="T8F921542E72F4BFEA1DD48569712F5B2">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalAssociationMembershipCredit>
      <TotalClaimFreeCredit id="T15A9C4AD8C2B464CB1406C7D63897E00">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalClaimFreeCredit>
      <TotalClaimsExperienceCredit id="TDED40A5D65484BAEB2B11184D12D658F">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalClaimsExperienceCredit>
      <TotalConsultationCredit id="T0E3B80C1D698458D912A5BBD3F7B9D65">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalConsultationCredit>
      <TotalDentalBoardExamCredit id="TDFC2DF0D25C74DAE883CE2D0C215CD1C">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalDentalBoardExamCredit>
      <TotalLossFreeCreditFortress id="TE0DF0464C05D42A2A22BB60E8B19A9B8">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalLossFreeCreditFortress>
      <TotalLossFreeDiscount id="TA93353F2B27C4CE3AAEE688AE682F720">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalLossFreeDiscount>
      <TotalMembershipCredit id="T77769662D4E44F56B779EFFF8359E30D">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalMembershipCredit>
      <TotalMilitaryPartTimeCredit id="T37C0A214ABEE4869A12DD695DCCBE73D">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalMilitaryPartTimeCredit>
      <TotalMilitarySuspensionCredit id="T8903E6E9AE0342169B04C0CA765E93AE">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalMilitarySuspensionCredit>
      <TotalMultiDentistGroupCredit id="T9596809CEB84407E9ED269CA49E85126">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalMultiDentistGroupCredit>
      <TotalNewBusinessClaimsFreeCredit id="T7E707F7EDB8E47DBB4F7EF965544A60B">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalNewBusinessClaimsFreeCredit>
      <TotalNewDentistCredit id="T2F1BDD37AB92491B8BCAF008707F9632">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalNewDentistCredit>
      <TotalNewToPracticeCredit id="T90A1090A3511498F802DD3068D9636D2">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalNewToPracticeCredit>
      <TotalPartTimeExclusionCredit id="TB6C41EC113594201A0B6C52D42B27AB5">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalPartTimeExclusionCredit>
      <TotalPartTimePractice id="T53952CC7740F477FBC45AA345FBD9FEE">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalPartTimePractice>
      <TotalResidentRiskManagementCredit id="T26D12E28FD4C40DEB8172F59ED6677E7">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalResidentRiskManagementCredit>
      <TotalRestrictedPartTimeCredit id="T46E0F3DBE9924CA3B8C27C5E0C901D76">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalRestrictedPartTimeCredit>
      <TotalRiskManagementCredit id="T9D90DA2C8FA64138957E9D0B545D5556">
        <Premium>-236</Premium>
        <change>-236</change>
        <offset>0</offset>
        <onset>-236</onset>
        <written>-236</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalRiskManagementCredit>
      <TotalScheduleCredit id="T6D4DB7EAD4D34B93A4C986F26601BC7E">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalScheduleCredit>
      <TotalSuspensionOfInsuranceCredit id="T9D4CB6935EF544188C800480120EA586">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalSuspensionOfInsuranceCredit>
      <TotalVolunteerPartTimeCredit id="TA5F2F1C6572045BA97CA45855FD9B7A5">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalVolunteerPartTimeCredit>
      <TotalWaiverOfConsentToSettleDiscount id="TBE506F8D8E1A4369B2882220E9F5F34D">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalWaiverOfConsentToSettleDiscount>
      <TotalDentalFacultyCredit id="T46E3F78B10D84F81A8C4EABE54721278">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalDentalFacultyCredit>
      <deathcredit id="d8CA0F1C1D2A948349D979FAD159A923D">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </deathcredit>
      <disabilitycredit id="d4B6B8D1F892E42E196A285326F58DEFD">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </disabilitycredit>
      <retirementcredit id="r1FE3CEA9F2BF43DB8CD86E3A9E2F3B98">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </retirementcredit>
      <premiumcredit id="p66B7922293B54A7E92779A57DC274AD3">
        <TotalPremium>0</TotalPremium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <TotalWritten>0</TotalWritten>
        <TotalChange>0</TotalChange>
        <written />
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </premiumcredit>
      <facultydiscount id="f5DFF35AF3A9D46A2B91A69D99A43549A">
        <TotalPremium>0</TotalPremium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <TotalChange>0</TotalChange>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </facultydiscount>
      <TotalBreachResponseAnddDefenseCredit id="T652ED756EF6349449EDCEADDF52901D3">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalBreachResponseAnddDefenseCredit>
      <TotalClaimsExperienceSurcharge id="TCBE155F79DA9425EAF80AF0FC09E47BD">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalClaimsExperienceSurcharge>
      <TotalExperienceDebitSurcharge id="TF3B60F0EA4394F3B995607A92C4E4895">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalExperienceDebitSurcharge>
      <TotalScheduleDebit id="T5B11BF51D6CB44159E92BBE457830DC5">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalScheduleDebit>
      <TotalSurcharge id="T92A7036849D6459DB843A67F0BB7E32C">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalSurcharge>
      <TotalPremiumDebit id="T0C1688AD8AE447D4BA0A6CB09671FD7A">
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalPremiumDebit>
      <TotalGrossPremium id="T0B79A8489FFF4AB696A87FFEF44E801B">
        <Premium>2361</Premium>
        <change>2361</change>
        <offset>0</offset>
        <onset>2361</onset>
        <written>2361</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </TotalGrossPremium>
      <TotalGrossPremiumAdjustment id="T11090CA601414E1BA969747F92440222">
        <Premium>0</Premium>
        <change>0</change>
        <written>0</written>
      </TotalGrossPremiumAdjustment>
      <TotalSurcharges id="T0091734FAEEB45AD9D33BA85562E9657" prior="0">
        <Premium>0</Premium>
        <written>0</written>
        <change>0</change>
      </TotalSurcharges>
      <TotalCredits id="T85C6AF024CE34B0DA5325F760DCE355F">
        <Premium>-236</Premium>
        <written>-236</written>
        <change>-236</change>
      </TotalCredits>
      <TotalPrimaryGrossPremium id="TBCBAC0FE05344B8CA20C939D02CDD766">
        <Premium>2361</Premium>
        <change>2361</change>
        <offset>0</offset>
        <onset>2361</onset>
        <written>2361</written>
      </TotalPrimaryGrossPremium>
      <taildownstream id="t359C6F5D3DB24A119BA5B2E4846C800A">
        <IsTail>0</IsTail>
      </taildownstream>
      <DeclineOverride>0</DeclineOverride>
      <Indicators id="IAAC4084877DA4FD18FC45E2779935E25">
        <Type>IsFinalReportable</Type>
        <bValue>0</bValue>
      </Indicators>
      <PrimaryRatingState>NY</PrimaryRatingState>
      <exposure id="eBB64E720FD73463A9D7AA07C06F78B11">
        <Type>DaysWithoutCoverage</Type>
        <sValue>N/A</sValue>
      </exposure>
      <Class>F1</Class>
      <IRRequestCounter>0</IRRequestCounter>
      <ReadyToIssue>1</ReadyToIssue>
      <taildiscount id="t045FAD6620624AFCB99586EED5C6C610">
        <TailDiscountDeathPercentage>1</TailDiscountDeathPercentage>
        <TailDiscountDisabilityPercentage>1</TailDiscountDisabilityPercentage>
        <TailDiscountRetirementPercentage>0</TailDiscountRetirementPercentage>
        <TailDiscountDeath>0</TailDiscountDeath>
        <TailDiscountDisability>0</TailDiscountDisability>
        <TailDiscountRetirement>0</TailDiscountRetirement>
      </taildiscount>
      <coverage id="c030AC6AED13E4388BF33B400B9F4B9A7">
        <Type>PolicyAdjustment</Type>
        <Indicator>0</Indicator>
        <Caption>Policy_Adjustment</Caption>
        <CoverageCode>POLADJ</CoverageCode>
        <Premium>0</Premium>
        <change>0</change>
        <offset>0</offset>
        <onset>0</onset>
        <written>0</written>
        <priorTerm>0</priorTerm>
        <prior>0</prior>
        <cancelPrem>0</cancelPrem>
      </coverage>
      <ManualRate>0</ManualRate>
      <ManualPremium>3294</ManualPremium>
      <PurePremium written="3294" id="PA0618ABDB2E242719EADCD230B5DA2D5" change="3294" prior="0">3294</PurePremium>
      <LineTotalPurePremium change="3294" id="L1026FFB8B3424F40A271983036465B8C" written="3294">3294</LineTotalPurePremium>
      <LineTotalTaxesSurcharges written="0" id="LD15FD54C4D7A49A5953DD1ED1E0D8D02" change="0">0</LineTotalTaxesSurcharges>
      <LineTotalPremium written="3294" id="LCB7C73223CAE475EAFBB4B28441D1BFD" change="3294">3294</LineTotalPremium>
      <ManualPremiumWritten>3294</ManualPremiumWritten>
      <priorInsurance id="p19A15D9CD4FA446991FA39A0752E54D1" />
      <agencyinput.id id="a608AA24D91374128B02AC4C7542CE4C6" />
      <IsMPRPolicy>0</IsMPRPolicy>
      <IssueSystem>DCT</IssueSystem>
      <MasterCompany>02</MasterCompany>
      <PolicyProcessingOrgUnitCode>02</PolicyProcessingOrgUnitCode>
      <billingreceivables id="b04937A56247F4804ABC61CB88326B83E">
        <premium id="p42F4687C03D6480290876C1FF86A9253" />
        <tax id="t24B8FD45D8654EB68A37091C79367D39" />
      </billingreceivables>
      <creditOrSurcharge id="cA9EB388DA45B409D806F5E19F8C5CC8E">
        <Type>ClaimFree</Type>
        <Factor>0</Factor>
      </creditOrSurcharge>
      <creditOrSurcharge id="c7D3C8BE7036F4B7B9F2325955F17E0AA">
        <Type>PriorInsurance</Type>
        <Factor>0</Factor>
      </creditOrSurcharge>
      <borletter id="bDE875AD0B2CC4C60AE42992CBC5234C9" />
      <otem id="o0F964DA356544C17B5DA34847D764382" />
      <nonrenewallettercm10days id="nBD81B2BA251F48FE9470DC263675A6F3" />
      <freeformendorsement id="fF07515BD681641869ECD2EA5F3965BD4" />
      <allcoveragescalc id="aE7A3CBF8E5104F7BA07CC7A267FD5385" />
      <amendedpolicypremiumendorsement2031la id="aDD23A7D9C4304754B9D5FA9D35117F2F" />
      <PrintJobFlag>ALL</PrintJobFlag>
      <endorsementpolicychange id="eF3A7AD37C450496EB35AF5AB383A7BC5" />
      <amendedpolicypremiumendorsement2031 id="a011FCA070FD740C1AF8CCEFF06E57DE9" />
      <ZipCodeLookup id="Z6A74D34A45AA40C4B6CE17EF64F96885">
        <ZipCodeLookupData id="ZA12D27250D78495BBB4C5FEB934C059B">
          <ZipCode>99518</ZipCode>
          <City>Anchorage</City>
          <StateCode>AK</StateCode>
          <County>Anchorage</County>
          <State>AK</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z999BD57DD2D248E09A06A57E536679B6">
          <ZipCode>35234</ZipCode>
          <City>Birmingham</City>
          <StateCode>AL</StateCode>
          <County>Jefferson</County>
          <State>AL</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z90ABD9391159467695ED2157E55089E5">
          <ZipCode>72205</ZipCode>
          <City>Little Rock</City>
          <StateCode>AR</StateCode>
          <County>Pulaski</County>
          <State>AR</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZD59E56A00AC845DAB8B669EB3526F9D7">
          <ZipCode>85004</ZipCode>
          <City>Phoenix</City>
          <StateCode>AZ</StateCode>
          <County>Maricopa</County>
          <State>AZ</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z18534F2A183A4F66B69634E76DD0E7EA">
          <ZipCode>94044</ZipCode>
          <City>Pacifica</City>
          <StateCode>CA</StateCode>
          <County>San Mateo</County>
          <State>CA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z8F3B9C3E058E47008D746935661A1B69">
          <ZipCode>80205</ZipCode>
          <City>Denver</City>
          <StateCode>CO</StateCode>
          <County>Denver</County>
          <State>CO</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZB643AABBFDF643E7BF1C6F4E71464DF5">
          <ZipCode>06247</ZipCode>
          <City>Hampton</City>
          <StateCode>CT</StateCode>
          <County>Windham</County>
          <State>CT</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z01C84C7CD215447F8B51370D46470237">
          <ZipCode>20001</ZipCode>
          <City>Washington</City>
          <StateCode>DC</StateCode>
          <County>District of columbia</County>
          <State>DC</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z65B2C1384B6F4D4AA30092A6D02AEEF6">
          <ZipCode>19701</ZipCode>
          <City>Bear</City>
          <StateCode>DE</StateCode>
          <County>New Castle</County>
          <State>DE</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZD2BD318A9731461FBC4E559EA13F9538">
          <ZipCode>32176</ZipCode>
          <City>Ormond Beach</City>
          <StateCode>FL</StateCode>
          <County>Volusia</County>
          <State>FL</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z1EE47C638C584B14818B0680186ACAE2">
          <ZipCode>31419</ZipCode>
          <City>Savannah</City>
          <StateCode>GA</StateCode>
          <County>Chatham</County>
          <State>GA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZF0B585F139BB45DAA128DBEF157A83D7">
          <ZipCode>96815</ZipCode>
          <City>Honolulu</City>
          <StateCode>HI</StateCode>
          <County>Honolulu</County>
          <State>HI</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z0F266DD37EEE46F0ABB89E078C5739F6">
          <ZipCode>50001</ZipCode>
          <City>Ackworth</City>
          <StateCode>IA</StateCode>
          <County>Warren'</County>
          <State>IA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z0D717C18AC484DDE9EE015E5F9868A90">
          <ZipCode>50309</ZipCode>
          <City>Des Moines</City>
          <StateCode>ID</StateCode>
          <County>Polk</County>
          <State>ID</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z29C7B0A84C5248C6854680FD09984B8D">
          <ZipCode>60619</ZipCode>
          <City>Chicago</City>
          <StateCode>IL</StateCode>
          <County>Cook</County>
          <State>IL</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z6AD3471768194350B6A2C1BBC8685411">
          <ZipCode>46001</ZipCode>
          <City>Alexandria</City>
          <StateCode>IN</StateCode>
          <County>Madison</County>
          <State>IN</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z71C442EA828A4ACC8B3657C046FE0976">
          <ZipCode>66006</ZipCode>
          <City>Baldwin City</City>
          <StateCode>KS</StateCode>
          <County>Douglas</County>
          <State>KS</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZFCD77BCE10184D348EEA90877A596BFE">
          <ZipCode>40003</ZipCode>
          <City>Bagdad</City>
          <StateCode>KY</StateCode>
          <County>Shelby</County>
          <State>KY</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZA7659C1668614EA797B07015C9D183B9">
          <ZipCode>70112</ZipCode>
          <City>New Orleans</City>
          <StateCode>LA</StateCode>
          <County>Orleans</County>
          <State>LA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZCD507CF5A9364D0C9E5DC1C0D052E0F3">
          <ZipCode>02109</ZipCode>
          <City>Boston</City>
          <StateCode>MA</StateCode>
          <County>Suffolk</County>
          <State>MA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z0F48232C21DE4A91BF7A4555DFC7CBA2">
          <ZipCode>20588</ZipCode>
          <City>Columbia</City>
          <StateCode>MD</StateCode>
          <County>Howard</County>
          <State>MD</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZE7829D1E7AC44BC88266DC7A819A4FF5">
          <ZipCode>03905</ZipCode>
          <City>Kittery Point</City>
          <StateCode>ME</StateCode>
          <County>York</County>
          <State>ME</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z334DB241989E4C4EBDD2C909E4168A08">
          <ZipCode>48001</ZipCode>
          <City>Clay</City>
          <StateCode>MI</StateCode>
          <County>St Clair</County>
          <State>MI</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZC72C69B29DE04436A12CA1C9E15766CC">
          <ZipCode>55001</ZipCode>
          <City>Afton</City>
          <StateCode>MN</StateCode>
          <County>Washington</County>
          <State>MN</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z0EA3D6A479C8407598ECF6EF7D6EB80B">
          <ZipCode>65613</ZipCode>
          <City>Bolivar</City>
          <StateCode>MO</StateCode>
          <County>Polk</County>
          <State>MO</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z764EAE5B5CA449D6B9125F0F3A44EA6F">
          <ZipCode>38606</ZipCode>
          <City>Batesville</City>
          <StateCode>MS</StateCode>
          <County>Panola</County>
          <State>MS</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZE46E12AAEDAA4C6AA3B84631D8526E9D">
          <ZipCode>59847</ZipCode>
          <City>Lolo</City>
          <StateCode>MT</StateCode>
          <County>Missoula</County>
          <State>MT</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z962940EAD2954056B6C7912865D9A7F5">
          <ZipCode>27009</ZipCode>
          <City>Asheville</City>
          <StateCode>NC</StateCode>
          <County>Buncombe</County>
          <State>NC</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZAB13718FEFD14C3D8AD888C3A42E529E">
          <ZipCode>57270</ZipCode>
          <City>Velden</City>
          <StateCode>ND</StateCode>
          <County>Marshall</County>
          <State>ND</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z5879B0BF308C469C98A7464A5E2A6AC7">
          <ZipCode>68004</ZipCode>
          <City>Bancroft</City>
          <StateCode>NE</StateCode>
          <County>Cuming</County>
          <State>NE</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z13E85ED37AE047A5BE98CF05E409B88D">
          <ZipCode>03036</ZipCode>
          <City>Chester</City>
          <StateCode>NH</StateCode>
          <County>Rockingham</County>
          <State>NH</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z0EE87DFAB1D64FB6B015E43ADACEBE24">
          <ZipCode>08212</ZipCode>
          <City>Cape May</City>
          <StateCode>NJ</StateCode>
          <County>Cape May</County>
          <State>NJ</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZA441D12C424D4947A6271EA2604551C0">
          <ZipCode>87007</ZipCode>
          <City>Casa Blanca</City>
          <StateCode>NM</StateCode>
          <County>Cibola</County>
          <State>NM</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZC5CF84B53CAF4085A339EAA3594108A9">
          <ZipCode>89004</ZipCode>
          <City>Blue Diamond</City>
          <StateCode>NV</StateCode>
          <County>Clark</County>
          <State>NV</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z34529D68E9764CB9B77121774EA9D33A">
          <ZipCode>10001</ZipCode>
          <City>New York</City>
          <StateCode>NY</StateCode>
          <County>New York</County>
          <State>NY</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z5566E7DF7DD1450A9332E4FAA1C298E7">
          <ZipCode>43202</ZipCode>
          <City>Columbus</City>
          <StateCode>OH</StateCode>
          <County>Franklin</County>
          <State>OH</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z481AB83114DD442D9B3136033C045F0C">
          <ZipCode>73001</ZipCode>
          <City>Albert</City>
          <StateCode>OK</StateCode>
          <County>Caddo</County>
          <State>OK</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z97ED6E6D97474B3FB54CF26B16CE135E">
          <ZipCode>97007</ZipCode>
          <City>Beaverton</City>
          <StateCode>OR</StateCode>
          <County>Washington</County>
          <State>OR</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z40823DA9728F4384A95A83B1502A967A">
          <ZipCode>19063</ZipCode>
          <City>Media</City>
          <StateCode>PA</StateCode>
          <County>Delaware</County>
          <State>PA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZAE4CBFE22E50479C8D6D08CD9914DD2C">
          <ZipCode>02801</ZipCode>
          <City>Adamsville</City>
          <StateCode>RI</StateCode>
          <County>Newport</County>
          <State>RI</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZFB79F54736C54880AB0F1207B73CCE7A">
          <ZipCode>29201</ZipCode>
          <City>Columbia</City>
          <StateCode>SC</StateCode>
          <County>Richland</County>
          <State>SC</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z78279D9D8F2C483D9B94FA71C2A9E142">
          <ZipCode>56160</ZipCode>
          <City>Odin</City>
          <StateCode>SD</StateCode>
          <County>Watonwan</County>
          <State>SD</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z91D1D457E96C4555949C8F1DC72DFDE6">
          <ZipCode>37011</ZipCode>
          <City>Antioch</City>
          <StateCode>TN</StateCode>
          <County>Davidson</County>
          <State>TN</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z02C04BBEE7C9422492DBD8A47D849908">
          <ZipCode>73301</ZipCode>
          <City>Austin</City>
          <StateCode>TX</StateCode>
          <County>Austin</County>
          <State>TX</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z2A15ACF4FCBA437CB7DAF778ED8C3537">
          <ZipCode>82930</ZipCode>
          <City>Evanston</City>
          <StateCode>UT</StateCode>
          <County>Travis</County>
          <State>UT</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z6A5980231C2D49FF9BE62A8318D29532">
          <ZipCode>22040</ZipCode>
          <City>Falls Church</City>
          <StateCode>VA</StateCode>
          <County>Falls Church</County>
          <State>VA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z56520354704C44B59CB8593CAEAD7AE4">
          <ZipCode>05052</ZipCode>
          <City>North Hartland</City>
          <StateCode>VT</StateCode>
          <County>Windsor</County>
          <State>VT</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z7286C66A017C426E82A7C5FE30EFC1DE">
          <ZipCode>98002</ZipCode>
          <City>Auburn</City>
          <StateCode>WA</StateCode>
          <County>King</County>
          <State>WA</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZFD996937F08944A39DDA7FF20BEEFC76">
          <ZipCode>53003</ZipCode>
          <City>Allenton</City>
          <StateCode>WI</StateCode>
          <County>Washington</County>
          <State>WI</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="ZDB1715244967412FA5CAD22CFB613343">
          <ZipCode>24622</ZipCode>
          <City>Tiptop</City>
          <StateCode>WV</StateCode>
          <County>Tazewell</County>
          <State>WV</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z4A6A448C0C934B4F9DD320794F16D6F1">
          <ZipCode>82002</ZipCode>
          <City>Cheyenne</City>
          <StateCode>WY</StateCode>
          <County>Laramie</County>
          <State>WY</State>
        </ZipCodeLookupData>
        <ZipCodeLookupData id="Z010DAF647BBD4FADA92898C101E8E26B">
          <ZipCode>42025</ZipCode>
          <City>Benton</City>
          <StateCode>KY</StateCode>
          <County>Marshall</County>
          <State>KY</State>
        </ZipCodeLookupData>
      </ZipCodeLookup>
      <billingpcninfo id="bD6F83B689BAB4EEB8A343100C8C7748A">
        <InvoiceAmountDue>0</InvoiceAmountDue>
        <OrigInvoiceDueDate>2012-01-01</OrigInvoiceDueDate>
        <CancelDate>2012-01-01</CancelDate>
        <CancelDueDate>2012-01-01</CancelDueDate>
        <PastDueAmount>590.25</PastDueAmount>
        <AccountReference>3222566</AccountReference>
      </billingpcninfo>
      <TaxesSurcharges change="0" id="T89E2D9C248AF4A43BE4C30D4C6F3614F" written="0">0</TaxesSurcharges>
      <PolicyNumber>3222566</PolicyNumber>
      <ClearanceOverride>0</ClearanceOverride>
      <SearchMode>AccountReference</SearchMode>
      <AttachToAccountReference />
      <AttachToAccountId />
      <AccountPlanCode />
      <PolicyPlanCode>FOR_ACT_P2</PolicyPlanCode>
      <AttachToAccountType />
      <LineOfBusiness>MedicalProfessionalLiability</LineOfBusiness>
      <LineOfBusinessCode>MPL</LineOfBusinessCode>
      <SearchString />
      <EntityPolicyNumber />
      <RenewalTo1>0</RenewalTo1>
      <ShowDeathDisabilityRetirement>1</ShowDeathDisabilityRetirement>
      <ManualPremiumChange>3294</ManualPremiumChange>
      <PremiumChange>3294</PremiumChange>
      <ResidentRiskManagementCreditApplied prior="0" id="R90CF2E563EA045A1B9E69AA6453CA40E" />
      <PremiumWritten>3294</PremiumWritten>
      <Premium>3294</Premium>
      <Form1049GrossPremium>3530</Form1049GrossPremium>
      <PartyInvolvementRoleCode>P</PartyInvolvementRoleCode>
      <TransactionDescription>Tail-Committed</TransactionDescription>
      <CountAffiliate id="C9E1F769A22C8448D880F9D65A91BC5A5">
        <CountAffiliatePrior>0</CountAffiliatePrior>
      </CountAffiliate>
      <ShowPolicyNumber>3222566</ShowPolicyNumber>
      <PreviousPolicyNumber />
      <BillingStatus>Cancelled</BillingStatus>
      <InforceStatus>Cancelled</InforceStatus>
      <RenewStatus>OK</RenewStatus>
      <FormsPrivateCurrentFormWithdrawnDate>2100-01-01</FormsPrivateCurrentFormWithdrawnDate>
      <ProducerForReissue />
      <DCT_JOBNAMES>FD3CDEC</DCT_JOBNAMES>
      <DCT_JOBCAPTIONS>Extended Reporting Policy Declarations </DCT_JOBCAPTIONS>
      <AgencyIDForReissue />
      <PremiumCheck>2361</PremiumCheck>
      <FD1CDECMilitaryPartTimeCreditPercent>0</FD1CDECMilitaryPartTimeCreditPercent>
      <DCT_FORMLIST>ExternalName, ExternalName, ExternalName, ExternalName</DCT_FORMLIST>
      <DCT_NAMELIST>ExternalCaption, ExternalCaption, ExternalCaption, ExternalCaption</DCT_NAMELIST>
      <SelectedIndicatorFRINAsTrue>0</SelectedIndicatorFRINAsTrue>
      <AffiliateEntities id="A85B1BCF0A00C49E281DEC6AC4584B0FF">
        <StoredProc.callStoredProcRs status="success" id="S5CC970120C9848D0BD33AFE0D3376F17">
          <rows />
          <rowStats count="0" id="rE1BC97BBEAED4B5D85A68325C4048EA9" />
        </StoredProc.callStoredProcRs>
      </AffiliateEntities>
      <Volunteer>0</Volunteer>
      <RotatingResident>0</RotatingResident>
      <Indicators id="IB4BA683275EE4DF7BA5581B2B2A9B27F">
        <Type>IsReportable</Type>
        <bValue>0</bValue>
      </Indicators>
      <Indicators id="ICF4639906B67426A8B0D55661DA035A6">
        <Type>IsMidPointReportable</Type>
        <bValue>0</bValue>
      </Indicators>
      <UWConcern>1</UWConcern>
      <UWComments>F/U on Aspen exposure when removing suspension</UWComments>
      <CancellationReason>nonPay</CancellationReason>
      <premiumaddreturnamount>1</premiumaddreturnamount>
      <premiumaddreturntax>0</premiumaddreturntax>
      <ProductLOB>MedicalProfessionalLiability</ProductLOB>
      <PreviousAAOMSMembershipStatus>0</PreviousAAOMSMembershipStatus>
      <SetAssignedUnderwriter />
      <OnlineData.listEntityLobsRs status="success" id="O2026BC5B38C5460F9541E5B4D1530CEE">
        <EntityLobs count="2" id="ECCE6E9A31AFF4E0E9ACBD1847F679771">
          <EntityLob id="10138" entityID="10041" state="NY" lob="MedicalProfessionalLiability" effectiveDate="" expirationDate="" entityName="Walsh Duffield Companies, Inc." expired="0" />
          <EntityLob id="10407" entityID="10041" state="PA" lob="MedicalProfessionalLiability" effectiveDate="" expirationDate="" entityName="Walsh Duffield Companies, Inc." expired="0" />
        </EntityLobs>
      </OnlineData.listEntityLobsRs>
      <TerminationDate>2025-11-07</TerminationDate>
      <TailQuoteExpiration>2026-04-25</TailQuoteExpiration>
    </policy>
    <ProRatingOn>1</ProRatingOn>
    <CurrentTransactionType>Tail</CurrentTransactionType>
    <policyAdmin id="p415C6CE5BA45468ABEEF1A13B61156FF">
      <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
      <PolicyManuScriptVersionID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptVersionID>
      <CommunicationsManuScriptID>Carrier_ProductBase_Communications_1_0_0_0</CommunicationsManuScriptID>
      <UseDCTForms>1</UseDCTForms>
      <UseDCTNotifications>1</UseDCTNotifications>
      <UseDCTTasks>1</UseDCTTasks>
      <UseDCTBilling>1</UseDCTBilling>
      <UseDCTFormsAndMessages>1</UseDCTFormsAndMessages>
      <UseDCTCommissionPlans>1</UseDCTCommissionPlans>
      <transactions id="tF3D156AFADC54B709A00069D2489920E">
        <transaction id="t1AE93D3D73464743A3A76229DFD455D3">
          <reasons id="r5CC7E56DF7C547CD9CEA6251B83EF7BB" />
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>webagent</StatusUser>
          <Type>New</Type>
          <TypeCaption>New Business</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2024-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2024-10-31T12:30:38</CreatedDate>
          <CreatedUser>webunderwriter</CreatedUser>
          <OriginalCharge>0</OriginalCharge>
          <Charge>2361</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2361</TermPremium>
          <PriorPremium />
          <NewPremium>2361</NewPremium>
          <HistoryID>619546</HistoryID>
          <ConvertedTransactionType>New</ConvertedTransactionType>
          <StatusUserContext>Agent</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <DeprecatedBy />
          <ExpirationDate>2025-11-07</ExpirationDate>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <WaiveChargeCheck />
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <PolicyStatus>InForce</PolicyStatus>
          <IssuedDate>2024-10-31T12:42:07</IssuedDate>
          <IssuedUserName>webagent</IssuedUserName>
          <DestinationPolicyID>0</DestinationPolicyID>
          <SourcePolicyID>0</SourcePolicyID>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tCA987CEEAD2A4F0FAE48BD831F294AD1">
          <reasons id="rEA47AEEA19C04AA9902BA30763834948">
            <reason id="rFA48D7BC3F904EB78E001EB46CEC9881">
              <Code>AddEndorse</Code>
              <CodeCaption />
              <Description>agent issued/audit</Description>
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\jpietrandoni</StatusUser>
          <Type>Endorse</Type>
          <TypeCaption>Endorse</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2024-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2024-11-01T08:10:09</CreatedDate>
          <CreatedUser>OMSNICPDC\jpietrandoni</CreatedUser>
          <OriginalCharge>2361</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2361</TermPremium>
          <PriorPremium />
          <NewPremium>2361</NewPremium>
          <HistoryID>619742</HistoryID>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <DeprecatedBy />
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <PolicyStatus>InForce</PolicyStatus>
          <IssuedDate>2024-11-01T08:15:38</IssuedDate>
          <IssuedUserName>OMSNICPDC\jpietrandoni</IssuedUserName>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tC5B722D530A04D43956816A9E8C3B39B">
          <reasons id="rF5ED10C8A73144EEBC54D05BE3B39CD3">
            <reason id="r675ACF261848463C91C2E51386D5D399">
              <Code>90DayRiskManagementEndorse</Code>
              <CodeCaption />
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <Type>Endorse</Type>
          <TypeCaption>Endorse</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2024-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-05-27T13:24:55</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2361</TermPremium>
          <PriorPremium />
          <NewPremium>2361</NewPremium>
          <HistoryID>669843</HistoryID>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Cancel-Pending</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <PolicyStatus>Cancel-Pending</PolicyStatus>
          <DeprecatedBy />
          <IssuedDate>2025-05-27T13:26:09</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t2CE7373D523C485BA0BCEF82C99101AA">
          <reasons id="r44686BB5179C40D9BEE1D03042496EE4">
            <reason id="r243C1D51D2D44DD8BC6CDC263ABE7C6D">
              <Code>nonPay</Code>
              <CodeCaption>Non-Payment of Premium</CodeCaption>
              <Description>Non-Payment</Description>
            </reason>
          </reasons>
          <OnsetBy>tC5B722D530A04D43956816A9E8C3B39B</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>669844</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ScheduleDate>2025-01-14</ScheduleDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Cancel Pending</TypeCaption>
          <CancelPendingDate>2025-01-07</CancelPendingDate>
          <PolicyStatus>Cancel-Pending</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Cancel-Pending</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>CancelPending</Type>
          <State>committed</State>
          <EffectiveDate>2025-01-07</EffectiveDate>
          <CreatedDate>2025-05-27T13:26:08</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <HistoryIDOriginal>627297</HistoryIDOriginal>
          <OriginalID>t3A7DEDFDE5984CAD9D9F101BEC1D1AFD</OriginalID>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2361</TermPremium>
          <PriorPremium />
          <NewPremium>2361</NewPremium>
          <ConvertedTransactionType>CancelPending</ConvertedTransactionType>
          <ProductIsReadOnly>1</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-05-27T13:26:08</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t6741EA8340F04139AE7C089B7B0B924C">
          <reasons id="rE0C61DAE30BB41E780B806FB377A4C41">
            <reason id="r9BE900FB793646679DCFFA92F79E6EA0">
              <Code>nonPay</Code>
              <CodeCaption>Non-Payment of Premium</CodeCaption>
              <Description>Non-Payment</Description>
            </reason>
          </reasons>
          <OnsetBy>tC5B722D530A04D43956816A9E8C3B39B</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>669845</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Rescind Cancel Pending</TypeCaption>
          <PolicyStatus>InForce</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>RescindCancelPending</Type>
          <State>committed</State>
          <EffectiveDate>2025-01-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-05-27T13:26:08</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <HistoryIDOriginal>631079</HistoryIDOriginal>
          <OriginalID>tB35F6658C070468498EC4A3747F2C725</OriginalID>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2361</TermPremium>
          <PriorPremium />
          <NewPremium>2361</NewPremium>
          <ConvertedTransactionType>RescindCancelPending</ConvertedTransactionType>
          <ProductIsReadOnly>1</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-05-27T13:26:08</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tF8B729F6DA2647C8999E7D9B4A6C126A">
          <reasons id="r47767560672449A9B6875A994CA43834">
            <reason id="r44109F1C97FE412BA3A0834326F73C3B">
              <Code>AddSuspension</Code>
              <CodeCaption>Add Suspension</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <Type>Endorse</Type>
          <TypeCaption>Endorse</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-05-01</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-06-16T10:36:06</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <OriginalCharge>0</OriginalCharge>
          <Charge>-1045</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>354</TermPremium>
          <PriorPremium />
          <NewPremium>1316</NewPremium>
          <HistoryID>674285</HistoryID>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <HistoryIDOriginal />
          <PolicyStatus>InForce</PolicyStatus>
          <DeprecatedBy />
          <IssuedDate>2025-06-16T10:37:27</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tF02FDDD8804145CCABC7CBD1DBCC460B">
          <reasons id="rC5B9DC0A22FC4EF6B5B82EBE8DF268CA">
            <reason id="r48467FDEEA4942999658627CB5E8830E">
              <Code>AddSuspension</Code>
              <CodeCaption>Add Suspension</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <OnsetBy>tF8B729F6DA2647C8999E7D9B4A6C126A</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>674286</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ScheduleDate />
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Endorse</TypeCaption>
          <PolicyStatus>InForce</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>Endorse</Type>
          <State>committed</State>
          <EffectiveDate>2025-05-08</EffectiveDate>
          <CreatedDate>2025-06-16T10:37:23</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <HistoryIDOriginal>671278</HistoryIDOriginal>
          <OriginalID>tEA6969141F8D432BBEB23AC51F5CCA69</OriginalID>
          <OriginalCharge>-1006</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>354</TermPremium>
          <PriorPremium />
          <NewPremium>1316</NewPremium>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-06-16T10:37:24</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t20208D2306164804B1203B504218A7BE">
          <reasons id="r54F12983F1934A02941A1D47FEFBB144">
            <reason id="r5ECBAD4983514A34B6A9DE4CABB5A1C9">
              <Code>ChangeAddress</Code>
              <CodeCaption />
              <Description />
            </reason>
          </reasons>
          <OnsetBy>tF8B729F6DA2647C8999E7D9B4A6C126A</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>674287</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ScheduleDate />
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Endorse</TypeCaption>
          <PolicyStatus>InForce</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>Endorse</Type>
          <State>committed</State>
          <EffectiveDate>2025-06-11</EffectiveDate>
          <CreatedDate>2025-06-16T10:37:24</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <HistoryIDOriginal>673596</HistoryIDOriginal>
          <OriginalID>tDC8B7F610D184AA781CF47FF42330D21</OriginalID>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>354</TermPremium>
          <PriorPremium />
          <NewPremium>1316</NewPremium>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-06-16T10:37:26</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t21DB2742F11B44C5AB5757FD017BA185">
          <reasons id="rE7BF1CD205C6434FBF4C2FA7CFDFD840">
            <reason id="r0490642049B94158B0C516DE6316591B">
              <Code>nonPay</Code>
              <CodeCaption>Non-Payment of Premium</CodeCaption>
              <Description>Non-Payment</Description>
            </reason>
          </reasons>
          <OnsetBy>tF8B729F6DA2647C8999E7D9B4A6C126A</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>674288</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ScheduleDate>2025-06-21</ScheduleDate>
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Cancel Pending</TypeCaption>
          <CancelPendingDate>2025-06-14</CancelPendingDate>
          <PolicyStatus>Cancel-Pending</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <DestinationPolicyID>0</DestinationPolicyID>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Cancel-Pending</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>CancelPending</Type>
          <State>committed</State>
          <EffectiveDate>2025-06-14</EffectiveDate>
          <CreatedDate>2025-06-16T10:37:26</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <HistoryIDOriginal>673597</HistoryIDOriginal>
          <OriginalID>t9D1F19362F564DE29B137057CFF46975</OriginalID>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>354</TermPremium>
          <PriorPremium />
          <NewPremium>1316</NewPremium>
          <ConvertedTransactionType>CancelPending</ConvertedTransactionType>
          <ProductIsReadOnly>1</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-06-16T10:37:26</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t1792ACCC13CB4ACDA394276F1CCB9AAC">
          <reasons id="r74F03F7617E0459BAC935C7C145EF380">
            <reason id="rBAC4BC8F3E644F5AACC96812ED0F6E3E">
              <Code>AddSuspension</Code>
              <CodeCaption>Add Suspension</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <OnsetBy>tF8B729F6DA2647C8999E7D9B4A6C126A</OnsetBy>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <HistoryID>674289</HistoryID>
          <Status>Committed</Status>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ScheduleDate />
          <ExpirationDate>2025-11-07</ExpirationDate>
          <CancellationDate />
          <TypeCaption>Rescind Cancel Pending</TypeCaption>
          <PolicyStatus>InForce</PolicyStatus>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <DestinationPolicyID>0</DestinationPolicyID>
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Type>RescindCancelPending</Type>
          <State>committed</State>
          <EffectiveDate>2025-06-14</EffectiveDate>
          <CreatedDate>2025-06-16T10:37:26</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <HistoryIDOriginal>673598</HistoryIDOriginal>
          <OriginalID>tAA653140E0BD49FE902F9C054B77F736</OriginalID>
          <OriginalCharge>0</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>354</TermPremium>
          <PriorPremium />
          <NewPremium>1316</NewPremium>
          <ConvertedTransactionType>RescindCancelPending</ConvertedTransactionType>
          <ProductIsReadOnly>1</ProductIsReadOnly>
          <TransactionDate>2024-11-07T00:00:00.000</TransactionDate>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <IssuedDate>2025-06-16T10:37:27</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <DeprecatedBy />
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="t0DC18AE0E4E8411D934FE6A29E24360A">
          <reasons id="r1AC37892B146456389ABD402B76FBF37">
            <reason id="r489E70471B97403A911207E9C13502E5">
              <Code>AddSuspension</Code>
              <CodeCaption>Add Suspension</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>BatchAgent</StatusUser>
          <Type>Renew</Type>
          <TypeCaption>Renew</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-08-01T05:07:38</CreatedDate>
          <CreatedUser>BatchAgent</CreatedUser>
          <OriginalCharge>0</OriginalCharge>
          <Charge>319</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>319</TermPremium>
          <PriorPremium />
          <NewPremium>319</NewPremium>
          <HistoryID>684263</HistoryID>
          <ConvertedTransactionType>Renew</ConvertedTransactionType>
          <ExpirationDate>2026-11-07</ExpirationDate>
          <StatusUserContext>Agent</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <TransactionDate>2025-11-07T00:00:00.000</TransactionDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <ActivityLogs id="A1B215EE898264EBB9DC2485859F429C0">
            <ActivityLog id="A2CE961ED794C4A42974C600EB776C60B">
              <DateTime>2025-08-01T05:07:39.401</DateTime>
              <HistoryID>684264</HistoryID>
              <Status>Referred</Status>
              <StatusUser>BatchAgent</StatusUser>
            </ActivityLog>
            <ActivityLog id="A383154CCB0654029803EF10A17BA0A12">
              <DateTime>2025-08-01T07:41:44.745</DateTime>
              <HistoryID>684311</HistoryID>
              <Status>Approved</Status>
              <StatusUser>OMSNICPDC\marreola</StatusUser>
            </ActivityLog>
          </ActivityLogs>
          <PolicyStatus>InForce</PolicyStatus>
          <DeprecatedBy />
          <HistoryIDOriginal />
          <IssuedDate>2025-08-24T22:26:36</IssuedDate>
          <IssuedUserName>BatchAgent</IssuedUserName>
          <DestinationPolicyID>0</DestinationPolicyID>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tFA795592761E42DE947C0D1F544A457A">
          <reasons id="r1C19623F066D40458688BC2136862A7D">
            <reason id="r222C054663B34D3889D6C5D793FC078C">
              <Code>AddEndorse</Code>
              <CodeCaption>Add Endorse</CodeCaption>
              <Description>Correct NY District</Description>
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\marreola</StatusUser>
          <Type>Endorse</Type>
          <TypeCaption>Endorse</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-10-03T13:14:43</CreatedDate>
          <CreatedUser>OMSNICPDC\marreola</CreatedUser>
          <OriginalCharge>319</OriginalCharge>
          <Charge>0</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>319</TermPremium>
          <PriorPremium />
          <NewPremium>319</NewPremium>
          <HistoryID>696957</HistoryID>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <StatusUserContext>EarnedPremiumLetter,Underwriter</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <DeprecatedBy />
          <TransactionDate>2025-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2026-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <HistoryIDOriginal />
          <PolicyStatus>InForce</PolicyStatus>
          <IssuedDate>2025-10-03T13:15:03</IssuedDate>
          <IssuedUserName>OMSNICPDC\marreola</IssuedUserName>
          <TempEndorseExpirationDate />
        </transaction>
        <transaction id="tE137BDCC66184226811B5D78C897E43D">
          <reasons id="r8A4C68D5008545728C1CA3E9E07D1D8D">
            <reason id="r096CF8127F674AE3B00968C39290BF74">
              <Code>DeleteSuspension</Code>
              <CodeCaption>Delete Suspension</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <Type>Endorse</Type>
          <TypeCaption>Endorse</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-12-09T15:49:13</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <OriginalCharge>0</OriginalCharge>
          <Charge>1806</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>2125</TermPremium>
          <PriorPremium />
          <NewPremium>2125</NewPremium>
          <HistoryID>710864</HistoryID>
          <ConvertedTransactionType>Endorse</ConvertedTransactionType>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <TransactionDate>2025-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2026-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>InForce</InforceStatus>
          <BillingStatus>Active</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Auditable />
          <PolicyStatus>InForce</PolicyStatus>
          <HistoryIDOriginal />
          <DeprecatedBy />
          <IssuedDate>2025-12-09T15:50:02</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
        </transaction>
        <transaction id="tD2F012331D15474C8B6897E9F65F4D7F">
          <reasons id="rBA7A527358FF4742B02B7C175A2BD187">
            <reason id="r1885F778C7FB4AD28A3FA117E3A6BC2E">
              <Code>insuredRequest</Code>
              <CodeCaption>Insured Request</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <Type>Cancel</Type>
          <TypeCaption>Cancel</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2025-12-09T15:51:43</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <OriginalCharge>1806</OriginalCharge>
          <Charge>-2125</Charge>
          <ProRateFactor>0</ProRateFactor>
          <TermPremium>2125</TermPremium>
          <PriorPremium />
          <NewPremium>0</NewPremium>
          <HistoryID>710873</HistoryID>
          <ConvertedTransactionType>Cancel</ConvertedTransactionType>
          <ShortRateFactor>1</ShortRateFactor>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ProductIsReadOnly>1</ProductIsReadOnly>
          <CancellationDate>2025-11-07</CancellationDate>
          <HistoryIDOriginal />
          <TransactionDate>2025-11-07T00:00:00.000</TransactionDate>
          <ExpirationDate>2026-11-07</ExpirationDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>Cancelled</InforceStatus>
          <BillingStatus>Cancelled</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Auditable />
          <PolicyStatus>Cancelled</PolicyStatus>
          <IssuedDate>2025-12-09T15:51:43</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
          <DeprecatedBy />
        </transaction>
        <transaction id="t2A81C7B8B8B24E1BA5CCAB16A4F7D6DA">
          <reasons id="r42508BCE36D14C27AFC3B29790179B5D">
            <reason id="r368E8E45AB594F5EAE01916066EA2E1A">
              <Code>Other</Code>
              <CodeCaption>See Detail</CodeCaption>
              <Description />
            </reason>
          </reasons>
          <PolicyManuScriptID>OMSNIC_MPL_Individual_1_0_0_0</PolicyManuScriptID>
          <StatusUser>OMSNICPDC\efigueroa</StatusUser>
          <Type>Tail</Type>
          <TypeCaption>Tail</TypeCaption>
          <State>committed</State>
          <Status>Committed</Status>
          <EffectiveDate>2025-11-07</EffectiveDate>
          <ScheduleDate />
          <CreatedDate>2026-01-27T10:25:23</CreatedDate>
          <CreatedUser>OMSNICPDC\efigueroa</CreatedUser>
          <OriginalCharge>-2125</OriginalCharge>
          <Charge>3294</Charge>
          <ProRateFactor>1</ProRateFactor>
          <TermPremium>3294</TermPremium>
          <PriorPremium />
          <NewPremium>3294</NewPremium>
          <HistoryID>720764</HistoryID>
          <ConvertedTransactionType>Tail</ConvertedTransactionType>
          <ExpirationDate>2026-11-07</ExpirationDate>
          <StatusUserContext>EarnedPremiumLetter,Assistant</StatusUserContext>
          <ProductIsReadOnly>0</ProductIsReadOnly>
          <CancellationDate />
          <HistoryIDOriginal />
          <TransactionDate>2025-11-07T00:00:00.000</TransactionDate>
          <WaiveChargeCheck>0</WaiveChargeCheck>
          <Deposit />
          <AuditPremium />
          <AuditCharge />
          <InforceStatus>Cancelled</InforceStatus>
          <BillingStatus>Cancelled</BillingStatus>
          <RenewStatus>OK</RenewStatus>
          <Auditable />
          <PolicyStatus>Cancelled</PolicyStatus>
          <DeprecatedBy />
          <IssuedDate>2026-02-24T14:10:37</IssuedDate>
          <IssuedUserName>OMSNICPDC\efigueroa</IssuedUserName>
        </transaction>
      </transactions>
      <policyAdminProcessing id="p09E72DCD1CAB4502A9A0741D47BBCD2A">
        <AdminManuScriptID>TransACTMaster2_10</AdminManuScriptID>
        <Description>Tail-Committed</Description>
        <ConstantsPolicyAdminProcessingSetPrintJob>FD1CDEC</ConstantsPolicyAdminProcessingSetPrintJob>
        <OldPrintJob>FD1CDEC</OldPrintJob>
        <ConstantsPolicyAdminProcessingCanSetOldPrintJob>0</ConstantsPolicyAdminProcessingCanSetOldPrintJob>
        <CancellationNonPaymentCounter>0</CancellationNonPaymentCounter>
        <CancellationNonPaymentNoticeCounter>2</CancellationNonPaymentNoticeCounter>
        <LastReasonType>nonPay</LastReasonType>
      </policyAdminProcessing>
      <UseSegmentValues>0</UseSegmentValues>
      <UseLossHistory>0</UseLossHistory>
      <UseExtractMapQuote>0</UseExtractMapQuote>
      <CurrentManuScriptID>OMSNIC_Billing_AccountPlanSetup_DirectBase_1_0_0_0</CurrentManuScriptID>
    </policyAdmin>
    <CurrentTransactionStatus>Committed</CurrentTransactionStatus>
    <userDetails userID="10331" id="u6C4F215B50894410B97A24307992126C">
      <userName producer="0" passwordExpiration="" securityLevel="3" status="0" id="uF4D930B917FB443C8AD56ACC34102DF0">webunderwriter</userName>
      <fullName>Web Underwriter</fullName>
      <email />
      <custom />
      <agency agencyID="1" id="a4ABD2C998FB44614BC17B68B91CF2EFD">Internal Users</agency>
      <userDetail id="u9AD381CA6FE04712A01A0A2B32CE85F4">
        <agency agencyID="1" homeEntity="1" producer="0" id="a9FDC88050615474F891907177AF94A50">Internal Users</agency>
        <Role name="BillingLogin" id="R6BEC97AD2219487286EB44C12F647DB3" />
        <Role name="CreateAndAssignTasks" id="RCFE396FFBA4B418EBB313912E24F99D5" />
        <Role name="Underwriter" id="RC9BE9F75E71F475C8604D4EB53D8AD18" />
      </userDetail>
      <contextList id="c527431DD4D7D4E02B8FBFA57E7BECE8A">
        <context name="Underwriter" id="c10AABAE7CE39462291BFDE7B89E78680" />
      </contextList>
    </userDetails>
    <agencyDetails id="a276E71DA7FEE4C66BD45C16673C495B2">
      <agency agencyID="10041" id="a5480E38DF1AD41198F2773B1D7C5A926">
        <name>Walsh Insurance</name>
        <reference>0000788063</reference>
        <contactName />
        <entityTypeID>1</entityTypeID>
        <entityType>Agency</entityType>
        <phoneNumber>(800) 853-3820</phoneNumber>
        <phoneExtension />
      </agency>
      <contextList />
      <mailingAddress id="mDB0ED1AE8EFA49E7BB58CF2BC4E30BD1">
        <address1>726 Exchange Street</address1>
        <address2>Ste 200</address2>
        <city>Buffalo</city>
        <state>NY</state>
        <ZIP>14210</ZIP>
      </mailingAddress>
      <Roles count="4" id="R1E7974B1D6704B3A8D2FCDFBAED7A036">
        <Role name="Agent" description="Administers self" inUse="False" id="R4C77B33098B442C686E9E0B0AEA2EB65">
          <Pools />
        </Role>
        <Role name="BillingLogin" description="Holds common login privileges for all billing roles" inUse="False" id="RF352CBFA4A214FD883F4E2623C019080">
          <Pools />
        </Role>
        <Role name="Underwriter" description="Underwriter privileges" inUse="False" id="R81913691C28347DDAF5AA0E69D2F3DBD">
          <Pools />
        </Role>
        <Role name="UnderwriterSenior" description="Senior Underwriter" inUse="False" id="R7611D711202B4CABBA6CD39185415E24">
          <Pools />
        </Role>
      </Roles>
      <misc id="m2FE0AA3C7990445B9A0CD7228623D02A">
        <AgencyEmail>cgarvelli@walshins.com</AgencyEmail>
        <FaxNumber />
        <Address3 />
        <Address4 />
        <Suite />
        <Signature />
        <OMSNICUnderwriter>10236</OMSNICUnderwriter>
        <FortressUnderwriter>10304</FortressUnderwriter>
        <IssueAuthorityForAgency>1</IssueAuthorityForAgency>
      </misc>
    </agencyDetails>
    <ProductVersion />
    <userinterface id="u7F4B597153A24DA1A0E73DA653E5E535">
      <VIPBanner>Attention: This record belongs to a Very Important Person (VIP)!</VIPBanner>
      <CurrentLanguage>English</CurrentLanguage>
      <LocationDetailReturnPage>PolicyDetail</LocationDetailReturnPage>
      <LocationDetailReturnPageSet>MainInterview</LocationDetailReturnPageSet>
      <HeaderValue>Jack  08DCF97D6C9D7004_LN DDS</HeaderValue>
      <Message>Please list all the claims made prior to Fortress</Message>
    </userinterface>
    <page id="pCBE0E6382C51491B922344C79796F291" />
    <users id="uD475298C30274BA39B380BB5F69CDAA4">
      <CEOName>Matthew J. Nielsen</CEOName>
      <CEOSignature>C:\Program Files\Duck Creek\Suite\Policy\images\Signatures\Matthew J. Nielsen.jpg</CEOSignature>
      <CEOTitle>JD, CPCU</CEOTitle>
      <OnlineData.listEntityUsersRs status="success" id="OD0CDA63212A44DA79DCD2B483B2B0263">
        <Users count="3" id="U9B74A1885F6F487C8D21771248600B69">
          <User id="10304" name="OMSNICPDC\marreola" fullName="Marco Arreola" email="marco.arreola@fortressins.com" partyID="0" status="0" failedLogins="0">
            <Roles count="3" id="R7182E85CD22F49629C5C482984D06F29">
              <Role id="1" name="Agent" inUse="False">
                <Pools />
              </Role>
              <Role id="6" name="Underwriter" inUse="False">
                <Pools />
              </Role>
              <Role id="54" name="BillingLogin" inUse="False">
                <Pools />
              </Role>
            </Roles>
            <UserEntityMemberships id="UB820E081AF6E4D2C8C3F14498CBAD356">
              <UserEntityMembership userID="10304" entityID="10041" entityName="Walsh Duffield Companies, Inc." home="False" producer="True" id="U6BB52F1E7A8B4B919682E0B642AEC4FD" />
            </UserEntityMemberships>
          </User>
          <User id="10236" name="OMSNICPDC\ride" fullName="Russ Ide" email="russ.ide@omsnic.com" partyID="0" status="0" failedLogins="0">
            <Roles count="3" id="R7ADB40C33F7E48BA8A9EC7B12D7B2329">
              <Role id="1" name="Agent" inUse="False">
                <Pools />
              </Role>
              <Role id="6" name="Underwriter" inUse="False">
                <Pools />
              </Role>
              <Role id="54" name="BillingLogin" inUse="False">
                <Pools />
              </Role>
            </Roles>
            <UserEntityMemberships id="U28582F376B1442AFBDDF81904CAFFD83">
              <UserEntityMembership userID="10236" entityID="10041" entityName="Walsh Duffield Companies, Inc." home="False" producer="True" id="UCDE53EDCFC184C178F087C0A427980AE" />
            </UserEntityMemberships>
          </User>
          <User id="10029" name="ride" fullName="Russ Ide" email="russ.ide@omsnic.com" partyID="0" status="0" failedLogins="0">
            <Roles count="3" id="R16C0705AD2C447D48CF04787E210D265">
              <Role id="1" name="Agent" inUse="False">
                <Pools />
              </Role>
              <Role id="54" name="BillingLogin" inUse="False">
                <Pools />
              </Role>
              <Role id="10008" name="UnderwriterSenior" inUse="False">
                <Pools />
              </Role>
            </Roles>
            <UserEntityMemberships id="U723050AE3C1A4B36B755BF50ACE49376">
              <UserEntityMembership userID="10029" entityID="10041" entityName="Walsh Duffield Companies, Inc." home="False" producer="True" id="UC2FB473B8E3441AFB3D9E23E0F362823" />
            </UserEntityMemberships>
          </User>
        </Users>
      </OnlineData.listEntityUsersRs>
    </users>
    <workflowdata id="w3178117F2279441D8E291C3E33B648A7">
      <ReferRemarks />
      <ApproveRemarks />
    </workflowdata>
    <carrier id="c037CCC9E6CFE4D2B80894F7C976413EB">
      <StoredProc.callStoredProcRs status="success" id="S47DD7D09F9804C1C85971FBBADFB9B0A">
        <rows id="r869A679E27644A619AA17015F8DFCC3C">
          <row rowID="1" id="rB619432D982B40E496C952AB33D29919">
            <EntityID>10001</EntityID>
            <Name>Fortress</Name>
            <Address1>425 N. Martingale Road</Address1>
            <Address2>Suite 900</Address2>
            <City>Schaumburg</City>
            <State>IL</State>
            <Zipcode>60173</Zipcode>
            <PhoneNumber>(800) 522-6675</PhoneNumber>
            <FaxNumber />
            <EntityTypeID>2</EntityTypeID>
          </row>
        </rows>
        <rowStats count="1" id="r3EB55F5E9DCE435ABBE488A57B687AF2" />
      </StoredProc.callStoredProcRs>
    </carrier>
    <ActivePage>Pricing</ActivePage>
    <ActivePageSet>MainInterview</ActivePageSet>
    <DataHubIntegration id="D890C7784C0994958BB14945D40AAC5A7">
      <PriorStatus>InForce</PriorStatus>
      <PreAction>InForce</PreAction>
    </DataHubIntegration>
    <billingmethod id="b233C6C2D6D834C6B96ED3BBFE2CAFD73" />
    <PageSupportData id="PEC26594904484A608383AA85525ADE1D">
      <CodeLists id="C4DBF646917CA41C29E2DE8F10AFC5D9C">
        <CodeList id="CECEF865B6AB24E979782A77A5221836B" ListName="BIL_CARDTYPE">
          <ListEntry id="LC8E097C9E1C142C793BDDC33995EDAC2" />
        </CodeList>
      </CodeLists>
    </PageSupportData>
    <TotalPremium change="3294" id="T43279FA8FB734E308C6D220BCD7311E1" written="3294">3294</TotalPremium>
    <TotalPurePremium written="3294" id="T9EE66B99206E4CBD964152411E2367FF" change="3294">3294</TotalPurePremium>
    <TotalTaxesSurcharges written="0" id="T7D900D9339E548F3AE3B6C0BDD23EDE9" change="0">0</TotalTaxesSurcharges>
    <FormsManuscript>Fortress_MedicalProfessionalLiability_Forms_US</FormsManuscript>
    <FormsIRFManuscript />
    <StatusForIR>TailInforce</StatusForIR>
    <ExecuteSuitePostPrintEvent>0</ExecuteSuitePostPrintEvent>
    <PrintFileName>New Business^_ViewPolicyForms^3222566^10_31_2024 124208462_DUMP.pdf</PrintFileName>
    <PrintFormCaption>New Business _ViewPolicyForms (1)</PrintFormCaption>
    <system id="s63F4A7140F454AADA213DFCB0B7B0364">
      <Spacer />
    </system>
    <BlackOutDatesCalendarRecord id="B41C9F4929196425EB7D98A41BCDD353A">
      <Calendar id="C8E3B83B1AC5140D2A91DE7A04116ABF4">
        <Name>DCT_Schedule</Name>
      </Calendar>
    </BlackOutDatesCalendarRecord>
    <collectionmethod id="c75E07FD38EFB454094A5FA624183E1AD" />
    <HeaderAdjustLastTransactionItems />
    <TransACTHeader>Jack  08DCF97D6C9D7004_LN DDS</TransACTHeader>
    <ExecuteSuspensionOrMilitarySuspensionForForms>success</ExecuteSuspensionOrMilitarySuspensionForForms>
    <CanCallAAOMSRq>0</CanCallAAOMSRq>
  </data>
  <messages />
</session>​‌


write me a stored procedure which will make changes in this XML from all the 3294.00 to 2040.00.
