```sql
USE PAC_Policy;

BEGIN TRY

    BEGIN TRANSACTION;

    DECLARE @HistoryId BIGINT = 692950;
    DECLARE @NewValue DECIMAL(19,4) = 35.00;
    DECLARE @XmlData XML;

    -- ============================================================
    -- 1. Load ONLY HistoryId = 692950
    -- ============================================================

    SELECT @XmlData = CAST(XmlData AS XML)
    FROM History
    WHERE HistoryId = @HistoryId;


    -- ============================================================
    -- 2. Safety check
    --
    -- Make sure the targeted transaction is actually:
    --     Type       = Tail
    --     HistoryID  = 692950
    --     Charge     = 6
    --     TermPremium= 6
    --     NewPremium = 6
    --
    -- If any value is different, THROW and make NO changes.
    -- ============================================================

    IF NOT EXISTS
    (
        SELECT 1
        FROM
        (
            SELECT
                T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
                T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
                T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
                T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
                T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
            FROM @XmlData.nodes(
                '/session/data/policy/line/transactions/transaction'
            ) AS T(N)
        ) AS X
        WHERE X.TransactionType = 'Tail'
          AND X.TransactionHistoryId = @HistoryId
          AND X.Charge = 6
          AND X.TermPremium = 6
          AND X.NewPremium = 6
    )
    BEGIN
        THROW 50001,
              'SAFETY CHECK FAILED: Expected Tail transaction with HistoryID 692950 and Charge/TermPremium/NewPremium = 6 was not found. No changes were made.',
              1;
    END;


    -- ============================================================
    -- 3. Update ONLY Charge
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            /session/data/policy/line/transactions/transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /Charge/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 4. Update ONLY TermPremium
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            /session/data/policy/line/transactions/transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /TermPremium/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 5. Update ONLY NewPremium
    -- ============================================================

    SET @XmlData.modify('
        replace value of
        (
            /session/data/policy/line/transactions/transaction
            [Type="Tail" and HistoryID=sql:variable("@HistoryId")]
            /NewPremium/text()
        )[1]
        with sql:variable("@NewValue")
    ');


    -- ============================================================
    -- 6. Update ONLY History.XMLData
    --
    -- No other relational columns are changed.
    -- ============================================================

    UPDATE History
    SET XmlData = CAST(@XmlData AS VARCHAR(MAX))
    WHERE HistoryId = @HistoryId;


    -- ============================================================
    -- 7. Verify the exact transaction BEFORE COMMIT
    -- ============================================================

    SELECT
        T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
        T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
        T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
        T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
        T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
    FROM @XmlData.nodes(
        '/session/data/policy/line/transactions/transaction'
    ) AS T(N)
    WHERE T.N.value('(Type/text())[1]', 'nvarchar(50)') = 'Tail'
      AND T.N.value('(HistoryID/text())[1]', 'bigint') = @HistoryId;


    -- ============================================================
    -- 8. COMMIT
    -- ============================================================

    COMMIT TRANSACTION;

END TRY

BEGIN CATCH

    IF @@TRANCOUNT > 0
        ROLLBACK TRANSACTION;

    SELECT
        ERROR_NUMBER() AS ErrorNumber,
        ERROR_MESSAGE() AS ErrorMessage,
        'ROLLBACK - NO CHANGES COMMITTED' AS Status;

    THROW;

END CATCH;
```
