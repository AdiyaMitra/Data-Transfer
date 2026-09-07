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
    FROM @XmlData.nodes(
        '/session/data/policy/line/transactions/transaction'
    ) AS T(N)
    WHERE
        N.value('(Type/text())[1]', 'nvarchar(50)') = 'Tail'
        AND N.value('(HistoryID/text())[1]', 'bigint') = 692950
        AND N.value('(Charge/text())[1]', 'decimal(19,4)') = 6.0000
        AND N.value('(TermPremium/text())[1]', 'decimal(19,4)') = 6.0000
        AND N.value('(NewPremium/text())[1]', 'decimal(19,4)') = 6.0000
)
BEGIN
    RAISERROR(
        'SAFETY CHECK FAILED: Target Tail transaction was not found with the expected values. No changes were made.',
        16,
        1
    );

    ROLLBACK TRANSACTION;
    RETURN;
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








```sql
USE PAC_Policy;

DECLARE @HistoryId BIGINT = 692950;
DECLARE @XmlData XML;

SELECT @XmlData = CAST(XmlData AS XML)
FROM History
WHERE HistoryId = @HistoryId;

-- Check whether History row was found
SELECT
    CASE
        WHEN @XmlData IS NULL THEN 'XML DATA IS NULL / HISTORY ROW NOT FOUND'
        ELSE 'XML DATA LOADED'
    END AS Status;


-- Show ALL transactions from this History record
SELECT
    T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
    T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
    T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
    T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
    T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
FROM @XmlData.nodes(
    '/session/data/policy/line/transactions/transaction'
) AS T(N);
```


``` sql
USE PAC_Policy;

SELECT
    HistoryId,
    CAST(XmlData AS VARCHAR(MAX)) AS XmlData
FROM History
WHERE HistoryId = 692950;
```

``` sql
USE PAC_Policy;

DECLARE @HistoryId BIGINT = 692950;
DECLARE @XmlData XML;

SELECT @XmlData = CAST(XmlData AS XML)
FROM History
WHERE HistoryId = @HistoryId;

SELECT
    T.N.value('(Type/text())[1]', 'nvarchar(50)') AS TransactionType,
    T.N.value('(HistoryID/text())[1]', 'bigint') AS TransactionHistoryId,
    T.N.value('(Charge/text())[1]', 'decimal(19,4)') AS Charge,
    T.N.value('(TermPremium/text())[1]', 'decimal(19,4)') AS TermPremium,
    T.N.value('(NewPremium/text())[1]', 'decimal(19,4)') AS NewPremium
FROM @XmlData.nodes('//transaction') AS T(N)
WHERE T.N.value('(HistoryID/text())[1]', 'bigint') = @HistoryId;
```
