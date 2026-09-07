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
Set @HistoryId=692950; 
Set @Policynumber=3223087;

Set @TotalPremium =35.00;
Set @Change =35.00;
Set @WrittenPremium =35.00;



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
WHERE HistoryId = 692950;



SELECT
    HistoryId,
    PolicyNumber,
    CAST(XmlData AS XML)
FROM History
WHERE HistoryId = 692950;
